# Atualizar status da doação pelo worker

A `fcs-donation-worker` atualizará o status da `Donation` no banco da `fcs-donations` após consumir `DonationReceivedEvent`. Isso fecha a rastreabilidade da doação, permitindo diferenciar intenções pendentes, processadas e com falha.

**Consequências**

- A `fcs-donation-worker` precisa de acesso de escrita ao banco da `fcs-donations`.
- O status técnico da doação segue `Pending`, `Processed` ou `Failed`.
- `ProcessedMessages` apoia idempotência para evitar reprocessamento da mesma mensagem.
