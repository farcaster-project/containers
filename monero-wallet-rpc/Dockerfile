FROM ubuntu:20.04 AS install

ARG VRS
ENV VERSION=${VRS:-v0.17.2.3}
RUN echo $VERSION

RUN apt update && apt install -y wget
# Get bins and signatures
RUN wget https://downloads.getmonero.org/cli/monero-linux-x64-$VERSION.tar.bz2
COPY SHASUM ./
RUN echo "$(head -n 1 ./SHASUM) monero-linux-x64-$VERSION.tar.bz2" | sha256sum -c

# Extract and install binaries
RUN tar xf monero-linux-x64-$VERSION.tar.bz2
RUN install -m 0755 -o root -g root -t /usr/bin monero-x86_64-linux-gnu-$VERSION/monero*

FROM ubuntu:20.04

COPY --from=install /usr/bin/monero* /usr/bin/

# monero-wallet-rpc port
EXPOSE 18083

CMD ["sh", "-c", "/usr/bin/monero-wallet-rpc --disable-rpc-login --wallet-dir wallets --daemon-host $MONEROD_ADDRESS --rpc-bind-ip 0.0.0.0 --rpc-bind-port 18083 --confirm-external-bind"]
