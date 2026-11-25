# Trademarks

This image contains Keycloak™.

Keycloak™ is a trademark of The Linux Foundation.

ReadyStack is not affiliated with or endorsed by The Linux Foundation.

The name identifies the software in this image; it is built from github.com/keycloak/keycloak source at 26.8.2 (commit e514445f9dcd). ReadyStack patches quarkus/deployment/src/main/java/org/keycloak/quarkus/deployment/KeycloakProcessor.java so the build output does not change from one JVM run to the next (provider maps are put in a stable order), and overrides the Guava dependency to 33.7.2-jre for CVE-2026-102554. The patch and the override are in the build kit.

ReadyStack uses no project logos.

Trademark policy: https://www.linuxfoundation.org/legal/trademark-usage (read 2026-11-04).
