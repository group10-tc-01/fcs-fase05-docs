# Validar elegibilidade da campanha via HTTP

A `fcg-donations` validara se uma **Campanha** pode receber **Intencao de Doacao** chamando a `fcg-campaigns` via HTTP antes de publicar o evento no Kafka. O cliente HTTP sera implementado com Refit e politicas de resiliencia com Polly, mantendo o contrato explicito e tratando falhas transientes entre servicos.

**Opcoes consideradas**

- Consultar a `fcg-campaigns` via HTTP no momento da doacao.
- Manter uma copia local do status da campanha na `fcg-donations` por eventos de campanha.

**Consequencias**

- A `fcg-donations` depende da disponibilidade da `fcg-campaigns` para aceitar novas intencoes de doacao.
- A validacao usa o estado atual da campanha, sem read model duplicado.
- O MVP evita sincronizacao adicional de eventos de campanha.
