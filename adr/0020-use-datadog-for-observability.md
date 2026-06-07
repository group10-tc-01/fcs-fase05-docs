# Usar Datadog para observabilidade

A observabilidade do MVP usara Datadog como plataforma central para metricas, dashboards, logs e APM. Essa escolha permite mostrar metricas reais do cluster e das aplicacoes durante o video, alem de adicionar visibilidade de performance das APIs, worker e chamadas entre servicos.

**Opcoes consideradas**

- Usar Datadog Agent e Cluster Agent no Kubernetes.
- Rodar uma stack open source de observabilidade dentro do Kubernetes.
- Usar uma plataforma SaaS de observabilidade equivalente.

**Consequencias**

- O `fcs-infra` deve conter manifests ou Helm values para Datadog Agent e Cluster Agent.
- A demo deve acessar os dashboards do Datadog para visualizar metricas reais.
- Os servicos devem expor `/health` e, quando aplicavel, `/metrics`.
- Os servicos .NET devem ser instrumentados com OpenTelemetry ou Datadog APM.
- O dashboard minimo deve mostrar CPU/memoria dos pods e contagem de requisicoes HTTP.
