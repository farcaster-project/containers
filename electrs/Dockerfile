FROM rust:latest AS install

ARG VRS
ENV VERSION=${VRS:-v0.9.9}
RUN echo $VERSION

RUN apt update && apt install -y clang cmake

RUN git clone https://github.com/romanz/electrs && cd electrs && git checkout ${VERSION}

WORKDIR /electrs

RUN cargo build --release --bin electrs

FROM debian:bullseye-slim

COPY --from=install /electrs/target/release/electrs /usr/bin/electrs

# Default electrum ports: bitcoin, testnet, regtest, signet
EXPOSE 50001 60001 60401 60601
# Default electrs monitoring ports: bitcoin, testnet, regtest, signet
EXPOSE 4224 14224 24224 34224

VOLUME /data

LABEL "org.opencontainers.image.description"="A ready to use electrs image suited for CI and tests with containers/bitcoin-core."

CMD ["sh", "-c", "/usr/bin/electrs --log-filters INFO --network $NETWORK --daemon-dir /data --daemon-rpc-addr $DAEMON_RPC_ADDR --daemon-p2p-addr $DAEMON_P2P_ADDR --electrum-rpc-addr 0.0.0.0:$ELECTRUM_RPC_PORT"]
