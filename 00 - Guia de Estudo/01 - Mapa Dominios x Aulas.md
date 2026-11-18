# 🗺️ Mapa: Domínios do Exame x Aulas do Curso

> Base: **Exam Guide oficial de agosto/2026** (exame publicado em 04/05/2026).
> [Guia oficial (PDF)](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Associate-Exam-Guide-August-2026.pdf)
> Confira o guia duas semanas antes da prova, a Databricks atualiza sem aviso.

Legenda: ✅ coberto pelo curso · 🟡 parcial · ❌ não coberto (estudar pelo resumo em `Resumos/`)

---

## Domínio 1 — Databricks Intelligence Platform (6%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Componentes principais: arquitetura, Delta Lake, Unity Catalog | ✅ | `03/1 - Arquitetura`, `04 - Unity Catalog`, `05/1-2 - Delta Lake` |
| Serviços de compute: características, limitações, modelo de custo, escolha por workload | ✅ | `03/2 - Cluster`, `10/2 - SQL Warehouses` |

## Domínio 2 — Data Ingestion and Loading (21%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Padrões de ingestão: batch, streaming, incremental, upload de arquivos locais, Lakeflow Connect | 🟡 | `07/1 - Ingestão Incremental`, `07/2 - Structured Streaming` |
| COPY INTO para carga incremental do storage (ADLS/S3/GCS) em tabelas UC | ✅ | `07/1 - Ingestão Incremental` |
| Auto Loader com schema enforcement/evolution, directory listing x file notification | ✅ | `07/2` e `07/3 - Auto Loader na prática` |
| Configurar Lakeflow Connect (conectores standard e managed) | ❌ | Resumo D2 (a fazer) |
| JDBC/ODBC e REST em notebooks, orquestrados por Jobs | ❌ | Resumo D2 (a fazer) |
| Escolher entre Auto Loader, Lakeflow Connect, partner connectors etc. | 🟡 | Resumo D2 (a fazer) |
| Ingerir semi-estruturados e não estruturados (JSON, nested) | ✅ | `06/04-07 - Ingestão JSON/CSV/TSV/XML`, `06/08 - Arrays` |

## Domínio 3 — Data Transformation and Modeling (22%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Limpeza Bronze → Silver (nulos, tipos) com PySpark/SQL | ✅ | `06/01 - Medalhão`, `06/09 - Silver Customers` |
| Joins: inner, left, broadcast, múltiplas chaves, cross, union, union all | ❌ | Resumo D3 (a fazer) |
| Manipular colunas/linhas: add, drop, split, rename, filter, explode | ✅ | `06/08 - Arrays` |
| Deduplicação e agregações (count, approx_count_distinct, mean/summary) | 🟡 | Resumo D3 (a fazer) |
| Parâmetros de tuning (`shuffle.partitions`, `autoBroadcastJoinThreshold`, memória) | ❌ | Resumo D3 (a fazer) |
| Objetos Gold: materialized views, views, streaming tables, tabelas | ✅ | `05/9 - Views`, `08/1-2 - DLT` |
| Data quality nas camadas Silver/Gold | ✅ | `08/1 - Expectations`, `05/8 - Constraints` |
| (Extra) DDL/DML Delta: CTAS, MERGE, CLONE, Time Travel | ✅ | `05/3-8` |
| (Extra) CDC com Lakeflow Declarative Pipelines (ex-DLT) | ✅ | `08/3 - CDC` |

## Domínio 4 — Working with Lakeflow Jobs (16%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Control flow: retries, If/else, For each | 🟡 | `09/1 - Introdução ao Jobs` (Run if) — completar no Resumo D4 |
| Tasks comuns (notebook, SQL, dashboard, pipeline) e dependências no DAG | ✅ | `09/1-4` |
| Schedules e triggers: scheduled, file arrival, table update | 🟡 | `09/1` — completar no Resumo D4 |
| Trigger por tempo x trigger por dado | ❌ | Resumo D4 (a fazer) |

## Domínio 5 — Implementing CI/CD (10%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Git folders: branches, commit, push, pull request | ✅ | `03/6 - Git Folders` |
| Variáveis e overrides de bundles por ambiente (dev/test/prod) | ❌ | [`Resumos/D5`](../Resumos/D5%20-%20Implementing%20CI-CD.md) |
| Deploy de Declarative Automation Bundles (ex-Databricks Asset Bundles) | ❌ | [`Resumos/D5`](../Resumos/D5%20-%20Implementing%20CI-CD.md) |
| Databricks CLI: validate, deploy, run | ❌ | [`Resumos/D5`](../Resumos/D5%20-%20Implementing%20CI-CD.md) |

## Domínio 6 — Troubleshooting, Monitoring, and Optimization (10%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Tendência de performance no run history dos Jobs | ❌ | [`Resumos/D6`](../Resumos/D6%20-%20Troubleshooting%20Monitoring%20Optimization.md) |
| Monitorar saúde do pipeline na UI de Jobs (status, DAG, falhas) | 🟡 | `09/1` + Resumo D6 |
| Gargalos no Spark UI: skew, shuffle, spill | ❌ | Resumo D6 |
| Liquid Clustering e Predictive Optimization | 🟡 | `05/7 - Otimização` + Resumo D6 |
| Falhas de startup de cluster, conflito de bibliotecas, OOM | ❌ | Resumo D6 |

## Domínio 7 — Governance and Security (15%)

| Objetivo do guia | Cobertura | Onde estudar |
|---|---|---|
| Managed x External: criar, alterar, excluir, **converter** | 🟡 | `05/2 - Managed vs External` — conversão no Resumo D7 |
| GRANT / REVOKE / DENY para usuários, grupos e service principals | ✅ | `11/1 - Governança` |
| Column masking e row-level security | ❌ | Resumo D7 (a fazer) |
| ABAC (políticas por atributo/tag) | ❌ | Resumo D7 (a fazer) |

---

## 📌 Nomes novos que aparecem na prova

| Nome antigo (no curso) | Nome atual (na prova) |
|---|---|
| Delta Live Tables (DLT) | Lakeflow Spark Declarative Pipelines |
| Databricks Workflows / Jobs | Lakeflow Jobs |
| Databricks Asset Bundles (DABs) | Declarative Automation Bundles |
| `APPLY CHANGES INTO` | `AUTO CDC INTO` (a sintaxe antiga ainda funciona) |

> Este mapa é atualizado a cada aula nova adicionada ao repositório.
