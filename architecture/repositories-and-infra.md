# Repositorios e infraestrutura

A fase 5 sera organizada em repositorios separados por aplicacao, com repositorios de apoio para infraestrutura compartilhada e pipelines reutilizaveis.

## Repositorios das aplicacoes

Cada aplicacao mantem autonomia para build, execucao local simples e deploy do proprio componente.

```text
fcs-identity
fcs-campaigns
fcs-donations
fcs-donation-worker
fcs-audit-logs
fcs-bff
fcs-web
```

Cada repositorio de aplicacao deve conter:

- `Dockerfile`
- `docker-compose` local da propria aplicacao e dependencias minimas
- manifests Kubernetes especificos do servico
- wrappers de CI/CD chamando os workflows reutilizaveis do `fcs-pipelines`
- README com instrucoes daquele componente

## Repositorio de infraestrutura

```text
fcs-infra
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

## Repositorio de pipelines

```text
fcs-pipelines
```

Responsabilidades:

- centralizar workflows reutilizaveis do GitHub Actions
- manter esteiras de CI para servicos .NET e aplicacoes Angular
- manter esteiras de delivery para build, scan, push de imagem e deploy quando aplicavel
- manter workflow de Terraform para infraestrutura Azure
- documentar inputs, secrets, variaveis e exemplos de adocao

## Padrao de CI/CD

A fase 5 usa workflows reutilizaveis centralizados no `fcs-pipelines`.

Repositorios de aplicacao .NET devem ter wrappers equivalentes a:

```text
.github/workflows/{service}-ci.yml
.github/workflows/{service}-cd.yml
.github/workflows/branch-name.yml
```

O CI chama:

```text
group10-tc-01/fcs-pipelines/.github/workflows/dotnet-service-ci.yml@main
```

O CD chama:

```text
group10-tc-01/fcs-pipelines/.github/workflows/dotnet-service-delivery.yml@main
```

A aplicacao Angular chama:

```text
group10-tc-01/fcs-pipelines/.github/workflows/angular-web-ci.yml@main
```

A politica de branch chama:

```text
group10-tc-01/fcs-pipelines/.github/workflows/branch-name-check.yml@main
```

O `fcs-infra` deve usar:

```text
group10-tc-01/fcs-pipelines/.github/workflows/terraform-azure.yml@main
```

Gates esperados para servicos .NET, incluindo o `fcs-bff`:

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

Gates esperados para aplicacoes Angular:

- branch policy
- secret scan com Gitleaks
- dependency vulnerability scan com `npm audit`
- verificacao de formatacao
- lint
- testes unitarios
- cobertura minima
- build Angular
- Docker build validation

## Estrutura sugerida do fcs-infra

```text
fcs-infra/
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
- O Terraform fica no `fcs-infra`.
- O CI/CD usa wrappers locais chamando workflows reutilizaveis do `fcs-pipelines`.
- O `fcs-pipelines` nao contem codigo de aplicacao; ele define apenas automacao compartilhada e exemplos de adocao.
- Namespaces Kubernetes das aplicacoes sao separados por servico.
- Componentes compartilhados de infraestrutura rodam no namespace `fcs-infra`.
- No ambiente Azure, o `fcs-bff` e APIs publicas necessarias sao expostos pelo Azure API Management.
- Endpoints `/internal/*` nao devem ser publicados no APIM.
- Credenciais e segredos nao devem ser versionados.
- Recursos Azure devem ser provisionados por IaC e parametrizados por ambiente.
- O `fcs-audit-logs` consome o topico `audit-log-requested` e persiste auditoria em MongoDB.
