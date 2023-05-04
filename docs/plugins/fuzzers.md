To make the creation of a plugin easy, our team developed the ***Fuzzers*** library. This library provides some functions that will allow you to create a plugin with a few lines of code.

The idea of Fuzzers is to provide functions that receive a request and inject the payload on each parameter of the request and, based on the matchers specified, it will return if a vulnerability was identified or not.

!!! Tip "Remember"
    Don't be restricted just to fuzzers library. They are pretty helpful, but don't forget: you can create your own fuzzers from zero, and this is the power of nucke!

## Import

```go
import (
    "github.com/cfsdes/nucke/pkg/plugins/fuzzers"
)
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
    | Parameter     | Description       | 
    | -----------   | ------------------| 
    | `match`       | Boolean value. If true, the vulnerability was detected    | 
    | `rawReq`      | If a vulnerability was identified, it'll return the raw request used to exploit     | 
    | `url`         | Vulnerable endpoint          |
    | `payload`     | Payload that matched the rule          |
    | `param`       | Vulnerable parameter injected          |
    

```go
func Run(r *http.Request, client *http.Client, pluginDir string) (
    severity string, 
    url string, 
    summary string, 
    vulnFound bool, 
    err error
) {

    // Set payloads and match rule
    payloads := []string{"'", "1 OR 1=1"}
    
    matcher := detections.Matcher{
        Body: &detections.BodyMatcher{
            RegexList: []string{"SQL Syntax"},
        },
    }

    // Using fuzzer
    match, rawReq, url, payload, param := fuzzers.Fuzz<TYPE>(r, client, payloads, matcher)

    //...
}


```


!!! tip "Matcher"
    If you want to learn how to use matchers, access the [Detections](/plugins/detections) guide

## Fuzzers

### Fuzzing Queries

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param := fuzzers.FuzzQuery(r, client, payloads, matcher)
```

### Fuzzing FormData

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param := fuzzers.FuzzFormData(r, client, payloads, matcher)
```

### Fuzzing JSON

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param := fuzzers.FuzzJSON(r, client, payloads, matcher)
```

### Fuzzing Headers

```go
payloads := []string{"'", "1 OR 1=1"}
headers := []string{"User-Agent","Referer"}
match, rawReq, url, payload, param := fuzzers.FuzzHeaders(r, client, payloads, headers, matcher)
```

### Fuzzing XML

```go
payloads := []string{"'", "1 OR 1=1"}
match, rawReq, url, payload, param := fuzzers.FuzzXML(r, client, payloads, matcher)
```

### All fuzzers at once

```go
payloads := []string{"'", "1 OR 1=1"}

fuzzers := []func(*http.Request, *http.Client, []string, initializers.Matcher) (bool, string, string, string, string){
    fuzzers.FuzzJSON,
    fuzzers.FuzzQuery,
    fuzzers.FuzzFormData,
    fuzzers.FuzzXML,
}

for _, fuzzer := range fuzzers {
    if match, rawReq, url, payload, param := fuzzer(r, client, payloads, matcher); match {
        return match, rawReq, url, payload, param
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
