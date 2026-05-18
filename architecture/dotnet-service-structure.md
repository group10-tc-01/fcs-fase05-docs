# Estrutura interna dos servicos .NET

Os repositorios .NET da fase 5 seguem o padrao observado no `fcg-users` da fase 4.

## API

Exemplo para `fcg-campaigns`:

```text
src/
  FCG.Campaigns.Domain/
  FCG.Campaigns.Application/
  FCG.Campaigns.Infrastructure.SqlServer/
  FCG.Campaigns.WebApi/
tests/
  FCG.Campaigns.UnitTests/
  FCG.Campaigns.IntegratedTests/
  FCG.Campaigns.FunctionalTests/
  FCG.Campaigns.CommonTestsUtilities/
```

## API com Kafka

Exemplo para `fcg-donations`:

```text
src/
  FCG.Donations.Domain/
  FCG.Donations.Application/
  FCG.Donations.Infrastructure.SqlServer/
  FCG.Donations.Infrastructure.Kafka/
  FCG.Donations.Infrastructure.Campaigns/
  FCG.Donations.WebApi/
tests/
  FCG.Donations.UnitTests/
  FCG.Donations.IntegratedTests/
  FCG.Donations.FunctionalTests/
  FCG.Donations.CommonTestsUtilities/
```

## Worker

Exemplo para `fcg-donation-worker`:

```text
src/
  FCG.DonationWorker.Domain/
  FCG.DonationWorker.Application/
  FCG.DonationWorker.Infrastructure.SqlServer/
  FCG.DonationWorker.Infrastructure.Kafka/
  FCG.DonationWorker.Infrastructure.Campaigns/
  FCG.DonationWorker.Worker/
tests/
  FCG.DonationWorker.UnitTests/
  FCG.DonationWorker.IntegratedTests/
  FCG.DonationWorker.CommonTestsUtilities/
```

## Convenções

- Codigo, namespaces, DTOs, eventos e topicos tecnicos ficam em ingles.
- Documentacao de dominio continua em pt-BR.
- Cada repositorio tem solution propria.
- Cada servico mantem migrations proprias.
- APIs mantem testes unitarios, integrados e funcionais quando aplicavel.
- Testes integrados das APIs tambem cobrem endpoints.
- Worker mantem testes unitarios e integrados, sem projeto de testes funcionais.
