# Go Cross Build Example

This command will cross-compile the Go application for non-native architectures.

NOTE: It only works when your dockerfile contains the pre-defined BUILDPLATFORM and TARGETPLATFORM build arguments.

```bash
# Using docker
docker build --platform linux/amd64,linux/arm64 -t go-server .

# Using buildkit daemonless
# Highly suitable for use in CI pipelines
# https://github.com/moby/buildkit/blob/master/docs/multi-platform.md#builds-are-very-slow-through-emulation
buildctl-daemonless.sh build \
  --progress=plain \
  --frontend=dockerfile.v0 \
  --local context=. \
  --local dockerfile=. \
  --opt platform=linux/amd64,linux/arm64 \
  --output type=image,name=go-server,oci-mediatypes=true,compression=zstd,compression-level=15,force-compression=true,push=true
```

