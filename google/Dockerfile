ARG TAG=alpha
FROM smartgic/ovos-tx-server-base:${TAG}

ARG BUILD_DATE=unknown
ARG VERSION=unknown

LABEL org.opencontainers.image.title="Open Voice OS OCI Google translator image"
LABEL org.opencontainers.image.description="A translator plugin for Open Voice OS using Google translate."
LABEL org.opencontainers.image.version=${VERSION}
LABEL org.opencontainers.image.created=${BUILD_DATE}
LABEL org.opencontainers.image.documentation="https://openvoiceos.github.io/community-docs"
LABEL org.opencontainers.image.source="https://github.com/OpenVoiceOS/ovos-docker-tx"
LABEL org.opencontainers.image.vendor="Open Voice OS"

ARG ALPHA=false
ARG USER=ovos

SHELL ["/bin/bash", "-c"]

RUN if [ "${ALPHA}" == "true" ]; then \
    pip3 install --no-cache-dir ovos-translate-server ovos-google-translate-plugin; \
    else \
    pip3 install --no-cache-dir ovos-translate-server ovos-google-translate-plugin; \
    fi

USER $USER

ENTRYPOINT ovos-translate-server --tx-engine ovos-google-translate-plugin --detect-engine ovos-google-lang-detector-plugin
