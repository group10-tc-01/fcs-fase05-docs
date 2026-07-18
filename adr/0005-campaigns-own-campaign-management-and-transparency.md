# Concentrar campanhas e transparência na fcs-campaigns

A `fcs-campaigns` será responsável pela administração das **Campanhas** pelo **GestorONG** e pelo **Painel de Transparência** público. Essa fronteira mantém em um único serviço as regras sobre status, período, meta financeira e exposição pública de campanhas ativas.

**Consequências**

- Endpoints administrativos de campanha exigem role `GestorONG`.
- Toda campanha nova nasce com status `Active`.
- Apenas campanhas com status `Active` podem ser editadas.
- Transições de status permitidas: `Active -> Completed` e `Active -> Canceled`.
- O **Painel de Transparência** é público e lista apenas **Campanhas Ativas**.
- A entidade de campanha inclui título, descrição, datas, meta financeira, status, valor total arrecadado e `CreatedByManagerId`.
- A `fcs-campaigns` mantém registros de doações processadas por campanha para idempotência da atualização de valor arrecadado.
- `CreatedByManagerId` é uma referência externa ao perfil do gestor, sem foreign key entre databases.
