FROM ubuntu:20.04 AS install

ARG VRS
ENV VERSION=${VRS:-0.21.1}
RUN echo $VERSION

RUN apt update && apt install -y wget gpg
# Get bins and signatures
RUN wget https://bitcoin.org/bin/bitcoin-core-$VERSION/bitcoin-$VERSION-x86_64-linux-gnu.tar.gz\
    && wget https://bitcoin.org/bin/bitcoin-core-$VERSION/SHA256SUMS.asc
# Verify bins and signatures
RUN sha256sum --ignore-missing --check SHA256SUMS.asc\
    && gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 01EA5486DE18A882D4C2684590C8019E36C2E964\
    && gpg --verify SHA256SUMS.asc
RUN tar xzf bitcoin-$VERSION-x86_64-linux-gnu.tar.gz
RUN install -m 0755 -o root -g root -t /usr/bin bitcoin-$VERSION/bin/*

FROM ubuntu:20.04

COPY --from=install /usr/bin/bitcoind /usr/bin/bitcoind
COPY --from=install /usr/bin/bitcoin-cli /usr/bin/bitcoin-cli

EXPOSE 8332 8333 18332 18333 18443 18444

VOLUME /data

CMD ["sh", "-c", "/usr/bin/bitcoind -$NETWORK -server -rest -rpcport=$RPC_PORT -rpcbind=0.0.0.0 -rpcallowip=0.0.0.0/0 -fallbackfee=$FALLBACKFEE -datadir=/data"]
