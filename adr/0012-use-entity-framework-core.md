# Usar Entity Framework Core com SQL Server

Os servicos .NET da plataforma usarao Entity Framework Core com provider SQL Server para persistencia e migrations. Cada servico manterá suas proprias migrations apontando para seu database, preservando a autonomia dos microsservicos.

**Consequencias**

- `fcs-identity` mantem migrations para `IdentityDb`.
- `fcs-campaigns` mantem migrations para `CampaignsDb`.
- `fcs-donations` mantem migrations para `DonationsDb`.
- Constraints de unicidade e idempotencia devem ser representadas nas configuracoes do EF Core.
