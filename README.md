# 📚 Curso de Preparação - Databricks Data Engineer Associate (DEA)

Material de estudo para a certificação **Databricks Certified Data Engineer Associate**, com foco em ambientes **Azure Databricks**.

---

## 🎯 Objetivo

Este repositório contém documentação, exemplos práticos e anotações organizadas por tópico para preparação completa para o exame de certificação DEA.

A certificação valida a capacidade de utilizar a **Databricks Data Intelligence Platform** para realizar tarefas de engenharia de dados, incluindo compreensão da arquitectura, execução de ETL com Apache Spark SQL e PySpark, e orquestração de workloads com Databricks Workflows.

---

## 📋 Conteúdo Programático

| Pasta | Tópico | Descrição |
|-------|--------|-----------|
| `00` | **Guia de Estudo** | Trilha de estudo e mapa domínio x aula (comece aqui) |
| `01` | Introdução ao Curso | Boas vindas e introdução |
| `02` | Criando Conta Free na Azure |Criando conta Azure com bonus de $200 |
| `03` | Componentes Databricks | Arquitectura, clusters, workspaces |
| `04` | Introdução ao Unity Catalog | Governança, metastore, namespaces |
| `05` | Databricks Lakehouse | Delta Lake, arquitectura medallion |
| `06` | ETL e Processamento Spark | Transformações, DataFrames, SQL |
| `07` | Spark Structured Streaming | Streaming, triggers, checkpoints |
| `08` | Pipelines Declarativos - DLT | Delta Live Tables, expectations |
| `09` | Databricks Jobs | Orquestração, triggers, task values |
| `10` | Databricks SQL | Warehouses, dashboards, alertas |
| `11` | Governança de Dados | Permissões, GRANT/REVOKE, security |
| `12` | Simulado Certificação | Simulado de Perguntas Certificação |
| `13` | Condideração Final| Encerramento do treinamento |
| `Resumos/` | Resumos por domínio | Um resumo por domínio do exame, incluindo o que o curso não cobre |
| `Questoes/` | Questões de prática | Questões inéditas no estilo da prova, com gabarito comentado |
---

## 🎓 Sobre o Exame

| Aspecto | Detalhe |
|---------|---------|
| **Tipo** | Proctored (supervisionado) |
| **Questões** | 45 questões pontuadas |
| **Duração** | 90 minutos |
| **Formato** | Múltipla escolha |
| **Taxa de Registo** | $200 USD |
| **Idiomas** | English, 日本語, Português BR, 한국어 |
| **Modalidade** | Online ou centro de testes |
| **Pré-requisitos** | Python e SQL nível básico |
| **Experiência Recomendada** | 6+ meses hands-on |
| **Validade** | 2 anos |
| **Recertificação** | Refazer exame na versão actual |

---

## 📊 Distribuição de Tópicos no Exame (versão de 04/05/2026, guia de ago/2026)

| Domínio | Peso |
|---------|------|
| 1. Databricks Intelligence Platform | 6% |
| 2. Data Ingestion and Loading | 21% |
| 3. Data Transformation and Modeling | 22% |
| 4. Working with Lakeflow Jobs | 16% |
| 5. Implementing CI/CD | 10% |
| 6. Troubleshooting, Monitoring, and Optimization | 10% |
| 7. Governance and Security | 15% |

```
Data Transformation and Modeling       ██████████████████████  22%
Data Ingestion and Loading             █████████████████████   21%
Working with Lakeflow Jobs             ████████████████        16%
Governance and Security                ███████████████         15%
Implementing CI/CD                     ██████████              10%
Troubleshooting, Monitoring, Optim.    ██████████              10%
Databricks Intelligence Platform       ██████                   6%
```

> O exame tem 45 questões pontuadas e pode ter até 10 não pontuadas, em 90 minutos.
> Veja em [`00 - Guia de Estudo/01 - Mapa Dominios x Aulas.md`](00%20-%20Guia%20de%20Estudo/01%20-%20Mapa%20Dominios%20x%20Aulas.md) o que o curso cobre em cada domínio.

---

## 🛠️ Estrutura do Material

Cada pasta contém:

- **Documentação em Markdown** — Resumos focados em pontos de certificação
- **Notebooks** — Exemplos práticos para execução no Databricks
- **Cenários de Exame** — Questões típicas e como responder

---

## 📖 Como Usar

1. **Estudo sequencial** — Seguir a numeração das pastas (01 → 12)
2. **Revisão por tópico** — Focar em áreas específicas de dificuldade
3. **Prática hands-on** — Executar notebooks no ambiente Azure Databricks
4. **Revisar Exam Guide** — Identificar gaps no conhecimento

---

## 📝 Passos para Preparação (Recomendado pela Databricks)

1. ✅ Revisar o [Exam Guide oficial](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Associate-Exam-Guide-August-2026.pdf)
2. ✅ Completar treino relacionado
3. ✅ Registar para o exame
4. ✅ Verificar [requisitos técnicos](https://kryterion.my.site.com/support/s/article/Online-Testing-Requirements?language=en_US) para exame online
5. ✅ Executar [system check](https://www.kryterion.com/systemcheck/)
6. ✅ Identificar gaps e estudar
7. ✅ Fazer o exame!

> **Nota:** O código no exame é fornecido em SQL quando possível. Nos demais casos, será em Python.

---

## ✅ Checklist de Preparação

O checklist detalhado, semana a semana, está em [`00 - Guia de Estudo/02 - Trilha de Estudo.md`](00%20-%20Guia%20de%20Estudo/02%20-%20Trilha%20de%20Estudo.md).

### 1. Databricks Intelligence Platform (6%)
- [ ] Componentes: arquitetura, Delta Lake, Unity Catalog
- [ ] Escolher o compute certo (serverless, classic, job cluster, SQL warehouse) e seus custos

### 2. Data Ingestion and Loading (21%)
- [ ] COPY INTO e Auto Loader (schema evolution, directory listing x file notification)
- [ ] Lakeflow Connect (conectores standard e managed), JDBC/REST
- [ ] Dados semi-estruturados (JSON, nested, arrays)

### 3. Data Transformation and Modeling (22%)
- [ ] Limpeza Bronze → Silver, joins (broadcast, cross, union), deduplicação, agregações
- [ ] Parâmetros de tuning do Spark
- [ ] Objetos Gold: views, materialized views, streaming tables; data quality

### 4. Working with Lakeflow Jobs (16%)
- [ ] Tasks e dependências no DAG, retries, If/else, For each
- [ ] Triggers: scheduled, file arrival, table update

### 5. Implementing CI/CD (10%)
- [ ] Git folders: branch, commit, push, pull request
- [ ] Declarative Automation Bundles (ex-DABs): variáveis, targets, deploy
- [ ] Databricks CLI: `bundle validate / deploy / run`

### 6. Troubleshooting, Monitoring, and Optimization (10%)
- [ ] Run history, matrix view, repair run
- [ ] Spark UI: skew, shuffle, spill
- [ ] Liquid Clustering e Predictive Optimization
- [ ] Falhas de cluster, conflitos de bibliotecas, OOM

### 7. Governance and Security (15%)
- [ ] Managed x External (incluindo conversão)
- [ ] GRANT / REVOKE / DENY para usuários, grupos e service principals
- [ ] Row filters, column masks e ABAC

---

## 📚 Recursos Oficiais

- [Página da Certificação](https://www.databricks.com/learn/certification/data-engineer-associate)
- [Exam Guide (PDF)](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Associate-Exam-Guide-August-2026.pdf)
- [Registo para Exame](http://webassessor.com/databricks)
- [Databricks Academy](https://www.databricks.com/learn/training/login)
- [Documentação Azure Databricks](https://learn.microsoft.com/pt-pt/azure/databricks/)
- [FAQ da Certificação](https://www.databricks.com/learn/certification/faq)

---

## 📁 Pasta Arquivos

Contém ficheiros de dados utilizados nos notebooks práticos (CSV, JSON, Parquet) para simulação de cenários reais de ingestão e transformação.

---

## ⚠️ Notas Importantes

- **Conteúdo não pontuado:** O exame pode incluir questões para fins estatísticos que não afetam a nota
- **Materiais permitidos:** Nenhum (closed book)
- **Código:** SQL é preferido, Python quando necessário

---

**Boa sorte na certificação!** 🚀