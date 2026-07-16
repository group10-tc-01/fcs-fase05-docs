# Usar SQL Server nos bancos dos serviços

A plataforma usará SQL Server como banco relacional para os dados operacionais de `fcs-identity`, `fcs-campaigns`, `fcs-donations` e Keycloak. No MVP, será usada uma instância de SQL Server com databases separados por serviço, evitando compartilhamento direto de tabelas entre microsserviços.

Os logs de auditoria ficam fora dos databases relacionais dos serviços. Eles são publicados no Kafka e persistidos em MongoDB pelo `fcs-audit-logs`, conforme [ADR 0023](./0023-use-explicit-business-audit-logs.md).

**Opções consideradas**

- Usar PostgreSQL.
- Usar SQL Server.

**Consequências**

- O ambiente local e Kubernetes devem incluir SQL Server.
- Os databases previstos são `IdentityDb`, `CampaignsDb`, `DonationsDb` e `KeycloakDb`.
- As constraints relacionais serão usadas para unicidade de CPF, email, idempotência de doações processadas e controle da outbox de doações.
- A justificativa do banco no relatório deve destacar integridade relacional, suporte a transações, familiaridade com o ecossistema .NET e compatibilidade com Entity Framework Core.
