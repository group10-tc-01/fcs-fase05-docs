# Usar Azure API Management como borda publica

A plataforma usara Azure API Management (APIM) como borda publica das APIs no ambiente Azure. O APIM publicara as rotas externas da aplicacao e encaminhara as chamadas para os servicos hospedados no AKS, enquanto endpoints internos entre servicos permanecem privados no cluster.

**Opcoes consideradas**

- Expor APIs publicas diretamente por Ingress no AKS.
- Usar API Gateway dedicado dentro do cluster.
- Usar Azure API Management como camada publica de API.

**Consequencias**

- O Terraform do `fcs-solidarity-infra` deve provisionar o APIM.
- O APIM deve expor apenas rotas publicas, como identidade, campanhas publicas/administrativas e intencoes de doacao.
- Rotas `/internal/*` continuam fora da superficie publica.
- O AKS ainda precisa de entrada tecnica para receber trafego do APIM, mas o cliente externo deve consumir as APIs pelo APIM.
- O APIM centraliza a exposicao publica e aplica rate limit.
- Validacao de JWT e autorizacao RBAC permanecem dentro das APIs.
- O APIM pode expor todas as rotas de negocio das APIs, incluindo rotas administrativas protegidas por RBAC.
- Rotas `/internal/*`, `/metrics` e `/health` nao devem ser publicadas no APIM.
