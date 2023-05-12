## Fuzzer Syntax

```go
import (
    "github.com/cfsdes/nucke/pkg/requests"
    "github.com/cfsdes/nucke/pkg/plugins/detections"
    "github.com/cfsdes/nucke/internal/parsers"
)

// Fuzz Function
func FuzzXYZ(r *http.Request, client *http.Client, payloads []string, matcher detections.Matcher) (bool, string, string, string, string, string) {

    // Clone request
    req := requests.CloneReq(r)
    
    // For loop to inject payloads
    for key, _ := range params {
            for _, payload := range payloads {
                
                // Update payloads {{.params}}
                payload = parsers.ParsePayload(payload)

                // Fuzz logic ...
        }
    }

}
```

## Detections

!!! note "Note"
    If you are a developer and want to create your own fuzzer that uses the ***detections*** library, you can use this guide.

The ***detections*** use channels to work with asynchronous detection. It's necessary to don't overload the machine and at the same time detect complex scenarios, like OOB interactions.

To start using them, you can import the package and create a result channel variable that will store all detections result:
```go
import (
    "github.com/cfsdes/nucke/pkg/plugins/detections"
)

// Result channel
resultChan := make(chan detections.Result)


// Fuzz Function
func FuzzXYZ(...) (bool, string, string, string, string, string) {

    // ...
    
    // For loop to inject payloads
    for key, _ := range params {
            for _, payload := range payloads {
                
                // Update payloads {{.params}}
                payload = parsers.ParsePayload(payload)

                // ...
                
                // Detection:
                go detections.MatchCheck(matcher, resp, elapsed, oobID, rawReq, payload, key, resultChan)

        }
    }

    // Wait for any goroutine to send a result to the channel
    for i := 0; i < len(params)*len(payloads); i++ {
        res := <-resultChan
        if res.Found {
            return true, res.RawReq, res.URL, res.Payload, res.Param, res.RawResp
        }
    }
}
```

???+ tip "detections.MatchCheck( )"
    The `MatchCheck()` function expect to receive the following arguments:
    
    | Parameter     | Type              | Description |
    | -----------   | ------------------| ------------|
    | `matcher`           | `detections.Matcher`    | matcher variable with detection rules
    | `resp`      | `*http.Response`     | Response of the request made during scan
    | `elapsed`    | `int`          | Response time
    | `oobID`     | `string`          | ID used in the oob url of interactsh.
    | `rawReq`     | `string`          | Raw request made during scan
    | `payload`     | `string`          | Payload used
    | `key`     | `string`          | Parameter injected
    | `resultChan`     | `chan detections.Result`          | Result channel created in the begining of the code

### Get oobID
```go
oobID := initializers.ExtractOobID(payload)
```

### Convert request to Raw
```go
rawReq := requests.RequestToRaw(reqCopy)
```

## Error Handling

For every error that you want to return, just return if the Debug option is enabled:

```go
import "github.com/cfsdes/nucke/internal/initializers"

if initializers.Debug {
    // Print error
}
```