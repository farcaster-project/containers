[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# Containers

A list of Docker images built for supporting **@farcaster-project** integration tests and help bootstrapping a swap environment.

- **bitcoin-core**: [`bitcoin-core` image](./bitcoin-core/)
- **electrs**: [`electrs` image](./electrs/)
- **monerod**: [`monerod` image](./monerod/)
- **monero-wallet-rpc**: [`monero-wallet-rpc` image](./monero-wallet-rpc/)
- **monero-lws**: [`monero-lws` image](./monero-lws/)

## About

All images are built with the same aim: flexibility and ease of use in GitHub Actions.

The later support adding `services` to a `job` described in yaml format, the docker command is issued with `docker create` and it is not possible to modify the Docker `CMD` attribute. Thus images aim to be configured for CI context through `env` variable with a default `CMD`.

To be able to reuse these images in other contexts no `ENTRYPOINT` is specified. Containers can be created by changing the `CMD` argument, e.g.

```
docker run --rm ghcr.io/farcaster-project/containers/bitcoin-core:latest /usr/bin/bitcoind\
    -testnet\
    -server\
    -rest
```

## Licensing

The code in this project is licensed under the [MIT License](LICENSE)
