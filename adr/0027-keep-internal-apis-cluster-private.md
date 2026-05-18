# Manter APIs internas privadas no cluster

Endpoints internos entre servicos, como validacao de elegibilidade de campanha e notificacao de doacao processada, ficarao acessiveis apenas dentro do Kubernetes. Eles nao devem ser publicados na borda publica da plataforma.

**Consequencias**

- Rotas `/internal/*` devem ser expostas apenas por Kubernetes Services internos.
- O APIM e qualquer entrada publica devem publicar somente endpoints voltados ao cliente.
- Comunicacao service-to-service usa DNS interno do cluster.
- Refit e Polly serao usados nos clientes HTTP internos quando aplicavel.
