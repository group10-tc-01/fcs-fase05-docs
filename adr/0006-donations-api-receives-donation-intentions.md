# Receber intencoes de doacao na fcg-donations

A `fcg-donations` sera uma API separada para receber **Intencoes de Doacao** de um **Doador** autenticado, validar o pedido e publicar um evento no broker. Ela nao atualiza diretamente o valor total arrecadado da campanha, preservando o fluxo assincrono exigido pelo hackathon.

**Consequencias**

- O endpoint principal sera `POST /donations`.
- O acesso exige role `Doador`.
- A API valida valor maior que zero e se a campanha pode receber doacao.
- Quando a intencao e aceita, a API persiste `Donation` com status `Pending`, registra uma `OutboxMessage` com `DonationReceivedEvent` e retorna `202 Accepted`.
- O banco da `fcg-donations` contem `Donations`, `OutboxMessages` e `ProcessedMessages`.
