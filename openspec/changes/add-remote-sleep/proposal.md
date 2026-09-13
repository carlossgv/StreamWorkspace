## Why

StreamLight can shut down the client, an approved StreamTweak host, or both, but cannot put them to sleep. Suspending the machines would let a user end a couch session without a full reboot and later wake the host through the existing Wake flow.

## What Changes

- Add a Sleep action with Client, Host, and Both targets to StreamLight's controller-accessible power UI, while retaining the existing shutdown behavior.
- Add a signed `SLEEP` bridge command that StreamTweak accepts only from an approved client and uses to suspend the Windows host.
- Suspend the local Windows client through the system power API. For Both, arrange the host request and session cleanup before suspending the client, and report failures rather than silently sleeping only one side.
- Keep the Windows Update-and-shutdown option exclusive to Shutdown; Sleep does not install updates.
- Prepare and validate Windows builds and installers for both forks, including real-device sleep and wake checks before publishing.

## Capabilities

### New Capabilities

- `device-sleep`: User-facing client, host, and both sleep actions, remote authorization, and outcome handling.

### Modified Capabilities

None; this workspace has no existing main capability specs.

## Impact

StreamLight QML power controls, C++ computer model, bridge, and Windows system backend; StreamTweak bridge, app power handling, and installer/version metadata. The bridge gains one backward-compatible command; older StreamTweak versions cannot perform Host or Both sleep. Windows build tooling and real hardware are needed for release validation.
