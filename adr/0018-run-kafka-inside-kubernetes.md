# Rodar Kafka dentro do Kubernetes

O Kafka rodara dentro do Kubernetes nos ambientes local e AKS, acompanhado por Kafka UI para apoio operacional e demonstracao. Essa escolha simplifica a paridade entre ambiente local e Azure e facilita mostrar topicos e mensagens no video do hackathon.

**Opcoes consideradas**

- Rodar Kafka dentro do Kubernetes.
- Usar um servico Kafka gerenciado fora do cluster.

**Consequencias**

- O `fcg-solidarity-infra` deve conter manifests ou Helm values para Kafka e Kafka UI.
- O video de demonstracao pode usar Kafka UI para mostrar o topico `donation-received`.
- O cluster passa a executar tambem o broker, entao requests/limits e persistencia precisam ser configurados de forma adequada para o MVP.
