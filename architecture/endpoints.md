# Endpoints

As APIs usam `/api/v1` e JWT quando indicado. O domínio e os caminhos publicados dependem do Ingress de cada aplicação.

| Serviço | Endpoints principais | Acesso |
| --- | --- | --- |
| `fcs-identity` | `POST /auth/register/donor`, `POST /auth/login`, `POST /auth/refresh`, `GET /me` | público/autenticado |
| `fcs-campaign` | `POST|PUT /campaigns`, `PATCH /campaigns/{id}/status`, `GET /transparency/campaigns` | GestorONG/público |
| `fcs-donations` | `POST /donations`, `GET /donations` | Doador/GestorONG |
| `fcs-donation-worker` | consome `donation-received` | interno |
| `fcs-audit-logs` | consome `audit-log-requested` | interno |

`GET /internal/campaigns/{id}/donation-eligibility` e `POST /internal/campaigns/{id}/donation-processed` são privados no cluster. Endpoints operacionais seguem a política de Ingress de cada serviço; não são expostos por padrão.
