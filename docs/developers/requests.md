!!! package
    ```go
    import "github.com/cfsdes/nucke/pkg/requests"
    ```

## Send Request
Reproduce the request and return a slice of responses for each request made (redirects response + final response).
```go
responses := requests.Do(r, client)
```
???- quote "Returns"
    | Parameter     | Type              | Description       | 
    | -----------   | ------------------| ------------------| 
    | `responses`     | `[]http.Response`             | Slice with all responses, including intermediate responses (3xx status code)     | 
    

## Send Basic Request

```go
resTime, resBody, statusCode, resHeaders, rawResponse := requests.BasicRequest(r, client)
```
???- quote "Returns"
    | Parameter     | Type              | Description       | 
    | -----------   | ------------------| ------------------| 
    | `resTime`     | `int`             | Response time in seconds     | 
    | `resBody`     | `string`          | Response body                | 
    | `statusCode`  | `string`          | Response Status Code   |
    | `resHeaders`  | `string`          | Response Headers           |
    | `rawResponse` | `string`          | Raw Response (Path + Headers + Body)  |
    | `err` | `error`          | Error  |
    

## Clone Request

Every time you need to create new fuzzers or handle requests, use the clone request module to avoid problems.

It's necessary because a request body cannot be read twice.

```go
// Request cloned to be handled
var req *http.Request 
req = requests.CloneReq(r)
```

## Raw Request

### Convert request to Raw
To get the raw request of a `req *http.Request` (return: `string`):

```go
rawReq := requests.RequestToRaw(req)
```

### Get URL from Raw Request
To extract the raw url of the request (return: `string`):

```go
url := requests.ExtractRawURL(rawReq)
```

### Get Status Code form Raw Response
returns: `string`
```go
resStatusCode := requests.StatusCodeFromRaw(rawResponse)
```

## Parse Response

Parses response type `*http.Response` and returns information:

```go
statusCode, responseBody, responseHeaders, rawResponse := requests.ParseResponse(resp)
```
???- quote "Returns"
    | Parameter         | Type              | Description       | 
    | -----------       | ------------------| ------------------| 
    | `statusCode`      | `string`          | Response Status Code   |
    | `responseBody`    | `string`          | Response body                | 
    | `responseHeaders` | `string`          | Response Headers           |
    | `rawResponse`     | `string`          | Raw Response (Path + Headers + Body)  |
    

## Check Authenticated Request

It's possible to check if the request requires authentication using the following function:

```go
requiresAuth := requests.CheckAuth(req, client)
```
Returns: `bool`