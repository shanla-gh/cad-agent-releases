# Requirements

These are the requirements of the current development line. Each public release will state the exact
environment it was tested against in its release notes.

## System

| Component | Requirement |
| --- | --- |
| Operating system | Windows, 64-bit |
| SOLIDWORKS | A licensed, activated desktop installation, started at least once |
| .NET | .NET Framework 4.8 runtime |
| Disk | Space for the parts, exports and logs you create |

## SOLIDWORKS versions

CAD Agent is developed and verified against **SOLIDWORKS 2026 SP03.2**, 64-bit. Every capability listed in
[Supported features](supported-features.md) has been run against that version and the resulting parts
measured.

Other SOLIDWORKS versions and service packs are **not yet verified**. CAD Agent uses the SOLIDWORKS API
directly, and API behaviour can differ between versions, so compatibility with other releases is not
guaranteed until it has been tested and listed here.

## Runtime conditions

- **SOLIDWORKS must be running** before CAD Agent connects. Starting SOLIDWORKS automatically is planned
  but not implemented.
- CAD Agent works with **part documents**. Assemblies and drawings are not supported yet.
- Requests and plans use **millimetres**.
- CAD Agent needs permission to write to its output and log folders in your user profile.

## Optional

| Feature | Requirement |
| --- | --- |
| Language-model planning | An API credential for the configured language-model provider, and network access to it |

The deterministic rule-based planner, plan execution, modification and verification work without a
network connection or any credential.

## Not required

- No SOLIDWORKS add-in has to be enabled for the current workflow; CAD Agent connects to the running
  session through the SOLIDWORKS API.
- Installation requirements, including any permissions the installer needs, will be documented with the
  first release.
