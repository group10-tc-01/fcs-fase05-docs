# Rodar Keycloak dentro do Kubernetes

O Keycloak rodara dentro do Kubernetes nos ambientes local e AKS, usando `KeycloakDb` no banco SQL gerenciado na Azure. A configuracao de realm, clients e roles ficara no `fcs-solidarity-infra`, sem versionar segredos reais.

**Consequencias**

- O AKS executa o Keycloak junto dos servicos da plataforma.
- O banco do Keycloak fica fora do cluster no ambiente Azure.
- O `fcs-solidarity-infra` deve conter configuracoes de realm, clients e roles `GestorONG` e `Doador`.
- A `fcs-identity` continua sendo a fachada usada pelos clientes para login, refresh e cadastro.
