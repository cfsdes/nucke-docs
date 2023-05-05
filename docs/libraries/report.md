!!! package
    ```go
    import "github.com/cfsdes/nucke/pkg/report"
    ```

## Generating Report

Parse template from string:

```go
result := report.ParseTemplateFromFile("Hello {{.msg}}", map[string]interface{}{
    "msg": "World",
})
```

Parse template from file:

```go
templateString, err := report.ReadFileToString("template-report.md", pluginName)
summary := report.ParseTemplate(templateString, map[string]interface{}{
    "msg": "World",
})
```
