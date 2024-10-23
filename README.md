# ryan4yin's container images

- Docker Hub: <https://hub.docker.com/r/ryan4yin>
- Github Packages(Container Registry): <https://github.com/ryan4yin?tab=packages>
- GIthub Workflow Syntax:
  <https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions>

## Multi-arch images

There are several ways to build multi-arch images:

1. Emulate the target architecture on the build machine
   - This is the most common way to build multi-arch images. The build machine emulates
     the target architecture using QEMU, and then builds the image as if it were running
     on the target architecture.
   - The downside of this approach is that it's slow, so **it's not suitable for building
     images that need a lot comlication or IO operations**.
2. Use cross-compilation to build the image
   - This approach is fast, but it requires that the image's build process supports
     cross-compilation.
   - it's ideal for building images for go/rust projects.
   - Related Projects & Examples:
     - Go: <https://github.com/ko-build/ko>,
       [Example multi-arch Dockerfile for Go projects](https://gist.github.com/AverageMarcus/78fbcf45e72e09d9d5e75924f0db4573)
     - Rust: <https://vnotes.pages.dev/fast-multi-arch-docker-for-rust/>
3. Build each architecture's image on a machine with that architecture, and then merge the
   images into a single multi-arch image
   - This approach is the fastest, but the workflow will be more complex.
   - It's ideal for building images that has a complex compile process and is hard to
     cross-compile.

Useful build tools: buildkit.

Useful Build Pipelines: Github Actions, Gitlab CI/CD,
[act](https://github.com/nektos/act),
[gitea/act_runner](https://gitea.com/gitea/act_runner) etc.
