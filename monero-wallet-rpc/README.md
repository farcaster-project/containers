`monero-wallet-rpc` container
===

Build the image against `ubuntu:20.04` with

```
docker build --build-arg VRS=v0.17.2.3 -t monero-wallet-rpc:latest .
```

Available `build-arg`:

- **VRS**: monero-wallet-rpc version to install, default *0.17.2.3*

Create a container with

```
docker create -p 18083:18083\
    --name monero-wallet-rpc\
    --env MONEROD_ADDRESS=monerod:18081\
    --link monerod\
    monero-wallet-rpc:latest
```

Available environment variables:

- **MONEROD_ADDRESS**: the address of the monero daemon to use

RPC is binded to `0.0.0.0` and accepts connections from everywhere.

All the ports are exposed by defaut. Other monero binaries, like the `monero-wallet-cli`, are also installed.

## GitHub Action usage

```yaml
    services:
      monerod:
        image: ghcr.io/farcaster-project/containers/monerod
        env:
          NETWORK: regtest
          MONEROD_RPC_PORT: 18081
          OFFLINE: --offline
          DIFFICULTY: 1
        ports:
          - 18081:18081
      monero-wallet-rpc:
        image: ghcr.io/farcaster-project/containers/monero-wallet-rpc
        env:
          MONEROD_RPC_PORT: monerod:18081
        ports:
          - 18083:18083
```

## Standalone usage with monerod

```
docker create -p 18081:18081\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:latest

docker create -p 18083:18083\
    --name monero-wallet-rpc\
    --link monerod\
    --env MONEROD_ADDRESS=monerod:18081\
    ghcr.io/farcaster-project/containers/monero-wallet-rpc:latest

docker start monerod
docker start monero-wallet-rpc

...

docker kill monerod monero-wallet-rpc
docker container rm monerod monero-wallet-rpc
```
