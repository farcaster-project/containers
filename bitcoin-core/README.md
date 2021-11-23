# `bitcoin-core` container

:warning: This image only works for version `22.0+` of Bitcoin Core!

Build the image against `ubuntu:20.04` with

```
docker build --build-arg VRS=22.0 -t bitcoind:latest .
```

Available `build-arg`:

- **VRS**: bitcoin-core version to install, default _22.0_

Create a container with

```
docker create -p 18443:18443\
    --env NETWORK=regtest\
    --env FALLBACKFEE=0.00001\
    --env RPC_PORT=18443
    --name bitcoind\
    bitcoind:latest
```

The bitcoin datadir is in the /data volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create` or create a named volume with `docker volume create --name bitcoind-data` and use the flag `-v bitcoind-data:/data`.

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `bitcoind` with a prepended `-`
- **RPC_PORT**: the RPC port to bind
- **FALLBACKFEE**: the fallbackfee parameter

RPC is binded to `0.0.0.0` and accept connection from everywhere.

All the ports are exposed by defaut. `bitcoin-cli` is also installed.

## GitHub Action usage

You can run the job on the `rust` image and add services such as `bitcoind` that share a named volume `bitcoind-data` mounted on `/data`:

```yaml
  job:
    runs-on: ubuntu-latest
    container:
      image: rust:latest
      volumes:
        - bitcoind-data:/data

    services:
      bitcoind:
        image: ghcr.io/farcaster-project/containers/bitcoin-core
        env:
          NETWORK: regtest
          RPC_PORT: 18443
          FALLBACKFEE: "0.00001"
        volumes:
          - bitcoind-data:/data
        ports:
          - 18443:18443
```

## Standalone usage

Pull the latest image, create a named volume, and finally create the container.

```
docker pull ghcr.io/farcaster-project/containers/bitcoin-core:latest
docker volume create --name bitcoind-data

ID=$(docker create -p 18443:18443\
    --name bitcoind\
    --env NETWORK=regtest\
    --env RPC_PORT=18443\
    --env FALLBACKFEE=0.00001\
    -v bitcoind-data:/data\
    ghcr.io/farcaster-project/containers/bitcoin-core:latest)

docker start $ID

...

docker kill $ID
docker container rm $ID
docker volume rm bitcoind-data
```
