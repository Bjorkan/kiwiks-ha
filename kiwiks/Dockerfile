# https://developers.home-assistant.io/docs/apps/configuration#app-dockerfile
ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23
FROM ${BUILD_FROM}

ARG KIWIX_TOOLS_VERSION=3.8.2
ARG TARGETARCH
ARG BUILD_ARCH

# Install official kiwix-serve (musl static binaries from download.kiwix.org).
# HA base is Alpine (musl), so the musl builds are the correct match.
# TARGETARCH comes from Docker BuildKit / HA builder (amd64, arm64);
# BUILD_ARCH (amd64, aarch64) is the Supervisor fallback.
RUN set -eux; \
    ARCH="${TARGETARCH}"; \
    if [ -z "${ARCH}" ]; then \
        case "${BUILD_ARCH:-}" in \
            amd64) ARCH="amd64" ;; \
            aarch64) ARCH="arm64" ;; \
        esac; \
    fi; \
    case "${ARCH}" in \
        amd64) KIWIX_ARCH="x86_64" ;; \
        arm64) KIWIX_ARCH="aarch64" ;; \
        *) echo "Unsupported arch (TARGETARCH=${TARGETARCH}, BUILD_ARCH=${BUILD_ARCH:-unset})" >&2; exit 1 ;; \
    esac; \
    apk add --no-cache curl; \
    URL="https://download.kiwix.org/release/kiwix-tools/kiwix-tools_linux-${KIWIX_ARCH}-musl-${KIWIX_TOOLS_VERSION}.tar.gz"; \
    echo "Downloading ${URL}"; \
    curl -fsSL "${URL}" | tar -xz -C /usr/local/bin --strip-components=1; \
    kiwix-serve --version; \
    kiwix-manage --help >/dev/null

# Copy s6-overlay service definitions
COPY rootfs /

# Replaces the obsolete config.yaml 'watchdog' key (rejected by the HA linter).
# The Supervisor hits this endpoint and restarts an unhealthy container.
HEALTHCHECK --interval=5m --timeout=10s --start-period=1m --retries=3 \
    CMD wget -q -O /dev/null http://127.0.0.1:8080/catalog/v2/entries?count=0 || exit 1

LABEL \
    org.opencontainers.image.title="Home Assistant App: Kiwiks" \
    org.opencontainers.image.description="Serve offline ZIM files (Kiwix) from your NAS network storage" \
    org.opencontainers.image.source="https://github.com/Bjorkan/kiwiks-ha" \
    org.opencontainers.image.licenses="GPL-3.0-or-later"
