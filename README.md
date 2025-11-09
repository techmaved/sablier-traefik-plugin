<!-- omit in toc -->
# Traefik Sablier Plugin

[![Go Report Card](https://goreportcard.com/badge/github.com/sablierapp/sablier-traefik-plugin)](https://goreportcard.com/report/github.com/sablierapp/sablier-traefik-plugin)
[![Discord](https://img.shields.io/discord/1298488955947454464?logo=discord&logoColor=5865F2&cacheSeconds=1&link=http%3A%2F%2F)](https://discord.gg/Gpz5ha8WWx)

Start your containers on demand, shut them down automatically when there's no activity using [Traefik](https://github.com/traefik/traefik).

- [Installation](#installation)
- [Usage](#usage)
  - [Docker](#docker)
  - [Docker Swarm](#docker-swarm)
  - [Kubernetes](#kubernetes)
- [Plugin](#plugin)
- [Other Plugins](#other-plugins)
- [Community](#community)
- [Support](#support)

## Installation

1. Add this snippet in the Traefik Static configuration

<!-- x-release-please-version-start -->
```yaml
experimental:
  plugins:
    sablier:
      moduleName: "github.com/sablierapp/sablier-traefik-plugin"
      version: "v1.0.0"
```
<!-- x-release-please-version-end -->

2. Configure the plugin using the Dynamic Configuration. Example:

```yaml
http:
  middlewares:
    my-sablier:
      plugin:
        sablier:
          sablierUrl: http://sablier:10000  # The sablier URL service, must be reachable from the Traefik instance
          names: whoami,nginx               # Comma separated names of containers/services/deployments etc.
          sessionDuration: 1m               # The session duration after which containers/services/deployments instances are shutdown
          # You can only use one strategy at a time
          # To do so, only declare `dynamic` or `blocking`

          # Dynamic strategy, provides the waiting webui
          dynamic:
            displayName: My Title       # (Optional) Defaults to the middleware name
            showDetails: true           # (Optional) Set to true or false to show details specifcally for this middleware, unset to use Sablier server defaults
            theme: hacker-terminal      # (Optional) The theme to use
            refreshFrequency: 5s        # (Optional) The loading page refresh frequency

          # Blocking strategy, waits until services are up and running
          # but will not wait more than `timeout`
          # blocking: 
          #   timeout: 1m
```

## Usage

### Docker

See the [docker example](./examples/docker/) on how to use the plugin with docker.

### Docker Swarm

⚠️ Limitations

- Traefik will evict the service from its pool as soon as the service is 0/0. You must add the [`traefik.docker.lbswarm`](https://doc.traefik.io/traefik/routing/providers/docker/#traefikdockerlbswarm) label.
    ```yaml
    services:
      whoami:
        image: acouvreur/whoami:v1.10.2
        deploy:
          replicas: 0
          labels:
            - traefik.docker.lbswarm=true
    ```
- We cannot use [allowEmptyServices](https://doc.traefik.io/traefik/providers/docker/#allowemptyservices) because if you use the [blocking strategy](LINKHERE) you will receive a `503`.

### Kubernetes

- The format of the `names` section is `<KIND>_<NAMESPACE>_<NAME>_<REPLICACOUNT>` where `_` is the delimiter.
  - Thus no `_` are allowed in `<NAME>`
- `KIND` can be either `deployment` or `statefulset`

⚠️ Limitations

- Traefik will evict the service from its pool as soon as there is no endpoint available. You must use [`allowEmptyServices`](https://doc.traefik.io/traefik/providers/kubernetes-ingress/#allowemptyservices)
- Blocking Strategy is not yet supported because of how Traefik handles the pod ip.

See [Kubernetes E2E Traefik Test script](./e2e/kubernetes.sh) to see how it is reproduced

## Plugin

The plugin is available in the Traefik [Plugin Catalog](https://plugins.traefik.io/plugins/633b4658a4caa9ddeffda119/sablier)

## Other Plugins

- [sablier-caddy-plugin](https://github.com/sablierapp/sablier-caddy-plugin)
- [sablier-proxywasm-plugin](https://github.com/sablierapp/sablier-proxywasm-plugin)

## Community

Join our Discord server to discuss and get support!

[![Discord](https://img.shields.io/discord/1298488955947454464?logo=discord&logoColor=5865F2&cacheSeconds=1&link=http%3A%2F%2F)](https://discord.gg/Gpz5ha8WWx)

## Support

See [SUPPORT.md](SUPPORT.md)