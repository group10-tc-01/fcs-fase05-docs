# Validar elegibilidade da campanha via HTTP

A `fcs-donations` validará se uma **Campanha** pode receber **Intenção de Doação** chamando a `fcs-campaigns` via HTTP antes de publicar o evento no Kafka. O cliente HTTP será implementado com Refit e políticas de resiliência com Polly, mantendo o contrato explícito e tratando falhas transientes entre serviços.

**Opções consideradas**

- Consultar a `fcs-campaigns` via HTTP no momento da doação.
- Manter uma cópia local do status da campanha na `fcs-donations` por eventos de campanha.

**Consequências**

- A `fcs-donations` depende da disponibilidade da `fcs-campaigns` para aceitar novas intenções de doação.
- A validação usa o estado atual da campanha, sem read model duplicado.
- O MVP evita sincronização adicional de eventos de campanha.
