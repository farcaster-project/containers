# [`monero-lws`](https://github.com/TheCharlatan/monero-lws) image

> We currently use a modified version of [vtnerd/monero-lws](https://github.com/vtnerd/monero-lws)

Build monero-lws image with

```
docker build -t monero-lws:latest .
```

Create a container with (you need a `monerod` container created to link)

```
docker create -p 38884:38884\
    --name monero-lws\
    --env MONEROD_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    monero-lws:latest
```

Available environment variables:

- **MONEROD_ADDRESS**: the address of the monero daemon to use
- **NETWORK**: the network to run on, currently either **main**, **stage** or **test**, use main for regtest

Incoming connections are accepted on `http://0.0.0.0:8443`, if you want to expose the port outside the container use `-p [hostPort]:8443`.

## Standalone usage with [containers/monerod](https://github.com/farcaster-project/containers/tree/main/monerod) image

```
docker pull ghcr.io/farcaster-project/containers/monerod:0.17.3.2
docker pull ghcr.io/farcaster-project/containers/monero-lws:latest

docker create -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env MONEROD_ZMQ_PORT=18082\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:0.17.3.2

docker create -p 38884:8443\
    --name monero-lws\
    --env MONEROD_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    ghcr.io/farcaster-project/containers/monero-lws:latest

docker start monerod
docker start monero-lws

docker kill monerod monero-lws
docker container rm monerod monero-lws
```
