Once a vulnerability is detected, nucke will create an automatic markdown report for the issue (if the option `-out` is specified).

However, this report is 100% customizable. If you want to create your own report template to be used by a specific plugin, you can follow the steps below.

## Creating a report template

To create a report template, you can simply create a new file in the same directory as the plugin, named `report-template.md` and add any information there. 

The report template support dynamic variables. A sample of a report template is shown below:

```markdown
## Issue Details
					
- **Scan Name**: {{.scanName}}
- **Severity**: {{.severity}}
- **Vulnerable Endpoint**: {{.url}}
- **Injection Point**: {{.param}}

## Proof of Concept

The payload below was used on **{{.param}}** to trigger the vulnerability:
` + "```" + `
{{.payload}}
` + "```" + `

### HTTP Request
Request:
` + "```http" + `
{{.request}}
` + "```" + `
					
Response:
` + "```http" + `
{{.response}}
```

## Custom variables

| Variable    | Description                          |
| ----------- | ------------------------------------ |
| `{{.scanName}}` | Return the name of the plugin |
| `{{.severity}}`  | Severity for the vulnerability |
| `{{.url}}`       | Vulnerable URL found |
| `{{.payload}}`   | Payload used to trigger the vuln |
| `{{.param}}`   | Affected parameter / Injection point |
| `{{.request}}`   | Raw Request |
| `{{.response}}`   | Raw Response |