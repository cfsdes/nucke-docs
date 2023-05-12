!!! package
    ```go
    import "github.com/cfsdes/nucke/pkg/requests"
    ```

## Send Basic Request

```go
resTime, resBody, statusCode, resHeaders, rawResponse := requests.BasicRequest(r, client)
```

## Clone Request

Every time you need to create new fuzzers or handle requests, use the clone request module to avoid problems.

It's necessary because a request body cannot be read twice.

```go
// Request cloned to be handled
req := requests.CloneReq(r)
```

## Raw Request

### Convert request to Raw
To get the raw request of a `req *http.Request`:

```go
rawReq := requests.RequestToRaw(req)
```

### Get URL from Raw Request
To extract the raw url of the request:

```go
url := requests.ExtractRawURL(rawReq)
```

### Get Status Code form Raw Response
```go
resStatusCode := requests.StatusCodeFromRaw(rawResponse)
```

## Parse Response

Parses response type `*http.Response` and returns information:

```go
statusCode, responseBody, responseHeaders, rawResponse := requests.ParseResponse(resp)
```

## Check Authenticated Request

It's possible to check if the request requires authentication using the following function:

```go
requiresAuth := requests.CheckAuth(req, client)
```