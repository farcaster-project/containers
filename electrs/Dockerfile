FROM rust:latest AS install

ARG VRS
ENV VERSION=${VRS:-v0.9.3}
RUN echo $VERSION

RUN apt update && apt install -y clang cmake

RUN git clone https://github.com/romanz/electrs && cd electrs && git checkout ${VERSION}

WORKDIR /electrs

RUN cargo build --release --bin electrs

FROM ubuntu:20.04

COPY --from=install /electrs/target/release/electrs /usr/bin/electrs

EXPOSE 3002 60401 50001

VOLUME /data

CMD ["sh", "-c", "/usr/bin/electrs --network $NETWORK --daemon-dir /data --daemon-rpc-addr $DAEMON_RPC_ADDR --electrum-rpc-addr 0.0.0.0:$ELECTRUM_RPC_PORT"]
