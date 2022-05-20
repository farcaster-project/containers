# [`monerod`](https://github.com/monero-project/monero) image

Build the default monerod image with

```
docker build -t monerod:latest .
```

Available `build-arg`:

- **VRS**: monerod version to install, default _0.17.3.2_

Create a container with

```
docker create -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env MONEROD_ZMQ_PORT=18082\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    monerod:latest
```

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `monerod` with a prepended `--`
- **MONEROD_RPC_PORT**: the port monerod RPC is listening on, usually 18081, 28081, and 38081
- **MONEROD_ZMQ_PORT**: the port monerod 0MQ is listening on, usually 18082, 28082, and 38082
- **OFFLINE**: a flag intended for usage with a regtest network. Needs to have `--` preprended, e.g. `--offline`
- **DIFFICULTY**: a flag intended for usage with a regtest network. Sets a fixed difficulty for generating blocks. For non-regtest set this to `0`, for regtest `1` is the recommended value.

RPC and 0MQ are binded to `0.0.0.0` to accept any connections, you probably want to expose the chosen ports outside the container with `-p`.

`monero-wallet-cli` and other Monero utilities are available inside the image, run `docker exec -it {monerod} /bin/bash` to get a shell and start using it.

## GitHub Action usage

```yaml
services:
  monerod:
    image: ghcr.io/farcaster-project/containers/monerod:latest
    env:
      NETWORK: regtest
      MONEROD_RPC_PORT: 18081
      MONEROD_ZMQ_PORT: 18082
      OFFLINE: --offline
      DIFFICULTY: 1
    ports:
      - 18081:18081
      - 18082:18082
```

## Standalone usage

```
docker pull ghcr.io/farcaster-project/containers/monerod:latest
docker create -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env MONEROD_ZMQ_PORT=18082\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:latest

docker start monerod
...
docker kill monerod
docker container rm monerod
```
