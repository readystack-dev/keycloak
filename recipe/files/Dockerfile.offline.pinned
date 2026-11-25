# GENERATED L-009 hermetic Java build (--network=none, SBOM+SLSA-attestation-producing).
# Pinned bases; vendored source + maven cache(s) + toolchain/runtime debs from Dockerfile.vendor.
FROM maven:3.9.8-eclipse-temurin-21@sha256:de18e4e51396ad9266f76669779ed6d6b49fbb0f45ed6f39169d87378d548d7d AS jbuild
# dpkg-install the vendored build toolchain (git/unzip) — no apt, fully offline.
RUN --mount=type=bind,source=vendored/build-debs,target=/tmp/bdebs \
    set --; for f in /tmp/bdebs/*.deb; do [ -e "$f" ] && set -- "$@" "$f"; done; \
    for pass in 1 2 3 4 5; do [ $# -eq 0 ] || dpkg -i --skip-same-version "$@" >/dev/null 2>&1 && break; done; \
    dpkg --configure -a && bad= && for d in "$@"; do p=$(dpkg-deb -f "$d" Package) v=$(dpkg-deb -f "$d" Version); \
      case "$(dpkg-query -W -f='${Version} ${db:Status-Abbrev}' "$p" 2>/dev/null)" in "$v ii"*) ;; *) bad="$bad $p=$v" ;; esac; done && \
    { [ -z "$bad" ] || { echo "vendored debs not installed at their vendored version:$bad" >&2; false; }; }
WORKDIR /src
COPY vendored/keycloak-src/ .
COPY vendored/caches/m2/ /m2/
COPY vendored/caches/pnpm-store/ /pnpm-store/
COPY vendored/caches/pnpm-cache/ /pnpm-cache/
ENV MAVEN_OPTS="-Xmx3g"
ENV npm_config_store_dir="/pnpm-store"
ENV npm_config_cache="/pnpm-cache"
ENV MAVEN_ARGS="-Dproject.build.outputTimestamp=2026-11-22T07:49:48Z"
RUN cd /src && KC_BUILD_TIME="$(printf '%s\n' $MAVEN_ARGS | sed -n 's/^-Dproject\.build\.outputTimestamp=\([0-9]\{4\}-[0-9][0-9]-[0-9][0-9]\)T\([0-9][0-9]:[0-9][0-9]\):[0-9][0-9]Z$/\1 \2/p')" && test -n "$KC_BUILD_TIME" && echo "keycloak-version.properties build-time=$KC_BUILD_TIME" && npm_config_store_dir=/pnpm-store npm_config_cache=/pnpm-cache MAVEN_OPTS=-Xmx3g mvn "-Dtimestamp=$KC_BUILD_TIME" -o -B -pl quarkus/deployment,quarkus/dist -am -DskipTests -DskipProtoLock=true -Dmaven.build.cache.enabled=false -Dmaven.repo.local=/m2 clean install
RUN tar -xzf quarkus/dist/target/keycloak-*.tar.gz -C /opt --strip-components=0 && mv /opt/keycloak-* /opt/keycloak && mkdir -p /opt/keycloak/data
RUN mkdir -p /tmp/rs-canon && printf '%s\n' 'import java.io.*;' 'import java.nio.file.*;' '' '// Re-serializes the Quarkus lib/deployment/appmodel.dat with every String interned, so a value that occurs twice is' '// always written as one TC_STRING plus back-references. ObjectOutputStream otherwise shares a String only when the' '// in-memory model happened to hold the same instance, which varies from build to build.' 'public class AppModelCanon {' '    public static void main(String[] args) throws Exception {' '        Path in = Path.of(args[0]), out = Path.of(args.length > 1 ? args[1] : args[0]);' '        byte[] original = Files.readAllBytes(in);' '        Object model;' '        try (ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(original))) {' '            model = ois.readObject();' '            if (ois.read() != -1) throw new IllegalStateException("trailing data after the model in " + in);' '        }' '        ByteArrayOutputStream bytes = new ByteArrayOutputStream();' '        try (ObjectOutputStream oos = new ObjectOutputStream(bytes) {' '            { enableReplaceObject(true); }' '            @Override protected Object replaceObject(Object o) { return o instanceof String s ? s.intern() : o; }' '        }) {' '            oos.writeObject(model);' '        }' '        Files.write(out, bytes.toByteArray());' '        System.out.println("canonicalized " + in + " -> " + out + " (" + original.length + " -> " + bytes.size() + " bytes)");' '    }' '}' > /tmp/rs-canon/AppModelCanon.java && java -cp '/opt/keycloak/lib/lib/deployment/*:/opt/keycloak/lib/lib/boot/*:/opt/keycloak/lib/lib/main/*' /tmp/rs-canon/AppModelCanon.java /opt/keycloak/lib/lib/deployment/appmodel.dat && rm -rf /tmp/rs-canon
RUN set -eu; hit=""; for spec in com.google.guava:guava:33.7.2-jre:0; do \
      g="${spec%%:*}"; r="${spec#*:}"; a="${r%%:*}"; r="${r#*:}"; v="${r%%:*}"; e="${r##*:}"; \
      for j in $(find /opt/keycloak -name "*${a}-[0-9]*.jar"); do b="${j##*/}"; \
        case "$b" in "${a}-"*|"${g}.${a}-"*) ;; *) continue ;; esac; \
        case "$b" in "${a}-${v}.jar"|"${g}.${a}-${v}.jar"|"${a}-${v}-"*.jar|"${g}.${a}-${v}-"*.jar) hit="$hit $e"; echo "maven_dependency_overrides: $j at $v" ;; \
          *) echo "maven_dependency_overrides: $j is not at $v" >&2; exit 1 ;; esac; \
      done; \
    done; for e in $(seq 0 0); do case " $hit " in *" $e "*) ;; *) \
      echo "maven_dependency_overrides[$e]: no jar of it in the distribution" >&2; exit 1 ;; esac; done
# the release's JRE (eclipse-temurin:21-jre-noble: /opt/java/openjdk) with the application tree, the commands that
# need a JRE, and what the two load (this image has ldd, not readelf)
FROM eclipse-temurin:21-jre-noble@sha256:ac595159aa2209f07a2331efa8a8ce4278b77a7b76a9365a0f173786d5067a9e AS japp
ARG SOURCE_DATE_EPOCH=1795333788
COPY --from=jbuild /opt/keycloak /opt/keycloak
RUN PROPS_DATE="$(date -u -d "@${SOURCE_DATE_EPOCH:?}" +%Y-%m-%dT%H:%M:%SZ)" && JAVA_OPTS_APPEND="-Djava.properties.date=$PROPS_DATE" /opt/keycloak/bin/kc.sh build --db=postgres --health-enabled=true --metrics-enabled=true
RUN set -eu; find /opt/java/openjdk /opt/keycloak -type f | LC_ALL=C sort | while read -r f; do \
      [ "$(od -An -c -N4 "$f" | tr -d ' ')" = 177ELF ] || continue; \
      ldd "$f" 2>/dev/null | sed -n 's/^[[:space:]]*\([^[:space:]]*\) => \/.*/\1/p; s/^[[:space:]]*\(\/[^[:space:]]*\) (0x.*/\1/p; s/^[[:space:]]*\([^[:space:]]*\) => not found.*/absent \1/p'; \
    done | LC_ALL=C sort -u > /tmp/ldd.all; \
    grep '^absent ' /tmp/ldd.all | cut -d' ' -f2 | tr '\n' ' ' | sed 's/^./not in the release image either (not required): &/'; echo; \
    grep -v '^absent ' /tmp/ldd.all | while read -r lib; do case "$lib" in \
      /*) echo "$lib" ;; *) [ -n "$(find /opt/java/openjdk /opt/keycloak -name "$lib" -print -quit)" ] || echo "$lib" ;; esac; \
    done > /kitforge.needs && rm -f /tmp/ldd.all && \
    echo "the JRE and the application load: $(tr '\n' ' ' < /kitforge.needs)"
# rs-probe: the HEALTHCHECK helper (standard library only, from the kit's rs-probe.go)
FROM golang:1.27.3-bookworm@sha256:972b4110ba0f47539a975c31e52c64765e1ff19f2d74469ae4a595c6676e80a5 AS build_rs_probe
WORKDIR /probe
COPY rs-probe.go main.go
COPY rs-probe.mod go.mod
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS="-buildvcs=false" GOPROXY=off SOURCE_DATE_EPOCH=1795333788
RUN go build -trimpath -ldflags='-s -w' -o /rs-probe .
# the chiselled rootfs (vendored/chisel, cut by Dockerfile.vendor), the service user, the binaries
FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289 AS rootfs
ARG SOURCE_DATE_EPOCH=1795333788
ADD vendored/chisel/rootfs.tar /rootfs/
RUN printf 'keycloak:x:999:999::/opt/keycloak:/bin/sh\n' >> /rootfs/etc/passwd && printf 'keycloak:x:999:\n' >> /rootfs/etc/group && \
    mkdir -p /rootfs/opt/keycloak /rootfs/opt/keycloak/data && chown -R 999:999 /rootfs/opt/keycloak /rootfs/opt/keycloak/data
COPY --from=build_rs_probe /rs-probe /rootfs/usr/local/bin/rs-probe
COPY --from=japp /opt/java/openjdk /rootfs/opt/java/openjdk
COPY --from=japp /opt/keycloak /rootfs/opt/keycloak
COPY --from=japp /kitforge.needs /tmp/needs/keycloak
RUN chmod 755 /rootfs/usr/local/bin/rs-probe && \
    chown -R 999:999 /rootfs/opt/keycloak/data
RUN --mount=type=bind,source=vendored/chisel,target=/chisel set -eu; \
    for n in /tmp/needs/*; do while read -r lib; do [ -n "$lib" ] || continue; \
      case "$lib" in /*) [ -e "/rootfs$lib" ] ;; *) [ -n "$(find /rootfs/usr/lib /rootfs/usr/lib64 -name "$lib" 2>/dev/null | head -1)" ] ;; esac || \
      { echo "$(basename "$n") needs $lib, which the chiselled rootfs does not have: add its slice to runtime_base.slices" >&2; exit 1; }; \
    done < "$n"; done; rm -rf /tmp/needs; \
    [ -e /rootfs/etc/os-release ] || { echo "no /etc/os-release: base-files_release-info is missing" >&2; exit 1; }; \
    [ "$(grep -c '^Package: ' /rootfs/var/lib/dpkg/status)" = "$(wc -l < /chisel/packages.tsv)" ] || \
      { echo "/var/lib/dpkg/status does not list every package of vendored/chisel/packages.tsv" >&2; exit 1; }; \
    rm -rf /rootfs/var/log/dpkg.log /rootfs/var/log/alternatives.log /rootfs/var/log/apt /rootfs/var/cache/ldconfig/aux-cache /rootfs/var/cache/fontconfig
FROM scratch
ARG SOURCE_DATE_EPOCH=1795333788
COPY --from=rootfs /rootfs/ /
ENV PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin JAVA_HOME=/opt/java/openjdk LANG=C.UTF-8 KC_RUN_IN_CONTAINER=true KC_DB=postgres KC_HEALTH_ENABLED=true KC_METRICS_ENABLED=true KC_HTTP_ENABLED=true
LABEL org.opencontainers.image.licenses="Apache-2.0"
LABEL org.opencontainers.image.title="Keycloak"
LABEL org.opencontainers.image.source="https://github.com/keycloak/keycloak"
LABEL org.opencontainers.image.url="https://www.keycloak.org/"
LABEL org.opencontainers.image.version="26.8.2-CE-ubuntu24.04-r5"
LABEL org.opencontainers.image.description="Keycloak — open-source Identity and Access Management (OIDC / OAuth2 / SAML) server"
LABEL org.opencontainers.image.vendor="Keycloak"
LABEL org.opencontainers.image.revision="e514445f9dcdabc5bdcf67cab56c1e49655de067"
HEALTHCHECK --interval=5s --timeout=120s --start-period=45s \
  CMD /usr/local/bin/rs-probe http://127.0.0.1:9000/health/ready || exit 1
WORKDIR /opt/keycloak
USER keycloak
EXPOSE 8080 8443 9000
ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
