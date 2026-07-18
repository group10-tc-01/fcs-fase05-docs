# Usar .NET 10 nos serviços

Os serviços .NET da fase 5 usarão .NET 10 como target principal. Essa escolha prioriza estabilidade LTS e alinhamento com o ecossistema esperado para os serviços do MVP.

**Consequencias**

- Projetos devem usar `TargetFramework` `net10.0`.
- Wrappers de CI devem configurar `dotnet_version` como `10.0.x`.
- Pacotes e templates devem ser escolhidos considerando compatibilidade com .NET 10.
