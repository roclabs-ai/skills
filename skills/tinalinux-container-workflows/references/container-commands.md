# Project paths and container commands

Load this reference when exact TinaLinux container commands are needed. Resolve paths first; the values below are verified defaults, not permission to assume missing directories exist.

## Verified defaults

```text
device-platform: /Volumes/Extend/roc-workspace/device-platform
SDK docs/tools:   /Volumes/Extend/roc-workspace/tinalinux-sdk-doc
Docker image:     tina-qt:5.12.9-cmake3.22-arm64
Apple image:      docker.io/library/tina-qt:5.12.9-cmake3.22-arm64
OCI archive:      /Volumes/Extend/roc-workspace/tinalinux-sdk-doc/docker-images/arm64-rebuild/tina-qt-5.12.9-cmake3.22-arm64.oci.tar
Tina path:        /home/meetyoo/t113/Tina-Linux
Toolchain bin:    /home/meetyoo/t113/Tina-Linux/prebuilt/gcc/linux-arm64/arm/toolchain-sunxi-musl/toolchain/bin
Qt root:          /home/meetyoo/t113/QT/staging_dir/qt-everywhere-src-5.12.9/arm-qt
Target staging:   /home/meetyoo/t113/Tina-Linux/out/t113-bingpi_md70/staging_dir/target
```

The arm64 image contains enough Tina paths for app cross-compilation but was assembled from the toolchain, Qt target/host files, and target staging directory. It is not proof that full Tina firmware sources are present.

## Read-only preflight

Resolve variables without changing runtime state:

```bash
DEVICE_PLATFORM=/Volumes/Extend/roc-workspace/device-platform
SDK_DOC=/Volumes/Extend/roc-workspace/tinalinux-sdk-doc
OCI="$SDK_DOC/docker-images/arm64-rebuild/tina-qt-5.12.9-cmake3.22-arm64.oci.tar"
APP_REPO=/path/to/business-app

test -d "$DEVICE_PLATFORM"
test -d "$APP_REPO"
test -f "$OCI"
command -v docker
command -v container
docker --version
container --version
```

Check Docker separately from the CLI:

```bash
docker info
docker image inspect tina-qt:5.12.9-cmake3.22-arm64 \
  --format '{{.Os}}/{{.Architecture}}'
```

If `docker info` points at a missing OrbStack socket, OrbStack is stopped or the Docker context is stale. When execution is requested, inspect `docker context show`; start OrbStack with `orb start` only if that context is intended, then retry `docker info`.

Check Apple Container separately from the CLI:

```bash
container system status
container image list
```

If the service is stopped and execution is requested:

```bash
container system start
```

Apple Container command availability can vary by installed release and macOS. Check the installed CLI help/version when behavior differs from the project’s validated 1.0.0 path.

## Load the Apple Container image

Apple Container consumes the OCI archive directly:

```bash
container image load \
  --input "$OCI"
```

Then verify the image name shown by `container image list`. The expected reference is:

```text
docker.io/library/tina-qt:5.12.9-cmake3.22-arm64
```

For Docker packaging, do not substitute `docker load` for the project logic without proving compatibility. The wrapper below checks for `linux/arm64` and performs the repository’s tested OCI-to-Docker import when needed.

## Smoke-test the image

Docker:

```bash
docker run --rm --platform linux/arm64 \
  tina-qt:5.12.9-cmake3.22-arm64 \
  /bin/bash -lc 'uname -m; cmake --version | head -n 1; qmake -v; arm-openwrt-linux-gcc --version | head -n 1'
```

Apple Container:

```bash
container run --rm --platform linux/arm64 --cpus 4 --memory 8G \
  docker.io/library/tina-qt:5.12.9-cmake3.22-arm64 \
  /bin/bash -lc 'uname -m; cmake --version | head -n 1; qmake -v; arm-openwrt-linux-gcc --version | head -n 1'
```

Expected container/tool results:

```text
uname: aarch64
CMake: 3.22.6
Qt: 5.12.9
compiler family: arm-openwrt-linux-*
```

## Open an interactive app-build shell

Docker is the preferred interactive path:

```bash
docker run --rm -it --platform linux/arm64 \
  --mount "type=bind,src=$APP_REPO,dst=/workspace/app" \
  --workdir /workspace/app \
  tina-qt:5.12.9-cmake3.22-arm64 \
  /bin/bash
```

Inside the shell, create the compatibility symlink only if a legacy project expects `linux-x86`:

```bash
mkdir -p /home/meetyoo/t113/Tina-Linux/prebuilt/gcc
test -e /home/meetyoo/t113/Tina-Linux/prebuilt/gcc/linux-x86 || \
  ln -s linux-arm64 /home/meetyoo/t113/Tina-Linux/prebuilt/gcc/linux-x86
```

Apple Container supports `-it`, `--volume`, and `--workdir`, but the project’s 1.0.0 validation found bind-mount/file-transfer behavior less stable. Use this for diagnostics and verify host writes afterward:

```bash
container run --rm -it --platform linux/arm64 --cpus 4 --memory 8G \
  --volume "$APP_REPO:/workspace/app" \
  --workdir /workspace/app \
  docker.io/library/tina-qt:5.12.9-cmake3.22-arm64 \
  /bin/bash
```

## Compile only

For a qmake, CMake/Ninja, Makefile, or single-file project, use the external helper. Preview first:

```bash
cd "$APP_REPO"
"$SDK_DOC/scripts/t113-docker-build.sh" \
  --image tina-qt:5.12.9-cmake3.22-arm64 \
  --platform linux/arm64 \
  --print \
  .
```

Then execute:

```bash
cd "$APP_REPO"
"$SDK_DOC/scripts/t113-docker-build.sh" \
  --image tina-qt:5.12.9-cmake3.22-arm64 \
  --platform linux/arm64 \
  .
```

The helper still defaults to `registry.roclabs.ai/tina-qt:latest` on `linux/amd64`; treat that as the legacy route and do not omit these overrides in the current arm64 workflow. The helper does not import the local arm64 OCI archive. If the arm64 Docker image is absent, use the project packaging wrapper's tested import path or explicitly prepare the image before this compile-only command.

Use `--pro`, `--mode`, `--writable`, `--build-dir`, and `--` only after reading `--help` or `Docs/20-Docker交叉编译/01-快速开始与脚本入口.md`.

If auto-detection reports multiple `.pro` files, select the business app explicitly. For the current `lft-water-232` checkout, the verified preview is:

```bash
"$SDK_DOC/scripts/t113-docker-build.sh" \
  --image tina-qt:5.12.9-cmake3.22-arm64 \
  --platform linux/arm64 \
  --pro lft-water-screen.pro \
  --print \
  "$APP_REPO"
```

## Prepare a staged app root without signing

This path currently supports Docker only:

```bash
APP_REPO="$APP_REPO" \
OUTPUT_ROOT="$APP_REPO/dist/dev-staged" \
MAIN_BINARY=water \
BUILD=1 \
sh "$DEVICE_PLATFORM/scripts/arm/prepare-device-app-root.sh"
```

Replace `water` with the project’s real main binary. Keep `OUTPUT_ROOT` under `APP_REPO`.

## Build and sign an update package

Run from the external business app repository. Docker is the default and preferred runtime:

```bash
cd "$APP_REPO"
ARM_USB_RUNTIME=docker \
sh "$DEVICE_PLATFORM/scripts/arm/package-device-app-usb-release.sh"
```

Use Apple Container explicitly:

```bash
cd "$APP_REPO"
ARM_USB_RUNTIME=apple-container \
ARM_TINA_QT_IMAGE=docker.io/library/tina-qt:5.12.9-cmake3.22-arm64 \
sh "$DEVICE_PLATFORM/scripts/arm/package-device-app-usb-release.sh"
```

These commands build and sign but do not write USB by default. Only add `USB_MOUNT=/Volumes/UPDATE CONFIRM=1` after the user explicitly requests physical USB replacement.

For an interactive guided flow:

```bash
cd "$APP_REPO"
sh "$DEVICE_PLATFORM/scripts/arm/guided-package-device-app-usb-release.sh"
```

## Verify a generated target binary

Run inside the container, or use an equivalent host tool that understands ELF:

```bash
file /path/to/device-binary
readelf -h /path/to/device-binary
readelf -l /path/to/device-binary | grep 'Requesting program interpreter'
```

Expected dynamic target characteristics:

```text
ELF 32-bit LSB executable, ARM, EABI5
/lib/ld-musl-armhf.so.1
```

An `aarch64` result is a container-host binary, not a T113 device binary. An x86-64 result is a host binary. A glibc interpreter indicates the wrong target ABI.

## Full firmware boundary

Before any firmware command, verify the environment really contains a complete SDK:

```bash
TINA=/home/meetyoo/t113/Tina-Linux
test -f "$TINA/build/envsetup.sh"
test -d "$TINA/target/allwinner"
test -d "$TINA/device/config/chips/t113"
test -d "$TINA/lichee/linux-5.4"
```

If any check fails, stop: the current app image cannot build complete firmware. Use a verified full Tina SDK host/container instead.

Only in a complete SDK, use a Bash session and select an actually listed board combo:

```bash
cd /home/meetyoo/t113/Tina-Linux
source build/envsetup.sh
lunch
make V=s
pack
```

Do not guess the lunch target. Inspect `target/allwinner/*/vendorsetup.sh` and preserve `.config`/board-specific state. Full builds are large and long-lived; do not run them in an ephemeral `--rm` container unless the complete SDK and output directories are mounted persistently.

## Canonical evidence

Project sources:

- `device-platform/scripts/arm/package-device-app-usb-release.sh`
- `device-platform/scripts/arm/prepare-device-app-root.sh`
- `device-platform/docs-20260629/tinalinux-qt-usb-adb-workflows.md`
- `tinalinux-sdk-doc/Docs/20-Docker交叉编译/01-快速开始与脚本入口.md`
- `tinalinux-sdk-doc/Docs/22-Docker镜像与分发/Tina-Qt-Apple-container-arm64镜像制作.md`

English upstream references:

- Apple Container: https://github.com/apple/container
- Apple Container command reference: https://github.com/apple/container/blob/main/docs/command-reference.md
- Apple Container file sharing and resource guidance: https://github.com/apple/container/blob/main/docs/how-to.md
- Docker `run`: https://docs.docker.com/reference/cli/docker/container/run/
