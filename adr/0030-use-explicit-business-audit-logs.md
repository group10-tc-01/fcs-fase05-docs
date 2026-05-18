# Usar auditoria explicita por eventos de negocio

A fase 5 usara auditoria explicita por eventos de negocio em vez de auditoria automatica baseada no ChangeTracker do Entity Framework. Cada servico persistira registros em `AuditLogs` nos casos de uso relevantes, com acao, entidade, ator, correlacao e metadados, evitando auditoria magica e pouco expressiva.

**Opcoes consideradas**

- Reutilizar auditoria automatica do `fcg-users` com interceptor de EF, `OldValues`, `NewValues` e `ChangedColumns`.
- Criar tabelas especificas para cada historico de dominio.
- Usar uma tabela `AuditLogs` por servico, gravada explicitamente pelos casos de uso.

**Consequencias**

- Cada servico decide quais eventos de negocio merecem auditoria.
- A auditoria fica mais legivel para demonstracao e investigacao.
- O dominio nao precisa carregar uma entidade tecnica de auditoria generica.
- Mudancas puramente tecnicas nao sao auditadas automaticamente, a menos que um caso de uso registre o evento.
