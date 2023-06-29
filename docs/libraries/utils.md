
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

### Check OOB Interaction

Check if the given ID received any external interaction
```go
bool ret = CheckOobInteraction(oob_id)
```