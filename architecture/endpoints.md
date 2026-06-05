# Endpoints

Este documento consolida os endpoints confirmados por servico. As respostas seguem o padrao `ApiResponse<T>` usado na fase 4 pelo `fcs-users`.

## Padrao de resposta

Sucesso:

```json
{
  "success": true,
  "data": {},
  "errorMessages": null
}
```

Erro:

```json
{
  "success": false,
  "data": null,
  "errorMessages": [
    "Mensagem de erro"
  ]
}
```

Convencoes:

- Rotas publicas de API usam versionamento no estilo `api/v1`.
- Todos os endpoints de listagem devem ter paginacao.
- Rotas `/internal/*`, `/metrics` e `/health` nao sao publicadas no APIM.
- Validacao de JWT e RBAC acontece nas APIs.

## fcs-identity

Base path:

```text
/api/v1
```

### POST /api/v1/auth/register/donor

Cadastra um **Doador** na aplicacao e no Keycloak.

Acesso:

```text
Publico
```

Request:

```json
{
  "fullName": "Maria Silva",
  "email": "maria@email.com",
  "cpf": "12345678909",
  "password": "StrongPassword123!"
}
```

Response `201 Created`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "fullName": "Maria Silva",
    "email": "maria@email.com",
    "cpf": "***.***.***-09"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
409 Conflict
```

### POST /api/v1/auth/login

Autentica o usuario pela fachada da aplicacao, delegando a validacao de credenciais ao Keycloak.

Acesso:

```text
Publico
```

Request:

```json
{
  "email": "maria@email.com",
  "password": "StrongPassword123!"
}
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "accessToken": "jwt",
    "refreshToken": "token",
    "expiresIn": 300,
    "tokenType": "Bearer"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
401 Unauthorized
```

### POST /api/v1/auth/refresh

Renova tokens usando o refresh token emitido pelo Keycloak.

Acesso:

```text
Publico
```

Request:

```json
{
  "refreshToken": "token"
}
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "accessToken": "jwt",
    "refreshToken": "new-token",
    "expiresIn": 300,
    "tokenType": "Bearer"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
401 Unauthorized
```

### GET /api/v1/me

Retorna o perfil do usuario autenticado, seja **Doador** ou **GestorONG**.

Acesso:

```text
Autenticado
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "keycloakUserId": "uuid-or-string",
    "fullName": "Maria Silva",
    "email": "maria@email.com",
    "role": "Doador"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
401 Unauthorized
404 Not Found
```

### GET /health

Endpoint operacional de saude.

Acesso:

```text
Operacional, fora do APIM
```

### GET /metrics

Endpoint operacional de metricas Prometheus/OpenTelemetry.

Acesso:

```text
Operacional, fora do APIM
```

## fcs-donation-worker

O `fcs-donation-worker` nao possui endpoints de negocio. Ele consome eventos do Kafka e executa processamento em background.

Entradas de negocio:

```text
Kafka topic donation-received
```

Saidas de negocio:

```text
POST /internal/campaigns/{id}/donation-processed
Atualizacao de Donation no DonationsDb
```

### GET /health

Endpoint operacional de saude.

Acesso:

```text
Operacional, fora do APIM
```

### GET /metrics

Endpoint operacional de metricas Prometheus/OpenTelemetry.

Acesso:

```text
Operacional, fora do APIM
```

## fcs-audit-logs

O `fcs-audit-logs` nao possui endpoints de negocio. Ele consome eventos de auditoria do Kafka e persiste os registros em MongoDB.

Entradas de negocio:

```text
Kafka topic audit-log-requested
Event AuditLogRequestedEvent
```

Saidas de negocio:

```text
MongoDB database AuditLogsDb
Collection audit_logs
```

### GET /health

Endpoint operacional de saude.

Acesso:

```text
Operacional, fora do APIM
```

### GET /metrics

Endpoint operacional de metricas Prometheus/OpenTelemetry.

Acesso:

```text
Operacional, fora do APIM
```

## fcs-donations

Base path:

```text
/api/v1
```

### POST /api/v1/donations

Cria uma intencao de doacao.

Acesso:

```text
Doador
```

Request:

```json
{
  "campaignId": "uuid",
  "amount": 100.00
}
```

Response `202 Accepted`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "campaignId": "uuid",
    "donorId": "uuid",
    "amount": 100.00,
    "status": "Pending",
    "createdAt": "2026-05-18T20:00:00Z"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
503 Service Unavailable
```

Regras:

```text
Amount deve ser maior que zero.
A campanha precisa estar apta a receber doacao.
A doacao aceita nasce com status Pending.
A API registra OutboxMessage e nao atualiza o valor arrecadado.
```

### GET /api/v1/donations

Lista doacoes.

Acesso:

```text
Doador
GestorONG
```

Query params:

```text
status
page
pageSize
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": "uuid",
        "campaignId": "uuid",
        "amount": 100.00,
        "status": "Processed",
        "createdAt": "2026-05-18T20:00:00Z",
        "processedAt": "2026-05-18T20:00:10Z"
      }
    ],
    "page": 1,
    "pageSize": 10,
    "totalCount": 1
  },
  "errorMessages": null
}
```

Regras:

```text
Doador lista apenas as proprias doacoes.
GestorONG pode listar doacoes de todos os doadores.
```

### GET /api/v1/donations/{id}

Busca uma doacao.

Acesso:

```text
Doador
GestorONG
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "campaignId": "uuid",
    "donorId": "uuid",
    "amount": 100.00,
    "status": "Processed",
    "createdAt": "2026-05-18T20:00:00Z",
    "processedAt": "2026-05-18T20:00:10Z",
    "failureReason": null
  },
  "errorMessages": null
}
```

Erros esperados:

```text
401 Unauthorized
403 Forbidden
404 Not Found
```

Regras:

```text
Doador so pode consultar doacao propria.
Se a doacao existir mas pertencer a outro Doador, retornar 404 Not Found.
GestorONG pode consultar qualquer doacao.
```

### GET /health

Endpoint operacional de saude.

Acesso:

```text
Operacional, fora do APIM
```

### GET /metrics

Endpoint operacional de metricas Prometheus/OpenTelemetry.

Acesso:

```text
Operacional, fora do APIM
```

## fcs-campaigns

Base path:

```text
/api/v1
```

### POST /api/v1/campaigns

Cria uma campanha.

Acesso:

```text
GestorONG
```

Request:

```json
{
  "title": "Campanha de inverno",
  "description": "Arrecadacao para acolhimento de criancas no inverno.",
  "startDate": "2026-06-01T00:00:00Z",
  "endDate": "2026-07-01T00:00:00Z",
  "financialGoal": 10000.00
}
```

Response `201 Created`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "Campanha de inverno",
    "description": "Arrecadacao para acolhimento de criancas no inverno.",
    "startDate": "2026-06-01T00:00:00Z",
    "endDate": "2026-07-01T00:00:00Z",
    "financialGoal": 10000.00,
    "status": "Active",
    "totalAmountRaised": 0.00,
    "createdByManagerId": "uuid",
    "createdAt": "2026-05-18T20:00:00Z"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
```

Regra:

```text
Toda campanha nova nasce com status Active.
```

### PUT /api/v1/campaigns/{id}

Edita os dados principais de uma campanha.

Acesso:

```text
GestorONG
```

Request:

```json
{
  "title": "Campanha de inverno atualizada",
  "description": "Nova descricao da campanha.",
  "startDate": "2026-06-01T00:00:00Z",
  "endDate": "2026-07-15T00:00:00Z",
  "financialGoal": 12000.00
}
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "Campanha de inverno atualizada",
    "description": "Nova descricao da campanha.",
    "startDate": "2026-06-01T00:00:00Z",
    "endDate": "2026-07-15T00:00:00Z",
    "financialGoal": 12000.00,
    "status": "Active",
    "totalAmountRaised": 0.00,
    "updatedAt": "2026-05-18T20:10:00Z"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
```

Regra:

```text
Apenas campanhas com status Active podem ser editadas.
```

### PATCH /api/v1/campaigns/{id}/status

Atualiza o status da campanha.

Acesso:

```text
GestorONG
```

Request:

```json
{
  "status": "Completed"
}
```

Valores aceitos:

```text
Active
Completed
Canceled
```

Transicoes permitidas:

```text
Active -> Completed
Active -> Canceled
```

Transicoes bloqueadas:

```text
Completed -> Active
Canceled -> Active
Completed -> Canceled
Canceled -> Completed
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "status": "Completed",
    "updatedAt": "2026-05-18T20:15:00Z"
  },
  "errorMessages": null
}
```

Erros esperados:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
```

### GET /api/v1/campaigns

Lista campanhas para gestao.

Acesso:

```text
GestorONG
```

Query params iniciais:

```text
status
page
pageSize
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": "uuid",
        "title": "Campanha de inverno",
        "financialGoal": 10000.00,
        "totalAmountRaised": 1500.00,
        "status": "Active",
        "startDate": "2026-06-01T00:00:00Z",
        "endDate": "2026-07-01T00:00:00Z"
      }
    ],
    "page": 1,
    "pageSize": 10,
    "totalCount": 1
  },
  "errorMessages": null
}
```

### GET /api/v1/campaigns/{id}

Busca uma campanha para gestao.

Acesso:

```text
GestorONG
```

### GET /api/v1/transparency/campaigns

Lista campanhas ativas no **Painel de Transparencia**.

Acesso:

```text
Publico
```

Query params:

```text
page
pageSize
```

Response `200 OK`:

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": "uuid",
        "title": "Campanha de inverno",
        "financialGoal": 10000.00,
        "totalAmountRaised": 1500.00
      }
    ],
    "page": 1,
    "pageSize": 10,
    "totalCount": 1
  },
  "errorMessages": null
}
```

### GET /internal/campaigns/{id}/donation-eligibility

Valida se uma campanha pode receber doacao.

Acesso:

```text
Interno, fora do APIM
```

### POST /internal/campaigns/{id}/donation-processed

Reflete uma doacao processada no valor arrecadado da campanha, de forma idempotente por `donationId`.

Acesso:

```text
Interno, fora do APIM
```

Request:

```json
{
  "donationId": "uuid",
  "amount": 100.00,
  "processedAt": "2026-05-18T20:00:00Z"
}
```

### GET /health

Endpoint operacional de saude.

Acesso:

```text
Operacional, fora do APIM
```

### GET /metrics

Endpoint operacional de metricas Prometheus/OpenTelemetry.

Acesso:

```text
Operacional, fora do APIM
```
