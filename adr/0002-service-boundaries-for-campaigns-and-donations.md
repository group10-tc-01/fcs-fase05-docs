# Definir aplicações do MVP da fase 5

A plataforma será composta por `fcs-identity`, `fcs-campaigns`, `fcs-donations`, `fcs-donation-worker`, `fcs-audit-logs`, `fcs-bff` e `fcs-web`. Essa divisão deixa explícitas as capacidades de identidade, administração de campanhas, recebimento de intenções de doação, processamento assíncrono, auditoria centralizada e interface web, além de atender ao requisito de pelo menos dois microsserviços distintos.

**Opções consideradas**

- Concentrar campanhas e doações em uma única API de domínio com um worker separado.
- Separar identidade, campanhas, doações, worker de doações, worker de auditoria, BFF e frontend em aplicações nomeadas.

**Consequências**

- `fcs-identity` encapsula a integração com Keycloak e os perfis da aplicação.
- `fcs-campaigns` fica responsável por campanhas e painel público de transparência.
- `fcs-donations` fica responsável por receber intenções de doação.
- `fcs-donation-worker` fica responsável pelo processamento assíncrono das doações.
- `fcs-audit-logs` fica responsável por consumir eventos de auditoria e persistir em MongoDB.
- `fcs-bff` fica responsável por agregar e adaptar contratos para a experiência web.
- `fcs-web` fica responsável pela experiência web.
