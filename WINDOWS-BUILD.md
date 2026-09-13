# Windows build notes for the sleep fork

Both applications target Windows 10/11 x64. Build and test StreamTweak on the host and StreamLight on the client. Install the new host build first; older StreamTweak versions reject `SLEEP`.

## Prerequisites

- Visual Studio 2022 with the MSVC C++ desktop workload and Windows SDK
- Qt 6.8.3 MSVC 2022 x64 with Qt Quick/QML deployment tools
- 7-Zip and Inno Setup 7
- .NET 8 SDK and Windows App SDK dependencies restored by the StreamTweak solution

## Build

In Windows PowerShell, from `StreamLight`, run `./build-release.ps1`. The script uses its own checkout path. Override the tool locations with `STREAMLIGHT_QT_BIN` and `STREAMLIGHT_7ZIP_DIR` environment variables if necessary. The script produces a portable directory at `StreamLight/build/release`; it does not compile the installer. Verify that directory contains the freshly built executable, DLLs, QML files, and `gamecontrollerdb.txt`, then compile `StreamLight.iss` with Inno Setup (`ISCC.exe StreamLight.iss`). Its `SourceDir` points to `build/release`.

From `StreamTweak`, build `StreamTweak.sln` for x64 Release with the .NET 8 SDK and Visual Studio (`dotnet build StreamTweak.sln -c Release -p:Platform=x64`). Check that both `StreamTweakUI/bin/x64/Release` and `StreamTweakService/bin/x64/Release` contain the expected `win-x64` outputs, then compile `Installer.iss` with Inno Setup (`ISCC.exe Installer.iss`). The installer packages both projects' x64 Release outputs.

These forks are intended to install beside the original applications, with only one host app active at a time. They use separate installer IDs, install folders, settings, single-instance keys, service/pipe/startup names, and TCP bridge port 48098. Pair the client with the fork host separately; the original host listens on port 47998 and does not support `SLEEP`. The StreamTweak Sleep installer adds a private-network inbound firewall rule for TCP 48098 and removes it on uninstall; verify that rule during Windows testing. Release/update links point to the planned `carlossgv` forks; verify that both repositories exist before publishing. The two host applications can still compete over the streaming-server configuration or NIC if run together, so do not run them simultaneously.

## Acceptance checks

On separate Windows client and host devices, verify bridge approval, existing shutdown behavior, Sleep Client, Sleep Host, and Sleep Both. Test a denied client and an older host. Check active-stream teardown, manual wake, and Wake-on-LAN. `powercfg /a` shows available sleep states; the app cannot select a particular hardware state. An `OK` from the host confirms acceptance of `SLEEP`, not that Windows completed suspension.
