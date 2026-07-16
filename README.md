# Conexão Solidária — documentação da entrega

Este repositório é a fonte de verdade arquitetural da Fase 05. Os READMEs dos serviços apontam para estes documentos, sem duplicar contratos, decisões e cenários de falha.

## Navegação

- [Visão geral da arquitetura](architecture/overview.md)
- [Repositórios e infraestrutura](architecture/repositories-and-infra.md)
- [Fluxos de endpoints e workers](architecture/endpoint-flows.md)
- [Modelos de identidade, campanhas, doações e banco](architecture/)
- [ADRs](adr/)

## Uso e contribuição

Mantenha nomes de repositório, contratos, diagramas e ADRs consistentes com a implementação. Novas decisões arquiteturais devem receber um ADR sequencial; mudanças de comportamento devem atualizar primeiro os modelos e fluxos relevantes antes dos READMEs consumidores.

## Ambiente integrado

A plataforma oficial utiliza VPS Hostinger com K3s, Traefik, GHCR, Infisical e Datadog. O `fcs-infra` mantém a plataforma compartilhada; cada aplicação mantém seu deployment e pipeline.
