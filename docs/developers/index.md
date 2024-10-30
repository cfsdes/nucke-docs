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