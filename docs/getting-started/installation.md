# Getting Started

Nucke is a proxy server that provides flexibility to create your own scan templates.

## Installation

```
go install github.com/cfsdes/nucke@latest
nucke -help
```

## Config

> Remember: The plugin id should be the name of the plugin. E.g.: sample.so => sample

```yaml
plugins:
  - name: Example 1
    path: ~/Desktop/plugins/
    ids:
      - "*" # It will load all plugins
    exclude:
      - xss-blind # Exclude specific plugins

  - name: Example 2
    path: gitub.com/<user>/<repo>/<plugins-path>
    ids:
      - sql-injection
      - ssrf
```

## Plugins

Access examples/ folder to understand how to create a plugin

## TODO

- Add concurrency scans
- Create server option to load the Markdown reports
- Criar fuzzers: fuzzGraphQL()
- Documentar utilização dos fuzzers
- Criar documentação mais bonita ao invés de READMEs
