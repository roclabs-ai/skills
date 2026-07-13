---
name: tinalinux-container-workflows
description: Operate the verified T113 TinaLinux/Qt cross-build environment with Docker/OrbStack or Apple Container, inspect the ARMv7 musl toolchain, open an interactive build shell, compile an external app, prepare a staged app root, or create a signed device-platform update package. Use for requests mentioning TinaLinux in Docker, Apple `container`/App Container, the `tina-qt:5.12.9-cmake3.22-arm64` image, T113 container cross-compilation, container image loading, or container runtime troubleshooting in the device-platform workspace.
---

# TinaLinux Container Workflows

Use the project’s verified linux/arm64 Tina Qt image and canonical wrappers without confusing the container host architecture with the T113 target architecture.

## Preserve the architecture contract

- Treat macOS as the host, `linux/arm64` as the current container architecture, and ARMv7 + musl as the device artifact architecture.
- Expect container-side host tools such as `qmake`, `moc`, and `arm-openwrt-linux-gcc` to be aarch64 executables.
- Expect generated device binaries to be 32-bit ARM EABI5 and use `/lib/ld-musl-armhf.so.1` when dynamically linked.
- Reject distro `arm-linux-gnueabihf-*` or other glibc-targeted toolchains for device artifacts.
- Do not describe the current arm64 Tina Qt image as a complete firmware SDK. It contains the app cross-toolchain, Qt, and target staging data. Require `build/envsetup.sh`, the selected board files, and full Tina sources before offering `lunch`, full `make`, or `pack`.

## Resolve context before acting

1. Locate the current project and read its `AGENTS.md` files.
2. Resolve `device-platform`, the external business app, and the sibling `tinalinux-sdk-doc` dynamically. Do not reuse stale `/Users/roc/Documents/...` examples when the actual workspace is under `/Volumes/Extend/...`.
3. Verify referenced paths exist, especially the external app and OCI archive.
4. Read [container-commands.md](references/container-commands.md) before running or proposing exact runtime commands.
5. Inspect the current canonical wrapper before executing it because its supported variables are the source of truth:
   - `device-platform/scripts/arm/package-device-app-usb-release.sh`
   - `device-platform/scripts/arm/prepare-device-app-root.sh`
   - `tinalinux-sdk-doc/scripts/t113-docker-build.sh`
6. When using `t113-docker-build.sh` with the current arm64 workflow, explicitly override both `--image` and `--platform`; that helper still defaults to the legacy amd64 image.

## Select the workflow

| Intent | Use |
| --- | --- |
| Inspect tools or open a shell | Direct Docker or Apple Container command from the reference |
| Compile an app only | `tinalinux-sdk-doc/scripts/t113-docker-build.sh` |
| Prepare a staged app root without signing | `device-platform/scripts/arm/prepare-device-app-root.sh` (Docker only) |
| Build, sign, and export an update package | `device-platform/scripts/arm/package-device-app-usb-release.sh` |
| Build platform runtime | `device-platform/scripts/build-platform-runtime.sh` in a verified toolchain environment |
| Build a package into firmware | Export packages first, then use a verified full Tina SDK |
| Build complete firmware | Stop and verify a full SDK; do not use the partial Tina Qt app image by assumption |

Prefer Docker/OrbStack for project work. Use Apple Container when the user requests it or Docker is unsuitable and the installed version passes service, image, and transfer checks. The project’s Apple Container 1.0.0 validation found direct bind mounts and stdin transfer less reliable than Docker, so report that limitation instead of silently claiming parity.

## Execute in this order

1. Run non-mutating preflight checks for the CLI, daemon/service, image, archive, and required workspace paths.
2. If execution was requested, start only the chosen runtime. Do not start both.
3. Load/import the image only if absent. Let the project packaging wrapper perform its tested Docker OCI import path; do not invent a second import implementation.
4. Prefer the highest-level wrapper that matches the intent. Run business-app wrappers from the business app repository or set `APP_REPO` explicitly.
5. Keep USB writing on the host. Do not expose a physical USB device to the build container.
6. Validate the runtime and output architecture before reporting success.

## Guard side effects

- Do not write or eject a USB drive unless explicitly requested. Omit `USB_MOUNT`, `USB_DRIVE`, and `WRITE_USB=1` otherwise.
- Do not deploy through ADB unless explicitly requested; packaging and deployment are separate workflows.
- Preserve the platform signing key. Do not enable `GENERATE_DEV_KEY=1` unless the user explicitly asks for a disposable development package.
- Do not use `--privileged`, mount the Docker socket, or expose host devices for ordinary compilation.
- Do not delete images, containers, build outputs, or caches merely to fix a startup problem.
- Treat an interactive shell as diagnostic. Use the project wrapper for repeatable releases.

## Validate completion

Check all applicable layers:

1. Container: `uname -m` is `aarch64` for the current arm64 image.
2. Host tools: compiler and Qt tools are executable inside the container.
3. Target: a smoke binary or app binary is 32-bit ARM, not aarch64 and not x86-64.
4. ABI: dynamic artifacts target musl, not glibc.
5. Workflow output: the expected build directory, staged root, or signed `update-package-*` exists on the host.
6. Release safety: signing checks pass and no USB/ADB mutation occurred unless requested.

Report the selected runtime, image reference, resolved app path, exact wrapper used, output path, architecture evidence, and any unverified boundary.
