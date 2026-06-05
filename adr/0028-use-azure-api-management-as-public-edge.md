# Usar Azure API Management como borda publica

A plataforma usara Azure API Management (APIM) como borda publica no ambiente Azure. O APIM publicara o `fcs-bff` como fachada principal do frontend e encaminhara chamadas para servicos hospedados no AKS, enquanto endpoints internos entre servicos permanecem privados no cluster.

**Opcoes consideradas**

- Expor APIs publicas diretamente por Ingress no AKS.
- Usar API Gateway dedicado dentro do cluster.
- Usar Azure API Management como camada publica de API.

**Consequencias**

- O Terraform do `fcs-infra` deve provisionar o APIM.
- O APIM deve expor o `fcs-bff` para o `fcs-web` e apenas rotas publicas necessarias, como identidade, campanhas publicas/administrativas e intencoes de doacao.
- Rotas `/internal/*` continuam fora da superficie publica.
- O AKS ainda precisa de entrada tecnica para receber trafego do APIM, mas o cliente externo deve consumir as APIs pelo APIM.
- O APIM centraliza a exposicao publica e aplica rate limit.
- Validacao de JWT e autorizacao RBAC permanecem dentro das APIs.
- O APIM pode expor rotas de negocio das APIs quando necessario, mas o fluxo principal do frontend passa pelo `fcs-bff`.
- Rotas `/internal/*`, `/metrics` e `/health` nao devem ser publicadas no APIM.
