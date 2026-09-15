# Support

CAD Agent is pre-release software and no public build is available yet. This page explains where to go
for help and how to get the most useful response.

## Before asking

- Read [Getting started](docs/getting-started.md) and [Troubleshooting](docs/troubleshooting.md).
- Check [Supported features](docs/supported-features.md) — many "it didn't work" reports are requests for
  capabilities that are planned rather than implemented.
- Search [existing issues](https://github.com/shanla-gh/cad-agent-releases/issues) for the same problem.

## Where to go

| Need | Where |
| --- | --- |
| Something does not work as documented | [Bug report](https://github.com/shanla-gh/cad-agent-releases/issues/new?template=bug_report.yml) |
| A capability you would like | [Feature request](https://github.com/shanla-gh/cad-agent-releases/issues/new?template=feature_request.yml) |
| A security vulnerability | [SECURITY.md](SECURITY.md) — never a public issue |
| Release announcements | Watch this repository for **Releases** |

## Writing a useful bug report

The most useful reports include:

- the CAD Agent version, and your Windows and SOLIDWORKS versions (including service pack),
- the exact request you gave, or the plan you ran,
- what you expected and what happened instead,
- the job status and any error codes shown, such as `SW_SELECTION_COUNT_MISMATCH`,
- the verification results, if the job ran,
- the relevant job log, **with anything confidential removed**.

Please do not attach proprietary CAD models or confidential drawings. If a model is needed to reproduce a
problem, a simplified part that shows the same behaviour is best.

## Response expectations

CAD Agent is in active pre-release development. Issues are read and triaged, but there is no guaranteed
response time before the first public release.
