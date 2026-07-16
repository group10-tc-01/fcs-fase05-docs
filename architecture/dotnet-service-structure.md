# Estrutura interna dos serviços .NET

Os repositórios .NET da fase 5 seguem o padrão observado no `fcs-users` da fase 4.

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
  Fcs.DonationWorker.Application/
  Fcs.DonationWorker.Worker/
tests/
  Fcs.DonationWorker.UnitTests/
```

## Worker com MongoDB

Exemplo para `fcs-audit-logs`:

```text
src/
  Fcs.AuditLogs.Application/
  Fcs.AuditLogs.Worker/
tests/
  Fcs.AuditLogs.UnitTests/
```

## Convenções

- Código, namespaces, DTOs, eventos e tópicos técnicos ficam em inglês.
- Documentação de domínio continua em pt-BR.
- Cada repositório tem solution própria.
- Cada serviço mantém migrations próprias.
- Serviços com MongoDB não usam migrations EF Core; devem declarar índices e bootstrap da coleção na infraestrutura.
- APIs mantêm testes unitários, integrados e funcionais quando aplicável.
- Testes integrados das APIs também cobrem endpoints.
- Worker mantém testes unitários e integrados, sem projeto de testes funcionais.
