## ADDED Requirements

### Requirement: User can choose sleep target
StreamLight SHALL offer a distinct Sleep action for Client, Host, and Both in its power UI. It SHALL preserve the existing Shutdown action and require an explicit confirmation before sleeping a device.

#### Scenario: Sleep client without a host
- **WHEN** the user opens Power without a reachable or approved host and confirms Sleep Client
- **THEN** StreamLight requests sleep for the local Windows client

#### Scenario: Sleep host or both with approval
- **WHEN** the user has a reachable, approved StreamTweak host and confirms Sleep Host or Sleep Both
- **THEN** StreamLight sends a signed remote sleep request for the selected host

#### Scenario: Host action unavailable
- **WHEN** the host is unavailable or has not approved the client
- **THEN** Sleep Host and Sleep Both are disabled while Sleep Client remains available

### Requirement: Sleep does not install updates
StreamLight SHALL keep update installation controls and update-state messaging exclusive to Shutdown.

#### Scenario: Sleep selected while updates are pending
- **WHEN** the user selects Sleep while Windows updates are pending
- **THEN** the UI does not offer to install updates as part of Sleep and sends no update-and-shutdown command

### Requirement: Remote sleep requires approval
StreamTweak SHALL accept `SLEEP` only after verifying the command signature and the client's approval using the same security boundary as remote shutdown.

#### Scenario: Approved command
- **WHEN** an approved client sends a valid signed `SLEEP` command
- **THEN** StreamTweak acknowledges acceptance and requests host sleep

#### Scenario: Unapproved or invalid command
- **WHEN** an unapproved client or a client with an invalid signature sends `SLEEP`
- **THEN** StreamTweak rejects the command and does not request host sleep

### Requirement: Both sleep respects host acceptance
For Sleep Both, StreamLight SHALL request host sleep before local sleep and SHALL sleep the client only after the host acknowledges acceptance. It SHALL keep the client awake and display a failure when the host rejects the request or does not answer before the timeout. Host acceptance SHALL NOT be presented as proof that the host completed suspension.

#### Scenario: Host accepts
- **WHEN** the approved host acknowledges a Sleep Both request with `OK`
- **THEN** StreamLight requests local client sleep

#### Scenario: Host rejects or times out
- **WHEN** the Sleep Both request is rejected, unsupported, or times out
- **THEN** StreamLight leaves the client awake and shows the failure

### Requirement: Windows sleep preserves wake capability
Both applications SHALL request Windows sleep rather than shutdown or hibernation, SHALL leave system wake events enabled, and SHALL report an operating-system call failure through their available UI or logs.

#### Scenario: Sleep call succeeds
- **WHEN** Windows accepts the sleep request
- **THEN** the selected device enters the sleep behavior supported by its Windows power configuration

#### Scenario: Sleep call fails
- **WHEN** Windows rejects a local sleep request
- **THEN** the application records the failure and, where its UI remains available, informs the user
