# `monero-lws` container

Build the image against `ubuntu:20.04` with

```
docker build -t monero-lws:latest .
```

Create a container with

```
docker create -p 38084:38084\
    --name monero-lws\
    --env MONEROD_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    monero-lws:latest
```

Available environment variables:

- **MONEROD_ADDRESS**: the address of the monero daemon to use
- **NETWORK**: the network to run on, currently either main, stage or test

## Standalone usage with monerod

```
docker create -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:latest

docker create -p 38084:38084\
    --name monero-lws\
    --env MONEROD_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    ghcr.io/farcaster-project/containers/monero-lws:latest

docker start monerod
docker start monero-lws

...

docker kill monerod monero-lws
docker container rm monerod monero-lws
```
