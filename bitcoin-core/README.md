# [`bitcoin-core](https://github.com/bitcoin/bitcoin)` image

> As of version `22.0+` Bitcoin Core changed the binaries verification process, making this Dockerfile invalid "as is" with prior versions.

Build the default bitcoin-core image with

```
docker build -t bitcoin-core:latest .
```

Available `build-arg`:

- **VRS**: bitcoin-core version to install, default _23.0_

Create a container with

```
docker create -p 18443:18443\
    --env NETWORK=regtest\
    --env FALLBACKFEE=0.00001\
    --env RPC_PORT=18443
    --name bitcoind\
    bitcoin-core:latest
```

The bitcoin datadir is in the `/data` volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create` or create a named volume with `docker volume create --name bitcoind-data` and use the flag `-v bitcoind-data:/data`.

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `bitcoind` with a prepended `-`
- **RPC_PORT**: the port bitcoin-core server is listening on, usually 8332, 18332, and 18443
- **FALLBACKFEE**: the fallbackfee parameter

RPC is binded to `0.0.0.0` to accept any connections, you probably want to expose the chosen port outside the container with `-p`.

`bitcoin-cli` is available inside the image, run `docker exec -it {bitcoind} /bin/bash` and then `bitcoin-cli -chain={} -datadir=/data {-getinfo}`.

## GitHub Action usage

You can run a job on your chosen image (here it's `rust`) and add a service (here named `bitcoin-core`) that share a named volume `bitcoind-data` mounted on `/data`:

```yaml
  job:
    runs-on: ubuntu-latest
    container:
      image: rust:latest
      volumes:
        - bitcoind-data:/data

    services:
      bitcoin-core:
        image: ghcr.io/farcaster-project/containers/bitcoin-core:latest
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

Pull the latest image, create a named volume, and finally create the container (here named `bitcoind`).

```
docker pull ghcr.io/farcaster-project/containers/bitcoin-core:latest
mkdir data

docker create -p 18443:18443\
    --name bitcoind\
    --env NETWORK=regtest\
    --env RPC_PORT=18443\
    --env FALLBACKFEE=0.00001\
    -v $(pwd)/data:/data\
    ghcr.io/farcaster-project/containers/bitcoin-core:latest

docker start bitcoind
sudo cat data/regtest/.cookie

...

docker kill bitcoind
docker container rm bitcoind
sudo rm -r ./data
```
