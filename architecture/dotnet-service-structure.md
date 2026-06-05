# Estrutura interna dos servicos .NET

Os repositorios .NET da fase 5 seguem o padrao observado no `fcs-users` da fase 4.

## API

Exemplo para `fcs-campaigns`:

```text
src/
  Fcs.Campaigns.Domain/
  Fcs.Campaigns.Application/
  Fcs.Campaigns.Infrastructure.SqlServer/
  Fcs.Campaigns.WebApi/
tests/
  Fcs.Campaigns.UnitTests/
  Fcs.Campaigns.IntegratedTests/
  Fcs.Campaigns.FunctionalTests/
  Fcs.Campaigns.CommonTestsUtilities/
```

## API com Kafka

Exemplo para `fcs-donations`:

```text
src/
  Fcs.Donations.Domain/
  Fcs.Donations.Application/
  Fcs.Donations.Infrastructure.SqlServer/
  Fcs.Donations.Infrastructure.Kafka/
  Fcs.Donations.Infrastructure.Campaigns/
  Fcs.Donations.WebApi/
tests/
  Fcs.Donations.UnitTests/
  Fcs.Donations.IntegratedTests/
  Fcs.Donations.FunctionalTests/
  Fcs.Donations.CommonTestsUtilities/
```

## Worker

Exemplo para `fcs-donation-worker`:

```text
src/
  Fcs.DonationWorker.Domain/
  Fcs.DonationWorker.Application/
  Fcs.DonationWorker.Infrastructure.SqlServer/
  Fcs.DonationWorker.Infrastructure.Kafka/
  Fcs.DonationWorker.Infrastructure.Campaigns/
  Fcs.DonationWorker.Worker/
tests/
  Fcs.DonationWorker.UnitTests/
  Fcs.DonationWorker.IntegratedTests/
  Fcs.DonationWorker.CommonTestsUtilities/
```

## Worker com MongoDB

Exemplo para `fcs-audit-logs`:

```text
src/
  Fcs.AuditLogs.Domain/
  Fcs.AuditLogs.Application/
  Fcs.AuditLogs.Infrastructure.Kafka/
  Fcs.AuditLogs.Infrastructure.MongoDB/
  Fcs.AuditLogs.Worker/
tests/
  Fcs.AuditLogs.UnitTests/
  Fcs.AuditLogs.IntegratedTests/
  Fcs.AuditLogs.CommonTestsUtilities/
```

## Convenções

- Codigo, namespaces, DTOs, eventos e topicos tecnicos ficam em ingles.
- Documentacao de dominio continua em pt-BR.
- Cada repositorio tem solution propria.
- Cada servico mantem migrations proprias.
- Servicos com MongoDB nao usam migrations EF Core; devem declarar indices e bootstrap da colecao na infraestrutura.
- APIs mantem testes unitarios, integrados e funcionais quando aplicavel.
- Testes integrados das APIs tambem cobrem endpoints.
- Worker mantem testes unitarios e integrados, sem projeto de testes funcionais.
