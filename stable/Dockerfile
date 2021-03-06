ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED
ARG FIREFOX_LANGUAGE="en-US"
ARG FIREFOX_PRODUCT="firefox-latest-ssl"

FROM debian:sid-slim as downloader

ARG FIREFOX_LANGUAGE
ARG FIREFOX_PRODUCT
ARG FIREFOX_DOWNLOAD_URL="https://download.mozilla.org/?product=${FIREFOX_PRODUCT}&os=linux64&lang=${FIREFOX_LANGUAGE}"

RUN apt-get update \
    && apt-get install -y \
    ca-certificates \
    curl \
    bzip2 \
    xz-utils \
    --no-install-recommends \
    && cd /opt/ \
    && echo "Downloading from ${FIREFOX_DOWNLOAD_URL}..." \                                                                                           
    && curl -fSL -o firefox.tar.bz2 "${FIREFOX_DOWNLOAD_URL}" \
    && tar xvjf firefox.tar.bz2 \
    && rm firefox.tar.bz2

FROM debian:sid-slim

ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED
ARG FIREFOX_LANGUAGE

COPY --from=downloader /opt/firefox/ /opt/firefox/

RUN apt-get update \
    && apt-get install -y \
    apulse \
    ca-certificates \
    hicolor-icon-theme \
    libasound2 \
    libgl1-mesa-dri \
    libgl1-mesa-glx \
    libgtk-3-0 \
    libdbus-glib-1-2 \
    libxrender1 \
    libx11-xcb-dev \
    libx11-xcb1 \
    libxt6 \
    ffmpeg \
    libpulse0 \
    fonts-noto \
    fonts-noto-cjk \
    fonts-noto-color-emoji \
    --no-install-recommends \
    && ln -s /opt/firefox/firefox /usr/local/bin/ \
    && update-alternatives --install /usr/bin/x-www-browser x-www-browser /opt/firefox/firefox 200 \
    && update-alternatives --set x-www-browser /opt/firefox/firefox \
    && apt-get autoremove -y \
    && apt-get autoclean -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

ENV LANG "${FIREFOX_LANGUAGE}"

COPY local.conf /etc/fonts/local.conf

RUN mkdir -p /etc/firefox && \  
    echo 'pref("browser.tabs.remote.autostart", false);' >> /etc/firefox/syspref.js

COPY entrypoint.sh /usr/bin/startfirefox

ENTRYPOINT [ "startfirefox" ]

LABEL org.opencontainers.image.title="Firefox" \
    org.opencontainers.image.description="Firefox in Docker" \
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"

