# Getting Started

!!! abstract "Introduction"

    Nucke is a powerful proxy that allows users to create custom vulnerability scanning plugins. It supports various fuzzing techniques and offers effortless customization, thanks to its ability to code plugins in Go.

## Features

- [x] Simple Proxy Server
- [x] Flexible Vulnerability Scanner ([Plugins](/plugins/introduction/))
- [x] Integration with Jaeles Server API

## Requirements

1. Install interactsh:
```
go install -v github.com/projectdiscovery/interactsh/cmd/interactsh-client@latest
```

2. Install redis
https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/

## Nucke Installation

=== "Remote"

    ```bash
    go install github.com/cfsdes/nucke@latest
    ```

=== "Build From Source"

    ```bash
    git clone git@github.com:cfsdes/nucke.git
    cd nucke/
    go build
    ```

## Configuring CA certificate
Before using nucke, you must add the CA certificate in your browser to be able to intercept requests. To export the CA certificate:
```
nucke -export-ca
mv nucke-cert.crt /usr/local/share/ca-certificates/
update-ca-certificates
```