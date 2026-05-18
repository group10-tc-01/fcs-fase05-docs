# Atualizar campanhas por API interna

A `fcg-donation-worker` nao escrevera diretamente no banco da `fcg-campaigns` para atualizar o valor arrecadado. Depois de consumir `DonationReceivedEvent`, o worker chamara uma API interna da `fcg-campaigns`, mantendo o servico de campanhas como dono das regras e da persistencia de campanha.

**Opcoes consideradas**

- Permitir que o worker escreva diretamente no banco da `fcg-campaigns`.
- Fazer o worker chamar uma API interna da `fcg-campaigns`.

**Consequencias**

- A `fcg-campaigns` preserva a propriedade do banco de campanhas.
- O worker precisa de cliente HTTP para a API interna de campanhas.
- A atualizacao de valor arrecadado fica centralizada no servico que conhece as regras de campanha.
- O endpoint interno deve ser idempotente por `donationId` para evitar somar a mesma doacao mais de uma vez.
