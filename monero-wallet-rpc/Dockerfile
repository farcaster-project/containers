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

COPY --from=install /usr/bin/monero-wallet* /usr/bin/

# Default monero wallet-rpc ports (mainnet testnet stagenet), regtest should use same as mainnet
EXPOSE 18083 28083 38083

LABEL "org.opencontainers.image.description"="A ready to use monero wallet-rpc image suited for CI and tests."

CMD ["sh", "-c", "/usr/bin/monero-wallet-rpc --disable-rpc-login --wallet-dir wallets --daemon-host $MONERO_DAEMON_HOST --rpc-bind-ip 0.0.0.0 --rpc-bind-port $WALLET_RPC_PORT --confirm-external-bind --trusted-daemon"]
