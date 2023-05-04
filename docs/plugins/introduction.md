Plugins are the core of the Nucke. They are responsible for every scan that we can perform with the tool. For example, if you want to run an SQL injection scan on an application, you will need to use an SQL injection plugin for that.

???+ info "Proposal"

    The proposal of Nucke is to be a completely flexible tool, allowing researchers to develop plugins to identify dynamic vulnerabilities of high complexity.

    The tool is not intended to be a CVE scanner. For that, we already have [Nuclei](https://github.com/projectdiscovery/nuclei), which fulfills this goal very well ;)

## Using a Plugin

To use a plugin, we need to create a configuration file and specify the directory where the plugin is located. By doing this, for each request received by Nucke, the plugin (scan) will be executed on the request.

!!! example

      Below is an example of the configuration file: `config.yaml`

      ```yaml
      plugins:
        - name: Example 1
          path: ~/Desktop/plugins/
          ids:
            - "*" # It will load all plugins
          exclude:
            - xss-blind # Exclude specific plugins

        - name: Example 2
          path: gitub.com/<user>/<repo>/<plugins-path>
          ids:
            - sql-injection
            - ssrf
      ```

      > Remember: The plugin id should be the name of the plugin. E.g.: sample.go => sample

      To load the config file, just run nucke using `-config` argument:

      ```
      nucke -config config.yaml
      ```
