# Security Policy

## Supported versions

No public build of CAD Agent has been released yet. Once builds are published, security fixes will be
provided for the most recent release. This section will list supported versions from the first release
onward.

## Reporting a vulnerability

Please **do not report security vulnerabilities in public issues.**

Use GitHub's private vulnerability reporting for this repository:

1. Open the repository's **Security** tab.
2. Choose **Report a vulnerability**.
3. Describe the issue, the affected version or build, and the steps needed to reproduce it.

If private reporting is unavailable, open a public issue that only asks for a private contact channel,
without any technical details of the vulnerability.

Please include, where possible:

- the CAD Agent version and SOLIDWORKS version,
- what an attacker could achieve,
- a minimal plan, request or file that demonstrates the problem,
- whether the issue is already publicly known.

You can expect an acknowledgement of a valid report, a status update as it is investigated, and credit in
the release notes if you would like it.

## Security model in brief

CAD Agent is designed so that planning — including planning by a language model — cannot do anything a
hand-written plan could not:

- **Plans are data.** A plan is a list of operations from a fixed catalog with typed arguments. There is no
  operation that runs a command, script or macro, and no argument that carries code.
- **Every plan is validated** before it reaches SOLIDWORKS, and there is no route from a planner to the CAD
  system that bypasses validation.
- **File access is sandboxed.** Plans can only express relative paths inside a dedicated output folder.
  Absolute paths, parent-directory traversal, network paths and unexpected file types are refused.
- **Nothing is overwritten silently.** Replacing an existing file requires explicit confirmation that a plan
  cannot grant itself, and a modification is never saved over the model it was opened from.
- **Open documents belong to the user.** CAD Agent only closes documents it created or opened itself, and
  only when they have no unsaved changes.
- **Credentials stay out of plans and logs.** An API credential for language-model planning is read from the
  environment and is never written to a plan or a log.

## Scope

In scope: CAD Agent builds published in this repository's releases, plan validation, file handling and the
handling of credentials.

Out of scope: vulnerabilities in SOLIDWORKS, Windows, or third-party language-model services themselves.
