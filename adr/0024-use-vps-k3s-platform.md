# Usar VPS Hostinger com K3s como plataforma integrada

O ambiente integrado da Conexão Solidária roda em uma VPS Hostinger com K3s. O Terraform no `fcs-infra` administra o host, o cluster e os componentes compartilhados.

## Decisões

- Traefik e cert-manager publicam apenas Ingresses declarados com TLS.
- GHCR armazena imagens imutáveis produzidas pelo GitHub Actions.
- HCP Terraform mantém state e lock; GitHub Actions executa o Terraform.
- Infisical é o cofre externo e sincroniza Secrets Kubernetes.
- Datadog Agent/Cluster Agent e OpenTelemetry coletam telemetria.
- SQL Server, Kafka, Keycloak e MongoDB permanecem no K3s para o MVP.

## Consequências

- A API Kubernetes não é pública; delivery usa túnel SSH temporário.
- Aplicações mantêm manifests e pipelines; `fcs-infra` é dono da plataforma compartilhada.
- Docker Compose local continua disponível para desenvolvimento, mas a integração oficial ocorre na VPS.
