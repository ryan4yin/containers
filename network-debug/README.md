# Image for network debug

- https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/

## Usage

```
kubectl debug -n default mypod -it --image=ghcr.io/ryan4yin/network-debug

# or maybe a better choice
kubectl debug -n default mypod -it --image=nicolaka/netshoot
```

