# Rodar Keycloak dentro do Kubernetes

Keycloak roda no K3s com banco dedicado no SQL Server. Realm, clients e roles são bootstrapados pela plataforma; a `fcs-identity` é a fachada para login, refresh e cadastro.

## Consequências

- Keycloak não recebe Ingress público por padrão.
- Credenciais de administração ficam no Infisical, não nos manifests.
