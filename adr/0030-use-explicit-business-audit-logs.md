# Centralizar auditoria explicita no fcg-audit-logs

A fase 5 usara auditoria explicita por eventos de negocio e seguranca em vez de auditoria automatica baseada no ChangeTracker do Entity Framework. Cada aplicacao decide, dentro dos seus casos de uso relevantes, quais eventos devem ser auditados e publica uma mensagem no topico Kafka `audit-log-requested`.

O novo worker `fcg-audit-logs` consome esse topico e persiste os registros em MongoDB. Os bancos relacionais dos servicos (`IdentityDb`, `CampaignsDb` e `DonationsDb`) nao mantem tabela `AuditLogs`.

Para este fluxo de auditoria nao sera usado outbox. A publicacao do evento de auditoria e responsabilidade da aplicacao que executou o caso de uso, preferencialmente por uma abstracao como `IAuditRecorder` ou `IAuditPublisher`, sem acoplar os handlers diretamente ao cliente Kafka.

**Opcoes consideradas**

- Reutilizar auditoria automatica do `fcg-users` com interceptor de EF, `OldValues`, `NewValues` e `ChangedColumns`.
- Criar tabelas especificas para cada historico de dominio.
- Usar uma tabela `AuditLogs` por servico, gravada explicitamente pelos casos de uso.
- Centralizar eventos explicitos em `fcg-audit-logs`, recebendo mensagens Kafka e persistindo em MongoDB.

**Consequencias**

- Cada aplicacao continua decidindo quais eventos de negocio e seguranca merecem auditoria.
- O envio do evento de auditoria ocorre em cada aplicacao, no caso de uso que conhece o significado do evento.
- O `fcg-audit-logs` nao decide o que e auditavel; ele apenas consome, valida, torna idempotente e persiste.
- A auditoria fica centralizada, com schema flexivel para metadados variaveis por acao.
- Mudancas puramente tecnicas nao sao auditadas automaticamente, a menos que um caso de uso registre o evento.
- Como nao ha outbox para auditoria, falhas na publicacao para Kafka podem causar perda do evento de auditoria. As aplicacoes devem registrar logs tecnicos e metricas para falhas de publicacao.
- Senhas, tokens, refresh tokens e segredos nao podem ser publicados em metadados de auditoria.
