# Receber intenções de doação na fcs-donations

A `fcs-donations` será uma API separada para receber **Intenções de Doação** de um **Doador** autenticado, validar o pedido e publicar um evento no broker. Ela não atualiza diretamente o valor total arrecadado da campanha, preservando o fluxo assíncrono exigido pelo hackathon.

**Consequências**

- O endpoint principal será `POST /donations`.
- O acesso exige role `Doador`.
- A API valida valor maior que zero e se a campanha pode receber doação.
- Quando a intenção é aceita, a API persiste `Donation` com status `Pending`, registra uma `OutboxMessage` com `DonationReceivedEvent` e retorna `202 Accepted`.
- O banco da `fcs-donations` contém `Donations`, `OutboxMessages` e `ProcessedMessages`.
