FROM moby/buildkit:v0.16.0-rootless

USER root

# https://github.com/awslabs/amazon-ecr-credential-helper/releases/tag/v0.9.0
RUN ARCH=$(uname -m) \
    && case ${ARCH} in \
        x86_64) TARGETARCH=amd64 ;; \
        aarch64) TARGETARCH=arm64 ;; \
        *) echo "Unsupported architecture: ${ARCH}"; exit 1 ;; \
    esac \
    && wget -O docker-credential-ecr-login "https://amazon-ecr-credential-helper-releases.s3.us-east-2.amazonaws.com/0.9.0/linux-${TARGETARCH}/docker-credential-ecr-login" \
    && chmod +x docker-credential-ecr-login \
    && mv docker-credential-ecr-login /usr/local/bin

RUN apk add python3 py3-pip aws-cli bash

# Run as non-root user
USER 1000:1000
ENV HOME=/home/user
ENV USER=user
ENV XDG_RUNTIME_DIR=/run/user/1000
ENV TMPDIR=/home/user/.local/tmp
ENV BUILDKIT_HOST=unix:///run/user/1000/buildkit/buildkitd.sock
VOLUME /home/user/.local/share/buildkit


SHELL ["/bin/bash", "--login", "-c"]
ENTRYPOINT [ "/bin/bash" ]

