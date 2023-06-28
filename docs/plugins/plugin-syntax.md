!!! info "Plugins"
    The plugins are written using [Go](https://go.dev/) programming language.

## Specifications

A plugin code must have a `Run()` function. This function will be called by Nucke for each request received. It is the entrypoint to run the scan:

```go
func Run(r *http.Request, client *http.Client, pluginDir string) (
    severity string, 
    url string, 
    summary string, 
    vulnFound bool, 
    rawResp string,
    err error
) {
    // Scan logic here!
}
```

???+ info "Returns"

    The `Run()` function should return:
    
    | Variable    | Description                          |
    | ----------- | ------------------------------------ |
    | `severity`  | Severity of the vulnerability. Should be ***Critical***, ***High***, ***Medium***, ***Low*** or ***Info***  |
    | `url`       | Vulnerable URL found |
    | `summary`   | Report of the vulnerability |
    | `vulnFound` | Boolean value. If ***true***, Nucke will report the vulnerability |
    | `rawResp`   | Raw Response |
    | `error`     | Error exception


??? example "Example: Plugin SQL Injection"
    ```go
    package main

    import (
        "net/http"
        "github.com/cfsdes/nucke/pkg/report"
        "github.com/cfsdes/nucke/pkg/plugins/fuzzers"
        "github.com/cfsdes/nucke/pkg/plugins/detections"
    )

    // SQL Injection scan
    func Run(r *http.Request, client *http.Client, pluginDir string) (
        string, 
        string, 
        string, 
        bool, 
        string,
        error,
    ) {

        // Set payloads and match rule
        payloads := []string{"'", "1 OR 1=1"}

        // Detection Rule
        matcher := detections.Matcher{
            Body: &detections.BodyMatcher{
                RegexList: []string{"SQL Syntax"},
            },
        }

        // Using fuzzer
        vulnFound, rawReq, url, _, _, rawResp := fuzzers.FuzzQuery(r, client, payloads, matcher)

        // Creating Report
        reportContent := report.ReadFileToString("report-template.md", pluginDir)
        summary := report.ParseTemplate(reportContent, map[string]interface{}{
            "request": rawReq,
        })

        return "High", url, summary, vulnFound, rawResp, nil
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


## Directory Structure

The plugin created can be placed in any directory to use further in the `config.yaml` file.

???+ example "Example: Plugin Directory"

    ```
    ./custom-plugins/
        .. ssrf/
            .. ssrf.go
            .. report-template.md

        .. sqli/
            .. sqli.go
            .. report-template.md
    ```

???+ example "Example: `config.yaml`"
    ```yaml
    scope: ".*example\.com"
    plugins:
    - name: Injection
      path: ./custom-plugins/
      ids:
      - sqli
      - ssrf
    ```
