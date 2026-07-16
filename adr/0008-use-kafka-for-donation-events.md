# Usar Kafka para eventos de doação

A plataforma usará Kafka como broker de mensageria para o fluxo assíncrono de doações. A `fcs-donations` publicará `DonationReceivedEvent` em um tópico Kafka e a `fcs-donation-worker` consumirá esse evento para processar a doação e refletir o valor arrecadado.

**Opções consideradas**

- Usar RabbitMQ.
- Usar Kafka.

**Consequências**

- O ambiente local e Kubernetes devem incluir Kafka.
- A demonstração deve mostrar o tópico Kafka e as mensagens usando uma interface como Kafka UI.
- O contrato do evento `DonationReceivedEvent` passa a ser parte importante da integração entre API e worker.
- O tópico inicial será `donation-received`.
- O payload mínimo do evento contém `eventId`, `donationId`, `campaignId`, `donorId`, `amount` e `occurredAt`.
