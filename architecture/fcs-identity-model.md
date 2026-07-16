# Modelo da fcs-identity

A `fcs-identity` encapsula o Keycloak para cadastro, login, refresh e consulta de perfil da aplicação. O Keycloak permanece dono das credenciais, senha, hash, emissão de JWT e roles; a `fcs-identity` armazena os perfis de domínio associados aos usuários autenticáveis.

## Entidades

### DonorProfile

Representa o perfil de domínio de um **Doador** cadastrado pela aplicação.

- `Id`
- `KeycloakUserId`
- `FullName`
- `Email`
- `Cpf`
- `CreatedAt`
- `UpdatedAt`

Restrições de unicidade:

```text
KeycloakUserId
Email
Cpf
```

Tabela: `DonorProfiles`.

### ManagerProfile

Representa o perfil de domínio de um **GestorONG** provisionado administrativamente.

- `Id`
- `KeycloakUserId`
- `FullName`
- `Email`
- `CreatedAt`
- `UpdatedAt`

Restrições de unicidade:

```text
KeycloakUserId
Email
```

Tabela: `ManagerProfiles`.

## Auditoria

A `fcs-identity` publica eventos explícitos de auditoria no tópico Kafka `audit-log-requested`. Ela não mantém tabela `AuditLogs` no `IdentityDb`.

Eventos iniciais:

```text
DonorRegistered
ManagerSeeded
LoginSucceeded
LoginFailed
TokenRefreshed
```

O envio do evento é responsabilidade do caso de uso que conhece o significado da ação. O `fcs-audit-logs` consome o tópico e persiste os registros em MongoDB.

## Endpoints principais

Públicos:

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

- O cadastro público é apenas para `Doador`.
- O **GestorONG** é provisionado administrativamente no Keycloak e também registrado no `IdentityDb` como `ManagerProfile`.
- A `fcs-identity` executa seed para criar ou encontrar o **GestorONG** no Keycloak, garantir a role `GestorONG` e criar ou atualizar o `ManagerProfile`.
- A aplicação não armazena senha nem hash de senha.
- Roles canônicas do MVP: `Doador` e `GestorONG`.
- Eventos de auditoria de identidade são publicados no Kafka pela própria `fcs-identity`, sem outbox.
