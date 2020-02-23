FROM debian:buster
LABEL maintainer "soltune <k@psycube.com>"
ENV EMSCRIPTEN_VERSION releases-fastcomp-1290d9deb93d67c4649999a8f2c8d9167d38dc04-64bit
ENV PORT_STATIC 5000
ENV PORT_API 5001

RUN apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends \
        apt-transport-https \
        autoconf \
        build-essential \
        ca-certificates \
        cmake \
        curl \
        git \
        gnupg \
        lbzip2 \
        libstdc++6 \
        libxml2 \
        nodejs \
        npm \
        pkg-config \
        python \
        tar \
        xz-utils \
    && rm /bin/sh && ln -s /bin/bash /bin/sh

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=1 apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
    && apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends yarn \
    && rm -rf /var/lib/apt/lists/*

RUN git clone --depth 1 https://github.com/emscripten-core/emsdk.git /usr/local/src/emsdk \
    && /usr/local/src/emsdk/emsdk install ${EMSCRIPTEN_VERSION} \
    && /usr/local/src/emsdk/emsdk activate ${EMSCRIPTEN_VERSION}

WORKDIR /usr/local/src/chip-player-js
RUN git clone --depth 1 https://github.com/soltune/chip-player-js.git /usr/local/src/chip-player-js \
    && sed -i \
        -e's/source ~/source \/usr\/local/' \
        -e's/homepage\": *\".\+\"/homepage\": \"\/\"/' package.json \
    && sed -i \
        -e's/localhost/0.0.0.0/' public/index.js \
    && sed -i \
        -e's/segmentCount = 1/segmentCount = 0/' public/404.html \
    && sed -i \
        -e's/API_BASE *= *process\.env\.REACT_APP_API_BASE/API_BASE = \"http:\/\/\"+window.location.hostname+\":'${PORT_API}'\/\"/' \
        -e's/CATALOG_PREFIX *= *process.env.REACT_APP_CATALOG_PREFIX/CATALOG_PREFIX = \"http:\/\/\"+window.location.hostname+\":'${PORT_STATIC}'\/static\/catalog\/\"/' \
        -e's/SOUNDFONT_URL_PATH *= *process\.env\.REACT_APP_SOUNDFONT_URL_PATH/SOUNDFONT_URL_PATH = \"http:\/\/\"+window.location.hostname+\":'${PORT_STATIC}'\/soundfonts\/\"/' src/config/index.js 

WORKDIR libxmp
RUN autoconf \
    && source /usr/local/src/emsdk/emsdk_env.sh \
    && emconfigure ./configure \
    && emmake make -f Makefile.lite

WORKDIR libxmp-lite-stagedir
RUN autoconf \
    && source /usr/local/src/emsdk/emsdk_env.sh \
    && emconfigure ./configure --enable-static --disable-shared \
    && emmake make

WORKDIR /usr/local/src/chip-player-js/fluidlite/build
RUN source /usr/local/src/emsdk/emsdk_env.sh \
    && emcmake cmake -DDISABLE_SF3=1 .. \
    && emmake make fluidlite-static

WORKDIR /usr/local/src/chip-player-js/
ADD ./rhythm/*WAV ./rhythm/*bin ./public/rhythm/
ADD ./firebaseConfig.js ./src/config/
ADD ./soundfonts/*sf2 ./soundfonts/*SF2 ./public/soundfonts/

RUN yarn install 
