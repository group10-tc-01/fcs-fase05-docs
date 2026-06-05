# Usar Kind como Kubernetes local

O ambiente Kubernetes local padrao da fase 5 sera Kind. O `fcs-solidarity-infra` deve conter instrucoes e scripts para criar o cluster local, carregar ou apontar imagens, aplicar manifests e subir os componentes compartilhados necessarios para a demo.

**Opcoes consideradas**

- Docker Desktop Kubernetes.
- Minikube.
- Kind.

**Consequencias**

- O README do ambiente integrado deve partir de Kind.
- Os manifests locais devem ser compativeis com Kind.
- O docker compose continua existindo para execucao local simples/integrada fora do cluster quando fizer sentido.
- A validacao da entrega local deve incluir `kubectl get pods` no cluster Kind.
