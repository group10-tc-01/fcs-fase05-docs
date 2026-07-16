# Usar .NET 8 nos servicos

Os servicos .NET da fase 5 usarao .NET 10 como target principal. Essa escolha prioriza estabilidade LTS e alinhamento com o ecossistema esperado para os servicos do MVP.

**Consequencias**

- Projetos devem usar `TargetFramework` `net10.0`.
- Wrappers de CI devem configurar `dotnet_version` como `10.0.x`.
- Pacotes e templates devem ser escolhidos considerando compatibilidade com .NET 8.
