# [`bitcoin-core`](https://github.com/bitcoin/bitcoin) image

> As of version `22.0+` Bitcoin Core changed the binaries verification process, making this Dockerfile invalid "as is" with prior versions.

Build the default bitcoin-core image with

```
docker build -t bitcoin-core:23.0 .
```

Available `build-arg`:

- **VRS**: bitcoin-core version to install, default _23.0_

Create a container with exposed ports for RPC and P2P connections

```
docker create -p 18443:18443 -p 18444:18444\
    --env NETWORK=regtest\
    --name bitcoind\
    bitcoin-core:23.0
```

The bitcoin datadir is in the `/data` volume and can be accessed by addtionally passing for example `-v /path/to/host/folder:data` to `docker create` or create a named volume with `docker volume create --name bitcoind-data` and use the flag `-v bitcoind-data:/data`.

Available environment variables:

- **NETWORK**: a flag intended for the network, **mainnet**, **testnet**, or **regtest**

RPC is binded to `0.0.0.0` to accept any connections, you probably want to expose the chosen port outside the container with `-p [hostPort]:[containerPort]`.

Available listening container ports:

- **rpc port**: the port bitcoin-core is listening on for RPC connections, **8332**, **18332**, or **18443**
- **p2p port**: the port bitcoin-core is listening on for peer-to-peer connections, **8333**, **18333**, or **18444**

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
        image: ghcr.io/farcaster-project/containers/bitcoin-core:23.0
        env:
          NETWORK: regtest
        volumes:
          - bitcoind-data:/data
        ports:
          - 18443:18443
          - 18444:18444
```

## Standalone usage

Pull the image, create a named volume, and finally create the container (here named `bitcoind`).

```
docker pull ghcr.io/farcaster-project/containers/bitcoin-core:23.0
mkdir data

docker create -p 18443:18443 -p 18444:18444\
    --name bitcoind\
    --env NETWORK=regtest\
    -v $(pwd)/data:/data\
    ghcr.io/farcaster-project/containers/bitcoin-core:23.0

docker start bitcoind
sudo cat data/regtest/.cookie

docker kill bitcoind
docker container rm bitcoind
sudo rm -r ./data
```
