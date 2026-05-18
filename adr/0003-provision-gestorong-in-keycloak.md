# Provisionar GestorONG no Keycloak

O **GestorONG** sera provisionado administrativamente por seed da `fcg-identity`, sem endpoint publico de cadastro na aplicacao. Mesmo sendo criado administrativamente, ele tambem tera um `ManagerProfile` no `IdentityDb`, porque a `fcg-identity` mantem os perfis de dominio dos atores autenticaveis.

**Opcoes consideradas**

- Criar endpoint na `fcg-identity` para cadastrar **GestorONG**.
- Provisionar **GestorONG** por seed da `fcg-identity`, criando ou encontrando o usuario no Keycloak e sincronizando o `ManagerProfile`.

**Consequencias**

- A demonstracao usa um **GestorONG** previamente criado para obter JWT e administrar campanhas.
- A `fcg-identity` nao expõe cadastro publico de gestor.
- O fluxo publico de cadastro atende apenas ao **Doador**.
- O seed da `fcg-identity` deve criar ou encontrar o usuario no Keycloak, garantir a role `GestorONG` e criar ou atualizar o `ManagerProfile` no `IdentityDb`.
