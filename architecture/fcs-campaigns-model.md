# Modelo da fcs-campaigns

A `fcs-campaigns` e dona das **Campanhas**, do **Painel de Transparencia** e da atualizacao idempotente do valor arrecadado quando uma doacao e processada.

## Entidades

### Campaign

Representa uma campanha de arrecadacao administrada por um **GestorONG**.

- `Id`
- `Title`
- `Description`
- `StartDate`
- `EndDate`
- `FinancialGoal`
- `Status`: `Active`, `Completed`, `Canceled`
- `TotalAmountRaised`
- `CreatedByManagerId`
- `CreatedAt`
- `UpdatedAt`

### CampaignDonationEntry

Representa uma doacao ja refletida no valor arrecadado de uma campanha.

- `Id`
- `CampaignId`
- `DonationId`
- `Amount`
- `ProcessedAt`

Restricao de unicidade:

```text
CampaignId + DonationId
```

## Endpoints principais

Administrativos:

```text
POST   /campaigns
PUT    /campaigns/{id}
PATCH  /campaigns/{id}/status
GET    /campaigns
GET    /campaigns/{id}
```

Publicos:

```text
GET /transparency/campaigns
```

Internos:

```text
GET  /internal/campaigns/{id}/donation-eligibility
POST /internal/campaigns/{id}/donation-processed
```

Payload de `POST /internal/campaigns/{id}/donation-processed`:

```json
{
  "donationId": "uuid",
  "amount": 100.00,
  "processedAt": "2026-05-18T20:00:00Z"
}
```

## Regras confirmadas

- Apenas `GestorONG` pode criar, editar, concluir ou cancelar campanhas.
- `CreatedByManagerId` referencia o gestor criador sem foreign key para o `IdentityDb`.
- Toda campanha nova nasce com status `Active`.
- Apenas campanhas com status `Active` podem ser editadas.
- Transicoes de status permitidas: `Active -> Completed` e `Active -> Canceled`.
- Campanhas `Completed` ou `Canceled` nao podem voltar para `Active` nem trocar entre si.
- O painel publico lista apenas campanhas com status `Active` e usa paginacao.
- As listagens retornam `items`, `page`, `pageSize` e `totalCount`.
- A listagem administrativa aceita filtros de status repetidos e ignora valores duplicados.
- `page` menor que 1 e normalizado para 1; `pageSize` fora de 1 a 100 e normalizado para 10.
- `EndDate` nao pode estar no passado.
- `FinancialGoal` deve ser maior que zero.
- Uma campanha com status `Completed` ou `Canceled` nao pode receber doacao.
- O endpoint interno de doacao processada e idempotente por `DonationId`.
