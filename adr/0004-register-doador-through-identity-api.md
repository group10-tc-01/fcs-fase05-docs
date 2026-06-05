# Cadastrar Doador pela API de Identidade

O cadastro publico de **Doador** sera feito pela `fcs-identity`, que valida os dados recebidos, cria o usuario no Keycloak com a role `Doador` e persiste o perfil de dominio da aplicacao. A senha nao sera armazenada pela aplicacao; credenciais, hash e autenticacao permanecem sob responsabilidade do Keycloak.

**Opcoes consideradas**

- Permitir cadastro direto do **Doador** no Keycloak.
- Cadastrar o **Doador** pela `fcs-identity`, mantendo o Keycloak encapsulado.

**Consequencias**

- A `fcs-identity` armazena dados como `keycloakUserId`, nome completo, email e CPF.
- A unicidade de CPF pertence ao banco da aplicacao.
- A unicidade de email deve ser coerente entre Keycloak e banco da aplicacao.
- O `IdentityDb` tambem mantem `ManagerProfile` para **GestorONG**, embora nao exista cadastro publico de gestor.
