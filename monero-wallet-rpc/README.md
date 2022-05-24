# [`monero-wallet-rpc`](https://github.com/monero-project/monero) image

Build the default monero-wallet-rpc image with

```
docker build -t monero-wallet-rpc:0.17.3.2 .
```

Available `build-arg`:

- **VRS**: monero-wallet-rpc version to install, default _0.17.3.2_

Create for example a container that connects to **stagenet** run

```
docker create -p 38083:38083\
    --name monero-wallet-rpc\
    --env MONERO_DAEMON_HOST=stagenet.melo.tools:38081\
    --env WALLET_RPC_PORT=38083\
    monero-wallet-rpc:0.17.3.2
```

Available environment variables:

- **MONERO_DAEMON_HOST**: the address of the monero daemon in format `{host}:{port}` to use as backend for the wallets
- **WALLET_RPC_PORT**: the listening RPC address of wallet-rpc, usually **18083**, **28083**, and **38083**

Wallet RPC is binded to `0.0.0.0` and the given port `WALLET_RPC_PORT`, you probably want to expose the chosen port outside the container with `-p`.

The `monero-wallet-cli` binary is also installed, you can use `docker run -it ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.17.3.2 /bin/bash` to use it stand alone or `docker exec -it {monero-wallet-rcp} /bin/bash` to enter a running container.

:warning: When running in **regtest** mode the default ports are the same as **mainnet**!

## GitHub Action usage

```yaml
services:
  monerod:
    image: ghcr.io/farcaster-project/containers/monerod:0.17.3.2
    env:
      NETWORK: regtest
      MONEROD_RPC_PORT: 18081
      OFFLINE: --offline
      DIFFICULTY: 1
    ports:
      - 18081:18081
  monero-wallet-rpc:
    image: ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.17.3.2
    env:
      MONERO_DAEMON_HOST: monerod:18081
      WALLET_RPC_PORT: 18083
    ports:
      - 18083:18083
```

## Standalone usage with [`containers/monerod`](https://github.com/farcaster-project/containers/tree/main/monerod) image

```
docker pull ghcr.io/farcaster-project/containers/monerod:0.17.3.2
docker pull ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.17.3.2

docker create -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env MONEROD_ZMQ_PORT=18082\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:0.17.3.2

docker create -p 18083:18083\
    --name monero-wallet-rpc\
    --link monerod\
    --env MONERO_DAEMON_HOST=monerod:18081\
    --env WALLET_RPC_PORT=18083\
    ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.17.3.2

docker start monerod
docker start monero-wallet-rpc

docker kill monerod monero-wallet-rpc
docker container rm monerod monero-wallet-rpc
```
