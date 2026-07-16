# Usar namespaces Kubernetes separados

As aplicações terão namespaces Kubernetes separados por serviço, enquanto componentes compartilhados de infraestrutura rodarão no namespace `fcs-infra`. Essa organização preserva isolamento operacional entre aplicações e concentra a plataforma compartilhada em um único namespace.

**Consequências**

- `fcs-identity` roda no namespace `fcs-identity`.
- `fcs-campaigns` roda no namespace `fcs-campaigns`.
- `fcs-donations` roda no namespace `fcs-donations`.
- `fcs-donation-worker` roda no namespace `fcs-donation-worker`.
- `fcs-audit-logs` roda no namespace `fcs-audit-logs`.
- Keycloak, Kafka, Kafka UI, MongoDB, Datadog Agent, Datadog Cluster Agent e componentes compartilhados rodam em `fcs-infra`.
- Pipelines de CD devem usar o namespace correspondente ao serviço.
