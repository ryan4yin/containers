FROM --platform=${BUILDPLATFORM} golang:1.22 as builder

# The following build arguments are pre-defined by Docker Buildx, 
# and can be used to specify the target platform for the build.
# 
# https://docs.docker.com/build/building/variables/#pre-defined-build-arguments
ARG TARGETPLATFORM
ARG BUILDPLATFORM
ARG TARGETOS
ARG TARGETARCH

ENV CGO_ENABLED=0
# https://pkg.go.dev/cmd/go#hdr-Build_and_test_caching
ENV GOMODCACHE=/gomod-cache
ENV GOCACHE=/go-cache

WORKDIR /app/
COPY . .
RUN --mount=type=cache,target=/gomod-cache \
    --mount=type=cache,target=/go-cache \
    GOOS=${TARGETOS} GOARCH=${TARGETARCH} \
    go build -ldflags="-w -s" -o yourapplication main.go

FROM --platform=${TARGETPLATFORM} ubuntu:22.04
WORKDIR /app/
COPY --from=builder /app/yourapplication /app/yourapplication
ENTRYPOINT ["/app/yourapplication"]

