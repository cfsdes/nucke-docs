To make the creation of a plugin easy, our team developed the ***Fuzzers*** library. This library provides some functions that will allow you to create a plugin with a few lines of code.

The idea of Fuzzers is to provide functions that receive a request and inject the payload on each parameter of the request and, based on the matchers specified, it will return if a vulnerability was identified or not.

!!! Tip "Remember"
    Don't be restricted just to fuzzers library. They are pretty helpful, but don't forget: you can create your own fuzzers from zero, and this is the power of nucke!

!!! Package

    ```go
    import "github.com/cfsdes/nucke/pkg/plugins/fuzzers"
    import "github.com/cfsdes/nucke/pkg/plugins/detections"
    ```

## Example Usage

Below is an example code of the usage of fuzzers:

???- quote "Fuzzers Arguments Expected"
    | Parameter     | Type              | Description |
    | -----------   | ------------------| ------------|
    | `r`           | `*http.Request`    | Request received by `Run()` func
    | `client`      | `*http.Client`     | Client received by `Run()` func
    | `payloads`    | `[]string`          | List of payloads to inject in the parameters
    | `matcher`     | `[]string`          | Match rule used to identify the vulnerable behavior

???- quote "Fuzzers Returns"
    | Parameter     | Type              | Description       | 
    | -----------   | ------------------| ------------------| 
    | `found`       | `bool`            | Boolean value. If true, the vulnerability was detected    | 
    | `url`         | `string`          | Vulnerable endpoint          |
    | `payload`     | `string`          | Payload that matched the rule          |
    | `param`       | `string`          | Vulnerable parameter injected          |
    | `rawReq`      | `string`          | Raw Request     | 
    | `rawResp`     | `string`          | Raw/Full Response        |
    | `logsScan`    | `[]detections.Result` | If the scan doesn't return success, it will return an array containing all tests executed
    
    > The `logsScan` array contains the following properties:
    
        - Found
        - URL
        - Payload
        - Param
        - RawReq
        - RawResp
        - ResBody

```go
// Set payloads and match rule
payloads := []string{"'", "1 OR 1=1"}

matcher := detections.Matcher{
    Body: &detections.BodyMatcher{
        RegexList: []string{"SQL Syntax"},
    },
}

// Using fuzzer
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.Fuzz<TYPE>(r, client, pluginDir, payloads, matcher)
```


!!! tip "Matcher"
    If you want to learn how to use matchers, access the [Detections](/plugins/detections) guide

## Fuzzers

### Fuzzing Queries

```go
payloads := []string{"'", "1 OR 1=1"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzQuery(r, client, pluginDir, payloads, matcher)
```

### Fuzzing FormData

```go
payloads := []string{"'", "1 OR 1=1"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzFormData(r, client, pluginDir, payloads, matcher)
```

### Fuzzing JSON

```go
payloads := []string{"'", "1 OR 1=1"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzJSON(r, client, pluginDir, payloads, matcher)
```

### Fuzzing XML

```go
payloads := []string{"'", "1 OR 1=1"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzXML(r, client, pluginDir, payloads, matcher)
```

### All fuzzers at once

```go
payloads := []string{"'", "1 OR 1=1"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzAll(r, client, pluginDir, payloads, matcher)
```

## Special Fuzzers
### Fuzzing Headers

```go
payloads := []string{"'", "1 OR 1=1"}
headers := []string{"User-Agent","Referer"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzHeaders(r, client, pluginDir, payloads, headers, matcher, "all")
```
> The last argument can be "all" or "". If "all", the payload will be added to all headers at once and sent in a single request.

### Fuzzing Path

```go
payloads := []string{"'", "1 OR 1=1"}
found, url, payload, param, rawReq, rawResp, logsScan := fuzzers.FuzzPath(r, client, pluginDir, payloads, matcher, "last")
```
> The last argument can be "last" or "*". If last, only the last path will be fuzzed, else all paths will be fuzzed.




## Built-in parameters

You can add some special values to your payload and nucke will replace them automatically:

- `{{.oob}}`: replace with oob interaction url (used during the OOB matcher)
- `{{.original}}`: replace with original value of the parameter

!!! example "Example"
    ```go
    payloads := []string{
        "https://{{.oob}}",
        "{{.original}}' -- -",
    }
    ```

???+ tip "Add custom parameters"
    You can also add custom parameters to the fuzzers and specify its value using the "***-p***" option:

    ``{{.replaceme}}``

    ```
    nucke -p "replaceme=test@123"
    ```