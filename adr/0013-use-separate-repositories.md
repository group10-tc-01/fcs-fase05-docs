# Usar repositorios separados por aplicacao

Cada aplicacao da fase 5 tera seu proprio repositorio: `fcs-identity`, `fcs-campaigns`, `fcs-donations`, `fcs-donation-worker`, `fcs-audit-logs` e `fcs-solidarity-web`. Alem deles, havera o repositorio `fcs-solidarity-infra` para infraestrutura compartilhada, ambiente integrado e provisionamento em Azure.

**Opcoes consideradas**

- Manter todas as aplicacoes em um monorepo.
- Criar um repositorio por aplicacao.

**Consequencias**

- Cada repositorio tera seu proprio pipeline de CI/CD.
- A documentacao de entrega deve listar todos os repositorios.
- Cada aplicacao mantem seu proprio `Dockerfile`, `docker-compose` local e manifests Kubernetes especificos do servico.
- O `fcs-solidarity-infra` concentra os manifests Kubernetes integrados, docker compose do ambiente completo, dashboards/configuracoes de observabilidade, configuracoes de Keycloak/Kafka e Terraform para provisionar recursos na Azure.
- Segredos e credenciais da Azure nao devem ser hardcoded; devem ser parametrizados e, quando aplicavel, armazenados em Key Vault.
