# Arquitetura da plataforma

```mermaid
flowchart LR
    User[Usuário] --> Web[fcs-web]
    Web --> Bff[fcs-bff]
    Bff --> Identity[fcs-identity]
    Bff --> Campaigns[fcs-campaign]
    Bff --> Donations[fcs-donations]
    Donations -->|DonationReceivedEvent| Kafka[Kafka]
    Kafka --> Worker[fcs-donation-worker]
    Worker -->|API interna| Campaigns
    Identity --> Keycloak[Keycloak]
    Identity --> Sql[(SQL Server)]
    Campaigns --> Sql
    Donations --> Sql
    Worker -->|AuditLogRequestedEvent| Kafka
    Kafka --> Audit[fcs-audit-logs]
    Audit --> Mongo[(MongoDB)]
    Internet[Internet] --> Traefik[Traefik + TLS]
    Traefik --> Web
    Traefik --> Bff
    Traefik --> Identity
    Traefik --> Campaigns
    subgraph VPS[Hostinger VPS / K3s]
      Web
      Bff
      Identity
      Campaigns
      Donations
      Worker
      Audit
      Kafka
      Keycloak
      Sql
      Mongo
    end
```

## Princípios

- APIs públicas entram por Traefik com TLS; endpoints `/internal/*` usam apenas Services e DNS interno do Kubernetes.
- `fcs-donations` registra a intenção e publica o evento; não altera o total da campanha diretamente.
- O worker consome o evento e chama a API interna de campanhas de modo idempotente.
- Terraform administra host, cluster e plataforma compartilhada. Cada aplicação mantém seu Deployment, Service, Ingress e pipeline.
- Secrets ficam no Infisical; manifests e state não recebem valores sensíveis.
