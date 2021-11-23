`electrs` container
===

Build the image against `ubuntu:20.04` with

```
docker build --build-arg VRS=v0.9.3 -t electrs:latest .
```

Available `build-arg`:

- **VRS**: electrs version to install, default *v0.9.3*

Create a container with

```
docker create -p 50001:50001\
    --env DAEMON_RPC_ADDR=bitcoind:18443\
    --env NETWORK=regtest\
    --env ELECTRUM_RPC_PORT=50001\
    --name electrs\
    electrs:latest
```

The bitcoin _datadir_ is expected to be in the `/data` volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create` or `--volumes-from bitcoind`. Additionally, to access bitcoind's rpc running in another container, pass in a `--link <container>` argument with the name of the bitcoind container.

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `electrs` with a prepended `-`
- **DAEMON_RPC_ADDR**: the address of the bitcoin daemon rpc
- **ELECTRUM_RPC_PORT**: the port the electrs server is listening on

RPC is binded to `0.0.0.0` and accept connection from everywhere.

All the ports are exposed by defaut.

## Standalone usage with bitcoin-core image

```
docker pull ghcr.io/farcaster-project/containers/electrs:latest
docker volume create --name bitcoind-data

docker create -p 18443:18443\
    --name bitcoind\
    --env NETWORK=regtest\
    --env RPC_PORT=18443\
    --env FALLBACKFEE=0.00001\
    -v bitcoind-data:/data\
    ghcr.io/farcaster-project/containers/bitcoin-core:latest

docker create -p 50001:50001\
    --name electrs\
    --env DAEMON_RPC_ADDR=bitcoind:18443\
    --env NETWORK=regtest\
    --env ELECTRUM_RPC_PORT=50001\
    --volumes-from bitcoind\
    --link bitcoind\
    ghcr.io/farcaster-project/containers/electrs:latest

docker start bitcoind
docker start electrs

...

docker kill bitcoind electrs
docker container rm bitcoind electrs
docker volume rm bitcoind-data
```
