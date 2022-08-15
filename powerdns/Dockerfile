ARG BUILD_FROM=ghcr.io/hassio-addons/base:12.2.2
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Copy root filesystem
COPY rootfs /

# Setup base
ENV POWERDNS_VERSION=4.6.3

SHELL /bin/ash

# hadolint ignore=DL3003
RUN set -o pipefail && \
    apk --no-cache add bash=5.1.16-r2 libpq=14.5-r0 sqlite-libs=3.38.5-r0 libstdc++=11.2.1_git20220219-r2 libgcc=11.2.1_git20220219-r2 mariadb-client=10.6.8-r0 mariadb-connector-c=3.1.13-r4 lua5.1-dev=5.1.5-r11 curl-dev=7.83.1-r2 && \
    apk --no-cache add --virtual build-deps \
      g++=11.2.1_git20220219-r2 make=4.3-r0 mariadb-dev=10.6.8-r0 postgresql13-dev=13.8-r0 sqlite-dev=3.38.5-r0 curl=7.83.1-r2 boost-dev=1.78.0-r1 mariadb-connector-c-dev=3.1.13-r4 && \
    curl -sSL https://downloads.powerdns.com/releases/pdns-$POWERDNS_VERSION.tar.bz2 | tar xj -C /tmp && \
    cd /tmp/pdns-$POWERDNS_VERSION && \
    ./configure --prefix="" --exec-prefix=/usr --sysconfdir=/etc/pdns \
      --with-modules="bind gmysql gpgsql gsqlite3" && \
    make && make install-strip && cd / && \
    mkdir -p /etc/pdns/conf.d && \
    addgroup -S pdns 2>/dev/null && \
    adduser -S -D -H -h /var/empty -s /bin/false -G pdns -g pdns pdns 2>/dev/null && \
    cp /usr/lib/libboost_program_options.so* /tmp && \
    apk del --purge build-deps && \
    apk --no-cache add boost1.78-libs=1.78.0-r1 && \
    mv /tmp/lib* /usr/lib/ && \
    rm -rf /tmp/pdns-$POWERDNS_VERSION

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
