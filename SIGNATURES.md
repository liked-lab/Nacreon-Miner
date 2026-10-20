# Release signatures

Official releases: https://github.com/liked-lab/Nacreon-Miner/releases.
Signatures confirm the publisher and file integrity. They do not establish build
reproducibility or prevent copying or reverse engineering.

## Verify a download

Install [cosign](https://docs.sigstore.dev/cosign/system_config/installation/)
3.1.3 or a compatible newer version. Download the Linux or HiveOS archive,
its matching `.sigstore.json` file, `SHA256SUMS` and `SHA256SUMS.sigstore.json`
from the same official release. In Bash, set `file` to the downloaded archive:

```bash
set -euo pipefail
file='Nacreon_hiveos_custom-0.23.0.tar.gz'
identity='https://github.com/liked-lab/Nacreon-Miner/.github/workflows/release-sign.yml@refs/heads/main'
issuer='https://token.actions.githubusercontent.com'
cosign verify-blob --bundle SHA256SUMS.sigstore.json \
  --certificate-identity "$identity" --certificate-oidc-issuer "$issuer" SHA256SUMS
cosign verify-blob --bundle "$file.sigstore.json" \
  --certificate-identity "$identity" --certificate-oidc-issuer "$issuer" "$file"
sha256sum --check --strict --ignore-missing SHA256SUMS
```

All commands must succeed. Do not relax identity or issuer checks and do not
disable transparency-log verification. The checksum command checks the archives
present in this directory; cosign above checks the specific archive selected.
Old releases without bundles are unsigned; their hashes alone do not authenticate
the publisher.

## Maintainer: sign finished archives

The workflow neither builds nor executes the miner. It uses a standard GitHub-hosted
Linux runner, Python's standard library and pinned cosign. No paid signing service,
PAT or persistent signing key is needed. Sigstore uses an ephemeral runner key and
publishes signing identity and digest information in its public transparency log.

1. Complete the existing release checks and upload the two final archives to an
   existing release. A draft release is supported and stays a draft. Calculate each
   SHA-256 from the approved **local** archive; do not take expected hashes from
   potentially replaced GitHub assets. If SHA256SUMS already exists, it must list
   exactly these two archives, with their approved hashes (comments are permitted).
2. After approval to change the remote release, open **Actions → Sign release files
   → Run workflow**. Select **main**, enter the existing tag, exact archive names
   and the two local hashes. One manual launch automates checking, signing,
   verification and attaching the three bundles. It also adds SHA256SUMS if absent.
3. Require a successful run and verify the downloaded files with the commands
   above. Publishing the release remains a separate decision and existing process.

The workflow fails on unexpected hashes, moved tags, changed input assets,
duplicate names or existing bundles. It never overwrites or deletes release files.
Uploads are not atomic: a failed upload can leave some valid bundles. Inspect the
failed run and existing files before any explicitly approved cleanup and retry.
Avoid editing the tag or assets while signing. Sign before enabling release
immutability; immutable releases reject additional assets.

Permissions are limited to `contents: write` for release attachments and
`id-token: write` for GitHub OIDC. No checkout or source upload occurs. The only
Action is cosign-installer v4.1.2, pinned to commit
`6f9f17788090df1f26f669e9d70d6ae9567deba6`; cosign is pinned to v3.1.3 and its
installer verifies the downloaded binary. Changes to this workflow require review:
trust in these signatures depends on control of the repository and its workflow.
