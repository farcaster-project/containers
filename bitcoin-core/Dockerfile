FROM ubuntu:20.04 AS install

ARG VRS
ENV VERSION=${VRS:-22.0}
RUN echo $VERSION

RUN apt update && apt install -y wget gpg
# Get the public key to verify signatures
RUN gpg --keyserver keys.openpgp.org --recv-keys 9DEAE0DC7063249FB05474681E4AED62986CD25D
RUN gpg --keyserver keys.openpgp.org --recv-keys 74E2DEF5D77260B98BC19438099BAD163C70FBFA
RUN gpg --keyserver keys.openpgp.org --recv-keys 152812300785C96444D3334D17565732E08E5E41
# Get bins, hashes, and signatures
RUN wget https://bitcoin.org/bin/bitcoin-core-$VERSION/bitcoin-$VERSION-x86_64-linux-gnu.tar.gz\
    && wget https://bitcoin.org/bin/bitcoin-core-$VERSION/SHA256SUMS\
    && wget https://bitcoin.org/bin/bitcoin-core-$VERSION/SHA256SUMS.asc
# Verify bins and signatures
RUN gpg --verify SHA256SUMS.asc SHA256SUMS 2>&1 | grep "Good signature"

RUN sha256sum --ignore-missing --check SHA256SUMS
RUN tar xzf bitcoin-$VERSION-x86_64-linux-gnu.tar.gz
RUN install -m 0755 -o root -g root -t /usr/bin bitcoin-$VERSION/bin/*

FROM ubuntu:20.04

COPY --from=install /usr/bin/bitcoind /usr/bin/bitcoind
COPY --from=install /usr/bin/bitcoin-cli /usr/bin/bitcoin-cli

EXPOSE 8332 8333 18332 18333 18443 18444

VOLUME /data

CMD ["sh", "-c", "/usr/bin/bitcoind -$NETWORK -server -rest -rpcport=$RPC_PORT -rpcbind=0.0.0.0 -rpcallowip=0.0.0.0/0 -fallbackfee=$FALLBACKFEE -datadir=/data"]
