# Repositorios e infraestrutura

A fase 5 sera organizada em repositorios separados por aplicacao, com um repositorio dedicado para infraestrutura compartilhada.

## Repositorios das aplicacoes

Cada aplicacao mantem autonomia para build, execucao local simples e deploy do proprio componente.

```text
fcg-identity
fcg-campaigns
fcg-donations
fcg-donation-worker
fcg-audit-logs
fcg-solidarity-web
```

Cada repositorio de aplicacao deve conter:

- `Dockerfile`
- `docker-compose` local da propria aplicacao e dependencias minimas
- manifests Kubernetes especificos do servico
- wrappers de CI/CD chamando os workflows reutilizaveis do `fcg-pipelines`
- README com instrucoes daquele componente

## Repositorio de infraestrutura

```text
fcg-solidarity-infra
```

Responsabilidades:

- docker compose do ambiente completo integrado
- manifests Kubernetes integrados
- ConfigMaps e Secrets de referencia sem valores sensiveis reais
- configuracao de Keycloak para realm, clients e roles
- configuracao de Kafka e Kafka UI
- configuracao de MongoDB para auditoria centralizada
- configuracao de Prometheus e dashboards do Grafana
- Terraform para provisionar recursos na Azure
- Azure API Management como borda publica das APIs
- README principal para subir o ambiente completo da demo

## Padrao de CI/CD

A fase 5 reutiliza o padrao da fase 4 observado em `fcg-users` e centralizado no `fcg-pipelines`.

Repositorios de aplicacao .NET devem ter wrappers equivalentes a:

```text
.github/workflows/{service}-ci.yml
.github/workflows/{service}-cd.yml
.github/workflows/branch-name.yml
```

O CI chama:

```text
group10-tc-01/fcg-pipelines/.github/workflows/dotnet-service-ci.yml@main
```

O CD chama:

```text
group10-tc-01/fcg-pipelines/.github/workflows/dotnet-service-delivery.yml@main
```

A politica de branch chama:

```text
group10-tc-01/fcg-pipelines/.github/workflows/branch-name-check.yml@main
```

O `fcg-solidarity-infra` deve usar:

```text
group10-tc-01/fcg-pipelines/.github/workflows/terraform-azure.yml@main
```

Gates esperados para servicos .NET:

- branch policy
- secret scan com Gitleaks
- dependency vulnerability scan
- restore e build
- testes unitarios, integrados e funcionais quando existirem
- cobertura minima
- SonarCloud
- Docker build validation
- build e push da imagem para ACR no CD
- Trivy scan da imagem
- deploy no AKS quando habilitado
- healthcheck apos rollout quando URL estiver configurada

## Estrutura sugerida do fcg-solidarity-infra

```text
fcg-solidarity-infra/
  docker/
    docker-compose.yml
  k8s/
    apps/
    platform/
    observability/
  keycloak/
  kafka/
  mongodb/
  grafana/
    dashboards/
  terraform/
    environments/
      dev/
    modules/
  docs/
```

## Diretrizes confirmadas

- O repo de infra orquestra o ambiente completo, mas nao substitui os arquivos de cada aplicacao.
- Cada aplicacao continua dona do seu build e dos seus manifests base.
- O Terraform fica no `fcg-solidarity-infra`.
- O CI/CD usa wrappers locais chamando workflows reutilizaveis do `fcg-pipelines`.
- Namespaces Kubernetes das aplicacoes sao separados por servico.
- Componentes compartilhados de infraestrutura rodam no namespace `fcg-infra`.
- No ambiente Azure, APIs publicas sao expostas pelo Azure API Management.
- Endpoints `/internal/*` nao devem ser publicados no APIM.
- Credenciais e segredos nao devem ser versionados.
- Recursos Azure devem ser provisionados por IaC e parametrizados por ambiente.
- O `fcg-audit-logs` consome o topico `audit-log-requested` e persiste auditoria em MongoDB.
