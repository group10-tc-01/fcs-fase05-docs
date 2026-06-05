# Usar Azure Container Registry para imagens

As imagens Docker das aplicacoes serao publicadas no Azure Container Registry (ACR) e consumidas pelo AKS. Cada repositorio de aplicacao tera pipeline proprio para compilar, gerar a imagem e publicar no registry.

**Consequencias**

- O Terraform do `fcs-solidarity-infra` deve provisionar o ACR.
- O AKS precisa de permissao para fazer pull das imagens do ACR.
- O ACR nao deve permitir pull anonimo.
- Os pipelines dos repositorios de aplicacao devem versionar as tags das imagens de forma rastreavel.
