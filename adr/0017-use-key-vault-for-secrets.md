# Usar Azure Key Vault para segredos

Os segredos do ambiente Azure serao armazenados no Azure Key Vault e consumidos pelo AKS por identidade gerenciada, preferencialmente com CSI driver. Connection strings, credenciais administrativas e client secrets nao devem ser versionados em manifests Kubernetes ou arquivos Terraform.

**Consequencias**

- O Terraform deve provisionar Key Vault e permissoes de acesso para o AKS.
- Os manifests Kubernetes devem referenciar segredos sem expor valores sensiveis.
- O ambiente local pode usar `.env` de exemplo, mas valores reais devem ficar fora do repositorio.
- Purge protection do Key Vault nao deve ser desabilitado.
