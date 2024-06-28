## Fuzzer Syntax


!!! note "Note"
    If you are a developer and want to create your own fuzzer that uses the ***detections*** library, you can use this guide.

    Use the **FuzzQuery** module as a reference to write fuzzers.


The ***detections*** use channels to work with asynchronous detection. It's necessary to don't overload the machine and at the same time detect complex scenarios, like OOB interactions.

To start using them, you can import the package and create a result channel variable that will store all detections result:
```go
package fuzzers

import (
	"bytes"
	"fmt"
	"io/ioutil"
	"net/http"
	"net/url"
	"strings"
	"time"

	"github.com/cfsdes/nucke/internal/parsers"
	"github.com/cfsdes/nucke/pkg/globals"
	"github.com/cfsdes/nucke/pkg/plugins/detections"
	"github.com/cfsdes/nucke/pkg/plugins/utils"
	"github.com/cfsdes/nucke/pkg/requests"
)

func FuzzQuery(r *http.Request, client *http.Client, pluginDir string, payloads []string, matcher detections.Matcher) (bool, string, string, string, string, string, []detections.Result) {
	req := requests.CloneReq(r)

	// Extract parameters from URL
	params := req.URL.Query()

	// Result channel
	resultChan := make(chan detections.Result)

	// Counter of channels opened
	var channelsOpened int

	// Array com os resultados de cada teste executado falho
	var logScans []detections.Result

	// Get request body, if method is POST
	var body []byte
	var err error
	body, err = ioutil.ReadAll(req.Body)
	if err != nil {
		// handle error
		if globals.Debug {
			fmt.Println("fuzzQuery:", err)
		}
		return false, "", "", "", "", "", nil
	}

	// For each parameter, send a new request with the parameter replaced by a payload
	for key, _ := range params {
		// Create a new query string with the parameter replaced by a payload
		for _, payload := range payloads {

			// Delay between requests
			time.Sleep(time.Duration(globals.Delay) * time.Millisecond)

			// Update payloads {{.params}}
			payload = parsers.ParsePayload(payload)

			newParams := make(url.Values)
			for k, v := range params {
				if k == key {
					payload = strings.Replace(payload, "{{.original}}", v[0], -1)
					newParams.Set(k, payload)
				} else {
					newParams.Set(k, v[0])
				}
			}

			// Copy Request
			reqCopy := requests.CloneReq(req)
			reqCopy.URL.RawQuery = newParams.Encode()

			// Add request body
			reqCopy.Body = ioutil.NopCloser(bytes.NewReader(body))

			// Get raw request
			rawReq := requests.RequestToRaw(reqCopy)

			// Send request
			start := time.Now()
			responses := requests.Do(reqCopy, client)

			// Get response time
			elapsed := int(time.Since(start).Seconds())

			// Extract OOB ID
			oobID := utils.ExtractOobID(payload)

			// Check if match vulnerability
			for _, resp := range responses {
				channelsOpened++
				go detections.MatchCheck(pluginDir, matcher, resp, elapsed, oobID, rawReq, payload, key, resultChan)
			}
		}
	}

	// Wait for any goroutine to send a result to the channel
	for i := 0; i < channelsOpened; i++ {
		res := <-resultChan
		log := detections.Result{
			Found:   res.Found,
			URL:     res.URL,
			Payload: res.Payload,
			Param:   res.Param,
			RawReq:  res.RawReq,
			RawResp: res.RawResp,
			ResBody: res.ResBody,
		}
		logScans = append(logScans, log)
	}

	for _, res := range logScans {
		if res.Found {
			return true, res.URL, res.Payload, res.Param, res.RawReq, res.RawResp, logScans
		}
	}

	return false, "", "", "", "", "", logScans
}

```

???+ tip "detections.MatchCheck( )"
    The `MatchCheck()` function expect to receive the following arguments:
    
    | Parameter     | Type              | Description |
    | -----------   | ------------------| ------------|
    | `pluginDir`           | `string`    | Plugin directory
    | `matcher`           | `detections.Matcher`    | Matcher variable with detection rules
    | `resp`      | `*http.Response`     | Response of the request made during scan
    | `elapsed`    | `int`          | Response time
    | `oobID`     | `string`          | ID used in the oob url of interactsh.
    | `rawReq`     | `string`          | Raw request made during scan
    | `payload`     | `string`          | Payload used
    | `key`     | `string`          | Parameter injected
    | `resultChan`     | `chan detections.Result`          | Result channel created in the begining of the code

## Error Handling

For every error that you want to return, just return if the Debug option is enabled:

```go
import (
    "github.com/cfsdes/nucke/pkg/globals"
    "github.com/fatih/color"
)

if globals.Debug {
    // Print error
    Red := color.New(color.FgRed, color.Bold).SprintFunc()
    fmt.Printf("[%s] Error message here\n", Red("ERR"))
    
    // Print Debug
    Blue := color.New(color.FgBlue, color.Bold).SprintFunc()
    fmt.Printf("[%s] Debug message here\n", Blue("DEBUG"))
}
```


## Profiler Debug Server

If you start nucke with the `-stats` option, the profiler debug endpoint will be also available on `http://localhost:8899/debug/pprof/`

You can analyze:

-  The `/debug/pprof/heap` to get information about memory alocation
-  The `/debug/pprof/goroutine` to get information about go routines running


## Request delay

Every time you make a manual request, be sure to add the following line before the request, to make the delay between requests work properly:
```go
time.Sleep(time.Duration(globals.Delay) * time.Millisecond)
```