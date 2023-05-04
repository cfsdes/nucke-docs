# Getting Started

!!! abstract "Introduction"

    Nucke is a powerful proxy that allows users to create custom vulnerability scanning plugins. It supports various fuzzing techniques and offers effortless customization, thanks to its ability to code plugins in Go.

## Features

- [x] Simple Proxy Server
- [x] Flexible Vulnerability Scanner ([Plugins](/plugins/introduction/))
- [x] Integration with Jaeles Server API

## Installation

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
```
nucke -export-ca
mv nucke-cert.crt /usr/local/share/ca-certificates/
update-ca-certificates
```