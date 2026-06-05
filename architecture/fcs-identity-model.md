# Modelo da fcs-identity

A `fcs-identity` encapsula o Keycloak para cadastro, login, refresh e consulta de perfil da aplicacao. O Keycloak permanece dono das credenciais, senha, hash, emissao de JWT e roles; a `fcs-identity` armazena os perfis de dominio associados aos usuarios autenticaveis.

## Entidades

### DonorProfile

Representa o perfil de dominio de um **Doador** cadastrado pela aplicacao.

- `Id`
- `KeycloakUserId`
- `FullName`
- `Email`
- `Cpf`
- `CreatedAt`
- `UpdatedAt`

Restricoes de unicidade:

```text
KeycloakUserId
Email
Cpf
```

Tabela: `DonorProfiles`.

### ManagerProfile

Representa o perfil de dominio de um **GestorONG** provisionado administrativamente.

- `Id`
- `KeycloakUserId`
- `FullName`
- `Email`
- `CreatedAt`
- `UpdatedAt`

Restricoes de unicidade:

```text
KeycloakUserId
Email
```

Tabela: `ManagerProfiles`.

## Auditoria

A `fcs-identity` publica eventos explicitos de auditoria no topico Kafka `audit-log-requested`. Ela nao mantem tabela `AuditLogs` no `IdentityDb`.

Eventos iniciais:

```text
DonorRegistered
ManagerSeeded
LoginSucceeded
LoginFailed
TokenRefreshed
```

O envio do evento e responsabilidade do caso de uso que conhece o significado da acao. O `fcs-audit-logs` consome o topico e persiste os registros em MongoDB.

## Endpoints principais

Publicos:

```text
POST /auth/register/donor
POST /auth/login
POST /auth/refresh
```

Autenticado:

```text
GET /me
```

## Regras confirmadas

- O cadastro publico e apenas para `Doador`.
- O **GestorONG** e provisionado administrativamente no Keycloak e tambem registrado no `IdentityDb` como `ManagerProfile`.
- A `fcs-identity` executa seed para criar ou encontrar o **GestorONG** no Keycloak, garantir a role `GestorONG` e criar ou atualizar o `ManagerProfile`.
- A aplicacao nao armazena senha nem hash de senha.
- Roles canonicas do MVP: `Doador` e `GestorONG`.
- Eventos de auditoria de identidade sao publicados no Kafka pela propria `fcs-identity`, sem outbox.
