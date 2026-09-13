## 1. StreamTweak host sleep

- [x] 1.1 Add the authenticated `SLEEP` bridge verb and an acceptance response; reject unsigned or unapproved requests and keep existing shutdown verbs unchanged.
- [x] 1.2 Dispatch accepted sleep requests through the interactive app, finish best-effort active-session cleanup, and call the Windows sleep API with wake events enabled and failure logging.
- [ ] 1.3 Add focused bridge tests for accepted and rejected `SLEEP` commands and verify that the acceptance response is sent before host suspension begins.

## 2. StreamLight client and UI

- [x] 2.1 Add a Windows local sleep operation with privilege handling, result/error reporting, and wake events enabled.
- [x] 2.2 Add a signed host-sleep request with response callback, timeout, and old-host/unsupported handling to the client bridge and computer model.
- [x] 2.3 Extend the Power dialog to select Shutdown or Sleep with Client/Host/Both targets, safe confirmation and controller navigation; show Windows Update controls only for Shutdown.
- [x] 2.4 Wire Host, Client, and Both sleep actions so Both waits for host `OK`, keeps the client awake on failure, and shows an actionable error.
- [ ] 2.5 Verify UI states, authentication gating, update-control exclusion, and Both success/failure behavior with focused automated checks where practical.

## 3. Windows release validation

- [x] 3.1 Make the StreamLight release build script usable from a fork checkout instead of depending on the original author's absolute paths; document required Windows tools for both apps.
- [x] 3.2 Resolve fork branding, installer IDs, and version metadata so the two installers have intentional upgrade/side-by-side behavior.
- [ ] 3.3 Build both applications and installers on Windows, inspect packaged files, and smoke-test launch, bridge approval, and existing shutdown behavior.
- [ ] 3.4 On real Windows client and host devices, test Client/Host/Both sleep, denial and old-host cases, active-stream teardown, manual wake, and host Wake-on-LAN; record outcomes and any hardware limitations.
- [x] 3.5 Prepare reviewable release artifacts and concise build/release instructions for both forks; publish only after artifact review and user authorization.
