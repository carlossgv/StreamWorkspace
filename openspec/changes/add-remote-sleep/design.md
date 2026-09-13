## Context

StreamLight is a Windows Qt/QML client. Its Power dialog selects Host, Client, or Both for shutdown; Host uses an `AUTH1`-signed TCP command, and Both relies on a fixed 1.8-second delay before shutting down locally. StreamTweak is a Windows .NET/WinUI host app. Its bridge verifies signatures and approval before dispatching shutdown to the interactive app. The current bridge command is fire-and-forget, so a sleep flow cannot safely infer host acceptance from sending bytes alone. This change spans both repos and Windows-only build paths.

## Goals / Non-Goals

**Goals:**

- Expose a clearly labeled Sleep action for Client, Host, and Both without changing Shutdown.
- Require the same verified, approved-client authorization for remote sleep as for shutdown.
- Keep wake events enabled and distinguish host acceptance from actual OS suspension.
- Produce reproducible Windows builds and installers for both forks, then validate on real devices.

**Non-Goals:**

- Hibernation, timed wake, or a guarantee that Windows enters a particular hardware sleep state.
- Installing Windows updates as part of Sleep.
- Automatic wake of a client that has also gone to sleep.
- Publishing GitHub releases as part of implementation without a separate review of built artifacts.

## Decisions

1. **UI:** Extend the existing Power flow with a distinct Shutdown/Sleep action choice and retain its Host/Client/Both target choice, confirmation, controller navigation, and safe default focus. Hide the update checkbox and update-state evidence when Sleep is selected, since updates belong to Shutdown. A separate dialog was considered, but it would duplicate authorization and navigation behavior.
2. **Protocol:** Add a `SLEEP` verb to the existing signed bridge. StreamTweak checks verified authorization exactly as for `SHUTDOWN` before scheduling sleep and replies `OK` on acceptance. An old or unreachable host yields an error or timeout. The client uses a response callback for Sleep rather than the current fire-and-forget shutdown path. A new service endpoint was considered unnecessary.
3. **Both ordering:** Request Host sleep first. Only after `OK` does StreamLight initiate local sleep; on rejection, timeout, or connection error it keeps the client awake and displays a failure. `OK` means accepted, not that the OS completed sleep. There is no reliable end-to-end completion acknowledgment once the host suspends. A fixed delay alone was considered insufficient.
4. **Windows power call:** Both applications call `SetSuspendState(FALSE, FALSE, FALSE)` with `SeShutdownPrivilege` enabled, check the result, and log failures. The final `FALSE` retains wake events. Client and host calls use their respective existing Windows power plumbing. Session telemetry/cleanup follows the existing shutdown best-effort pattern before requesting host sleep; avoid claiming telemetry delivery is guaranteed when the machine suspends.
5. **Compatibility:** Client Sleep always works without StreamTweak. Host/Both require a reachable, approved host running the new StreamTweak; preserve the existing disabled Host/Both state for unavailable integration. If capability detection cannot distinguish older approved hosts, treat `ERR`/timeout as unsupported and show a useful message rather than sleeping the client.
6. **Release path:** Implement and review on this Linux workspace, then compile and exercise both applications on Windows with Qt/MSVC, .NET 8/WinUI, and Inno Setup. Remove machine-specific paths or document overrides in StreamLight's release script as part of making a fork buildable. The user chose side-by-side installation, so the forks need distinct installer IDs, installation directories, app settings, single-instance keys, service/pipe/startup names, and bridge port. Publication links depend on the eventual fork URLs; do not assume the current script already creates the final installer.

## Risks / Trade-offs

- **Host accepts but fails to sleep** → `OK` is only an acceptance signal. Log OS failures on the host; document this limit and verify real hardware. A future status protocol would be needed for stronger guarantees.
- **Modern Standby, firmware, or wake configuration differs by device** → test `powercfg /a`, sleep, manual wake, and Wake-on-LAN on intended host hardware. The app cannot force a particular sleep state.
- **Active stream/socket teardown races with suspension** → flush the bridge response before dispatching host sleep, perform best-effort session cleanup, and test during an active stream.
- **Older StreamTweak rejects `SLEEP`** → keep the client awake for Both and surface an actionable compatibility error.
- **Windows toolchain is unavailable in this workspace** → separate source-level checks from Windows build, installer, and hardware acceptance; treat the latter as release gates.

## Migration Plan

Build and install the new StreamTweak host first, then the new StreamLight client. Existing shutdown and older clients remain compatible. If sleep misbehaves, revert to previous installers; no stored data or bridge migration is required. Before publication, inspect installer identity and branding so a fork does not unintentionally overwrite upstream installations.

## Open Questions

- Which Windows client and host devices will be used for real sleep/wake testing, and whether Wake-on-LAN is enabled on the host.
- Verify on Windows that the original and fork can remain installed while only one host application is active at a time, as requested by the user. Concurrent operation is outside the acceptance criteria because both can modify the same streaming-server configuration and NIC.
- Verify that `carlossgv/StreamLight` and `carlossgv/StreamTweak` exist before publishing release links.
