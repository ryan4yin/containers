# Repository Guidelines

Container images built and published for my homelab and personal use. Each image lives in
its own directory with a matching `.github/workflows/build-<name>.yaml` that builds and
pushes it.

## Related Repositories

- [`ryan4yin/k8s-gitops`](https://github.com/ryan4yin/k8s-gitops) — consumes these images;
  when a build changes, bump the image tag there.
- [`ryan4yin/nix-config`](https://github.com/ryan4yin/nix-config) — homelab hosts that run
  some of these images.

## Conventions

- Follow the layout of the existing image directories (`buildkit-python`,
  `kubernetes-python-client`, `network-debug`) rather than inventing a new one.
- Keep credentials out of the repository; images and workflows must take build-time
  secrets from the CI environment.
