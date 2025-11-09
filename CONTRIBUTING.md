# Contributing Guide

Thank you for your interest in contributing to this project! 🎉

To contribute, please follow these steps:

1. Fork the repository and create a new branch for your changes.
2. Make your changes and ensure they are well-tested.
3. Submit a pull request with a clear description of your changes.

We appreciate your contributions and look forward to reviewing your pull request!

Thank you for helping improve this project! 🙌

## Development Setup

### Prerequisites

- Go (version specified in `go.mod`)
- golangci-lint for linting and formatting

### Installing golangci-lint

Install golangci-lint by following the [official installation guide](https://golangci-lint.run/docs/welcome/install/#local-installation)

## Development

You can use this to load the plugin.

```yaml
services:
  traefik:
    image: traefik:3.6.0
    command:
      - --experimental.localPlugins.sablier.moduleName=github.com/sablierapp/sablier-traefik-plugin
      - --entryPoints.http.address=:80
      - --providers.docker=true
    ports:
      - "8080:80"
    volumes:
      - '/var/run/docker.sock:/var/run/docker.sock'
      - '../..:/plugins-local/src/github.com/sablierapp/sablier-traefik-plugin'
      - './dynamic-config.yml:/etc/traefik/dynamic-config.yml'
```

Check out https://plugins.traefik.io/create