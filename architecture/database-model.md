# Modelo de Banco de Dados

Este documento consolida as tabelas confirmadas por servico. A arquitetura usa SQL Server, com databases separados por servico e sem foreign keys entre databases.

## Tabela comum de auditoria

Cada servico deve manter uma tabela `AuditLogs` no proprio database. A auditoria e explicita por eventos de negocio, nao automatica pelo ChangeTracker do Entity Framework.

### AuditLogs

| Coluna | Tipo SQL Server | Obrigatorio | Observacao |
| --- | --- | --- | --- |
| `Id` | `uniqueidentifier` | Sim | PK |
| `ActorId` | `uniqueidentifier` | Nao | Perfil ou usuario que executou a acao |
| `ActorType` | `nvarchar(50)` | Nao | Ex.: `Public`, `Doador`, `GestorONG`, `System` |
| `Action` | `nvarchar(100)` | Sim | Evento de negocio auditado |
| `EntityName` | `nvarchar(100)` | Sim | Entidade afetada |
| `EntityId` | `nvarchar(100)` | Nao | Identificador da entidade afetada |
| `OccurredAt` | `datetime2` | Sim | Data/hora UTC |
| `CorrelationId` | `nvarchar(100)` | Nao | Correlacao da requisicao/processamento |
| `IpAddress` | `nvarchar(45)` | Nao | IPv4 ou IPv6 quando existir requisicao HTTP |
| `UserAgent` | `nvarchar(500)` | Nao | Quando existir requisicao HTTP |
| `MetadataJson` | `nvarchar(max)` | Nao | Metadados sem segredos |

Indices e constraints:

```text
PK_AuditLogs_Id
IX_AuditLogs_EntityName_EntityId
IX_AuditLogs_Action
IX_AuditLogs_OccurredAt
IX_AuditLogs_CorrelationId
```

Regras:

- Nao armazenar senha, token, refresh token ou segredo em `MetadataJson`.
- Mascarar dados sensiveis quando fizer sentido, como CPF.
- Registrar eventos relevantes nos casos de uso/handlers, de forma explicita.

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

### AuditLogs

Tabela comum de auditoria do `IdentityDb`.

Eventos iniciais esperados:

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

### AuditLogs

Tabela comum de auditoria do `CampaignsDb`.

Eventos iniciais esperados:

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

### AuditLogs

Tabela comum de auditoria do `DonationsDb`.

Eventos iniciais esperados:

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
