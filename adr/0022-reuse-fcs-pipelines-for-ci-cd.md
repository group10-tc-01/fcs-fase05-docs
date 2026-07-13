# Reutilizar fcs-pipelines para CI/CD

Aplicações mantêm workflows-wrapper e chamam os workflows reutilizáveis de `fcs-pipelines` para CI, publicação GHCR e delivery no K3s.

## Consequências

- CI executa scans, build, testes, cobertura e validação de imagem.
- A entrega usa uma imagem `sha-<commit>` já publicada e um túnel SSH temporário para a API privada do K3s.
- `fcs-infra` usa Terraform com state remoto no HCP Terraform.
