# Atualizar campanhas por API interna

A `fcs-donation-worker` não escreverá diretamente no banco da `fcs-campaigns` para atualizar o valor arrecadado. Depois de consumir `DonationReceivedEvent`, o worker chamará uma API interna da `fcs-campaigns`, mantendo o serviço de campanhas como dono das regras e da persistência de campanha.

**Opções consideradas**

- Permitir que o worker escreva diretamente no banco da `fcs-campaigns`.
- Fazer o worker chamar uma API interna da `fcs-campaigns`.

**Consequências**

- A `fcs-campaigns` preserva a propriedade do banco de campanhas.
- O worker precisa de cliente HTTP para a API interna de campanhas.
- A atualização de valor arrecadado fica centralizada no serviço que conhece as regras de campanha.
- O endpoint interno deve ser idempotente por `donationId` para evitar somar a mesma doação mais de uma vez.
