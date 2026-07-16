# Usar OpenTelemetry nos serviços

Os serviços .NET usarão OpenTelemetry para instrumentar métricas, traces e chamadas HTTP, além de health checks em `/health`. O Datadog Agent coletará métricas do Kubernetes e receberá a telemetria das aplicações para exibição em dashboards e APM.

**Consequências**

- APIs e worker devem configurar OpenTelemetry.
- APIs devem instrumentar requisições HTTP recebidas.
- Clientes HTTP entre serviços devem ser instrumentados.
- O worker deve expor métricas de processamento de eventos quando possível.
- O `fcs-infra` deve configurar Datadog Agent e Cluster Agent para coleta de métricas do cluster e das aplicações.
