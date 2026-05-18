# Rodar Prometheus e Grafana dentro do Kubernetes

A observabilidade do MVP usara Prometheus e Grafana rodando dentro do Kubernetes. Essa escolha deixa a demonstracao autocontida, evita dependencia de conta externa e permite mostrar metricas reais do cluster e das aplicacoes durante o video.

**Opcoes consideradas**

- Rodar Prometheus e Grafana dentro do Kubernetes.
- Usar Grafana Cloud com agente de coleta no cluster.

**Consequencias**

- O `fcg-solidarity-infra` deve conter manifests ou Helm values para Prometheus e Grafana.
- A demo pode usar `kubectl port-forward` para acessar o Grafana.
- Os servicos devem expor `/health` e, quando aplicavel, `/metrics`.
- O dashboard minimo deve mostrar CPU/memoria dos pods e contagem de requisicoes HTTP.
