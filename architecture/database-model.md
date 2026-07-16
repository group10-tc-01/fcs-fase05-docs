# Modelo de banco de dados

Este documento consolida as tabelas e coleções confirmadas por serviço. A arquitetura usa SQL Server para os dados operacionais dos serviços, com databases separados e sem foreign keys entre databases.

Auditoria não fica nos databases relacionais de cada serviço. As aplicações publicam eventos explícitos no Kafka e o `fcs-audit-logs` persiste os registros em MongoDB.

## Auditoria centralizada

Topic Kafka:

```text
audit-log-requested
```

Evento:

```text
AuditLogRequestedEvent
```

Cada aplicação publica seus próprios eventos de auditoria a partir dos casos de uso relevantes. Este fluxo não usa outbox.

Payload mínimo:

```json
{
  "eventId": "uuid",
  "occurredAt": "2026-05-18T20:00:00Z",
  "serviceName": "fcs-identity",
  "action": "DonorRegistered",
  "entityName": "DonorProfile",
  "entityId": "uuid",
  "actorId": "uuid",
  "actorType": "Doador",
  "correlationId": "correlation-id",
  "ipAddress": "127.0.0.1",
  "userAgent": "user-agent",
  "metadata": {}
}
```

Regras:

- Não publicar senha, token, refresh token ou segredo em `metadata`.
- Mascarar dados sensíveis quando fizer sentido, como CPF.
- Registrar eventos relevantes nos casos de uso/handlers, de forma explícita.
- Não usar auditoria automática por ChangeTracker do Entity Framework.
- Não usar outbox para eventos de auditoria.
- O `fcs-audit-logs` deve garantir idempotência por `eventId`.

## fcs-identity

Database:

```text
IdentityDb
```

### DonorProfiles

Armazena o perfil de domínio do **Doador** cadastrado pela aplicação. Credenciais e senha ficam no Keycloak.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `KeycloakUserId` | `nvarchar(100)` | Sim | Unique |
| `FullName` | `nvarchar(200)` | Sim |  |
| `Email` | `nvarchar(320)` | Sim | Unique |
| `Cpf` | `nvarchar(14)` | Sim | Unique |
| `CreatedAt` | `datetime2` | Sim |  |
| `UpdatedAt` | `datetime2` | Não |  |

Índices e constraints:

```text
PK_DonorProfiles_Id
UX_DonorProfiles_KeycloakUserId
UX_DonorProfiles_Email
UX_DonorProfiles_Cpf
```

### ManagerProfiles

Armazena o perfil de domínio do **GestorONG** provisionado por seed da `fcs-identity`.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `KeycloakUserId` | `nvarchar(100)` | Sim | Unique |
| `FullName` | `nvarchar(200)` | Sim |  |
| `Email` | `nvarchar(320)` | Sim | Unique |
| `CreatedAt` | `datetime2` | Sim |  |
| `UpdatedAt` | `datetime2` | Não |  |

Índices e constraints:

```text
PK_ManagerProfiles_Id
UX_ManagerProfiles_KeycloakUserId
UX_ManagerProfiles_Email
```

Eventos de auditoria publicados por `fcs-identity`:

```text
DonorRegistered
ManagerSeeded
LoginSucceeded
LoginFailed
TokenRefreshed
```

## fcs-campaigns

Database:

```text
CampaignsDb
```

### Campaigns

Armazena as campanhas de arrecadação administradas por **GestorONG**.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `Title` | `nvarchar(200)` | Sim |  |
| `Description` | `nvarchar(2000)` | Sim |  |
| `StartDate` | `datetime2` | Sim |  |
| `EndDate` | `datetime2` | Sim | Não pode estar no passado ao criar/editar |
| `FinancialGoal` | `decimal(18,2)` | Sim | Deve ser maior que zero |
| `Status` | `nvarchar(30)` | Sim | `Active`, `Completed`, `Canceled` |
| `TotalAmountRaised` | `decimal(18,2)` | Sim | Valor agregado a partir das doações processadas |
| `CreatedByManagerId` | `uniqueidentifier` | Sim | Referência externa ao `ManagerProfile`, sem FK |
| `CreatedAt` | `datetime2` | Sim |  |
| `UpdatedAt` | `datetime2` | Não |  |

Índices e constraints:

```text
PK_Campaigns_Id
IX_Campaigns_Status
IX_Campaigns_CreatedByManagerId
CK_Campaigns_FinancialGoal_GreaterThanZero
CK_Campaigns_TotalAmountRaised_GreaterOrEqualZero
```

### CampaignDonationEntries

Armazena as doações já refletidas no valor arrecadado de uma campanha, garantindo idempotência por `DonationId`.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `CampaignId` | `uniqueidentifier` | Sim | FK para `Campaigns` |
| `DonationId` | `uniqueidentifier` | Sim | Id da doação no `DonationsDb`, sem FK entre databases |
| `Amount` | `decimal(18,2)` | Sim | Deve ser maior que zero |
| `ProcessedAt` | `datetime2` | Sim |  |

Índices e constraints:

```text
PK_CampaignDonationEntries_Id
FK_CampaignDonationEntries_Campaigns_CampaignId
UX_CampaignDonationEntries_CampaignId_DonationId
IX_CampaignDonationEntries_DonationId
CK_CampaignDonationEntries_Amount_GreaterThanZero
```

Eventos de auditoria publicados por `fcs-campaigns`:

```text
CampaignCreated
CampaignUpdated
CampaignCompleted
CampaignCanceled
DonationReflected
DuplicateDonationIgnored
```

## fcs-donations

Database:

```text
DonationsDb
```

### Donations

Armazena a intenção de doação aceita pela `fcs-donations`.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `CampaignId` | `uniqueidentifier` | Sim | Referência externa à campanha, sem FK entre databases |
| `DonorId` | `uniqueidentifier` | Sim | Referência externa ao `DonorProfile`, sem FK entre databases |
| `Amount` | `decimal(18,2)` | Sim | Deve ser maior que zero |
| `Status` | `nvarchar(30)` | Sim | `Pending`, `Processed`, `Failed` |
| `CreatedAt` | `datetime2` | Sim |  |
| `ProcessedAt` | `datetime2` | Não | Preenchido pelo worker ao finalizar processamento |
| `FailureReason` | `nvarchar(1000)` | Não | Preenchido pelo worker quando `Status = Failed` |

Índices e constraints:

```text
PK_Donations_Id
IX_Donations_CampaignId
IX_Donations_DonorId
IX_Donations_Status
IX_Donations_CreatedAt
CK_Donations_Amount_GreaterThanZero
```

### OutboxMessages

Armazena eventos pendentes de publicação no Kafka, garantindo que uma doação aceita não perca seu evento.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `AggregateId` | `uniqueidentifier` | Sim | Id da entidade relacionada, para `DonationReceivedEvent` será `Donation.Id` |
| `EventType` | `nvarchar(200)` | Sim | Ex.: `DonationReceivedEvent` |
| `Payload` | `nvarchar(max)` | Sim | JSON do evento |
| `Status` | `nvarchar(30)` | Sim | `Pending`, `Published`, `Failed` |
| `CreatedAt` | `datetime2` | Sim |  |
| `PublishedAt` | `datetime2` | Não |  |
| `RetryCount` | `int` | Sim | Default `0` |
| `LastError` | `nvarchar(2000)` | Não |  |

Índices e constraints:

```text
PK_OutboxMessages_Id
IX_OutboxMessages_Status_CreatedAt
IX_OutboxMessages_AggregateId
IX_OutboxMessages_EventType
CK_OutboxMessages_RetryCount_GreaterOrEqualZero
```

### ProcessedMessages

Armazena mensagens já tratadas por consumidor para apoiar idempotência no processamento do Kafka.

| Coluna | Tipo SQL Server | Obrigatório | Observação |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `MessageId` | `uniqueidentifier` | Sim | Id do evento, como `eventId` |
| `Topic` | `nvarchar(200)` | Sim | Ex.: `donation-received` |
| `ProcessedAt` | `datetime2` | Sim |  |

Índices e constraints:

```text
PK_ProcessedMessages_Id
UX_ProcessedMessages_MessageId_Topic
IX_ProcessedMessages_ProcessedAt
```

Eventos de auditoria publicados por `fcs-donations` e `fcs-donation-worker`:

```text
DonationRequested
DonationRejected
DonationEventQueued
DonationEventPublished
DonationProcessed
DonationFailed
DuplicateMessageIgnored
```

## Keycloak

Database:

```text
KeycloakDb
```

O `KeycloakDb` pertence ao Keycloak e não terá suas tabelas internas modeladas neste documento. A plataforma apenas provisiona o database e configura o Keycloak para usá-lo; migrations e estrutura interna são responsabilidade do próprio Keycloak.

## fcs-audit-logs

Storage:

```text
MongoDB
```

Database:

```text
AuditLogsDb
```

Coleção:

```text
audit_logs
```

### audit_logs

| Campo | Tipo | Obrigatório | Observação |
| --- | --- | --- | --- |
| `_id` | ObjectId | Sim | PK do MongoDB |
| `eventId` | string/uuid | Sim | Idempotência do evento recebido |
| `occurredAt` | date | Sim | Data/hora UTC do evento original |
| `receivedAt` | date | Sim | Data/hora UTC de persistência pelo worker |
| `serviceName` | string | Sim | Serviço que publicou o evento |
| `action` | string | Sim | Evento de negócio ou segurança auditado |
| `entityName` | string | Sim | Entidade afetada |
| `entityId` | string | Não | Identificador da entidade afetada |
| `actorId` | string | Não | Perfil ou usuário que executou a ação |
| `actorType` | string | Não | Ex.: `Public`, `Doador`, `GestorONG`, `System` |
| `correlationId` | string | Não | Correlação da requisição/processamento |
| `ipAddress` | string | Não | IPv4 ou IPv6 quando existir requisição HTTP |
| `userAgent` | string | Não | Quando existir requisição HTTP |
| `metadata` | document | Não | Metadados sem segredos |

Índices:

```text
UX_audit_logs_eventId
IX_audit_logs_serviceName_action
IX_audit_logs_entityName_entityId
IX_audit_logs_occurredAt
IX_audit_logs_correlationId
IX_audit_logs_actorId
```
