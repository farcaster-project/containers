# [`electrs`](https://github.com/romanz/electrs) image

Build the default electrs image with

```
docker build -t electrs:0.9.7 .
```

Available `build-arg`:

- **VRS**: electrs version to install, default _v0.9.7_

Create a container with exposed ports for RPC connections (you need a `bitcoind` container created to link with)

```
docker create -p 60401:60401\
    --env DAEMON_RPC_ADDR=bitcoind:18443\
    --env DAEMON_P2P_ADDR=bitcoind:18444\
    --env ELECTRUM_RPC_PORT=60401\
    --env NETWORK=regtest\
    --name electrs\
    --volumes-from bitcoind\
    --link bitcoind\
    electrs:0.9.7
```

The bitcoin _datadir_ is expected to be in the `/data` volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create` or `--volumes-from bitcoind`. Additionally, to access bitcoind's rpc running in another container, pass in a `--link <container>` argument with the name of the bitcoind container.

Available environment variables:

- **NETWORK**: a flag intended for the network, **bitcoin**, **testnet**, **regtest** or **signet**
- **DAEMON_RPC_ADDR**: the listening RPC address as `[host]:[port]` of bitcoind, usually **8332**, **18332**, or **18443**
- **DAEMON_P2P_ADDR**: the listening P2P address as `[host]:[port]` of bitcoind, usually **8333**, **18333**, or **18444**
- **ELECTRUM_RPC_PORT**: electrs listening RPC port, default ports are **50001**, **60001**, and **60401**

Available listening container ports:

- **electrum rpc**: the port the electrs server is listening on, the argument chosen in **ELECTRUM_RPC_PORT**

`electrum_rpc_addr` is generated with `0.0.0.0` and the chosen electrum port **ELECTRUM_RPC_PORT**, you probably want to expose the chosen port outside the container with `-p [hostPort]:[containerPort]`.

## GitHub Action usage

```yaml
services:
  bitcoind:
    image: ghcr.io/farcaster-project/containers/bitcoin-core:23.0
    env:
      NETWORK: regtest
    volumes:
      - bitcoind-data:/data
  electrs:
    image: ghcr.io/farcaster-project/containers/electrs:0.9.7
    env:
      NETWORK: regtest
      DAEMON_RPC_ADDR: bitcoind:18443
      DAEMON_P2P_ADDR: bitcoind:18444
      ELECTRUM_RPC_PORT: 60401
    volumes:
      - bitcoind-data:/data
```

## Standalone usage with [`containers/bitcoin-core`](https://github.com/farcaster-project/containers/tree/main/bitcoin-core) image

```
docker pull ghcr.io/farcaster-project/containers/bitcoin-core:23.0
docker pull ghcr.io/farcaster-project/containers/electrs:0.9.7
docker volume create --name bitcoind-data

docker create -p 18443:18443 -p 18444:18444\
    --name bitcoind\
    --env NETWORK=regtest\
    -v bitcoind-data:/data\
    ghcr.io/farcaster-project/containers/bitcoin-core:23.0

docker create -p 60401:60401\
    --name electrs\
    --env DAEMON_RPC_ADDR=bitcoind:18443\
    --env DAEMON_P2P_ADDR=bitcoind:18444\
    --env ELECTRUM_RPC_PORT=60401\
    --env NETWORK=regtest\
    --volumes-from bitcoind\
    --link bitcoind\
    ghcr.io/farcaster-project/containers/electrs:0.9.7

docker start bitcoind
docker start electrs

docker kill bitcoind electrs
docker container rm bitcoind electrs
docker volume rm bitcoind-data
```
