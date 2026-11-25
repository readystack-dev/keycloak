# Keycloak 26.8.2 — ReadyStack release `26.8.2-CE-ubuntu24.04-r5`

Generated 2026-11-22 from this release's build records and strategy. Statements that come from a single record name it in brackets; the others come from this release's strategy and build log.

## Release

- **Image:** `readystack/keycloak:26.8.2-CE-ubuntu24.04-r5` (linux/amd64). Index digest `sha256:bee5c9ed876873a4e58eb067eab11a5091f7cd38202e8ec87f25c7c45acfdffd` [`docs/provenance/LOCKED_DIGEST.txt`].
- **Image manifest digest:** `sha256:d26a943af80397d2aa6986f505cef74c2a5475a3d01511ac2219ff0be9719190` [`TARGET_DIGEST.txt`].
- **Upstream:** Keycloak 26.8.2 built from source at `26.8.2` of https://github.com/keycloak/keycloak — upstream notes: https://github.com/keycloak/keycloak/releases/tag/26.8.2 [`docs/provenance/BUILD_RECIPE.json`].
- **Base image:** none (`FROM scratch`): a chiselled Ubuntu 24.04 runtime, the files Canonical's chisel cut from Ubuntu's own packages (`vendored/chisel/slices.txt`, `vendored/chisel/packages.tsv`); runtime user `keycloak`.
- **Build:** `source-rebuild-java`, compiled offline with `--network=none`.
- **Supersedes:** ReadyStack release `26.8.2-CE-ubuntu24.04-r4`.

## What changed in this release

- Upstream version unchanged (26.8.2); ReadyStack rebuild.
- Keycloak 26.8.2 is unchanged from the release this supersedes (26.8.2-CE-ubuntu24.04-r4); this release rebuilds it. Changes: the image is rebuilt with the Eclipse Temurin 21 JRE from the current eclipse-temurin:21-jre-noble image (sha256:ac595159aa22…), which its maintainers updated after 26.8.2-CE-ubuntu24.04-r4 was built on sha256:b4f9d2a0bc27…. Settings, ports and data volumes are those of 26.8.2-CE-ubuntu24.04-r4: replace the container with this image on the same volumes.

## Security status at release (measured 2026-11-22)

- **Docker Scout:** 0 fixable critical/high vulnerabilities; runs as `keycloak` (non-root); SBOM + provenance attestations present (SPDX SBOM + SLSA v1) [`docs/provenance/SCOUT_VERDICT.json`].
- **OSV cross-check:** no fixable critical/high vulnerability beyond Docker Scout's findings.
- **Signature:** cosign signature verified by ReadyStack's release gate before publishing; check it yourself with `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/keycloak:26.8.2-CE-ubuntu24.04-r5` (signatures are recorded in the public Sigstore transparency log).
- **ReadyStack release check at release:** passed (ReadyStack's own test, not a Docker Scout grade). Vulnerabilities are disclosed every day and the image never changes, so it is re-checked daily, beside Docker Scout's own dated grade, at https://readystack.dev/queue.

## Reproducibility

Rebuilds offline from this archive to the identical image digest `sha256:d26a943af80397d2aa6986f505cef74c2a5475a3d01511ac2219ff0be9719190`: run `./BUILD_OFFLINE.sh`, then `./VERIFY_OFFLINE.sh` (add `--online` to confirm the Docker Hub tag holds the same digest). Before publishing, ReadyStack rebuilt the kit with `./BUILD_OFFLINE.sh` and compared the result with the published digest [`docs/provenance/REPRO_VERIFICATION.json`], then ran `./BUILD_OFFLINE.sh` and `./VERIFY_OFFLINE.sh` from this archive (without `--online`).

## Validation

- 4 of 4 use cases passed (4 authored) against a container of this image started with `start --optimized --hostname=localhost` after the image name (the image has no default command) [`docs/validation/test-results.json`]. The image has no default command (with no arguments `kc.sh` prints its usage and exits).

## Known issues

- None recorded.

## Defects fixed

- 28 problems found before release and fixed before it shipped (28 by review); each is listed in `docs/defects/defects.json`.

## Verify this archive

```
sha256sum -c CHECKSUMS.sha256
./BUILD_OFFLINE.sh && ./VERIFY_OFFLINE.sh --online
cosign verify --key https://readystack.dev/keys/cosign.pub readystack/keycloak:26.8.2-CE-ubuntu24.04-r5
```

ReadyStack records every signature in the public Sigstore transparency log (Rekor), so the command needs no extra flags.

