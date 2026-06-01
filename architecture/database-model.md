# Modelo de Banco de Dados

Este documento consolida as tabelas e colecoes confirmadas por servico. A arquitetura usa SQL Server para os dados operacionais dos servicos, com databases separados e sem foreign keys entre databases.

Auditoria nao fica nos databases relacionais de cada servico. As aplicacoes publicam eventos explicitos no Kafka e o `fcg-audit-logs` persiste os registros em MongoDB.

## Auditoria centralizada

Topic Kafka:

```text
audit-log-requested
```

Evento:

```text
AuditLogRequestedEvent
```

Cada aplicacao publica seus proprios eventos de auditoria a partir dos casos de uso relevantes. Este fluxo nao usa outbox.

Payload minimo:

```json
{
  "eventId": "uuid",
  "occurredAt": "2026-05-18T20:00:00Z",
  "serviceName": "fcg-identity",
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

- Nao publicar senha, token, refresh token ou segredo em `metadata`.
- Mascarar dados sensiveis quando fizer sentido, como CPF.
- Registrar eventos relevantes nos casos de uso/handlers, de forma explicita.
- Nao usar auditoria automatica por ChangeTracker do Entity Framework.
- Nao usar outbox para eventos de auditoria.
- O `fcg-audit-logs` deve garantir idempotencia por `eventId`.

## fcg-identity

Database:

```text
IdentityDb
```

### DonorProfiles

Armazena o perfil de dominio do **Doador** cadastrado pela aplicacao. Credenciais e senha ficam no Keycloak.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `KeycloakUserId` | `nvarchar(100)` | Sim | Unique |
| `FullName` | `nvarchar(200)` | Sim |  |
| `Email` | `nvarchar(320)` | Sim | Unique |
| `Cpf` | `nvarchar(14)` | Sim | Unique |
| `CreatedAt` | `datetime2` | Sim |  |
| `UpdatedAt` | `datetime2` | Nao |  |

Indices e constraints:

```text
PK_DonorProfiles_Id
UX_DonorProfiles_KeycloakUserId
UX_DonorProfiles_Email
UX_DonorProfiles_Cpf
```

### ManagerProfiles

Armazena o perfil de dominio do **GestorONG** provisionado por seed da `fcg-identity`.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `KeycloakUserId` | `nvarchar(100)` | Sim | Unique |
| `FullName` | `nvarchar(200)` | Sim |  |
| `Email` | `nvarchar(320)` | Sim | Unique |
| `CreatedAt` | `datetime2` | Sim |  |
| `UpdatedAt` | `datetime2` | Nao |  |

Indices e constraints:

```text
PK_ManagerProfiles_Id
UX_ManagerProfiles_KeycloakUserId
UX_ManagerProfiles_Email
```

Eventos de auditoria publicados por `fcg-identity`:

```text
DonorRegistered
ManagerSeeded
LoginSucceeded
LoginFailed
TokenRefreshed
```

## fcg-campaigns

Database:

```text
CampaignsDb
```

### Campaigns

Armazena as campanhas de arrecadacao administradas por **GestorONG**.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `Title` | `nvarchar(200)` | Sim |  |
| `Description` | `nvarchar(2000)` | Sim |  |
| `StartDate` | `datetime2` | Sim |  |
| `EndDate` | `datetime2` | Sim | Nao pode estar no passado ao criar/editar |
| `FinancialGoal` | `decimal(18,2)` | Sim | Deve ser maior que zero |
| `Status` | `nvarchar(30)` | Sim | `Active`, `Completed`, `Canceled` |
| `TotalAmountRaised` | `decimal(18,2)` | Sim | Valor agregado a partir das doacoes processadas |
| `CreatedByManagerId` | `uniqueidentifier` | Sim | Referencia externa ao `ManagerProfile`, sem FK |
| `CreatedAt` | `datetime2` | Sim |  |
| `UpdatedAt` | `datetime2` | Nao |  |

Indices e constraints:

```text
PK_Campaigns_Id
IX_Campaigns_Status
IX_Campaigns_CreatedByManagerId
CK_Campaigns_FinancialGoal_GreaterThanZero
CK_Campaigns_TotalAmountRaised_GreaterOrEqualZero
```

### CampaignDonationEntries

Armazena as doacoes ja refletidas no valor arrecadado de uma campanha, garantindo idempotencia por `DonationId`.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `CampaignId` | `uniqueidentifier` | Sim | FK para `Campaigns` |
| `DonationId` | `uniqueidentifier` | Sim | Id da doacao no `DonationsDb`, sem FK entre databases |
| `Amount` | `decimal(18,2)` | Sim | Deve ser maior que zero |
| `ProcessedAt` | `datetime2` | Sim |  |

Indices e constraints:

```text
PK_CampaignDonationEntries_Id
FK_CampaignDonationEntries_Campaigns_CampaignId
UX_CampaignDonationEntries_CampaignId_DonationId
IX_CampaignDonationEntries_DonationId
CK_CampaignDonationEntries_Amount_GreaterThanZero
```

Eventos de auditoria publicados por `fcg-campaigns`:

```text
CampaignCreated
CampaignUpdated
CampaignCompleted
CampaignCanceled
DonationReflected
DuplicateDonationIgnored
```

## fcg-donations

Database:

```text
DonationsDb
```

### Donations

Armazena a intencao de doacao aceita pela `fcg-donations`.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `CampaignId` | `uniqueidentifier` | Sim | Referencia externa a campanha, sem FK entre databases |
| `DonorId` | `uniqueidentifier` | Sim | Referencia externa ao `DonorProfile`, sem FK entre databases |
| `Amount` | `decimal(18,2)` | Sim | Deve ser maior que zero |
| `Status` | `nvarchar(30)` | Sim | `Pending`, `Processed`, `Failed` |
| `CreatedAt` | `datetime2` | Sim |  |
| `ProcessedAt` | `datetime2` | Nao | Preenchido pelo worker ao finalizar processamento |
| `FailureReason` | `nvarchar(1000)` | Nao | Preenchido pelo worker quando `Status = Failed` |

Indices e constraints:

```text
PK_Donations_Id
IX_Donations_CampaignId
IX_Donations_DonorId
IX_Donations_Status
IX_Donations_CreatedAt
CK_Donations_Amount_GreaterThanZero
```

### OutboxMessages

Armazena eventos pendentes de publicacao no Kafka, garantindo que uma doacao aceita nao perca seu evento.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `AggregateId` | `uniqueidentifier` | Sim | Id da entidade relacionada, para `DonationReceivedEvent` sera `Donation.Id` |
| `EventType` | `nvarchar(200)` | Sim | Ex.: `DonationReceivedEvent` |
| `Payload` | `nvarchar(max)` | Sim | JSON do evento |
| `Status` | `nvarchar(30)` | Sim | `Pending`, `Published`, `Failed` |
| `CreatedAt` | `datetime2` | Sim |  |
| `PublishedAt` | `datetime2` | Nao |  |
| `RetryCount` | `int` | Sim | Default `0` |
| `LastError` | `nvarchar(2000)` | Nao |  |

Indices e constraints:

```text
PK_OutboxMessages_Id
IX_OutboxMessages_Status_CreatedAt
IX_OutboxMessages_AggregateId
IX_OutboxMessages_EventType
CK_OutboxMessages_RetryCount_GreaterOrEqualZero
```

### ProcessedMessages

Armazena mensagens ja tratadas por consumidor para apoiar idempotencia no processamento do Kafka.

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `MessageId` | `uniqueidentifier` | Sim | Id do evento, como `eventId` |
| `Topic` | `nvarchar(200)` | Sim | Ex.: `donation-received` |
| `ProcessedAt` | `datetime2` | Sim |  |

Indices e constraints:

```text
PK_ProcessedMessages_Id
UX_ProcessedMessages_MessageId_Topic
IX_ProcessedMessages_ProcessedAt
```

Eventos de auditoria publicados por `fcg-donations` e `fcg-donation-worker`:

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

O `KeycloakDb` pertence ao Keycloak e nao tera suas tabelas internas modeladas neste documento. A plataforma apenas provisiona o database e configura o Keycloak para usa-lo; migrations e estrutura interna sao responsabilidade do proprio Keycloak.

## fcg-audit-logs

Storage:

```text
MongoDB
```

Database:

```text
AuditLogsDb
```

Colecao:

```text
audit_logs
```

### audit_logs

| Campo | Tipo | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `_id` | ObjectId | Sim | PK do MongoDB |
| `eventId` | string/uuid | Sim | Idempotencia do evento recebido |
| `occurredAt` | date | Sim | Data/hora UTC do evento original |
| `receivedAt` | date | Sim | Data/hora UTC de persistencia pelo worker |
| `serviceName` | string | Sim | Servico que publicou o evento |
| `action` | string | Sim | Evento de negocio ou seguranca auditado |
| `entityName` | string | Sim | Entidade afetada |
| `entityId` | string | Nao | Identificador da entidade afetada |
| `actorId` | string | Nao | Perfil ou usuario que executou a acao |
| `actorType` | string | Nao | Ex.: `Public`, `Doador`, `GestorONG`, `System` |
| `correlationId` | string | Nao | Correlacao da requisicao/processamento |
| `ipAddress` | string | Nao | IPv4 ou IPv6 quando existir requisicao HTTP |
| `userAgent` | string | Nao | Quando existir requisicao HTTP |
| `metadata` | document | Nao | Metadados sem segredos |

Indices:

```text
UX_audit_logs_eventId
IX_audit_logs_serviceName_action
IX_audit_logs_entityName_entityId
IX_audit_logs_occurredAt
IX_audit_logs_correlationId
IX_audit_logs_actorId
```
