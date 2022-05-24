# [`monero-lws`](https://github.com/TheCharlatan/monero-lws) image

> We currently use a modified version of [vtnerd/monero-lws](https://github.com/vtnerd/monero-lws)

Build monero-lws image with

```
docker build -t monero-lws:latest .
```

Create a container with exposed ports for RPC connections (you need a `monerod` container created to link with)

```
docker create -p 8443:8443\
    --name monero-lws\
    --env MONERO_DAEMON_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    monero-lws:latest
```

Available environment variables:

- **NETWORK**: the network to run on, currently either **main**, **stage** or **test**, use main for regtest
- **MONERO_DAEMON_ADDRESS**: the address of the monero daemon to use in format `{host}:{port}`

Available listening container ports:

- **lws rpc**: the port the lws server is listening on for RPC connections, **8443**

Incoming connections are accepted on `http://0.0.0.0:8443`, if you want to expose the port outside the container use `-p [hostPort]:8443`.

## Standalone usage with [containers/monerod](https://github.com/farcaster-project/containers/tree/main/monerod) image

```
docker pull ghcr.io/farcaster-project/containers/monerod:0.17.3.2
docker pull ghcr.io/farcaster-project/containers/monero-lws:latest

docker create -p 18080:18080 -p 18081:18081 -p 18082:18082\
    --name monerod\
    --env NETWORK=regtest\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:0.17.3.2

docker create -p 8443:8443\
    --name monero-lws\
    --env MONERO_DAEMON_ADDRESS=monerod:18082\
    --env NETWORK=main\
    --link monerod\
    ghcr.io/farcaster-project/containers/monero-lws:latest

docker start monerod
docker start monero-lws

docker kill monerod monero-lws
docker container rm monerod monero-lws
```
