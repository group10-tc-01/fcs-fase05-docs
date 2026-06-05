# Atualizar status da doacao pelo worker

A `fcs-donation-worker` atualizara o status da `Donation` no banco da `fcs-donations` apos consumir `DonationReceivedEvent`. Isso fecha a rastreabilidade da doacao, permitindo diferenciar intencoes pendentes, processadas e com falha.

**Consequencias**

- A `fcs-donation-worker` precisa de acesso de escrita ao banco da `fcs-donations`.
- O status tecnico da doacao segue `Pending`, `Processed` ou `Failed`.
- `ProcessedMessages` apoia idempotencia para evitar reprocessamento da mesma mensagem.
