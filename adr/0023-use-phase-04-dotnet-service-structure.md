# Reutilizar estrutura interna .NET da fase 4

Os servicos .NET da fase 5 seguirao a estrutura interna usada na fase 4, tomando `fcg-users` como referencia. Cada servico sera organizado em projetos de Domain, Application, Infrastructure, WebApi ou Worker, alem de projetos de testes unitarios, integrados, funcionais e utilitarios quando aplicavel.

**Consequencias**

- APIs usam projetos `Domain`, `Application`, `Infrastructure.SqlServer` e `WebApi`.
- Servicos com Kafka adicionam `Infrastructure.Kafka`.
- Integracoes HTTP entre servicos podem ficar em projeto de infraestrutura dedicado ou no projeto de infraestrutura do servico, conforme o tamanho.
- O worker usa projeto executavel de worker em vez de `WebApi`, mantendo os demais projetos compartilhaveis dentro do repositorio dele.
- APIs usam `UnitTests`, `IntegratedTests`, `FunctionalTests` e `CommonTestsUtilities` quando aplicavel.
- Nos repositorios de API, testes integrados tambem devem cobrir endpoints.
- O worker nao tera projeto de testes funcionais; ele usa testes unitarios e integrados.
