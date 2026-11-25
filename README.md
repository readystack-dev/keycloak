# Keycloak: ReadyStack build kit

The offline build kit for the Docker image `readystack/keycloak:26.8.2-CE-ubuntu24.04-r5`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [26.8.2-CE-ubuntu24.04-r5](https://github.com/readystack-dev/keycloak/releases/tag/26.8.2-CE-ubuntu24.04-r5)
- Image: [`docker.io/readystack/keycloak:26.8.2-CE-ubuntu24.04-r5`](https://hub.docker.com/r/readystack/keycloak/tags), manifest digest `sha256:d26a943af80397d2aa6986f505cef74c2a5475a3d01511ac2219ff0be9719190`
- Product page: https://readystack.dev/products/keycloak
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=keycloak

Keycloak is distributed under its upstream licence (Apache-2.0); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [26.8.2-CE-ubuntu24.04-r5 release](https://github.com/readystack-dev/keycloak/releases/tag/26.8.2-CE-ubuntu24.04-r5): `ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.tar.gz` (2.4 GB), SHA-256 `77bf506689076f6ae201aebc13d2d62bd5edc276aeb16223d49862345ef1be1d`.

GitHub limits a release file to 2 GiB, so the kit is published in 2 parts. Download them all and join them:

```sh
cat ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.tar.gz.part-* > ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.tar.gz
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.rebuild.json.sigstore ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.tar.gz
cd 26.8.2-CE-ubuntu24.04-r5
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-keycloak-26.8.2-CE-ubuntu24.04-r5.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/keycloak/recipe. The complete kit is the release download. Each release is a tag here.
