# Usar repositórios separados por aplicação

Cada aplicação da fase 5 possui seu próprio repositório, Dockerfile, execução local, manifests Kubernetes e wrappers de CI/CD. O `fcs-infra` administra a plataforma compartilhada da VPS/K3s; `fcs-pipelines` centraliza automações reutilizáveis.

## Consequências

- Cada serviço evolui e testa de forma independente.
- Secrets não são versionados: Infisical os sincroniza para o cluster.
- Imagens são publicadas no GHCR e os deployments são feitos no namespace correspondente.
