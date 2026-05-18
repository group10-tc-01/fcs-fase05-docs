# Reutilizar fcg-pipelines para CI/CD

Os repositorios da fase 5 usarao o padrao da fase 4 com workflows reutilizaveis do repositorio `fcg-pipelines`. Cada aplicacao manterá apenas wrappers pequenos em `.github/workflows`, enquanto a logica de CI, scans, build Docker, push para ACR, deploy em AKS e Terraform ficara centralizada no `fcg-pipelines`.

**Referencia da fase 4**

- `fcg-users` usa `users-ci.yml`, `users-cd.yml` e `branch-name.yml` como wrappers.
- O CI chama `group10-tc-01/fcg-pipelines/.github/workflows/dotnet-service-ci.yml@main`.
- O CD chama `group10-tc-01/fcg-pipelines/.github/workflows/dotnet-service-delivery.yml@main`.
- A politica de branch chama `group10-tc-01/fcg-pipelines/.github/workflows/branch-name-check.yml@main`.

**Consequencias**

- Cada repositorio da fase 5 deve configurar wrappers equivalentes ao `fcg-users`.
- Servicos .NET usam o workflow reutilizavel `dotnet-service-ci.yml` para restore, build, testes, cobertura, secret scan, dependency scan, SonarCloud e validacao de Docker build.
- Servicos .NET usam `dotnet-service-delivery.yml` para build, scan de imagem, push para ACR e deploy no AKS quando habilitado.
- O CD dos servicos segue o padrao do `fcg-users`: dispara por `workflow_run` apos CI bem-sucedido na `main` e tambem por `workflow_dispatch`.
- O repositorio `fcg-solidarity-infra` usa `terraform-azure.yml` para `fmt`, `init`, `validate`, `plan` e `apply` controlado.
- A politica de branch deve aceitar `feature/*`, `release/*` e `hotfix/*`.
- O threshold minimo de cobertura sera 80%.
- SonarCloud sera habilitado nos servicos .NET com `run_sonar: true`.
- O padrao de chave sera `group10-tc-01_fcg-{service}` na organizacao `group10-tc-01`.
- Secrets e variaveis seguem o contrato do `fcg-pipelines`, incluindo Azure OIDC, ACR, AKS, SonarCloud e tokens necessarios.
