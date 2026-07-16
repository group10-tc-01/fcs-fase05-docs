# Reutilizar estrutura interna .NET da fase 4

Os serviços .NET da fase 5 seguirão a estrutura interna usada na fase 4, tomando `fcg-users` como referência. Cada serviço será organizado em projetos de Domain, Application, Infrastructure, WebApi ou Worker, além de projetos de testes unitários, integrados, funcionais e utilitários quando aplicável.

**Consequências**

- APIs usam projetos `Domain`, `Application`, `Infrastructure.SqlServer` e `WebApi`.
- Serviços com Kafka adicionam `Infrastructure.Kafka`.
- Integrações HTTP entre serviços podem ficar em projeto de infraestrutura dedicado ou no projeto de infraestrutura do serviço, conforme o tamanho.
- Workers usam projeto executável de worker em vez de `WebApi`, mantendo os demais projetos compartilháveis dentro do repositório deles.
- O `fcs-audit-logs` adiciona infraestrutura de Kafka para consumo e infraestrutura de MongoDB para persistência dos registros de auditoria.
- APIs usam `UnitTests`, `IntegratedTests`, `FunctionalTests` e `CommonTestsUtilities` quando aplicável.
- Nos repositórios de API, testes integrados também devem cobrir endpoints.
- O worker não terá projeto de testes funcionais; ele usa testes unitários e integrados.
