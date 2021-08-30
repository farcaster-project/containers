`bitcoin-core` container
===

Build the image against `alpine:edge` with

```
docker build --build-arg VRS=0.21.1 -t ...:latest .
```

Available `build-arg`:

- **VRS**: bitcoin-core version to install, default *0.21.1*

Create a container with

```
docker create -p 18443:18443 --env RPC_USER=... --env RPC_PASS=... --env NETWORK=regtest --env FALLBACKFEE=0.00001 ...:latest
```

Available environment variables:

- **NETWORK**: a flag inteded for the network, but this can be used more broadly as it is directly passed to `bitcoind` with a prepended `-`
- **RPC_USER**: the RPC user name
- **RPC_PASS**: the RPC password
- **FALLBACKFEE**: the fallbackfee parameter

RPC is binded to `0.0.0.0` and accept connection from everywhere.

All the ports are exposed by defaut. `bitcoin-cli` is also installed.

## GitHub Action usage

```yaml
    services:
      bitcoin-core:
        image: ghcr.io/farcaster-project/containers/bitcoin-core
        env:
          NETWORK: regtest
          RPC_USER: ci
          RPC_PASS: ${{ secrets.RPC_PASS }}
          FALLBACKFEE: 0.00001
        ports:
          - 18443:18443
```
