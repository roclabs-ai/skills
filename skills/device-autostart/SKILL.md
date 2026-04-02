---
name: device-autostart
description: Deploy current project to an adb-connected TinaLinux device and configure it as the boot-time autostart app, replacing the existing one. Use when user says "deploy to device", "set as autostart", "replace autostart app", "把当前项目设成开机自启", "停掉旧程序，用当前项目", or asks to make the current project the boot-time app on a TinaLinux board. Do NOT use for systemd services or non-adb targets.
---

# Device Autostart

Use this skill when the user says things like "把当前项目设成开机自启", "停掉旧程序，用当前项目", "deploy to device", "set as autostart", "replace autostart app", or asks to deploy the current workspace to a TinaLinux board and make it become the new boot-time app.

This skill is for adb-connected embedded Linux targets where startup is controlled by files such as `/etc/init.d/rc.modules`, not for generic systemd services.

## Quick Start

1. Inspect the current project before changing anything on the device.
2. Find how the project is built, deployed, restarted, and verified.
3. Back up the device boot file before modifying it.
4. Remove or disable the old autostart entry.
5. Deploy the current project.
6. Install the new autostart entry.
7. Restart the app now and verify process, logs, IP, and listening ports.

## Project Discovery

Treat the current workspace as the default target project.

Look for these files first:

- `scripts/deploy*.sh`
- `device/start.sh`
- `device/restart.sh`
- `Makefile`, `CMakeLists.txt`, `*.pro`
- shell variables such as `DEVICE_DIR`, `DEVICE_RC_MODULES`, `ADB_BIN`, `INSTALL_AUTOSTART`

Infer these values before acting:

- device deploy directory (e.g. `/opt/<app-name>`)
- restart script path (e.g. `/opt/<app-name>/bin/restart.sh`)
- process name
- main log path
- whether the project already knows how to install autostart

If the project already contains a deploy script that safely handles build, push, restart, and autostart, prefer using it over reimplementing the workflow by hand.

## TinaLinux Autostart Workflow

Read [tinalinux-autostart.md](./references/tinalinux-autostart.md) when the device uses `/etc/init.d/rc.modules` or when a default demo app needs to be disabled.

Default workflow:

1. Confirm `adb` can see the target device. If not, try `adb kill-server && adb devices`, check USB connection, and report the failure before proceeding.
2. Print a summary of what will change on the device (files modified, entries removed/added, artifacts pushed) and ask the user for confirmation before proceeding.
3. Read the existing boot file and back it up once before editing — store on the device at `/etc/init.d/rc.modules.bak.<timestamp>` and pull a copy to the local project directory via `adb pull`.
4. Identify the old autostart line and remove or comment it out.
5. If the board currently starts a default demo app (e.g. `lv_examples`), disable that line when the new app should own the display.
6. Build the project. If the project uses cross-compilation (e.g., a toolchain prefix like `arm-openwrt-linux-`), verify the toolchain is available before attempting to build. Stop and report if the build fails.
7. Push the built artifacts to the device.
8. Ensure the deployed restart script is executable.
9. Add exactly one autostart line for the new restart script.
10. Start the new app immediately and verify it is healthy.

When replacing an existing app, do not assume its path. Inspect the current boot file first.

## Execution Rules

- Prefer the project's own deploy script when it already supports `INSTALL_AUTOSTART`, `DEVICE_DIR`, `ADB_SERIAL`, or similar overrides.
- Avoid destructive cleanup on the device unless the user explicitly asks for it.
- Preserve unrelated lines in `/etc/init.d/rc.modules`.
- Make idempotent edits: running the flow twice should not duplicate autostart entries.
- Keep backups with a clear suffix before the first change.
- If the device already runs another app, stop or replace only the startup hook you understand.

## Verification

Run all checks from [tinalinux-autostart.md § What to Verify](./references/tinalinux-autostart.md#what-to-verify), plus:

- expected IP configuration is present if the app sets networking
- expected TCP listeners are present if the app exposes services

Prefer concrete device checks over assumptions. If the user asks whether the app is really live, inspect process state, logs, and listening sockets.

## Rollback

If the new app fails to start or the device behaves unexpectedly after switching autostart:

1. Restore the backup boot file from the timestamped device copy or the local pull:
   - `adb push rc.modules.bak.<timestamp> /etc/init.d/rc.modules`
2. Kill the new app process if it is running.
3. Reboot the device or manually start the previous app.
4. Report what went wrong before retrying.

Always remind the user that a backup exists (both on-device and local) and how to restore it.

## Examples

Example 1: Deploy a Qt HMI project
User says: "把当前项目部署到板子上，设成开机自启"
Actions:
1. Discover `scripts/deploy.sh` and `Makefile` in workspace
2. Connect via adb, back up `/etc/init.d/rc.modules`
3. Disable old default demo entry if present
4. Build with cross-toolchain, push artifacts to device deploy directory
5. Add restart script to `rc.modules`, start app
Result: App running on device, confirmed via `ps` and log output

Example 2: Replace an existing autostart app
User says: "replace autostart app with this project"
Actions:
1. Read `rc.modules`, find existing `/opt/old-app/bin/restart.sh` entry
2. Comment out old entry, back up boot file
3. Build and push new project
4. Add new autostart line, verify process and ports
Result: Old app replaced, new app running as boot-time service

## Troubleshooting

Error: `adb: device not found`
Cause: Device not connected or adb daemon stale
Solution: Check USB cable, run `adb kill-server && adb devices`, verify device appears

Error: Cross-compilation toolchain not found
Cause: Toolchain prefix (e.g. `arm-openwrt-linux-gcc`) not on PATH
Solution: Source the SDK environment script or add toolchain bin directory to PATH before building

Error: Duplicate autostart entries after re-deploy
Cause: Idempotency guard failed or manual edits to `rc.modules`
Solution: Inspect `rc.modules` for duplicate lines, remove extras, ensure the skill's grep-based guard is working

Error: App starts but immediately exits
Cause: Missing runtime dependencies on device, wrong permissions, or display conflict with a default demo app
Solution: Check app logs, verify any default demo app is disabled, ensure restart script is executable (`chmod +x`)

## Missing Pieces

If the current project lacks a clear deploy script or startup model, stop after discovery and report exactly what is missing:

- no build entrypoint
- no deploy path
- no restart script
- no known autostart file
- no reliable way to verify success

In that case, propose the minimum missing pieces instead of inventing a fragile deployment flow.
