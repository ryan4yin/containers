# Go Cross Build Example

This command will cross-compile the Go application for non-native architectures.
it only works when your dockerfile contains the pre-defined BUILDPLATFORM and TARGETPLATFORM build arguments.

```bash
docker build --platform linux/amd64,linux/arm64 -t go-server .
```


