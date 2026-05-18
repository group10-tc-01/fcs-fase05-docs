# Usar AKS como alvo Kubernetes na Azure

A plataforma usara Azure Kubernetes Service (AKS) como alvo de deploy Kubernetes na Azure. O `fcg-solidarity-infra` tera Terraform para provisionar os recursos Azure necessarios e manifests Kubernetes integrados para executar as aplicacoes e componentes de plataforma.

**Consequencias**

- O Terraform do `fcg-solidarity-infra` deve provisionar AKS e recursos auxiliares.
- As imagens Docker das aplicacoes devem ser publicadas no Azure Container Registry.
- Os manifests Kubernetes devem ser validos tanto para o ambiente local quanto para o alvo AKS, com parametrizacao quando necessario.
