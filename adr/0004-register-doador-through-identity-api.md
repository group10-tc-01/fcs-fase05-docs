# Cadastrar Doador pela API de Identidade

O cadastro público de **Doador** será feito pela `fcs-identity`, que valida os dados recebidos, cria o usuário no Keycloak com a role `Doador` e persiste o perfil de domínio da aplicação. A senha não será armazenada pela aplicação; credenciais, hash e autenticação permanecem sob responsabilidade do Keycloak.

**Opções consideradas**

- Permitir cadastro direto do **Doador** no Keycloak.
- Cadastrar o **Doador** pela `fcs-identity`, mantendo o Keycloak encapsulado.

**Consequências**

- A `fcs-identity` armazena dados como `keycloakUserId`, nome completo, email e CPF.
- A unicidade de CPF pertence ao banco da aplicação.
- A unicidade de email deve ser coerente entre Keycloak e banco da aplicação.
- O `IdentityDb` também mantém `ManagerProfile` para **GestorONG**, embora não exista cadastro público de gestor.
