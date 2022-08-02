FROM ubuntu:20.04 AS install

ARG TAG
ENV MNRTAG=${TAG:-0.17.3.0}
RUN echo $MNRTAG

RUN apt update && apt install -y git
RUN DEBIAN_FRONTEND=noninteractive apt-get --no-install-recommends --yes install \
    automake \
    autotools-dev \
    bsdmainutils \
    build-essential \
    ca-certificates \
    ccache \
    cmake \
    curl \
    git \
    libtool \
    pkg-config \
    gperf \
    libboost-chrono-dev \
    libboost-date-time-dev \
    libboost-filesystem-dev  \
    libboost-locale-dev \
    libboost-program-options-dev \
    libboost-regex-dev \
    libboost-serialization-dev \
    libboost-system-dev \
    libboost-thread-dev \
    libssl-dev \
    libzmq3-dev \
    libpgm-dev \
    libunbound-dev \
    libsodium-dev

# build monero
RUN git clone https://github.com/monero-project/monero && \
    cd monero && \
    git checkout v$MNRTAG && \
    git submodule init && git submodule update && \
    mkdir build && cd build && \
    cmake -DCMAKE_CXX_FLAGS="-mno-avx512f" -DCMAKE_C_FLAGS="-mno-avx512f" .. && \
    make -j$(nproc)

# build monero-lws
RUN git clone https://github.com/TheCharlatan/monero-lws && \
    cd ../monero-lws && \
    git checkout changes && \
    mkdir build && cd build && \
    cmake -DMONERO_SOURCE_DIR=/monero -DMONERO_BUILD_DIR=/monero/build .. && \
    make && \
    make install

# separate deployment container
FROM ubuntu:20.04

COPY --from=install /usr/local/bin/monero* /usr/bin/

# install shared libraries
RUN apt update
RUN DEBIAN_FRONTEND=noninteractive apt-get --no-install-recommends --yes install \
    libboost-chrono-dev \
    libboost-date-time-dev \
    libboost-filesystem-dev  \
    libboost-locale-dev \
    libboost-program-options-dev \
    libboost-regex-dev \
    libboost-serialization-dev \
    libboost-system-dev \
    libboost-thread-dev \
    libssl-dev \
    libzmq3-dev \
    libpgm-dev \
    libunbound-dev \
    libsodium-dev

# Rest server listening port
EXPOSE 38884

RUN mkdir /data

LABEL "org.opencontainers.image.description"="An image of a modified monero-lws suited for CI and tests."

CMD ["sh", "-c", "/usr/bin/monero-lws-daemon --network $NETWORK --daemon tcp://$MONERO_DAEMON_ADDRESS --confirm-external-bind --rest-server http://0.0.0.0:38884 --auto-accept-requests --db-path /data"]
