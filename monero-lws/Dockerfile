FROM ubuntu:20.04

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
	git checkout v0.17.3.0 && \
    git submodule init && git submodule update && \
	mkdir build && cd build && \
	cmake .. && \
    make -j$(nproc)

# build monero-lws
RUN git clone https://github.com/TheCharlatan/monero-lws && \
	cd ../monero-lws && \
    git checkout auto_accept_requests && \
	mkdir build && cd build && \
    cmake -DMONERO_SOURCE_DIR=/monero -DMONERO_BUILD_DIR=/monero/build .. && \
	make && \
	make install

# install shared libraries
# RUN apt update
# RUN DEBIAN_FRONTEND=noninteractive apt-get --no-install-recommends --yes install \
#     libboost-chrono-dev \
#     libboost-date-time-dev \
#     libboost-filesystem-dev  \
#     libboost-locale-dev \
#     libboost-program-options-dev \
#     libboost-regex-dev \
#     libboost-serialization-dev \
#     libboost-system-dev \
#     libboost-thread-dev \
# 	libssl-dev \
# 	libzmq3-dev \
# 	libpgm-dev \
# 	libunbound-dev \
# 	libsodium-dev

EXPOSE 38084

RUN mkdir /data
RUN arch
RUN uname -a
RUN lshw
RUN ./usr/local/bin/monero-lws-daemon

CMD ["sh", "-c", "/usr/local/bin/monero-lws-daemon --network $NETWORK --daemon tcp://$MONEROD_ADDRESS --confirm-external-bind --rest-server http://0.0.0.0:38084 --auto-accept-requests --db-path /data"]

