
!!! abstract "Introduction"
    Some random useful functions :smile:

    ```go
    import "github.com/cfsdes/nucke/pkg/plugins/utils"
    ```

### File To Slice

If you want to load a list of payloads or rules (or anything else), you can use the FileToSlice function:

```go
payloads := utils.FileToSlice(pluginDir, "payloads.txt")
```

### Similarity

Module to help you check the similarity between two texts. Returns a `float64` number representing the percentage of similarity (e.g.: 0.75)

```go
text1 := "This is one example"
text2 := "This is another example"

similarity := utils.TextSimilarity(text1, text2)
```

### Replace OOB

Replace the "{{.oob}}" in the string with the interactsh url
```go
updatedPayload := utils.ReplaceOob("https://{{.oob}}")
```

### Extract OOB ID

Return the random ID appened to the OOB url:
```go
oob_id := ExtractOobID(updatedPayload)
```
