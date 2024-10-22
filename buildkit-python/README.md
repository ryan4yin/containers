# BuildKit with Python, AWS CLI, etc.

## Replacements

Buildkit is mainly used for rootless & daemonless image building, other similar projects:

1. https://github.com/moby/buildkit: moby(docker)'s official project, can perform as a non-root user. BuildKit supports "cross-building" multi-arch containers by leveraging QEMU.
1. https://github.com/GoogleContainerTools/kaniko: not a google official project, seems not very actively maintained, and required privileds to work.

## Gitlab CI example

```yaml
# buildkit:
# https://github.com/moby/buildkit/blob/master/docs/rootless.md
# gitlab runner:
# https://docs.gitlab.com/runner/executors/kubernetes/index.html

container-get-tag:
  stage: pre-container-build-stage
  image: busybox
  script:
    # All other branches are tagged with the currently built commit SHA hash
    - |
      # If pipeline runs on the default branch: Set tag to "latest"
      if test "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH"; then
        tag="latest"
      # If pipeline is a tag pipeline, set tag to the git commit tag
      elif test -n "$CI_COMMIT_TAG"; then
        tag="$CI_COMMIT_TAG"
      # Else set the tag to the git commit sha
      else
        DATETIME=$(TZ=Hongkong date +%y%m%d_%H%M%S)
        tag="${DATETIME}-${CI_COMMIT_SHORT_SHA}"
      fi
    - |
      cat <<EOF > build.env
        IMAGE_TAG=$tag
        IMAGE_URL=ryan4yin/xxx
        CONTEXT_PATH=$(pwd)/images/xxx
      EOF
  # parse tag to the build and merge jobs.
  # See: https://docs.gitlab.com/ee/ci/variables/#pass-an-environment-variable-to-another-job
  artifacts:
    reports:
      dotenv: build.env

build-xxx:arm64:
  stage: build
  tags:
    - arm64
    - linux
  dependencies:
    - container-get-tag # get IMAGE_TAG & other variables from this job
  image: &build_image
    name: "docker.io/ryan4yin/buildkit-python:v0.16.0-rootless"
  variables: &build_variables # For buildkit
    BUILDKITD_FLAGS: --oci-worker-no-process-sandbox

    # overwrite the default values
    KUBERNETES_CPU_REQUEST: "6"
    KUBERNETES_CPU_LIMIT: "8"
    KUBERNETES_MEMORY_REQUEST: "13Gi"
    KUBERNETES_MEMORY_LIMIT: "15Gi"
    KUBERNETES_EPHEMERAL_STORAGE_REQUEST: "50Gi"
  script: &build_script
    - |
      set -ex

      ARCH=$(arch)
      case ${ARCH} in 
          x86_64) ARCH=amd64 ;; 
          aarch64) ARCH=arm64 ;; 
          *) echo "Unsupported architecture: ${ARCH}"; exit 1 ;; 
      esac 

      mkdir -p ~/.docker
      echo '{ "credsStore": "ecr-login" }' > ~/.docker/config.json
      buildctl-daemonless.sh build \
        --progress=plain \
        --frontend=dockerfile.v0 \
        --local context=${CONTEXT_PATH} \
        --local dockerfile=${CONTEXT_PATH} \
        --output type=image,name=${IMAGE_URL}:${IMAGE_TAG}-${ARCH},push=true \
        --export-cache type=registry,mode=max,image-manifest=true,ref=${IMAGE_URL}:buildcache-${ARCH} \
        --import-cache type=registry,ref=${IMAGE_URL}:buildcache

  rules: &build_rules
    - if: $CI_PIPELINE_SOURCE == "merge_request_event" && $CI_BRANCH_NAME == "master"
      changes:
        - images/xxxx/*
      when: on_success
    - when: manual

build-xxx:amd64:
  stage: build
  tags:
    - amd64
    - linux
  image: *build_image
  variables: *build_variables
  script: *build_script
  rules: *build_rules

build-xxx:manifest:
  stage: build
  dependencies:
    - container-get-tag
    - build-xxx:arm64
    - build-xxx:amd64
  image:
    # https://github.com/estesp/manifest-tool
    name: mplatform/manifest-tool:alpine
  script:
    - |
      # 'ARCH' is a placeholder, and will be replaced by manifest-tool
      manifest-tool \
        --username=${CI_REGISTRY_USER} --password=${CI_REGISTRY_PASSWORD} \
        push from-args \
        --platforms linux/amd64,linux/arm64  \
        --template ${IMAGE_URL}:${IMAGE_TAG}-ARCH
        --target ${IMAGE_URL}:${IMAGE_TAG}

  rules: *build_rules
```
