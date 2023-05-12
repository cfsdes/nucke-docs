# TODOs

- [ ] Create server option to load the Markdown reports
- [x] Criar opção para add parâmetros nos fuzzers
- [x] Não retornar vulns que tenham status code 429
- [ ] Add flag -x para excluir extensões. e.g: -x jpg,js,png,woff,woff2
- [x] Ajustar o parse payloads para fazer parsing de apenas um payload, para resolver o problema do SSRF
- [x] Resolver problema de concorrência dos "Pending Scans"
- [x] Ajustar o interactsh para a cada hora ele ser reiniciado
- [ ] Add FuzzCookie()
- [ ] Add FuzzPath() - Deve ser capaz de detectar os parametros na URL para injetar, por exemplo quando tiver um numero no PATH: /user/123/info
