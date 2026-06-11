# Databricks AI Dev Kit — Workspace

## Contexto

Workspace principal para desenvolvimento de projetos Databricks com Claude Code.
Contém skills instaladas para todos os domínios Databricks e um MCP server configurado.

- **Workspace**: `https://dbc-79f7f0ef-6d29.cloud.databricks.com`
- **Profile CLI**: `DEFAULT`
- **MCP Server**: Databricks MCP em `.mcp.json` (carregado automaticamente)

---

## OBRIGATÓRIO: Regras de Invocação de Skills

Antes de escrever qualquer código, configuração ou consulta, identifique o domínio e invoque a skill correspondente. Nunca pule este passo.

### Pipelines e Ingestão de Dados

| Cenário | Skill |
|---------|-------|
| Criar/editar pipeline SDP (bronze/silver/gold), Auto Loader, `CREATE OR REFRESH STREAMING TABLE` | `databricks-spark-declarative-pipelines` |
| Criar/editar pipeline de streaming com Python/Scala (Structured Streaming) | `databricks-spark-structured-streaming` |
| Ingestão de dados externos, conectores, fontes customizadas Python | `spark-python-data-source` |
| Ingestão via ZeroBus / event-driven ingestion | `databricks-zerobus-ingest` |

### Bundle, Jobs e Orquestração

| Cenário | Skill |
|---------|-------|
| Criar/editar `databricks.yml`, `resources/*.yml`, deploy de bundle (DABs) | `databricks-bundles` |
| Criar/editar jobs, schedules, tasks, email notifications | `databricks-jobs` |

### Dashboards e Analytics

| Cenário | Skill |
|---------|-------|
| Criar/editar dashboard AI/BI (`.lvdash.json`), widgets, KPIs, filtros | `databricks-aibi-dashboards` |
| Criar/editar Genie Space, perguntas em linguagem natural sobre dados | `databricks-genie` |
| Criar/editar Metric Views, métricas reutilizáveis | `databricks-metric-views` |
| Queries SQL, SQL Warehouse, DBSQL | `databricks-dbsql` |

### Unity Catalog e Governança

| Cenário | Skill |
|---------|-------|
| Criar/gerenciar catalogs, schemas, volumes, grants, tabelas | `databricks-unity-catalog` |
| Trabalhar com tabelas Iceberg, open table formats | `databricks-iceberg` |

### Machine Learning e MLflow

| Cenário | Skill |
|---------|-------|
| Primeiros passos com MLflow no Databricks | `mlflow-onboarding` |
| Instrumentar código com MLflow Tracing (spans, traces) | `instrumenting-with-mlflow-tracing` |
| Avaliar modelos e experimentos MLflow | `databricks-mlflow-evaluation` |
| Consultar métricas e runs MLflow | `querying-mlflow-metrics` |
| Buscar e recuperar traces MLflow | `retrieving-mlflow-traces` |
| Analisar traces de sessão de chat | `analyze-mlflow-chat-session` |
| Analisar um trace MLflow específico | `analyze-mlflow-trace` |
| Buscar documentação MLflow | `searching-mlflow-docs` |
| Servir modelos (Model Serving endpoints) | `databricks-model-serving` |

### AI e Agentes

| Cenário | Skill |
|---------|-------|
| Construir agentes com Mosaic AI Agent Bricks | `databricks-agent-bricks` |
| Avaliar agentes (Agent Evaluation) | `agent-evaluation` |
| Usar AI Functions no SQL (`ai_query`, `ai_generate_text`) | `databricks-ai-functions` |
| Vector Search (índices, embeddings, busca semântica) | `databricks-vector-search` |

### Apps e SDK

| Cenário | Skill |
|---------|-------|
| Criar Databricks App (dashboard/UI) com Python | `databricks-apps-python` |
| Criar Databricks App com APX framework | `databricks-app-apx` |
| Gerenciar apps via CLI/SDK | `databricks-apps` |
| Usar Databricks Python SDK | `databricks-python-sdk` |

### Dados e Infraestrutura

| Cenário | Skill |
|---------|-------|
| Lakebase (PostgreSQL compatível no Databricks) | `databricks-lakebase` |
| Lakebase com autoscaling | `databricks-lakebase-autoscale` |
| Lakebase provisionado | `databricks-lakebase-provisioned` |
| Geração de dados sintéticos para testes/demos | `databricks-synthetic-data-gen` |
| Geração e upload de PDFs não estruturados | `databricks-unstructured-pdf-generation` |
| Configurar profiles, autenticação CLI | `databricks-config` |

---

## Gatilhos por Intenção do Usuário

Mapeamento de linguagem natural para skill:

- "pipeline", "bronze", "silver", "gold", "Auto Loader", "SDP", "streaming table" → `databricks-spark-declarative-pipelines`
- "dashboard", "gráfico", "KPI", "widget", "counter", "chart", "filtro global" → `databricks-aibi-dashboards`
- "bundle", "deploy", "databricks.yml", "target prod" → `databricks-bundles`
- "job", "schedule", "agendamento", "task", "trigger" → `databricks-jobs`
- "catalog", "schema", "volume", "grants", "Unity Catalog" → `databricks-unity-catalog`
- "agente", "Agent Bricks", "RAG", "compound AI" → `databricks-agent-bricks`
- "model serving", "endpoint", "inferência" → `databricks-model-serving`
- "vector search", "embedding", "busca semântica" → `databricks-vector-search`
- "MLflow", "experimento", "run", "trace", "log" → skill MLflow correspondente ao objetivo
- "app", "Streamlit", "Dash", "interface web" → `databricks-apps-python`
- "dados sintéticos", "dados de teste", "demo data" → `databricks-synthetic-data-gen`
- "Genie", "perguntar sobre dados", "NL2SQL" → `databricks-genie`

---

## Workflow Obrigatório para Dashboards AI/BI

NUNCA pular qualquer etapa:

```
1. get_table_stats_and_schema()   → conhecer schema e tipos das colunas
2. Escrever queries SQL dos datasets
3. execute_sql()                  → testar CADA query (se falhar, corrigir antes de continuar)
4. Construir o JSON do dashboard  → usar apenas campos validados no passo 3
5. manage_dashboard(action="create_or_update") → deploy
6. manage_dashboard(action="publish")          → publicar
```

---

## Notas Operacionais

- **Serverless REPL não funciona** neste workspace (Client-1 channel não suportado). Usar `execute_sql` via SQL Warehouse ou `manage_jobs` para código Spark.
- **queryLines em dashboards** DEVEM ser uma única string por array entry — arrays multi-linha sem espaço final concatenam tokens incorretamente (ex: `anoFROM` em vez de `ano FROM`).
- **Paths em DABs**: `resources/*.yml` usa `../src/...`; `databricks.yml` usa `./src/...`
- **Mode production** em DABs requer `workspace.root_path` obrigatoriamente.
- Após `bundle deploy` de dashboard, republicar com `manage_dashboard(action="publish")`.
