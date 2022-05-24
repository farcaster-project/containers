# [`electrs`](https://github.com/romanz/electrs) image

Build the default electrs image with

```
docker build -t electrs:0.9.7 .
```

Available `build-arg`:

- **VRS**: electrs version to install, default _v0.9.7_

Create a container from image with

```
docker create -p 60401:60401\
    --env DAEMON_RPC_ADDR=bitcoind:18443\
    --env DAEMON_P2P_ADDR=bitcoind:18444\
    --env NETWORK=regtest\
    --env ELECTRUM_RPC_PORT=60401\
    --name electrs\
    electrs:0.9.7
```

The bitcoin _datadir_ is expected to be in the `/data` volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create` or `--volumes-from bitcoind`. Additionally, to access bitcoind's rpc running in another container, pass in a `--link <container>` argument with the name of the bitcoind container.

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `electrs`
- **DAEMON_RPC_ADDR**: the listening RPC address of bitcoind, usually **8332**, **18332**, and **18443**
- **DAEMON_P2P_ADDR**: the listening P2P address of bitcoind, usually **8333**, **18333**, and **18444**
- **ELECTRUM_RPC_PORT**: the port the electrs server is listening on, usually **50001**, **60001**, and **60401**

`electrum_rpc_addr` is generated with `0.0.0.0` and the given port `ELECTRUM_RPC_PORT`, you probably want to expose the chosen port outside the container with `-p`.

## Standalone usage with [`containers/bitcoin-core`](https://github.com/farcaster-project/containers/tree/main/bitcoin-core) image

```
docker pull ghcr.io/farcaster-project/containers/bitcoin-core:23.0
docker pull ghcr.io/farcaster-project/containers/electrs:0.9.7
docker volume create --name bitcoind-data

docker create\
    --name bitcoind\
    --env NETWORK=regtest\
    --env RPC_PORT=18443\
    --env P2P_PORT=18444\
    --env FALLBACKFEE=0.00001\
    -v bitcoind-data:/data\
    ghcr.io/farcaster-project/containers/bitcoin-core:23.0

docker create -p 60401:60401\
    --name electrs\
    --env DAEMON_RPC_ADDR=bitcoind:18443\
    --env DAEMON_P2P_ADDR=bitcoind:18444\
    --env NETWORK=regtest\
    --env ELECTRUM_RPC_PORT=60401\
    --volumes-from bitcoind\
    --link bitcoind\
    ghcr.io/farcaster-project/containers/electrs:0.9.7

docker start bitcoind
docker start electrs

docker kill bitcoind electrs
docker container rm bitcoind electrs
docker volume rm bitcoind-data
```
