# Keycloak: ReadyStack build kit

The offline build kit for the Docker image `readystack/keycloak:26.8.2-CE-ubuntu24.04-r5`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [26.8.2-CE-ubuntu24.04-r5](https://github.com/readystack-dev/keycloak/releases/tag/26.8.2-CE-ubuntu24.04-r5)
- Image: [`docker.io/readystack/keycloak:26.8.2-CE-ubuntu24.04-r5`](https://hub.docker.com/r/readystack/keycloak/tags), manifest digest `sha256:d26a943af80397d2aa6986f505cef74c2a5475a3d01511ac2219ff0be9719190`
- Product page: https://readystack.dev/products/keycloak
- Source and build recipe: https://readystack.dev/products/keycloak/recipe
- Build trail: https://readystack.dev/products/keycloak/trail/26.8.2-CE-ubuntu24.04-r5
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=keycloak

## The image

Single container: a Quarkus-based Identity and Access Management server (OpenID Connect / OAuth2 / SAML provider, admin console, account console). It REQUIRES an external PostgreSQL database that you provide: point the container at it with KC_DB_URL_HOST and KC_DB_URL_DATABASE (or one KC_DB_URL JDBC string) plus KC_DB_USERNAME and KC_DB_PASSWORD. The image was pre-built for PostgreSQL (`kc.sh build --db=postgres --health-enabled=true --metrics-enabled=true`) and is started with `start --optimized`; Keycloak creates and migrates its own schema in that database on first start. NOT a docker-compose-only or Kubernetes-only product, but it is NOT self-contained: PostgreSQL is a hard external dependency (this image is built for PostgreSQL only; `start-dev` with embedded H2 does not run in it).

## Who uses Keycloak and why

Developers and identity teams who need login, single sign-on and user management for their applications.

Keycloak is open-source identity and access management. Users sign in to Keycloak instead of each application, which gives single sign-on and sign-out, social login, LDAP user federation and fine-grained authorization.

A typical use: A company builds several internal web apps and does not want each one to store passwords. Each app hands login to Keycloak over OpenID Connect; employees sign in once with their existing directory accounts, and logging out of one app logs them out of all of them.

From the upstream project's own description: https://github.com/keycloak/keycloak, https://www.keycloak.org/.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-22 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 13 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`keycloak`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-22; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/keycloak.

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

— ReadyStack · https://readystack.dev
