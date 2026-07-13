# Manter APIs internas privadas no cluster

Rotas `/internal/*` são expostas somente por Kubernetes Services. Traefik publica apenas rotas de cliente declaradas por Ingress.

## Consequências

- Serviços comunicam-se por DNS interno do K3s.
- Health e metrics seguem a política explícita de cada aplicação; não são publicados por padrão.
- A API Kubernetes permanece privada e não recebe Ingress.
