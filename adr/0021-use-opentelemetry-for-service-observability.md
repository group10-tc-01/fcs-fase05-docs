# Usar OpenTelemetry nos servicos

Os servicos .NET usarao OpenTelemetry para expor metricas de aplicacao e infraestrutura via endpoint `/metrics`, alem de health checks em `/health`. O Prometheus coletara essas metricas e o Grafana exibira dashboards com dados reais do cluster e das aplicacoes.

**Consequencias**

- APIs e worker devem configurar OpenTelemetry.
- APIs devem instrumentar requisicoes HTTP recebidas.
- Clientes HTTP entre servicos devem ser instrumentados.
- O worker deve expor metricas de processamento de eventos quando possivel.
- O `fcs-infra` deve configurar Prometheus para scrape dos servicos.
