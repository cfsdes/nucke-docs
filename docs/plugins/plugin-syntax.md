!!! info "Plugins"
    The plugins are written using [Go](https://go.dev/) programming language.

## Set up Environment

### Install dependencies
Before start creating the plugins, you must set up the environment.

Let's first create the plugins' directory and install all required dependencies:

```bash
mkdir nucke-plugins && cd nucke-plugins
go mod init nucke-plugins
go get github.com/cfsdes/nucke@latest
```

### Directory Structure

The plugins created must have the following structure:

???+ example "Example: Plugin Directory"

    ```
    nucke-plugins/
    ├── go.mod
    ├── config.yaml
    ├── ssrf/
    │   ├── ssrf.go
    │   └── report-template.md
    └── sqli/
        ├── sqli.go
        └── report-template.md
    ```

### **Updating Nucke**

If nucke version is different than the current version of go.mod, you must update it manually running the following commands:
```
go get github.com/cfsdes/nucke@latest
go mod tidy
```

## Specifications

A plugin code must have a `Run()` function. This function will be called by Nucke for each request received. It is the entrypoint to run the scan:

```go
func Run(r *http.Request, client *http.Client, pluginDir string) (
    found bool, 
    severity, 
    url, 
    payload, 
    param, 
    rawReq, 
    rawResp string,
) {
    // Scan logic here!
}
```

???+ info "Returns"

    The `Run()` function should return:
    
    | Variable    | Description                          |
    | ----------- | ------------------------------------ |
    | `found` | Boolean value. If ***true***, Nucke will report the vulnerability |
    | `severity`  | Severity of the vulnerability. Should be ***Critical***, ***High***, ***Medium***, ***Low*** or ***Info***  |
    | `url`       | Vulnerable URL found |
    | `payload`   | Payload used to trigger the vuln |
    | `param`   | Affected parameter / Injection point |
    | `rawReq`   | Raw Request |
    | `rawResp`   | Raw Response |


??? example "Example: Plugin SQL Injection"
    ```go
    package main

    import (
        "net/http"
        "github.com/cfsdes/nucke/pkg/plugins/utils"
        "github.com/cfsdes/nucke/pkg/plugins/fuzzers"
        "github.com/cfsdes/nucke/pkg/plugins/detections"
    )


    func Run(r *http.Request, client *http.Client, pluginDir string) (found bool, severity, url, payload, param, rawReq, rawResp string) {
        severity = "High"
        
        // Read rules file
        rules := utils.FileToSlice(pluginDir, "regex_match.txt")

        // Creating payload and matcher
        payloads := []string{"{{.original}}'", "{{.original}}\\"}
        matcher := detections.Matcher{
            Body: &detections.BodyMatcher{
                RegexList: rules,
            },
        }

        // Running All Fuzzers
        found, url, payload, param, rawReq, rawResp, _ = fuzzers.FuzzAll(r, client, payloads, matcher)
        
        return
    }
    ```

## Plugin Workflow

Below is a representation of the plugin workflow and the phases that Nucke interacts with it. 
``` mermaid
sequenceDiagram
  autonumber
  Client->>Nucke: Send Request
  par
    Nucke->>Plugin: Foward Request to Plugin
    Nucke->>Client: Return Response
  end

  loop
      Plugin->>Plugin: Run Vulnerability Scan
  end
  Plugin->>Nucke: Scan Result
  loop
    Note right of Nucke: vulnerability found?
    Nucke->>Nucke: Print Result
  end
```



