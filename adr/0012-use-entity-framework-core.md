# Usar Entity Framework Core com SQL Server

Os serviços .NET da plataforma usarão Entity Framework Core com provider SQL Server para persistência e migrations. Cada serviço manterá suas próprias migrations apontando para seu database, preservando a autonomia dos microsserviços.

**Consequências**

- `fcs-identity` mantém migrations para `IdentityDb`.
- `fcs-campaigns` mantém migrations para `CampaignsDb`.
- `fcs-donations` mantém migrations para `DonationsDb`.
- Constraints de unicidade e idempotência devem ser representadas nas configurações do EF Core.
