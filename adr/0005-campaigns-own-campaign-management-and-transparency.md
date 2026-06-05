# Concentrar campanhas e transparencia na fcs-campaigns

A `fcs-campaigns` sera responsavel pela administracao das **Campanhas** pelo **GestorONG** e pelo **Painel de Transparencia** publico. Essa fronteira mantem em um unico servico as regras sobre status, periodo, meta financeira e exposicao publica de campanhas ativas.

**Consequencias**

- Endpoints administrativos de campanha exigem role `GestorONG`.
- Toda campanha nova nasce com status `Active`.
- Apenas campanhas com status `Active` podem ser editadas.
- Transicoes de status permitidas: `Active -> Completed` e `Active -> Canceled`.
- O **Painel de Transparencia** e publico e lista apenas **Campanhas Ativas**.
- A entidade de campanha inclui titulo, descricao, datas, meta financeira, status, valor total arrecadado e `CreatedByManagerId`.
- A `fcs-campaigns` mantem registros de doacoes processadas por campanha para idempotencia da atualizacao de valor arrecadado.
- `CreatedByManagerId` e uma referencia externa ao perfil do gestor, sem foreign key entre databases.
