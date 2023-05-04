!!! abstract "Introduction"
    The ***Detections*** library is the one used to detect if a vulnerability was detected during the [Fuzzers](/plugins/fuzzers) scan.

## Import

```go
import (
    "github.com/cfsdes/nucke/pkg/plugins/detections"
)
```

## Usage

The detections accepts multiple matchers. You can use just one or combine multiple matchers to detect vulnerabilities.

The matcher variable is passed later as a Fuzzer argument.

```go
matcher := detections.Matcher{
    Body: &detections.BodyMatcher{
        RegexList: []string{"SQL Syntax"},
    },
    Time: &detections.TimeMatcher{
        Operator: ">", // Supports: "==", "!=", ">", "<", ">=", "<="
        Seconds:  10,
    },
    Header: &detections.HeaderMatcher{
        RegexList: []string{"Content-Type: application/json"},
    },
    StatusCode: &detections.StatusCodeMatcher{
        Operator: "==", // Supports: "==", "!=", ">", "<", ">=", "<="
        Code: 200,
    },
    ContentLength: &detections.ContentLengthMatcher{
        Operator: ">",
        Length: 1000,
    },
    OOB: true,
    Operator: "AND", //Supports OR / AND operator
}
```

| Matcher     | Description              | 
| -----------   | ------------------| 
| `Body`           | Check if the response body matches any regex of `RegexList`    | 
| `Time`      | Compare the response time with the `Seconds` value using the `Operator`    | 
| `Header`    | Check if header matches any regex of `RegexList`         | 
| `StatusCode`     | Compare the response status code with the `Code` value using the `Operator`           | 
| `ContentLength`     | Compare the response length with the `Length` value using the `Operator`           | 
| `OOB`     | Check if the parameter with `{{.oob}}` received any interaction| 
| `Operator`     | Logical operator to use when multiple matchers are used | 
