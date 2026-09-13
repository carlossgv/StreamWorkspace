# Sleep fork Windows build candidate — 2026-09-13

Built on Windows x64 from workspace submodule commits `c70624c` (StreamLight) and `8afa3be` (StreamTweak). Both installers were published as GitHub prereleases on 2026-09-13. The StreamLight installer includes the Modern Standby client-sleep fix, validated on the target laptop.

| Installer | Local path | Size | SHA-256 |
| --- | --- | ---: | --- |
| StreamLight Sleep 5.7.1 | `StreamLight/build/installer/StreamLightSleep_5.7.1_Installer.exe` | 29,372,223 bytes | `9F15462794995F456B8DD7A75748CF469AFCF58C65ACA187EABB7CC091A953A3` |
| StreamTweak Sleep 8.4.1 | `StreamTweak/Output/StreamTweakSleep_8.4.1_Installer.exe` | 13,651,512 bytes | `317B28A863C70D17E13D762E87412B7F314FE31275CE578DF46C5559BF353D69` |

StreamTweak built with `dotnet build StreamTweak.sln -c Release -p:Platform=x64` and Inno Setup 7.1.0. The build succeeded with five CS1998 warnings and no errors. The installer compiled with one Inno warning about an `[UninstallRun]` entry lacking `RunOnceId`.

StreamLight built with Qt 6.8.3 MSVC 2022 x64, Visual Studio 2022 Build Tools, and 7-Zip. `build-release.ps1` produced `build/release` with 319 files, including `StreamLight.exe`, `Qt6Core.dll`, and `gamecontrollerdb.txt`; Inno Setup 7.1.0 compiled the installer. Its underlying batch script exits unsuccessfully at the obsolete WiX step, which looks for `Moonlight.exe`; the fork's Inno installer compiled successfully from the PowerShell script's release directory.

Client sleep and mouse wake were validated on the target laptop. Before marking these releases stable, complete the remaining OpenSpec acceptance checks on separate client and host devices: bridge approval, existing shutdown, Host/Both sleep, denial and old-host behavior, active-stream teardown, and host Wake-on-LAN. Do not treat the bridge's `OK` as proof that Windows suspended.

Prereleases: [StreamLight Sleep 5.7.1](https://github.com/carlossgv/StreamLight/releases/tag/v5.7.1) and [StreamTweak Sleep 8.4.1](https://github.com/carlossgv/StreamTweak/releases/tag/v8.4.1). The uploaded asset digests match the local SHA-256 values above.
