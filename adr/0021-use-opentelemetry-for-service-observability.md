# Usar OpenTelemetry nos servicos

Os servicos .NET usarao OpenTelemetry para instrumentar metricas, traces e chamadas HTTP, alem de health checks em `/health`. O Datadog Agent coletara metricas do Kubernetes e recebera a telemetria das aplicacoes para exibicao em dashboards e APM.

**Consequencias**

- APIs e worker devem configurar OpenTelemetry.
- APIs devem instrumentar requisicoes HTTP recebidas.
- Clientes HTTP entre servicos devem ser instrumentados.
- O worker deve expor metricas de processamento de eventos quando possivel.
- O `fcs-infra` deve configurar Datadog Agent e Cluster Agent para coleta de metricas do cluster e das aplicacoes.
