# `monero-lws` container

Build the image against `ubuntu:20.04` with

```
docker build -t monero-lws-daemon:latest .
```

Create a container with

```
docker create -p 38084:38084\
    --name monero-lws-daemon\
    --env MONEROD_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    monero-lws-daemon:latest
```

Available environment variables:

- **MONEROD_ADDRESS**: the address of the monero daemon to use
- **NETWORK**: the network to run on, currently either main, stage or test

## Standalone usage with monerod

```
docker create -p 18081:18081\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:latest

docker create -p 38084:38084\
    --name monero-lws-daemon\
    --link monerod\
    --env MONEROD_ADDRESS=monerod:18082\
    --env NETWORK=main\
    ghcr.io/farcaster-project/containers/monero-lws:latest

docker start monerod
docker start monero-lws-daemon

...

docker kill monerod monero-lws-daemon
docker container rm monerod monero-lws-daemon
```
