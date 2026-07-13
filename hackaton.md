# Hackathon — Conexão Solidária

## Requisitos funcionais

- JWT com papéis `GestorONG` e `Doador`.
- Gestão de campanhas por GestorONG, incluindo validação de período e meta financeira.
- Cadastro público de Doador com dados únicos e senha protegida.
- Painel público com campanhas ativas, meta e valor arrecadado.
- Intenção de doação por Doador autenticado, bloqueada para campanhas encerradas ou canceladas.

## Requisitos técnicos

- Microsserviços para identidade, campanhas, doações e processamento assíncrono.
- Kafka para `DonationReceivedEvent`; o worker atualiza a campanha após o consumo.
- Kubernetes com Deployments, Services e ConfigMaps.
- `/health` ou `/metrics` e dashboard Datadog com dados reais.
- GitHub Actions executado a cada push na principal, compilando e gerando imagem Docker.

## Evidências de entrega

- Diagrama em [architecture/overview.md](architecture/overview.md).
- Justificativa de SQL Server e MongoDB nas ADRs e READMEs dos serviços.
- Instruções locais em cada repositório e ambiente integrado em `fcs-vps-infra-guide`.
- Demonstração: CI, pods K3s, dashboard Datadog, autenticação, campanha, doação, Kafka e atualização da transparência.
