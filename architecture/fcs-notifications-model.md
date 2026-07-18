# fcs-notifications

O `fcs-notifications` recebe pedidos de e-mail transacional no tópico Kafka `email-notification-requested` e não possui banco próprio.

## Contrato

```json
{
  "eventId": "11111111-1111-1111-1111-111111111111",
  "type": "DonationProcessed",
  "recipientEmail": "doador@example.com",
  "donationId": "22222222-2222-2222-2222-222222222222",
  "amount": 100.00,
  "occurredAt": "2026-07-16T12:00:00Z"
}
```

Tipos: `DonorWelcome`, `DonationCreated` e `DonationProcessed`. O e-mail não deve ser incluído em logs ou auditoria.

O worker confirma o offset somente depois de o Resend aceitar a mensagem. Falhas transitórias permanecem para retry; payload inválido e falhas permanentes são descartados com log seguro. O header `Idempotency-Key` tem o formato `notification/<eventId>`.

Os conteúdos são templates hospedados no Resend. O ambiente configura os IDs de boas-vindas, doação criada e doação processada; os dois últimos recebem `donation_id` e `amount` como variáveis.
