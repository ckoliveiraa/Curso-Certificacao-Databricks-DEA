# Questões — Domínio 6: Troubleshooting, Monitoring, and Optimization

> Questões **inéditas** escritas no estilo da prova, a partir dos objetivos do guia oficial (ago/2026). Não são questões vazadas.

---

### 1. Um job diário costumava levar 20 minutos e, na última semana, passou a levar 50. Onde o engenheiro identifica rapidamente qual task começou a demorar mais?

A) No Event log do cluster
B) Na aba Runs do job, usando a matrix view e o gráfico de duração das execuções
C) No Version History do notebook
D) No Catalog Explorer

<details><summary>Resposta</summary>

**B.** A matrix view mostra cada task em cada execução com status e duração, permitindo comparar com a linha de base.
</details>

---

### 2. Em um job com 6 tasks, a task 4 falhou por um erro já corrigido. As tasks 1 a 3 levaram 2 horas. Como reexecutar com o menor custo?

A) Rodar o job inteiro de novo com "Run now"
B) Usar **Repair run**, que reexecuta apenas a task que falhou e suas dependentes
C) Clonar o job e remover as tasks 1 a 3
D) Aumentar o número de retries e aguardar

<details><summary>Resposta</summary>

**B.** Repair run reaproveita as tasks bem-sucedidas e roda só as falhas e o que depende delas.
</details>

---

### 3. Uma task é cancelada sempre aos 45 minutos, mas precisa de cerca de 60 minutos para terminar. Qual configuração ajustar?

A) O auto-termination do cluster
B) O timeout (limite de duração) da task
C) O número de workers
D) O `spark.sql.shuffle.partitions`

<details><summary>Resposta</summary>

**B.** O timeout da task cancela a execução ao atingir o limite. Auto-termination só desliga clusters ociosos. (Um *duration warning* apenas notificaria, não cancelaria.)
</details>

---

### 4. No Spark UI, um stage de `groupBy("customer_id")` tem 200 tasks: a mediana é 8 s e a máxima é 14 min. A coluna Shuffle Read da task mais lenta é muito maior que as outras. Qual é o problema?

A) Falta de cache
B) Data skew na chave `customer_id`
C) Driver sem memória
D) Arquivos pequenos demais

<details><summary>Resposta</summary>

**B.** Poucas tasks com duração e volume muito acima da mediana indicam **skew**. Correções: salting da chave, AQE skew handling, tratar chaves nulas/dominantes.
</details>

---

### 5. As métricas de um stage mostram valores altos em **Spill (Disk)**. O que isso indica e qual ação ajuda?

A) A tabela está particionada demais; remover particionamento
B) Dados de partições não couberam na memória dos executores; aumentar o número de partições do shuffle ou usar nós com mais memória
C) O driver está sobrecarregado; usar `collect()` menos vezes
D) O checkpoint de streaming está corrompido

<details><summary>Resposta</summary>

**B.** Spill acontece quando a partição não cabe na memória e o Spark grava em disco. Partições menores (mais partições) ou mais memória por executor reduzem o spill.
</details>

---

### 6. Uma tabela managed do Unity Catalog é filtrada por colunas diferentes ao longo do tempo, e o time não quer escolher chaves de clustering nem agendar OPTIMIZE e VACUUM. Qual combinação atende?

A) `PARTITIONED BY` na coluna de data e ZORDER semanal
B) `CLUSTER BY AUTO` com Predictive Optimization habilitada
C) `ZORDER BY` em todas as colunas
D) Converter a tabela para external

<details><summary>Resposta</summary>

**B.** Automatic liquid clustering escolhe as chaves e a Predictive Optimization executa OPTIMIZE/VACUUM/ANALYZE automaticamente em tabelas **managed** do UC.
</details>

---

### 7. Qual afirmação sobre Liquid Clustering é **verdadeira**?

A) Pode ser combinado com `PARTITIONED BY` na mesma tabela
B) Trocar as chaves com `ALTER TABLE ... CLUSTER BY` exige reescrever toda a tabela imediatamente
C) Substitui particionamento e ZORDER, e as chaves podem ser alteradas sem reescrever os dados existentes
D) Só funciona em tabelas external

<details><summary>Resposta</summary>

**C.** Liquid Clustering não convive com partições nem ZORDER, e mudar chaves vale para novos dados e próximos OPTIMIZE, sem reescrita imediata.
</details>

---

### 8. Um notebook falha com *"The spark driver has stopped unexpectedly and is restarting"* logo após `df.toPandas()` em uma tabela de 80 GB. Qual é a causa mais provável e a correção?

A) OOM no executor; aumentar o número de workers
B) OOM no driver por trazer todos os dados para ele; manter o processamento no Spark (agregar/filtrar antes) ou usar um driver maior
C) Conflito de bibliotecas; reinstalar o pandas
D) Falha no init script; recriar o cluster

<details><summary>Resposta</summary>

**B.** `toPandas()` e `collect()` levam todo o resultado para o driver. Adicionar workers não ajuda, porque o gargalo é o driver.
</details>

---

### 9. Um cluster fica em *Pending* e falha ao iniciar. O Event log mostra que o script `/Volumes/.../install.sh` retornou código de saída 1. O que fazer primeiro?

A) Aumentar o tamanho do driver
B) Verificar os logs do init script e corrigir o comando que falhou
C) Trocar a versão do Python no notebook
D) Habilitar Photon

<details><summary>Resposta</summary>

**B.** Init script com erro impede o cluster de subir. Os logs do init script mostram o comando que falhou.
</details>
