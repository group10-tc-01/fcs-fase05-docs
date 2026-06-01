# Usar namespaces Kubernetes separados

As aplicacoes terao namespaces Kubernetes separados por servico, enquanto componentes compartilhados de infraestrutura rodarao no namespace `fcg-infra`. Essa organizacao preserva isolamento operacional entre aplicacoes e concentra plataforma compartilhada em um unico namespace.

**Consequencias**

- `fcg-identity` roda no namespace `fcg-identity`.
- `fcg-campaigns` roda no namespace `fcg-campaigns`.
- `fcg-donations` roda no namespace `fcg-donations`.
- `fcg-donation-worker` roda no namespace `fcg-donation-worker`.
- `fcg-audit-logs` roda no namespace `fcg-audit-logs`.
- Keycloak, Kafka, Kafka UI, MongoDB, Prometheus, Grafana e componentes compartilhados rodam em `fcg-infra`.
- Pipelines de CD devem usar o namespace correspondente ao servico.
