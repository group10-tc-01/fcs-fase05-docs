# Reutilizar fcs-pipelines para CI/CD

Os repositorios da fase 5 usarao workflows reutilizaveis do repositorio `fcs-pipelines`. Cada aplicacao mantera apenas wrappers pequenos em `.github/workflows`, enquanto a logica de CI, scans, build Docker, push para ACR, deploy em AKS e Terraform ficara centralizada no `fcs-pipelines`.

**Workflows reutilizaveis**

- `branch-name-check.yml`: politica de nome de branch.
- `dotnet-service-ci.yml`: CI para servicos .NET.
- `dotnet-service-delivery.yml`: delivery para servicos com imagem Docker e deploy em AKS quando habilitado.
- `angular-web-ci.yml`: CI para aplicacoes Angular.
- `terraform-azure.yml`: validacao, plan e apply controlado da infraestrutura Azure.

**Consequencias**

- Cada repositorio da fase 5 deve configurar wrappers locais chamando os workflows reutilizaveis do `fcs-pipelines`.
- Servicos .NET usam `dotnet-service-ci.yml` para restore, build, testes, cobertura, secret scan, dependency scan, SonarCloud e validacao de Docker build.
- Servicos .NET usam `dotnet-service-delivery.yml` para build, scan de imagem, push para ACR e deploy no AKS quando habilitado.
- Aplicacoes Angular usam `angular-web-ci.yml` para `npm ci`, audit, formatacao, lint, testes, cobertura, build Angular e validacao de Docker build.
- O repositorio `fcs-infra` usa `terraform-azure.yml` para `fmt`, `init`, `validate`, `plan` e `apply` controlado.
- A politica de branch deve aceitar `feature/*`, `release/*` e `hotfix/*`.
- O threshold minimo de cobertura sera 80%.
- SonarCloud sera habilitado nos servicos .NET com `run_sonar: true`.
- O padrao de chave sera `group10-tc-01_fcs-{service}` na organizacao `group10-tc-01`, incluindo `fcs-audit-logs`.
- Secrets e variaveis seguem o contrato do `fcs-pipelines`, incluindo Azure OIDC, ACR, AKS, SonarCloud e tokens necessarios.
