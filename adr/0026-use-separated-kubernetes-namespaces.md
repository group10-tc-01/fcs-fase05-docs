# Usar namespaces Kubernetes separados

As aplicacoes terao namespaces Kubernetes separados por servico, enquanto componentes compartilhados de infraestrutura rodarao no namespace `fcs-infra`. Essa organizacao preserva isolamento operacional entre aplicacoes e concentra plataforma compartilhada em um unico namespace.

**Consequencias**

- `fcs-identity` roda no namespace `fcs-identity`.
- `fcs-campaigns` roda no namespace `fcs-campaigns`.
- `fcs-donations` roda no namespace `fcs-donations`.
- `fcs-donation-worker` roda no namespace `fcs-donation-worker`.
- `fcs-audit-logs` roda no namespace `fcs-audit-logs`.
- Keycloak, Kafka, Kafka UI, MongoDB, Datadog Agent, Datadog Cluster Agent e componentes compartilhados rodam em `fcs-infra`.
- Pipelines de CD devem usar o namespace correspondente ao servico.
