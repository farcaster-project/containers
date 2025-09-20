# [`monerod`](https://github.com/monero-project/monero) image

Build the default monerod image with

```
docker build -t monerod:0.18.3.4 .
```

Available `build-arg`:

- **VRS**: monerod version to install, default _0.18.3.4_

Create a container with

```
docker create -p 18080:18080 -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    monerod:0.18.3.4
```

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `monerod` with a prepended `--`
- **OFFLINE**: a flag intended for usage with a regtest network. Needs to have `--` preprended, e.g. `--offline`
- **DIFFICULTY**: a flag intended for usage with a regtest network. Sets a fixed difficulty for generating blocks. For non-regtest set this to `0`, for regtest `1` is the recommended value.

Available listening container ports:

- **monerod p2p**: the port monerod is listening on for P2P connections, **18080**, **28080**, or **38080**
- **monerod rpc**: the port monerod RPC is listening on, **18081**, **28081**, or **38081**
- **monerod zmq**: the port monerod Zero MQ is listening on, **18082**, **28082**, or **38082**

:warning: When running in **regtest** mode the default ports are the same as **mainnet**!

RPC and Zero MQ are binded to `0.0.0.0` to accept any connections, you probably want to expose the chosen ports outside the container with `-p [hostPort]:[containerPort]`.

`monero-wallet-cli` and other Monero utilities are available inside the image, run `docker exec -it {monerod} /bin/bash` to get a shell and start using it.

## GitHub Action usage

```yaml
services:
  monerod:
    image: ghcr.io/farcaster-project/containers/monerod:0.18.3.4
    env:
      NETWORK: regtest
      OFFLINE: --offline
      DIFFICULTY: 1
    ports:
      - 18080:18080
      - 18081:18081
      - 18082:18082
```

## Standalone usage

```
docker pull ghcr.io/farcaster-project/containers/monerod:0.18.3.4
docker create -p 18080:18080 -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:0.18.3.4

docker start monerod

docker kill monerod
docker container rm monerod
```
