# Centralizar auditoria explícita no fcs-audit-logs

A fase 5 usará auditoria explícita por eventos de negócio e segurança em vez de auditoria automática baseada no ChangeTracker do Entity Framework. Cada aplicação decide, dentro dos seus casos de uso relevantes, quais eventos devem ser auditados e publica uma mensagem no tópico Kafka `audit-log-requested`.

O novo worker `fcs-audit-logs` consome esse tópico e persiste os registros em MongoDB. Os bancos relacionais dos serviços (`IdentityDb`, `CampaignsDb` e `DonationsDb`) não mantêm tabela `AuditLogs`.

Para este fluxo de auditoria não será usado outbox. A publicação do evento de auditoria é responsabilidade da aplicação que executou o caso de uso, preferencialmente por uma abstração como `IAuditRecorder` ou `IAuditPublisher`, sem acoplar os handlers diretamente ao cliente Kafka.

**Opções consideradas**

- Reutilizar auditoria automática do `fcs-users` com interceptor de EF, `OldValues`, `NewValues` e `ChangedColumns`.
- Criar tabelas específicas para cada histórico de domínio.
- Usar uma tabela `AuditLogs` por serviço, gravada explicitamente pelos casos de uso.
- Centralizar eventos explícitos em `fcs-audit-logs`, recebendo mensagens Kafka e persistindo em MongoDB.

**Consequências**

- Cada aplicação continua decidindo quais eventos de negócio e segurança merecem auditoria.
- O envio do evento de auditoria ocorre em cada aplicação, no caso de uso que conhece o significado do evento.
- O `fcs-audit-logs` não decide o que é auditável; ele apenas consome, valida, torna idempotente e persiste.
- A auditoria fica centralizada, com schema flexível para metadados variáveis por ação.
- Mudanças puramente técnicas não são auditadas automaticamente, a menos que um caso de uso registre o evento.
- Como não há outbox para auditoria, falhas na publicação para Kafka podem causar perda do evento de auditoria. As aplicações devem registrar logs técnicos e métricas para falhas de publicação.
- Senhas, tokens, refresh tokens e segredos não podem ser publicados em metadados de auditoria.
