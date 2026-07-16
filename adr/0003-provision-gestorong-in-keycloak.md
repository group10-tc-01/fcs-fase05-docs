# Provisionar GestorONG no Keycloak

O **GestorONG** será provisionado administrativamente por seed da `fcs-identity`, sem endpoint público de cadastro na aplicação. Mesmo sendo criado administrativamente, ele também terá um `ManagerProfile` no `IdentityDb`, porque a `fcs-identity` mantém os perfis de domínio dos atores autenticáveis.

**Opções consideradas**

- Criar endpoint na `fcs-identity` para cadastrar **GestorONG**.
- Provisionar **GestorONG** por seed da `fcs-identity`, criando ou encontrando o usuário no Keycloak e sincronizando o `ManagerProfile`.

**Consequências**

- A demonstração usa um **GestorONG** previamente criado para obter JWT e administrar campanhas.
- A `fcs-identity` não expõe cadastro público de gestor.
- O fluxo público de cadastro atende apenas ao **Doador**.
- O seed da `fcs-identity` deve criar ou encontrar o usuário no Keycloak, garantir a role `GestorONG` e criar ou atualizar o `ManagerProfile` no `IdentityDb`.
