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

Merge these policies before switching the paused proof jobs to the split
identities. Revoke the old repository-local shared policy, then verify that the
export identity cannot exchange the publish-write policy and the publish identity
cannot exchange the staging-write policy before enabling scratch writes.
