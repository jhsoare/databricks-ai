# Databricks AI Dev Kit

Workspace configurado para desenvolver projetos Databricks com Claude Code — MCP Server conectado ao seu workspace, skills especializadas para todos os domínios Databricks, e guias prontos para uso imediato.

---

## O que está incluído

- **MCP Server Databricks** — executa SQL, cria jobs, gerencia clusters, dashboards, pipelines e muito mais direto do Claude Code
- **Skills especializadas** — 30+ skills com guias e padrões para cada domínio Databricks
- **Claude Code configurado** — CLAUDE.md com regras de invocação automática de skills
- **Cursor e VSCode** — MCP também configurado para uso nos dois editores

---

## Pré-requisitos

### 1. Node.js 18+

Necessário para o Claude Code.

```bash
node --version   # deve ser v18 ou superior
```

Instale via [nodejs.org](https://nodejs.org) ou `brew install node`.

### 2. Claude Code

```bash
npm install -g @anthropic-ai/claude-code
claude --version
```

### 3. Python 3.11+

Necessário para o MCP Server Databricks.

```bash
python3 --version   # deve ser 3.11 ou superior
```

Instale via [python.org](https://python.org) ou `brew install python@3.11`.

### 4. Databricks CLI v2

> Atenção: use a CLI v2 (Go-based). O pacote `databricks-cli` do pip é a versão legada e está deprecado.

```bash
# macOS
brew tap databricks/tap && brew install databricks

# Linux / macOS sem Homebrew
curl -fsSL https://raw.githubusercontent.com/databricks/setup-cli/main/install.sh | sh

# Verifique (deve ser 0.200+)
databricks --version
```

### 5. Autenticação configurada

Configure o profile `DEFAULT` apontando para o seu workspace:

```bash
databricks configure --profile DEFAULT
```

Confirme que está funcionando:

```bash
databricks auth describe --profile DEFAULT
```

---

## Estrutura do projeto

```
databricks-ai/
├── .mcp.json               # MCP Server Databricks (carregado automaticamente)
├── .claude/
│   ├── settings.json       # Hooks e permissões do Claude Code
│   └── skills/             # Skills instaladas localmente
├── .ai-dev-kit/
│   ├── version             # Versão do kit (0.1.12)
│   └── .installed-skills   # Lista de skills ativas
├── .venv/                  # Ambiente virtual Python local (opcional)
├── CLAUDE.md               # Regras e contexto para o Claude Code
└── README.md               # Este arquivo
```

---

## Como usar

### 1. Abrir o workspace no Claude Code

```bash
cd ~/Documents/develop/databricks-ai
claude
```

O MCP Server Databricks inicia automaticamente via `.mcp.json` usando o Python em `~/.ai-dev-kit/.venv`. Na primeira mensagem, o Claude conecta ao workspace configurado no profile `DEFAULT`.

### 2. Pedir qualquer coisa em linguagem natural

O Claude identifica o domínio, invoca a skill correta e usa as ferramentas MCP para executar no Databricks. Exemplos:

```
"Crie um pipeline bronze → silver → gold para os dados de vendas no volume raw/"
"Construa um dashboard de KPIs de receita com filtro por mês"
"Crie um job que roda todo dia às 7h e manda e-mail se falhar"
"Configure um Knowledge Assistant RAG sobre os documentos em /Volumes/..."
"Crie um endpoint de model serving para o modelo registrado no Unity Catalog"
```

---

## Skills disponíveis

### Pipelines e Ingestão

| O que fazer | Skill invocada automaticamente |
|---|---|
| Pipeline medallion (bronze/silver/gold), Auto Loader | `databricks-spark-declarative-pipelines` |
| Streaming com Python/Scala | `databricks-spark-structured-streaming` |
| Fontes de dados customizadas Python | `spark-python-data-source` |
| Ingestão event-driven via ZeroBus | `databricks-zerobus-ingest` |

### Jobs e Deploy

| O que fazer | Skill invocada automaticamente |
|---|---|
| Bundles (DABs), `databricks.yml`, multi-env deploy | `databricks-bundles` |
| Jobs, schedules, tasks, notificações | `databricks-jobs` |

### Dashboards e Analytics

| O que fazer | Skill invocada automaticamente |
|---|---|
| Dashboard AI/BI (Lakeview), KPIs, widgets, filtros | `databricks-aibi-dashboards` |
| Genie Space — perguntas em linguagem natural | `databricks-genie` |
| Metric Views — métricas reutilizáveis | `databricks-metric-views` |
| Queries SQL, SQL Warehouse, DBSQL | `databricks-dbsql` |

### Unity Catalog e Governança

| O que fazer | Skill invocada automaticamente |
|---|---|
| Catalogs, schemas, volumes, grants, tabelas | `databricks-unity-catalog` |
| Tabelas Iceberg, open table formats | `databricks-iceberg` |

### Machine Learning e MLflow

| O que fazer | Skill invocada automaticamente |
|---|---|
| Primeiros passos com MLflow | `mlflow-onboarding` |
| Instrumentar código com traces/spans | `instrumenting-with-mlflow-tracing` |
| Avaliar modelos e experimentos | `databricks-mlflow-evaluation` |
| Consultar métricas e runs | `querying-mlflow-metrics` |
| Buscar e analisar traces | `retrieving-mlflow-traces` |
| Servir modelos (endpoints de inferência) | `databricks-model-serving` |

### AI e Agentes

| O que fazer | Skill invocada automaticamente |
|---|---|
| Knowledge Assistant (RAG sobre documentos) | `databricks-agent-bricks` |
| Genie Space (NL2SQL sobre tabelas) | `databricks-agent-bricks` |
| Supervisor Agent / multi-agent | `databricks-agent-bricks` |
| Avaliar qualidade de agentes | `agent-evaluation` |
| AI Functions no SQL (`ai_query`, `ai_summarize`) | `databricks-ai-functions` |
| Vector Search, embeddings, busca semântica | `databricks-vector-search` |

### Apps e SDK

| O que fazer | Skill invocada automaticamente |
|---|---|
| Databricks App com Python (Dash/Streamlit) | `databricks-apps-python` |
| Databricks App com APX framework | `databricks-app-apx` |
| Python SDK direto | `databricks-python-sdk` |

### Infraestrutura e Dados

| O que fazer | Skill invocada automaticamente |
|---|---|
| Lakebase (PostgreSQL compatível) | `databricks-lakebase` |
| Geração de dados sintéticos para testes | `databricks-synthetic-data-gen` |
| Geração e upload de PDFs | `databricks-unstructured-pdf-generation` |
| Configurar profiles, autenticação CLI | `databricks-config` |

---

## Exemplos práticos

### Criar um pipeline medallion completo

```
Crie um pipeline SDP com três camadas:
- Bronze: ingere CSV do volume /Volumes/catalog/schema/raw/vendas/
- Silver: limpa nulos, padroniza tipos, deduplica por id_venda
- Gold: agrega receita total por produto e por mês
```

O Claude invoca `databricks-spark-declarative-pipelines`, escreve o código Python/SQL e cria o pipeline no workspace via MCP.

### Criar um dashboard com dados reais

```
Crie um dashboard de vendas com:
- KPI de receita total do mês
- Gráfico de linha: receita por dia (últimos 30 dias)
- Tabela top-10 produtos mais vendidos
- Filtro global por loja
```

O Claude segue o workflow obrigatório: lê o schema → testa cada query no SQL Warehouse → constrói o JSON → publica o dashboard.

### Deploy de um projeto completo com bundle

```
Crie um bundle DAB para o projeto de pipeline de vendas com:
- Target dev (modo development)
- Target prod (modo production com root_path)
- Job diário às 7h no horário de Brasília com alerta por e-mail
```

### Criar um Knowledge Assistant sobre documentos PDF

```
Crie um Knowledge Assistant RAG para responder perguntas sobre os PDFs 
armazenados em /Volumes/main/docs/manuais/
```

---

## Ferramentas MCP disponíveis

O MCP Server expõe mais de 50 ferramentas que o Claude usa automaticamente:

| Categoria | Ferramentas |
|---|---|
| Compute | `list_compute`, `manage_cluster`, `manage_sql_warehouse` |
| Código e SQL | `execute_code`, `execute_sql`, `execute_sql_multi` |
| Jobs e Pipelines | `manage_jobs`, `manage_job_runs`, `manage_pipeline`, `manage_pipeline_run` |
| Dashboards | `manage_dashboard` |
| Unity Catalog | `manage_uc_objects`, `manage_uc_grants`, `get_table_stats_and_schema` |
| Vector Search | `manage_vs_index`, `manage_vs_endpoint`, `query_vs_index` |
| Model Serving | `manage_serving_endpoint` |
| Apps | `manage_app` |
| Agentes | `manage_ka`, `manage_genie`, `manage_mas` |
| Arquivos | `manage_workspace_files`, `manage_volume_files` |
| Lakebase | `manage_lakebase_database`, `manage_lakebase_branch` |

---

## Notas importantes

- **Serverless REPL desabilitado** — use `execute_sql` via SQL Warehouse ou `manage_jobs` para executar código Spark neste workspace
- **Autenticação** — o MCP usa o profile `DEFAULT` do Databricks CLI (definido em `.mcp.json`)
- **Updates automáticos** — ao iniciar uma sessão, o hook `SessionStart` verifica se há nova versão do kit disponível

---

## Atualizar o kit

```bash
# Verificar versão atual
cat .ai-dev-kit/version

# Atualizar (se disponível)
bash ~/.ai-dev-kit/repo/.claude-plugin/check_update.sh
```
