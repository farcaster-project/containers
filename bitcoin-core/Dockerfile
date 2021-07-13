FROM alpine:edge

ARG VRS
ENV VERSION=${VRS:-0.21.1}
RUN echo $VERSION

RUN apk add --no-cache bitcoin~$VERSION bitcoin-cli~$VERSION

EXPOSE 8332 8333 18332 18333 18443 18444

CMD ["sh", "-c", "/usr/bin/bitcoind -$NETWORK -server -rpcbind=0.0.0.0 -rpcallowip=0.0.0.0/0 -rpcuser=$RPC_USER -rpcpassword=$RPC_PASS"]
