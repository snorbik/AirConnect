ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base:9.5.1
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Same root filesystem as hassio-addons/app-aircast 5.2.3
COPY rootfs /

# Locally built aircast (AirConnect 1.12.4 + fixes, see README.md)
COPY aircast /usr/bin/aircast
RUN chmod a+x /usr/bin/aircast \
    /etc/s6-overlay/s6-rc.d/aircast/run /etc/s6-overlay/s6-rc.d/aircast/finish \
    /etc/s6-overlay/s6-rc.d/init-aircast/run
