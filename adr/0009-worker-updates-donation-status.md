# Atualizar status da doacao pelo worker

A `fcg-donation-worker` atualizara o status da `Donation` no banco da `fcg-donations` apos consumir `DonationReceivedEvent`. Isso fecha a rastreabilidade da doacao, permitindo diferenciar intencoes pendentes, processadas e com falha.

**Consequencias**

- A `fcg-donation-worker` precisa de acesso de escrita ao banco da `fcg-donations`.
- O status tecnico da doacao segue `Pending`, `Processed` ou `Failed`.
- `ProcessedMessages` apoia idempotencia para evitar reprocessamento da mesma mensagem.
