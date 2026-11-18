# Domínio 6 — Troubleshooting, Monitoring, and Optimization (10% · ~4-5 questões)

> O curso cobre Liquid Clustering (`05/7`) e OPTIMIZE/ZORDER/VACUUM (`05/3-6`). Spark UI, run history e falhas de cluster são estudados por este resumo.

**Objetivos do guia:**
1. Identificar tendências de performance no **run history** dos Lakeflow Jobs, comparando com a linha de base.
2. Monitorar a saúde do pipeline na UI de Jobs: status, grafo de tasks (DAG), tempos e taxa de falha.
3. Identificar gargalos (**skew**, **shuffle**, **spill**) pelas métricas de estágio no **Spark UI**.
4. Entender **Liquid Clustering** e **Predictive Optimization**.
5. Diagnosticar falhas de **startup de cluster**, **conflito de bibliotecas** e **out of memory (OOM)**.

---

## 1. Monitorando Lakeflow Jobs

**Jobs → seu job → aba Runs**
- **Matrix view**: cada coluna é uma execução e cada linha uma task. Mostra status por cor e duração. Ideal para ver "a task silver começou a demorar o dobro desde terça".
- **Gráfico de duração**: compara o tempo de cada run com as anteriores (a linha de base).
- Clicar numa run abre o **DAG** da execução: a task que falhou fica vermelha e as dependentes ficam como *Upstream failed* / *Skipped*, o que ajuda a achar o bloqueio upstream.

**Recuperação de falhas**
- **Repair run**: reexecuta **somente as tasks que falharam e suas dependentes**, reaproveitando o que deu certo. É a resposta para "corrigir sem rodar o job inteiro de novo".
- **Retries** por task: número de tentativas, intervalo, e opção de repetir em timeout.

**Limites de duração por task / job**
| Configuração | O que faz |
|---|---|
| **Timeout** (`timeout_seconds`) | **Cancela** a task se passar do tempo |
| **Duration warning / health rule** (`RUN_DURATION_SECONDS`) | Só **avisa** (notificação) quando passa do limite esperado |

> 🎯 Task que leva ~60 min sendo cancelada aos 45 min → aumentar o **limite de duração (timeout) da task**, não do cluster.

- **Notificações**: e-mail, Slack, Teams, webhook em *start, success, failure, duration warning, streaming backlog*.
- **System tables** para histórico em SQL: `system.lakeflow.jobs`, `system.lakeflow.job_run_timeline`, `system.lakeflow.job_task_run_timeline`; custo em `system.billing.usage`.

## 2. Spark UI: achando o gargalo

Hierarquia: **Job → Stage → Task**. Um novo *stage* nasce a cada **shuffle** (wide transformation: `groupBy`, `join`, `distinct`, `orderBy`, `repartition`).

| Sintoma no Spark UI | Diagnóstico | Correções típicas |
|---|---|---|
| Na aba *Stages*, **Max** da duração da task ≫ **Median**; poucas tasks demoram muito | **Data skew** (chave muito concentrada) | AQE skew join (`spark.sql.adaptive.skewJoin.enabled`), **salting** da chave, broadcast da tabela pequena, filtrar chaves nulas |
| Colunas **Spill (Memory)** / **Spill (Disk)** > 0 | Partição não coube na memória do executor | Mais partições (`spark.sql.shuffle.partitions`), nós com mais memória, evitar skew |
| **Shuffle Read/Write** enorme | Muito dado trafegando entre executores | Filtrar e selecionar colunas antes do join, **broadcast join**, Liquid Clustering na chave |
| Milhares de tasks minúsculas | Muitos arquivos pequenos / partições demais | `OPTIMIZE`, auto compaction, menos partições |
| Executor parado, só o driver trabalhando | Código no driver (`collect`, loops Python, pandas) | Usar API distribuída do Spark |

Notas:
- **AQE** (Adaptive Query Execution) é ligado por padrão: junta partições pequenas, troca para broadcast join e divide partições com skew em tempo de execução. Mesmo assim, skew extremo numa **agregação** (`groupBy`) pode precisar de **salting**.
- **Aba SQL / DataFrame**: mostra o plano físico. Procure `BroadcastHashJoin` x `SortMergeJoin` e `Exchange` (shuffle).
- Em SQL Warehouse use o **Query Profile** (mesma ideia, visual).

## 3. Liquid Clustering

```sql
CREATE TABLE vendas (id BIGINT, cliente_id BIGINT, data DATE, valor DOUBLE)
CLUSTER BY (cliente_id, data);          -- até 4 colunas

ALTER TABLE vendas CLUSTER BY (data);    -- muda as chaves SEM reescrever dados antigos
ALTER TABLE vendas CLUSTER BY AUTO;      -- Databricks escolhe as chaves (automatic liquid clustering)
ALTER TABLE vendas CLUSTER BY NONE;      -- desliga
OPTIMIZE vendas;                         -- aplica o clustering de forma incremental
OPTIMIZE vendas FULL;                    -- reclusteriza tudo (após trocar chaves)
```

- Substitui **particionamento** e **ZORDER**. **Não** pode ser combinado com `PARTITIONED BY` nem com `ZORDER BY` na mesma tabela.
- Bom para: colunas de alta cardinalidade, filtros que mudam com o tempo, tabelas com skew, tabelas que crescem rápido.
- `CLUSTER BY AUTO` exige tabela **managed** no Unity Catalog com **Predictive Optimization**.

## 4. Predictive Optimization

- Roda **automaticamente** `OPTIMIZE`, `VACUUM` e `ANALYZE` (estatísticas) em **tabelas managed do Unity Catalog**. Não vale para tabelas external.
- Usa compute **serverless** (cobrado à parte), sem você agendar nada.
- Habilitada por herança: conta → catálogo → schema.
  ```sql
  ALTER CATALOG vendas_cat ENABLE PREDICTIVE OPTIMIZATION;
  ALTER SCHEMA vendas_cat.silver DISABLE PREDICTIVE OPTIMIZATION;
  ALTER SCHEMA vendas_cat.gold INHERIT PREDICTIVE OPTIMIZATION;
  ```
- Histórico das operações: `system.storage.predictive_optimization_operations_history`.

> 🎯 "Quero parar de agendar OPTIMIZE/VACUUM manualmente" → tabelas **managed** + **Predictive Optimization**.

## 5. Falhas de cluster, bibliotecas e memória

| Problema | Causas comuns | O que fazer |
|---|---|---|
| **Cluster não sobe** | Cota de vCPU da nuvem esgotada, tipo de instância indisponível na região, **init script** com erro, problema de rede/VNet, política de cluster | Ver **Event log** do cluster e os logs do init script, trocar tipo de nó, pedir aumento de cota |
| **Conflito de biblioteca** | Versão instalada no cluster diferente da exigida, duas libs pedindo versões incompatíveis, lib que conflita com o runtime | Fixar versões (`%pip install pacote==1.2.3`), usar libs **notebook-scoped** (`%pip`) em vez de cluster-wide, trocar a versão do Databricks Runtime, ver *Libraries* → status *Failed* |
| **OOM no driver** | `collect()`, `toPandas()`, broadcast de tabela grande, muitos resultados exibidos | Evitar trazer dados para o driver, aumentar o nó driver, reduzir `autoBroadcastJoinThreshold` |
| **OOM no executor** | Partições grandes, skew, UDF Python pesada | Mais partições, corrigir skew, nós com mais memória, trocar UDF Python por funções nativas |

- `%pip install` reinicia o processo Python do notebook: rode no topo do notebook.
- Mensagens típicas: `java.lang.OutOfMemoryError`, *"The spark driver has stopped unexpectedly and is restarting"* (driver), *"ExecutorLostFailure"* (executor).

---

## ✅ Checklist do domínio
- [ ] Sei usar matrix view e duração das runs para achar regressão
- [ ] Sei a diferença entre timeout e duration warning, e quando usar repair run
- [ ] Reconheço skew, spill e shuffle no Spark UI e sei uma correção para cada
- [ ] Sei a sintaxe `CLUSTER BY`, `CLUSTER BY AUTO` e as restrições do Liquid Clustering
- [ ] Sei o que a Predictive Optimization faz e onde ela vale (managed UC)
- [ ] Sei diagnosticar cluster que não sobe, conflito de lib e OOM driver x executor

## 📚 Fontes oficiais
- [Monitorar Lakeflow Jobs](https://learn.microsoft.com/azure/databricks/jobs/monitor)
- [Repair run](https://learn.microsoft.com/azure/databricks/jobs/repair-job-failures)
- [Spark UI: diagnóstico de skew e spill](https://learn.microsoft.com/azure/databricks/optimizations/spark-ui-guide/)
- [Liquid Clustering](https://learn.microsoft.com/azure/databricks/delta/clustering)
- [Predictive Optimization](https://learn.microsoft.com/azure/databricks/optimizations/predictive-optimization)
