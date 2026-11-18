# Questões — Domínio 5: Implementing CI/CD

> Questões **inéditas** escritas no estilo da prova, a partir dos objetivos do guia oficial (ago/2026). Não são questões vazadas.
> Responda todas antes de abrir as respostas (clique em "Resposta").

---

### 1. Um engenheiro terminou uma alteração em um notebook dentro de um Git folder e quer que ela seja revisada pela equipe antes de entrar na branch `main`. Qual é a sequência correta?

A) Fazer commit & push na `main` pela UI do Databricks e pedir revisão por e-mail
B) Criar uma branch de feature, fazer commit & push pela UI do Git folder e abrir um pull request no provedor Git
C) Exportar o notebook como `.dbc` e anexá-lo a um pull request
D) Usar o Version History do notebook para enviar a versão para revisão

<details><summary>Resposta</summary>

**B.** O Git folder permite criar branch, commit e push na UI. A revisão e a aprovação acontecem no **pull request**, que é aberto e mesclado no provedor Git (GitHub, Azure DevOps…). Version History é local e não tem fluxo de revisão.
</details>

---

### 2. Qual arquivo é obrigatório na raiz de um Declarative Automation Bundle (antigo Databricks Asset Bundle)?

A) `bundle.json`
B) `databricks.yml`
C) `pipeline.yaml`
D) `.databrickscfg`

<details><summary>Resposta</summary>

**B.** O `databricks.yml` define o bundle (`bundle`, `include`, `variables`, `targets`…). `.databrickscfg` guarda perfis de autenticação da CLI, não faz parte do bundle.
</details>

---

### 3. Um bundle deve escrever no catálogo `dev_catalog` no ambiente de desenvolvimento e em `prod_catalog` em produção, usando o mesmo código. Qual é a abordagem recomendada?

A) Manter dois bundles separados, um por ambiente
B) Criar uma variável `catalog` com `default: dev_catalog` e sobrescrevê-la em `targets.prod.variables`
C) Usar `if` dentro do notebook verificando o nome do workspace
D) Alterar o valor no YAML manualmente antes de cada deploy em produção

<details><summary>Resposta</summary>

**B.** Variáveis com valor padrão e **override por target** são o mecanismo de configuração por ambiente. O código referencia `${var.catalog}`.
</details>

---

### 4. Durante o deploy automatizado no GitHub Actions, a senha de um banco de dados não pode aparecer no `databricks.yml` nem no código. Como o valor deve chegar à variável `db_password` do bundle?

A) Definir `default:` com a senha e restringir o acesso ao repositório
B) Escrever a senha em um widget do notebook
C) Declarar a variável sem valor e passá-la pela variável de ambiente `BUNDLE_VAR_db_password`, alimentada por um secret do CI
D) Salvar a senha em um arquivo `.txt` dentro de `src/`

<details><summary>Resposta</summary>

**C.** Variáveis de ambiente com prefixo `BUNDLE_VAR_` preenchem variáveis do bundle. O valor vem do cofre de secrets do CI (ex.: GitHub Secrets) e nunca é commitado. (Alternativa válida em runtime: Databricks secrets.)
</details>

---

### 5. Qual comando da CLI verifica a sintaxe e a configuração do bundle para o target `prod` **sem** criar ou alterar recursos no workspace?

A) `databricks bundle deploy -t prod --dry-run`
B) `databricks bundle validate -t prod`
C) `databricks bundle run -t prod`
D) `databricks bundle summary -t prod`

<details><summary>Resposta</summary>

**B.** `validate` checa a configuração. `deploy` cria/atualiza recursos, `run` executa um job/pipeline, `summary` mostra o que já está implantado.
</details>

---

### 6. Dois engenheiros fazem deploy do mesmo bundle com `mode: development` no mesmo workspace. O que acontece?

A) O segundo deploy sobrescreve o job do primeiro
B) O deploy falha porque o nome do job já existe
C) Cada um recebe uma cópia própria, com o nome prefixado por `[dev <usuário>]` e schedules pausados
D) Os jobs são criados já com o schedule de produção ativo

<details><summary>Resposta</summary>

**C.** O modo development isola os recursos por usuário (prefixo `[dev usuario]`) e pausa schedules e triggers para evitar execuções acidentais.
</details>

---

### 7. Uma equipe criou um job complexo pela UI e agora quer passá-lo a ser gerenciado por um bundle no Git. Qual é a forma mais rápida?

A) Recriar o YAML à mão olhando a UI
B) `databricks bundle generate job --existing-job-id <id>`
C) Exportar o job como notebook
D) `databricks jobs reset`

<details><summary>Resposta</summary>

**B.** `bundle generate` gera a configuração YAML (e baixa os arquivos) a partir de um recurso existente.
</details>

---

### 8. Qual é a principal vantagem de bundles em relação a criar jobs manualmente na UI de cada workspace?

A) Bundles executam jobs mais rápido
B) Bundles dispensam Unity Catalog
C) A definição dos recursos fica versionada no Git e pode ser promovida de forma repetível entre dev, test e prod
D) Bundles só funcionam com clusters all-purpose

<details><summary>Resposta</summary>

**C.** Bundles são infraestrutura como código: versionamento, revisão por PR e deploy reproduzível em vários ambientes. Não mudam a velocidade de execução.
</details>
