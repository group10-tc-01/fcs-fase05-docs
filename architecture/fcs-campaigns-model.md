# Modelo da fcs-campaigns

A `fcs-campaigns` é dona das **Campanhas**, do **Painel de Transparência** e da atualização idempotente do valor arrecadado quando uma doação é processada.

## Entidades

### Campaign

Representa uma campanha de arrecadação administrada por um **GestorONG**.

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

Representa uma doação já refletida no valor arrecadado de uma campanha.

- `Id`
- `CampaignId`
- `DonationId`
- `Amount`
- `ProcessedAt`

Restrição de unicidade:

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

Públicos:

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
- Transições de status permitidas: `Active -> Completed` e `Active -> Canceled`.
- Campanhas `Completed` ou `Canceled` não podem voltar para `Active` nem trocar entre si.
- O painel público lista apenas campanhas com status `Active` e usa paginação.
- As listagens retornam `items`, `page`, `pageSize` e `totalCount`.
- A listagem administrativa aceita filtros de status repetidos e ignora valores duplicados.
- `page` menor que 1 é normalizado para 1; `pageSize` fora de 1 a 100 é normalizado para 10.
- `EndDate` não pode estar no passado.
- `FinancialGoal` deve ser maior que zero.
- Uma campanha com status `Completed` ou `Canceled` não pode receber doação.
- O endpoint interno de doação processada é idempotente por `DonationId`.
