# Modelo da fcg-identity

A `fcg-identity` encapsula o Keycloak para cadastro, login, refresh e consulta de perfil da aplicacao. O Keycloak permanece dono das credenciais, senha, hash, emissao de JWT e roles; a `fcg-identity` armazena os perfis de dominio associados aos usuarios autenticaveis.

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

### AuditLog

Registro explicito de auditoria para eventos relevantes da identidade.

Tabela: `AuditLogs`.

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
- A `fcg-identity` executa seed para criar ou encontrar o **GestorONG** no Keycloak, garantir a role `GestorONG` e criar ou atualizar o `ManagerProfile`.
- A aplicacao nao armazena senha nem hash de senha.
- Roles canonicas do MVP: `Doador` e `GestorONG`.
