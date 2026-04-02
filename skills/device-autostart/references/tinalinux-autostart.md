# TinaLinux rc.modules Notes

Use this reference when a board controls startup through `/etc/init.d/rc.modules`.

## Common Pattern

- Application files live under an app directory such as `/opt/<app-name>`
- Startup is delegated to a restart script such as `/opt/<app-name>/bin/restart.sh`
- Autostart is enabled by appending that restart script path as a single line in `/etc/init.d/rc.modules`

## Safe Edit Pattern

1. Back up `/etc/init.d/rc.modules` once before the first edit.
2. Read the file and identify:
   - existing app restart lines
   - default demo lines (e.g. `lv_examples 0 &`, `launcher &`)
3. Remove or comment only the entries you understand.
4. Add the new restart script once, without duplicates.

## Default Demo Apps

Some boards boot a default demo app (e.g. `lv_examples 0 &`) via `rc.modules`. If the new app owns the display, disable that line before enabling the new app. A clear comment is acceptable, for example:

`# disabled by deploy: lv_examples 0 &`

## What to Verify

- `ps` shows the expected process
- the app log file updates after restart
- network listeners match the app's expected ports
- the boot file still contains all unrelated lines

## Typical Device Paths

- boot file: `/etc/init.d/rc.modules`
- app dir: `/opt/<app-name>`
- runtime logs: `/opt/<app-name>/logs`
- helper scripts: `/opt/<app-name>/bin`

## Command Cheat Sheet

```bash
# backup (timestamped, persistent across reboot)
adb shell cp /etc/init.d/rc.modules /etc/init.d/rc.modules.bak.$(date +%Y%m%d%H%M%S)
# also pull to host
adb pull /etc/init.d/rc.modules ./rc.modules.bak

# disable old entry
adb shell sed -i 's|^/opt/old-app/bin/restart.sh|# disabled by deploy: &|' /etc/init.d/rc.modules

# disable default demo app (e.g. lv_examples)
adb shell sed -i 's|^lv_examples 0 \&|# disabled by deploy: &|' /etc/init.d/rc.modules

# add new entry (idempotent)
adb shell "grep -q '/opt/new-app/bin/restart.sh' /etc/init.d/rc.modules || echo '/opt/new-app/bin/restart.sh &' >> /etc/init.d/rc.modules"

# verify boot file
adb shell cat /etc/init.d/rc.modules

# verify process
adb shell ps | grep new-app

# verify listeners
adb shell netstat -tlnp
```
