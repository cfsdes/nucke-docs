## Fuzzer Syntax


!!! note "Note"
    If you are a developer and want to create your own fuzzer that uses the ***detections*** library, you can use this guide.

    Use the **FuzzQuery** module as a reference to write fuzzers.


The ***detections*** use channels to work with asynchronous detection. It's necessary to don't overload the machine and at the same time detect complex scenarios, like OOB interactions.

To start using them, you can import the package and create a result channel variable that will store all detections result:
```go
import (
    "github.com/cfsdes/nucke/pkg/plugins/detections"
    "github.com/cfsdes/nucke/pkg/requests"
    "github.com/cfsdes/nucke/pkg/globals"
    "github.com/cfsdes/nucke/internal/parsers"
    "github.com/cfsdes/nucke/pkg/plugins/utils"
)

// Result channel
resultChan := make(chan detections.Result)


// Fuzz Function
func FuzzXYZ(r *http.Request, client *http.Client, payloads []string, matcher detections.Matcher) (bool, string, string, string, string, string) {

    // Clone Request
    req := requests.CloneReq(r)

    // Array containing failed scan logs
    var logScans []detections.Result

    // ...
    
    // For loop to inject payloads
    for key, _ := range params {
            for _, payload := range payloads {
                
                // Update payloads {{.params}}
                payload = parsers.ParsePayload(payload)

                // ...
                
                // Get response time
                elapsed := int(time.Since(start).Seconds())

                // Extract OOB ID
                oobID := utils.ExtractOobID(payload)

                // Detection:
                go detections.MatchCheck(matcher, resp, elapsed, oobID, rawReq, payload, key, resultChan)

        }
    }

    // Wait for any goroutine to send a result to the channel
    for i := 0; i < len(params)*len(payloads); i++ {
        res := <-resultChan
        if res.Found {
            return true, res.RawReq, res.URL, res.Payload, res.Param, res.RawResp, nil
        } else {
            log := detections.Result{
                Found: false,
                RawReq: res.RawReq,
                URL: res.URL,
                Payload: res.Payload,
                Param: res.Param,
                RawResp: res.RawResp,
            }
            logScans = append(logScans, log)
        }
    }

    return false, "", "", "", "", "", logScans
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
oobID := utils.ExtractOobID(payload)
```

### Convert request to Raw
```go
rawReq := requests.RequestToRaw(reqCopy)
```

## Error Handling

For every error that you want to return, just return if the Debug option is enabled:

```go
import (
    "github.com/cfsdes/nucke/internal/globals"
    "github.com/fatih/color"
)

if globals.Debug {
    // Print error
    Red := color.New(color.FgBlue, color.Bold).SprintFunc()
    fmt.Printf("[%s] Error message here\n", Red("ERR"))
    
    // Print Debug
    Blue := color.New(color.FgBlue, color.Bold).SprintFunc()
    fmt.Printf("[%s] Debug message here\n", Blue("DEBUG"))
}
```


## Profiler Debug Server

If you start nucke with the `-stats` option, the profiler debug endpoint will be also available on `http://localhost:8899/debug/pprof/`

You can analyze:
    - The `/debug/pprof/heap` to get information about memory alocation
    - The `/debug/pprof/goroutine` to get information about go routines running
