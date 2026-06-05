# Renomear prefixo fcg para fcs em todos os projetos

Os repositorios foram renomeados de `fcg-*` para `fcs-*` para refletir o nome do projeto Conexao Solidaria. O rename abrangeu nomes de repositorio, diretorios, nomes de projeto .NET, namespaces, workflows, Dockerfiles e referencias entre projetos.

**Consequencias**

### Alteracoes realizadas

- Repositorios GitHub renomeados de `fcg-*` para `fcs-*`.
- Diretorios locais renomeados para `fcs-*`.
- Nomes de projeto .NET (`*.csproj`, `*.slnx`) atualizados para `Fcs.*` (PascalCase).
- Namespaces em codigo C# atualizados para `fcs.*` (lowercase).
- Workflows CI/CD (`*.yml`) atualizados com novos nomes de repositorio, servico e artefatos.
- Dockerfiles atualizados com novos caminhos e nomes de assembly.
- Frontend web padronizado como `fcs-web`.

### Problemas encontrados e corrigidos

#### 1. Case-sensitivity em Linux (CI e Docker)

O rename introduziu inconsistencia de maiusculas/minusculas em paths de arquivo. O sistema de arquivos Windows e case-insensitive, entao os erros so apareceram nos runners Linux do GitHub Actions.

**Arquivos afetados e corrigidos:**

| Tipo de arquivo | fcs-campaign | fcs-audit-logs | fcs-donations |
|---|---|---|---|
| `.slnx` (project refs) | 11 refs `fcs.*` -> `Fcs.*` | 3 refs `fcs.*` -> `Fcs.*` | OK |
| `.csproj` (ProjectReference) | 9 arquivos, 20 refs `fcs.*` -> `Fcs.*` | 2 arquivos, 2 refs `fcs.*` -> `Fcs.*` | OK |
| `docker-compose.yml` | `fcs.` -> `Fcs.` | `fcs.` -> `Fcs.` | OK |
| `Dockerfile` paths | slnx + csproj + ENTRYPOINT | `fcs.` -> `Fcs.` | OK |
| Workflow YAML indent | 3 linhas (7->6 espacos) | 2 linhas (7->6 espacos) | OK |

`fcs-identity` e `fcs-web` nao apresentaram problemas de case.

#### 2. Versao de pacote inexistente

`Microsoft.Extensions.DependencyInjection.Abstractions` versao `10.0.0` nao existe no NuGet (versoes comecam em `10.0.3`).

**Repositorios afetados:** fcs-audit-logs, fcs-campaign, fcs-donations

**Correcao:** bump para `10.0.8` (mesma versao usada pelo identity, compativel com .NET 8).

#### 3. Dockerfile restaurava por .slnx com SDK 8.0

O SDK .NET 8 (`mcr.microsoft.com/dotnet/sdk:8.0`) nao suporta o formato `.slnx` (suporte adicionado apenas no SDK 9.0.200+). O CI do GitHub Actions funciona porque o runner tem SDK mais novo pre-instalado.

**Repositorios afetados:** fcs-campaign, fcs-donations

**Correcao:** Dockerfiles refatorados para restaurar pelo `.csproj` individual em vez do `.slnx`, seguindo o padrao ja usado pelo fcs-identity e fcs-audit-logs.

#### 4. Branchs de rename inconsistentes

Apos o rename, cada repositorio ficou em uma branch diferente:

| Branch | Repos |
|---|---|
| `chore/rename` | identity, web, fase05-docs, donations |
| `main` | campaign, audit-logs, pipelines |

**Correcao:** Unificado para `chore/rename` nos repositorios pendentes.

### Recomendacoes

- Ao criar novos projetos .NET, usar `Fcs.Xxx` (PascalCase) para nomes de projeto, diretorios e assembly; usar `fcs.xxx` (lowercase) para namespaces C# se desejar consistencia com o codigo existente.
- Dockerfiles devem sempre restaurar pelo `.csproj` individual, nao pelo `.slnx`, para compatibilidade com SDK 8.0 em containers.
- Validar workflows CI com push em branch de desenvolvimento antes de fazer merge em main.
