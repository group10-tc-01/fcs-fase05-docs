# Usar banco SQL gerenciado fora do AKS na Azure

No ambiente local, os bancos SQL Server poderao rodar em container para simplificar a execucao. Na Azure, os databases relacionais da plataforma ficarao fora do AKS em um servico SQL gerenciado, mantendo persistencia e operacao de banco separadas do cluster Kubernetes.

O MongoDB usado pelo `fcs-audit-logs` e tratado separadamente da decisao de SQL Server, por ser o storage especializado dos logs de auditoria.

**Opcoes consideradas**

- Rodar SQL Server em container dentro do AKS.
- Usar banco SQL gerenciado fora do AKS na Azure.

**Consequencias**

- O AKS executa as aplicacoes e componentes de plataforma, mas nao e responsavel por hospedar os bancos relacionais gerenciados.
- O Terraform deve provisionar o recurso SQL gerenciado e os databases `IdentityDb`, `CampaignsDb`, `DonationsDb` e `KeycloakDb`.
- O Terraform deve provisionar ou referenciar o MongoDB usado pelo `AuditLogsDb`, conforme o ambiente escolhido.
- Connection strings devem ser tratadas como segredo e nao versionadas em manifests.
- O ambiente local continua podendo usar SQL Server e MongoDB em containers via docker compose.
