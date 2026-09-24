# .github
Default files to be used for any public repository under the chainguard-sandbox organization.

This repository contains tooling and templates to assist in the management and daily work of the Chainguard-sandbox GitHub organisation.

## Wolfi publication proof

The OS-2854 jbcg.dev proof uses separate Google identities and private scratch
repositories. These organization OctoSTS policies stay outside the Git history
copied by the publication job:

| Policy | Google subject | Repository | Contents |
| --- | --- | --- | --- |
| `export-wolfi-proof-export` | `104839036529472558325` | `export-wolfi-proof` | write |
| `export-wolfi-proof-publish-read` | `105415554954833304623` | `export-wolfi-proof` | read |
| `export-wolfi-proof-publish` | `105415554954833304623` | `export-wolfi-publish-proof` | write |

The subjects belong to `export-wolfi-proof@borg-chainguard-dev.iam.gserviceaccount.com`
and `export-wolfi-proof-publish@borg-chainguard-dev.iam.gserviceaccount.com`,
respectively. Each policy explicitly names one repository. Never omit that list
or add staging/production identities to these dev policies.

The jobs use images built directly with ko. Fetching the pinned git-export source
is a build-time concern; these policies authorize only runtime access to the
scratch destination repositories. Neither runtime identity receives access to
the git-export source repository or an enterprise package-builder policy.

Merge these policies before switching the paused proof jobs to the split
identities. The old repository-local shared policy was retired in
[export-wolfi-proof#2](https://github.com/chainguard-sandbox/export-wolfi-proof/pull/2).
Verify that the export identity cannot exchange the publish-write policy and the
publish identity cannot exchange the staging-write policy before enabling scratch
writes.

## Wolfi staging validation

The chainops.dev deployment for [OS-2866](https://linear.app/chainguard/issue/OS-2866)
uses its own runtime identities and dedicated branches in the same two scratch
repositories. These subjects are the actual service-account IDs created by
[mono#61323](https://github.com/chainguard-dev/mono/pull/61323):

| Policy | Google subject | Repository | Contents |
| --- | --- | --- | --- |
| `export-wolfi-staging-export` | `104108212822327502512` | `export-wolfi-proof` | write |
| `export-wolfi-staging-publish-read` | `115577276536345601463` | `export-wolfi-proof` | read |
| `export-wolfi-staging-publish` | `115577276536345601463` | `export-wolfi-publish-proof` | write |

The export account is
`export-wolfi-staging@staging-enforce-cd1e.iam.gserviceaccount.com`; publication
uses `export-wolfi-staging-publish@staging-enforce-cd1e.iam.gserviceaccount.com`.
The separate source policy in `chainguard-dev/stereo-staging` grants only the
export account source-read access. Neither scheduler invoker receives a GitHub
grant. Keep all three destination policies here, outside the mirrored history,
and retain each explicit single-repository restriction.

Validation targets `os-2866-chainops-export` and `os-2866-chainops-publish` in the
respective repositories. GitHub grants apply to repositories, not individual
branches. Dev grants remain active, so pausing dev schedules does not prevent
manual dev writes or establish exclusive staging ownership. Keep that limitation
in the proof, and revoke dev grants or use separate repositories before claiming
exclusive staging write access.

Follow the [staging runbook](https://github.com/chainguard-dev/mono/blob/main/env/chainops.dev/iac/400-export-wolfi/README.md)
to seed the scratch histories, verify actual identity exchanges and repository
scope, and complete the first signed export/publication before enabling schedules.
These grants provide no build-time git-export access and include no production
identities or production destinations.
