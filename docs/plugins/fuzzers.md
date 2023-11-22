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
    | `match`       | `bool`            | Boolean value. If true, the vulnerability was detected    | 
    | `rawReq`      | `string`          | If a vulnerability was identified, it'll return the raw request used to exploit     | 
    | `url`         | `string`          | Vulnerable endpoint          |
    | `payload`     | `string`          | Payload that matched the rule          |
    | `param`       | `string`          | Vulnerable parameter injected          |
    | `rawResp`     | `string`          | Raw/Full Response        |
    | `logsScan`    | `[]detections.Result` | If the scan doesn't return success, it will return an array containing all tests executed
    
    > The `logsScan` array contains the following properties:
    
        - Found
        - RawReq
        - URL
        - Payload
        - Param
        - RawResp
        - ResBody

```go
func scan(r *http.Request, client *http.Client, pluginDir string) (
    vulnFound bool, 
    rawReq string, 
    url string, 
    rawResp string
) {

    // Set payloads and match rule
    payloads := []string{"'", "1 OR 1=1"}
    
    matcher := detections.Matcher{
        Body: &detections.BodyMatcher{
            RegexList: []string{"SQL Syntax"},
        },
    }

    // Using fuzzer
    match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.Fuzz<TYPE>(r, client, payloads, matcher)

    return
}
```


!!! tip "Matcher"
    If you want to learn how to use matchers, access the [Detections](/plugins/detections) guide

## Fuzzers

### Fuzzing Queries

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.FuzzQuery(r, client, payloads, matcher)
```

### Fuzzing FormData

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.FuzzFormData(r, client, payloads, matcher)
```

### Fuzzing JSON

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.FuzzJSON(r, client, payloads, matcher)
```

### Fuzzing XML

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.FuzzXML(r, client, payloads, matcher)
```

### Fuzzing Headers

```go
payloads := []string{"'", "1 OR 1=1"}
headers := []string{"User-Agent","Referer"}
match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.FuzzHeaders(r, client, payloads, headers, matcher, "all")
```
> The last argument can be "all" or "". If "all", the payload will be added to all headers at once and sent in a single request.

### Fuzzing Path

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param, rawResp, logsScan := fuzzers.FuzzPath(r, client, payloads, matcher, "last")
```
> The last argument can be "last" or "*". If last, only the last path will be fuzzed, else all paths will be fuzzed.


### All fuzzers at once

```go
payloads := []string{"'", "1 OR 1=1"}

allfuzz := []func(*http.Request, *http.Client, []string, detections.Matcher) (bool, string, string, string, string, string, []detections.Result){
    fuzzers.FuzzJSON,
    fuzzers.FuzzQuery,
    fuzzers.FuzzFormData,
    fuzzers.FuzzXML,
}

for _, fuzzer := range allfuzz {
    if match, rawReq, url, payload, param, rawResp, logsScan := fuzzer(r, client, payloads, matcher); match {
        return match, rawReq, url, payload, param, rawResp
    }
}
```

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