# Manter o Keycloak atras da API de Identidade

A plataforma usara o Keycloak como provedor de identidade para credenciais, hash de senha, emissao de JWT e roles de RBAC, mas os clientes nao chamarao o Keycloak diretamente. A API `fcg-identity` atuara como fachada de identidade da aplicacao, expondo endpoints de cadastro e login enquanto delega operacoes de credencial ao Keycloak e armazena apenas dados de perfil da aplicacao, como CPF e `keycloakUserId`.

**Opcoes consideradas**

- Permitir que o cliente chame o Keycloak diretamente via OpenID Connect.
- Encapsular o Keycloak atras da `fcg-identity` e mante-lo como infraestrutura interna.

**Consequencias**

- O frontend, o Swagger e as demonstracoes no Postman usam endpoints da aplicacao em vez de endpoints do Keycloak.
- A `fcg-identity` deve intermediar os fluxos de login e refresh e retornar tokens emitidos pelo Keycloak.
- Os endpoints minimos da `fcg-identity` incluem `POST /auth/register/donor`, `POST /auth/login`, `POST /auth/refresh` e `GET /me`.
- O RBAC do MVP usara apenas as roles `GestorONG` e `Doador`.
- Os servicos da aplicacao validam JWTs emitidos pelo Keycloak, mas nao sao donos do armazenamento de senha nem da validacao de credenciais.
