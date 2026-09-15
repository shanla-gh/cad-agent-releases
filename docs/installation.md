# Installation

> **Public builds are not available yet. First release: TBA.**
>
> This page describes how installation will work so that you can prepare your environment. It will be
> updated with exact steps when the first build is published.

## Where builds will come from

CAD Agent builds will be published only on this repository's
[GitHub Releases](https://github.com/shanla-gh/cad-agent-releases/releases) page. Each release will include:

- the downloadable build,
- release notes describing what changed,
- the SOLIDWORKS version and service pack it was tested against,
- known issues for that release.

A build obtained anywhere else is not an official CAD Agent build.

## Before you install

1. Check the [requirements](requirements.md): 64-bit Windows, a licensed SOLIDWORKS installation, and the
   .NET Framework 4.8 runtime.
2. Start SOLIDWORKS at least once after installing or updating it, so that its first-run setup and
   licence activation are complete.
3. Make sure you can create and save a new part in SOLIDWORKS normally.

## Expected installation flow

When a build is available, installation is expected to look like this:

1. Download the release asset for the latest version from GitHub Releases.
2. Verify that the download came from this repository's release page.
3. Install or extract CAD Agent as described in that release's notes.
4. Start SOLIDWORKS.
5. Start CAD Agent and connect to the running SOLIDWORKS session.

The exact package format and any installer options will be documented with the first release.

## Where CAD Agent writes files

CAD Agent writes the parts and exports it creates into a dedicated output folder in your Windows user
profile, and per-job logs into a separate logs folder beside it. It does not write models anywhere else,
and it does not overwrite existing files without explicit confirmation.

## Optional: language-model planning

The deterministic rule-based planner needs no configuration. The experimental language-model planner
needs an API credential for its configured provider, supplied through an environment variable. The
credential is never written to plans or logs. Details will be documented with the first release.

## Updating and uninstalling

Update and uninstall steps will be documented with the first release. Models you created remain ordinary
SOLIDWORKS files and do not depend on CAD Agent being installed.
