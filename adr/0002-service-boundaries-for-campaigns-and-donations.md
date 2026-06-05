# Definir aplicacoes do MVP da fase 5

A plataforma sera composta por `fcs-identity`, `fcs-campaigns`, `fcs-donations`, `fcs-donation-worker`, `fcs-audit-logs` e `fcs-solidarity-web`. Essa divisao deixa explicitas as capacidades de identidade, administracao de campanhas, recebimento de intencoes de doacao, processamento assincrono, auditoria centralizada e interface web, alem de atender ao requisito de pelo menos dois microsservicos distintos.

**Opcoes consideradas**

- Concentrar campanhas e doacoes em uma unica API de dominio com um worker separado.
- Separar identidade, campanhas, doacoes, worker de doacoes, worker de auditoria e frontend em aplicacoes nomeadas.

**Consequencias**

- `fcs-identity` encapsula a integracao com Keycloak e os perfis da aplicacao.
- `fcs-campaigns` fica responsavel por campanhas e painel publico de transparencia.
- `fcs-donations` fica responsavel por receber intencoes de doacao.
- `fcs-donation-worker` fica responsavel pelo processamento assincrono das doacoes.
- `fcs-audit-logs` fica responsavel por consumir eventos de auditoria e persistir em MongoDB.
- `fcs-solidarity-web` fica responsavel pela experiencia web.
