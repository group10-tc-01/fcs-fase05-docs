# Manter o Keycloak atrás da API de Identidade

A plataforma usará o Keycloak como provedor de identidade para credenciais, hash de senha, emissão de JWT e roles de RBAC, mas os clientes não chamarão o Keycloak diretamente. A API `fcs-identity` atuará como fachada de identidade da aplicação, expondo endpoints de cadastro e login enquanto delega operações de credencial ao Keycloak e armazena apenas dados de perfil da aplicação, como CPF e `keycloakUserId`.

**Opções consideradas**

- Permitir que o cliente chame o Keycloak diretamente via OpenID Connect.
- Encapsular o Keycloak atrás da `fcs-identity` e mantê-lo como infraestrutura interna.

**Consequências**

- O frontend, o Swagger e as demonstrações no Postman usam endpoints da aplicação em vez de endpoints do Keycloak.
- A `fcs-identity` deve intermediar os fluxos de login e refresh e retornar tokens emitidos pelo Keycloak.
- Os endpoints minimos da `fcs-identity` incluem `POST /auth/register/donor`, `POST /auth/login`, `POST /auth/refresh` e `GET /me`.
- O RBAC do MVP usará apenas as roles `GestorONG` e `Doador`.
- Os serviços da aplicação validam JWTs emitidos pelo Keycloak, mas não são donos do armazenamento de senha nem da validação de credenciais.
