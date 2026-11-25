# Provenance — Keycloak (`26.8.2-CE-ubuntu24.04-r5`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://github.com/keycloak/keycloak
- Revision built: `26.8.2` (commit `e514445f9dcdabc5bdcf67cab56c1e49655de067`)
- Source tree: `5dd18d89b866300380e8063e8d776b341a341f81c6b9ca3e3f30ce4313139a78` over 13524 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: enterprise code is removed or refused before the build (CE audit at acquire).

## Build

- Method: `source-rebuild-java`; base image `scratch + a chiselled Ubuntu 24.04 runtime (vendored/chisel/slices.txt, packages.tsv)`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1795333788` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/keycloak:26.8.2-CE-ubuntu24.04-r5` — index digest `sha256:bee5c9ed876873a4e58eb067eab11a5091f7cd38202e8ec87f25c7c45acfdffd` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:d26a943af80397d2aa6986f505cef74c2a5475a3d01511ac2219ff0be9719190` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/keycloak:26.8.2-CE-ubuntu24.04-r5`

## Pinned inputs, and why

- 33 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 3, image digests 4, language runtimes and toolchains 2, dependency versions readystack sets 4, pins in the kit's dockerfiles 7, readystack's build engine 7, dependencies upstream chose 1, services the release checks run beside 3).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.trail.json`, published beside this archive and signed with the release key (`ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.trail.json.sigstore`), and readable at https://readystack.dev/products/keycloak/trail/26.8.2-CE-ubuntu24.04-r5.

