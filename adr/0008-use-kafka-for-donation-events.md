# Usar Kafka para eventos de doacao

A plataforma usara Kafka como broker de mensageria para o fluxo assincrono de doacoes. A `fcs-donations` publicara `DonationReceivedEvent` em um topico Kafka e a `fcs-donation-worker` consumira esse evento para processar a doacao e refletir o valor arrecadado.

**Opcoes consideradas**

- Usar RabbitMQ.
- Usar Kafka.

**Consequencias**

- O ambiente local e Kubernetes devem incluir Kafka.
- A demonstracao deve mostrar o topico Kafka e as mensagens usando uma interface como Kafka UI.
- O contrato do evento `DonationReceivedEvent` passa a ser parte importante da integracao entre API e worker.
- O topico inicial sera `donation-received`.
- O payload minimo do evento contem `eventId`, `donationId`, `campaignId`, `donorId`, `amount` e `occurredAt`.
