FROM debian:bullseye-slim AS install

ARG VRS
ENV VERSION=${VRS:-v0.17.3.0}
RUN echo $VERSION

RUN apt update && apt install -y wget tar bzip2
# Get bins and signatures
RUN wget https://downloads.getmonero.org/cli/monero-linux-x64-$VERSION.tar.bz2
COPY SHASUM ./
RUN echo "$(head -n 1 ./SHASUM) monero-linux-x64-$VERSION.tar.bz2" | sha256sum -c

# Extract and install binaries
RUN tar xf monero-linux-x64-$VERSION.tar.bz2
RUN install -m 0755 -o root -g root -t /usr/bin monero-x86_64-linux-gnu-$VERSION/monero*

FROM debian:bullseye-slim

COPY --from=install /usr/bin/monero* /usr/bin/

#monerod default p2p ports (mainnet testnet stagenet)
EXPOSE 18080 28080 38080
# monerd default rpc ports (mainnet testnet stagenet)
EXPOSE 18081 28081 38081
# monerod default zmq ports (mainnet testnet stagenet)
EXPOSE 18082 28082 38082

CMD ["sh", "-c", "/usr/bin/monerod --$NETWORK --p2p-bind-ip 0.0.0.0 --rpc-bind-ip 0.0.0.0 --rpc-bind-port $MONEROD_RPC_PORT --zmq-rpc-bind-ip 0.0.0.0 --zmq-bind-port $MONEROD_ZMQ_PORT --confirm-external-bind --non-interactive $OFFLINE --fixed-difficulty $DIFFICULTY"]
