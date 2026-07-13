# Rodar Kafka dentro do Kubernetes

Kafka e Kafka UI rodam no K3s, no namespace `fcs-infra`, para atender os tópicos `donation-received` e `audit-log-requested`.

## Consequências

- A plataforma configura persistência, requests e limits para o MVP.
- Kafka UI auxilia a demonstração e a operação sem ser exposta sem controle administrativo.
