# Hackathon — Plataforma "Conexão Solidária"

## Contexto

A ONG **Esperança Solidária** atua há mais de 10 anos acolhendo crianças em situação de vulnerabilidade. A gestão de doadores e campanhas hoje é manual, o que limita o crescimento da organização.

O objetivo deste hackathon é arquitetar e desenvolver o **MVP da plataforma Conexão Solidária**, com foco em:
- Escalabilidade
- Observabilidade
- Automação

---

## Requisitos Funcionais

### 1. Autenticação e Autorização (RBAC)

- Autenticação via **JWT (JSON Web Tokens)**.
- Dois perfis de usuário:
  - `GestorONG`
  - `Doador`
- Endpoints de gestão acessíveis **somente** para `GestorONG`.

---

### 2. Gestão de Campanhas

> Acesso: `GestorONG`

**Campos obrigatórios ao criar/editar uma campanha:**

| Campo            | Tipo     | Valores possíveis            |
|------------------|----------|------------------------------|
| Título           | string   | —                            |
| Descrição        | string   | —                            |
| DataInicio       | datetime | —                            |
| DataFim          | datetime | —                            |
| MetaFinanceira   | decimal  | > 0                          |
| Status           | enum     | `Ativa`, `Concluida`, `Cancelada` |

**Regras de negócio:**
- A `DataFim` não pode estar no passado.
- A `MetaFinanceira` deve ser maior que zero.

---

### 3. Cadastro de Doador

> Acesso: Público

**Campos obrigatórios:**

| Campo          | Regra                                  |
|----------------|----------------------------------------|
| Nome Completo  | —                                      |
| Email          | Único no banco de dados                |
| CPF            | Formato válido                         |
| Senha          | Armazenada com hash (ex.: BCrypt)      |

---

### 4. Painel de Transparência

> Acesso: Público

- Endpoint público que lista **apenas campanhas com status `Ativa`**.
- Dados retornados por campanha:
  - Título
  - Meta Financeira
  - Valor Total Arrecadado (calculado a partir das doações processadas)

---

### 5. Processo de Doação

> Acesso: `Doador` logado

- O doador envia uma intenção de doação com:
  - `IdCampanha`
  - `ValorDoacao`
- **Regra de negócio:** não é permitido doar para campanhas com status `Concluida` ou `Cancelada`.

---

## Requisitos Técnicos Obrigatórios

### Arquitetura de Microsserviços

- Mínimo de **dois microsserviços distintos**.
- Exemplo de separação:
  - `API de Campanhas/Usuários`
  - `Worker de Processamento de Doações`

---

### Comunicação Assíncrona (Mensageria)

Fluxo obrigatório ao receber uma doação:

```
Cliente → API (publica evento) → Broker (RabbitMQ ou Kafka) → Worker (consome e atualiza campanha)
```

- A API **não** deve atualizar o valor arrecadado diretamente no banco.
- A API publica um evento (ex.: `DoacaoRecebidaEvent`) no broker.
- O Worker consome a fila e atualiza o `ValorTotalArrecadado` da campanha.

---

### Orquestração com Kubernetes

- A aplicação deve rodar em um cluster Kubernetes (Minikube, Kind, Docker Desktop K8s, etc.).
- Entregar arquivos `.yaml` para:
  - `Deployments`
  - `Services`
  - `ConfigMaps`

---

### Observabilidade (Datadog)

- A aplicação deve expor endpoint de saúde: `/health` ou `/metrics`.
- O Datadog deve ter **pelo menos um dashboard** com métricas reais, por exemplo:
  - Consumo de CPU/Memória dos pods
  - Contagem de requisições HTTP

---

### Pipeline de CI/CD

- Pipeline automatizado (GitHub Actions, Azure DevOps, etc.) acionado a cada push na branch principal.
- O pipeline deve obrigatoriamente:
  - Compilar o código (`.NET build`)
  - Gerar a imagem Docker
- Deploy automático no Kubernetes é **opcional**.

---

## Requisitos Técnicos Opcionais (Bônus)

> Sem impacto na nota

- Testes de unidade (xUnit/NUnit) rodando dentro da esteira de CI.
- API Gateway (ex.: Ocelot, KrakenD) roteando requisições para os microsserviços.

---

## Entregáveis

### 1. Repositório de Código Público

- Deve conter um `README.md` com **instruções passo a passo** para subir a infraestrutura e a aplicação localmente.

### 2. Desenho da Arquitetura

- Diagrama com: microsserviços, bancos de dados, broker de mensageria e ferramentas de observabilidade.
- Documento em PDF justificando a escolha dos bancos de dados.

### 3. Vídeo de Demonstração (máximo 15 minutos)

O vídeo deve cobrir obrigatoriamente:

1. Explicação do diagrama de arquitetura.
2. Pipeline de CI executando e gerando a imagem Docker com sucesso.
3. Terminal com `kubectl get pods` e dashboard do Datadog com dados em tempo real.
4. Demonstração funcional:
   - Autenticação via Postman/Swagger e obtenção do token JWT.
   - Criação de uma campanha.
   - Simulação de doação:
     - Envio do payload
     - Interface do RabbitMQ/Kafka mostrando a mensagem na fila
     - Consulta à API pública comprovando que o valor foi atualizado pelo Worker

### 4. Relatório de Entrega (PDF ou TXT)

Deve conter:
- Nome do grupo
- Participantes e usernames no Discord
- Link da documentação
- Link do(s) repositório(s)
- Link do vídeo (YouTube ou similar)
