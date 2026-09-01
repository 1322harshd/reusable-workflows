FROM ghcr.io/actions/actions-runner:latest

USER root

# Runtime libraries Firefox needs even in headless mode (see main.yml history
# for how each one was diagnosed).
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        xz-utils \
        curl \
        python3 \
        python3-venv \
        python3-pip \
        libgtk-3-0 \
        libasound2t64 \
        libdbus-glib-1-2 \
        libxt6 \
        libx11-xcb1 \
        libnss3 \
        libnspr4 \
        libcups2 \
        libdrm2 \
        libgbm1 \
        libxcomposite1 \
        libxdamage1 \
        libxfixes3 \
        libxrandr2 \
        libxtst6 \
        fonts-liberation \
    && rm -rf /var/lib/apt/lists/*

# Firefox itself (arm64 build - Mozilla has no official Chrome-style
# "for testing" channel, but does publish linux64-aarch64 tarballs).
ARG FIREFOX_VERSION=latest
RUN curl -fsSL "https://download.mozilla.org/?product=firefox-${FIREFOX_VERSION}&os=linux64-aarch64&lang=en-US" -o /tmp/firefox.tar.xz \
    && tar -xJf /tmp/firefox.tar.xz -C /opt \
    && rm /tmp/firefox.tar.xz \
    && ln -s /opt/firefox/firefox /usr/local/bin/firefox

# geckodriver matching arch (pin a version so the image is reproducible;
# bump this when you deliberately want a newer Firefox/geckodriver pair).
ARG GECKODRIVER_VERSION=0.35.0
RUN curl -fsSL "https://github.com/mozilla/geckodriver/releases/download/v${GECKODRIVER_VERSION}/geckodriver-v${GECKODRIVER_VERSION}-linux-aarch64.tar.gz" -o /tmp/geckodriver.tar.gz \
    && tar -xzf /tmp/geckodriver.tar.gz -C /usr/local/bin \
    && rm /tmp/geckodriver.tar.gz \
    && chmod +x /usr/local/bin/geckodriver

# Python venv with test deps baked in (build-time pip install, so CI
# doesn't hit the network for these every run).
RUN python3 -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

WORKDIR /workspace

COPY selenium/requirements.txt /workspace/requirements.txt

RUN /opt/venv/bin/pip install \
    --no-cache-dir \
    -r /workspace/requirements.txt

USER runner
