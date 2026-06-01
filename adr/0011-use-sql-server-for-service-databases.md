# Usar SQL Server nos bancos dos servicos

A plataforma usara SQL Server como banco relacional para os dados operacionais de `fcg-identity`, `fcg-campaigns`, `fcg-donations` e Keycloak. No MVP, sera usada uma instancia de SQL Server com databases separados por servico, evitando compartilhamento direto de tabelas entre microsservicos.

Os logs de auditoria ficam fora dos databases relacionais dos servicos. Eles sao publicados no Kafka e persistidos em MongoDB pelo `fcg-audit-logs`, conforme [ADR 0030](./0030-use-explicit-business-audit-logs.md).

**Opcoes consideradas**

- Usar PostgreSQL.
- Usar SQL Server.

**Consequencias**

- O ambiente local e Kubernetes devem incluir SQL Server.
- Os databases previstos sao `IdentityDb`, `CampaignsDb`, `DonationsDb` e `KeycloakDb`.
- As constraints relacionais serao usadas para unicidade de CPF, email, idempotencia de doacoes processadas e controle da outbox de doacoes.
- A justificativa do banco no relatorio deve destacar integridade relacional, suporte a transacoes, familiaridade com o ecossistema .NET e compatibilidade com Entity Framework Core.
