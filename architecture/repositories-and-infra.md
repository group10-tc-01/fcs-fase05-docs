# Repositórios e infraestrutura

| Repositório | Responsabilidade |
| --- | --- |
| `fcs-identity` | autenticação, perfis e fachada do Keycloak |
| `fcs-campaign` | campanhas e transparência pública |
| `fcs-donations` | intenção de doação, outbox e publicação Kafka |
| `fcs-donation-worker` | consumo de doações e atualização da campanha |
| `fcs-audit-logs` | consumo de auditoria e persistência MongoDB |
| `fcs-bff` | agregação opcional para o frontend |
| `fcs-web` | experiência pública, Doador e GestorONG |
| `fcs-infra` | Terraform, K3s e componentes compartilhados |
| `fcs-pipelines` | workflows reutilizáveis de CI e delivery |
| `fcs-vps-infra-guide` | implantação, operação e recuperação da VPS |

## Plataforma compartilhada

- Hostinger VPS, Ubuntu e K3s.
- Traefik e cert-manager para borda HTTPS.
- SQL Server, Kafka, Keycloak e MongoDB no namespace `fcs-infra`.
- Namespaces separados para as aplicações.
- GHCR para imagens, HCP Terraform para state/lock, Infisical para secrets e Datadog/OpenTelemetry para observabilidade.

O desenvolvimento local permanece por repositório, com Docker Compose e dependências mínimas. A integração acontece na VPS.
