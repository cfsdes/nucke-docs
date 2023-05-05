
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
