# Usar Datadog para observabilidade

A observabilidade do MVP usará Datadog como plataforma central para métricas, dashboards, logs e APM. Essa escolha permite mostrar métricas reais do cluster e das aplicações durante o vídeo, além de adicionar visibilidade de performance das APIs, worker e chamadas entre serviços.

**Opções consideradas**

- Usar Datadog Agent e Cluster Agent no Kubernetes.
- Rodar uma stack open source de observabilidade dentro do Kubernetes.
- Usar uma plataforma SaaS de observabilidade equivalente.

**Consequências**

- O `fcs-infra` deve conter manifests ou Helm values para Datadog Agent e Cluster Agent.
- A demonstração deve acessar os dashboards do Datadog para visualizar métricas reais.
- Os serviços devem expor `/health` e, quando aplicável, `/metrics`.
- Os serviços .NET devem ser instrumentados com OpenTelemetry ou Datadog APM.
- O dashboard mínimo deve mostrar CPU/memória dos pods e contagem de requisições HTTP.
