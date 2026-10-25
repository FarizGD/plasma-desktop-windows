# Windows port

Experimental effort to run KDE Plasma Desktop on Windows. The upstream Linux build must remain unchanged. This is not usable yet.

## Status
- [x] First-pass CMake gating for Linux-only dependencies and subdirectories
- [ ] Verify Qt 6, KDE Frameworks 6 and libplasma build with KDE Craft
- [ ] Build plasma-workspace on Windows
- [ ] Build a minimal slice of this repository
- [ ] Run plasmashell as a normal app over Explorer
- [ ] Implement native Windows backends
- [ ] Test shell replacement in a disposable VM only

## Architecture
Qt 6 -> KDE Frameworks 6 -> libplasma -> plasma-workspace -> plasma-desktop. The plasmashell process and Corona live in plasma-workspace, not this repository, so both repositories need porting.

| Linux integration | Candidate Windows backend |
| --- | --- |
| KWin/X11/Wayland task list | EnumWindows, SetWinEventHook, DWM thumbnails |
| Panel edge reservation | SHAppBarMessage AppBar APIs |
| Desktop and wallpaper | Dedicated bottom-of-z-order window |
| StatusNotifierItem tray | Native notification-area integration or compatibility layer |
| .desktop launcher entries | Start Menu shortcuts and packaged apps |
| KGlobalAccel | RegisterHotKey |
| Freedesktop notifications | Windows notification APIs |
| Session D-Bus | D-Bus for Windows or targeted IPC shims |

## Windows build gating
The initial CMake pass skips X11, XCB, Polkit, xkbregistry, UDev, KWin's D-Bus interface, PipeWire QML, ibus/scim integrations, tablet KCM dependencies, SDDM theme installation, kded, solid-device-automounter, knetattach, appiumtests and documentation by default. This is only build-system triage; transitive dependencies and source code may still assume Linux APIs. Use -DWINDOWS_PORT_BUILD_KCMS=ON to attempt KCMs, most of which are expected to be platform-specific.

## CI
Run .github/workflows/windows-craft.yml from Actions. It bootstraps Craft and attempts selected packages. Inspect the summary and craft-logs artifact; blueprint names and package availability must be confirmed from real output.

## Safety
Do not replace Explorer on your daily Windows installation. Test only in a VM with a disposable local account and recovery path. If the test shell dies, use Ctrl+Shift+Esc, Run new task, and start explorer.exe. Do not change shell registry settings until the app is stable.
