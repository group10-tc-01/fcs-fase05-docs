# Conexão Solidária — documentação da entrega

A Conexão Solidária é o MVP da ONG Esperança Solidária para campanhas, doações e transparência pública. A plataforma usa microsserviços .NET, Kafka e Kubernetes.

## Ambiente oficial

O ambiente integrado é uma VPS Hostinger com K3s. O `fcs-infra` declara a plataforma com Terraform; Traefik publica somente os Ingresses necessários, Infisical sincroniza secrets e Datadog recebe a telemetria.

Os `docker-compose` de cada aplicação continuam sendo o caminho de desenvolvimento local e de correção do hackathon. Eles não substituem o ambiente integrado.

## Navegação

- [Requisitos do hackathon](hackaton.md)
- [Arquitetura](architecture/overview.md)
- [Repositórios e infraestrutura](architecture/repositories-and-infra.md)
- [Endpoints](architecture/endpoint-flows.md)
- [ADRs](adr/)

## Rastreabilidade do hackathon

| Critério | Componentes responsáveis |
| --- | --- |
| JWT, RBAC e cadastro de Doador | `fcs-identity` + Keycloak |
| Campanhas e painel de transparência | `fcs-campaign` |
| Intenção de doação assíncrona | `fcs-donations` + Kafka |
| Processamento e atualização do valor arrecadado | `fcs-donation-worker` + `fcs-campaign` |
| Auditoria de negócio | `fcs-audit-logs` + MongoDB |
| Frontend e agregação de APIs | `fcs-web` + `fcs-bff` |
| Kubernetes, TLS, secrets e observabilidade | `fcs-infra` + `fcs-vps-infra-guide` |
| CI, imagens GHCR e delivery | `fcs-pipelines` |

## Decisão de plataforma

[ADR 0024](adr/0024-use-vps-k3s-platform.md) registra a VPS Hostinger, K3s, Traefik, GHCR, Infisical, Datadog, HCP Terraform e GitHub Actions como padrão integrado.
