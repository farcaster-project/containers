# [`monero-wallet-rpc`](https://github.com/monero-project/monero) image

Build the default monero-wallet-rpc image with

```
docker build -t monero-wallet-rpc:0.18.1.2 .
```

Available `build-arg`:

- **VRS**: monero-wallet-rpc version to install, default _0.18.1.2_

Create a container with exposed ports for RPC connections (you need a `monerod` container created to link with)

```
docker create -p 18083:18083\
    --name monero-wallet-rpc\
    --env MONERO_DAEMON_ADDRESS=monerod:18081\
    --env WALLET_RPC_PORT=18083\
    --link monerod\
    monero-wallet-rpc:0.18.1.2
```

Available environment variables:

- **MONERO_DAEMON_ADDRESS**: the address of the monero daemon in format `{host}:{port}` to use as backend for the wallets
- **WALLET_RPC_PORT**: the listening RPC port to bind to, usually **18083**, **28083**, or **38083**

Available listening container ports:

- **wallet rpc**: the listening RPC address of wallet-rpc, the chosen **WALLET_RPC_PORT**; when running in **regtest** mode the default ports are the same as **mainnet**

Wallet RPC is binded to `0.0.0.0` and the chosen **WALLET_RPC_PORT**, you probably want to expose the chosen port outside the container with `-p [hostPort]:[containerPort]`.

The `monero-wallet-cli` binary is also installed, you can use `docker run -it ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.18.1.2 /bin/bash` to use it stand alone or `docker exec -it {monero-wallet-rcp} /bin/bash` to enter a running container.

## GitHub Action usage

```yaml
services:
  monerod:
    image: ghcr.io/farcaster-project/containers/monerod:0.18.1.2
    env:
      NETWORK: regtest
      OFFLINE: --offline
      DIFFICULTY: 1
  monero-wallet-rpc:
    image: ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.18.1.2
    env:
      MONERO_DAEMON_ADDRESS: monerod:18081
      WALLET_RPC_PORT: 18083
```

## Standalone usage with [`containers/monerod`](https://github.com/farcaster-project/containers/tree/main/monerod) image

```
docker pull ghcr.io/farcaster-project/containers/monerod:0.18.1.2
docker pull ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.18.1.2

docker create -p 18080:18080 -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:0.18.1.2

docker create -p 18083:18083\
    --name monero-wallet-rpc\
    --link monerod\
    --env MONERO_DAEMON_ADDRESS=monerod:18081\
    --env WALLET_RPC_PORT=18083\
    ghcr.io/farcaster-project/containers/monero-wallet-rpc:0.18.1.2

docker start monerod
docker start monero-wallet-rpc

docker kill monerod monero-wallet-rpc
docker container rm monerod monero-wallet-rpc
```
