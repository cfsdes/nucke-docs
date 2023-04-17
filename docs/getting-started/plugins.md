# Creating plugins

## Plugins Standard

A plugin can be created using the sample file as a starter code.

The plugin should return:

- **severity**: Critical, High, Medium, Low or Info
- **url**: Vulnerable endpoint identified
- **summary**: Vulnerability report (it supports markdown)
- **vulnFound**: Boolean value. If true, the plugin will report the vulnerability
- **error**

The `Run()` function is the function that will be called by the Nucke:

```go
func Run(r *http.Request, w http.ResponseWriter, client *http.Client, pluginDir string) (string, string, string, bool, error)
```

After created, the plugin should be compiled using the following command:

```bash
go build -buildmode=plugin -o plugin.so plugin.go
```

## Report & Directory Structure

The report should be placed in the same directory of plugin.

Example of structure:

```
~/Desktop/nucke-plugins/
    .. sample/
        .. sample.so
        .. report-template.txt

    .. sqli/
        .. sqli.so
        .. report-template.txt
```

Configuration file:

```yaml
plugins:
  - name: Example
    path: ~/Desktop/nucke-plugins/
    ids:
      - sample
      - sqli
```

## Fuzzers

Basic Request:

```go
resTime, resBody, statusCode, resHeaders, err := utils.BasicRequest(r, w, client)
```

Fuzzing Queries:

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, err := fuzzers.FuzzQuery(r, w, client, payloads, matches)
```

Fuzzing FormData:

```go
payloads := []string{"'", "1 OR 1=1"}
_, _, err := fuzzers.FuzzFormData(r, w, client, payloads, matcher)
```

Fuzzing JSON:

```go
payloads := []string{"'", "1 OR 1=1"}
_, _, err := fuzzers.FuzzJSON(r, w, client, payloads, matcher)
```

Fuzzing Headers:

```go
payloads := []string{"'", "1 OR 1=1"}
headers := []string{"User-Agent","Referer"}
_, _, err := fuzzers.FuzzHeaders(r, w, client, payloads, headers, matcher)
```

Fuzzing XML:

```go
payloads := []string{"'", "1 OR 1=1"}
_, _, err := fuzzers.FuzzXML(r, w, client, payloads, matcher)
```

Usando matcher:

```go
matcher := utils.Matcher{
    Body: &utils.BodyMatcher{
        RegexList: []string{"SQL Syntax"},
    },
    Time: &utils.TimeMatcher{
        Operator: ">",
        Seconds:  10,
    },
    Header: &utils.HeaderMatcher{
        RegexList: []string{"Content-Type: application/json"},
    },
    OOB: true,
}
```

Extrair URL final:

```go
url := utils.ExtractRawURL(raw)
```
