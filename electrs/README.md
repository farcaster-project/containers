`electrs` container
===

Build the image against `ubuntu:20.04` with

```
docker build --build-arg VRS=v0.8.11 -t ...:latest .
```

Available `build-arg`:

- **VRS**: electrs version to install, default *v0.8.11*

Create a container with

```
docker create -p 50001:50001\
    --env DAEMON_RPC_ADDR=...\
    --env NETWORK=regtest\
    --env ELECTRUM_RPC_PORT=50001\
	--name electrs\
    ...:latest
```

The bitcoin datadir is expected to be in the /data volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create`. Additionally, to access bitcoind's rpc running in another container, pass in a `--link` argument with the name of the bitcoind container.

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `electrs` with a prepended `-`
- **DAEMON_RPC_ADDR**: the address of the bitcoin daemon rpc
- **ELECTRUM_RPC_PORT**: the port the electrs server is listening on

RPC is binded to `0.0.0.0` and accept connection from everywhere.

All the ports are exposed by defaut.

## Standalone usage

```
ID=$(docker create -p 50001:50001\
    --env DAEMON_RPC_ADDR=bitcoin:18443\
    --env NETWORK=regtest\
    --env ELECTRUM_RPC_PORT=50001\
    -v ~/data_dir:/data\
    ghcr.io/farcaster-project/containers/electrs:latest)

docker start $ID
...
docker kill $ID
docker container rm $ID
```
