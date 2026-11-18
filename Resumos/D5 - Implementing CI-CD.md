# Domínio 5 — Implementing CI/CD (10% · ~4-5 questões)

> ⚠️ O curso cobre apenas Git folders (`03/6`). Bundles e CLI são estudados por este resumo.
> Nome atual: **Declarative Automation Bundles** (antes: *Databricks Asset Bundles*, "DABs"). A prova pode usar os dois nomes.

**Objetivos do guia:**
1. Gerenciar o fluxo de desenvolvimento pela UI do workspace: branches em Git folders, commit, push e pull request.
2. Configuração por ambiente com **variáveis** e **overrides** de bundle (dev / test / prod).
3. Fazer deploy de bundles para empacotar e promover Jobs, pipelines e outros recursos.
4. Usar a **Databricks CLI** para validar, fazer deploy e gerenciar bundles em pipelines de CI/CD.

---

## 1. Git folders (antigo "Repos")

| Ação | Onde acontece |
|---|---|
| Clonar repositório, criar/trocar branch, pull, commit & push, merge, resolver conflito | **UI do Databricks** (Git folder → botão da branch) |
| Abrir e aprovar **pull request** | **No provedor Git** (GitHub, GitLab, Azure DevOps, Bitbucket). O Databricks não aprova PR |
| Credencial | Token pessoal (PAT) ou GitHub App, configurado em *User settings → Linked accounts* |

Pontos de prova:
- Um Git folder é ligado a **um** repositório remoto. Cada usuário costuma trabalhar no seu próprio Git folder (em `/Workspace/Users/<você>/`).
- Fluxo típico: criar branch de feature → editar → **commit & push** → abrir PR no Git → merge na `main` → CI faz o deploy.
- Notebooks são salvos como arquivo-fonte (`.py`, `.sql`) ou `.ipynb`. Saídas de células não vão para o Git por padrão.
- **Version history** do notebook ≠ Git: é local ao workspace, sem branches.

## 2. Bundles: o que são e por que usar

Um bundle é **infraestrutura como código** para o Databricks: um projeto com arquivos YAML + código (notebooks, `.py`, SQL) que descreve Jobs, pipelines, dashboards, etc.

| Deploy "tradicional" | Bundles |
|---|---|
| Criar Job na UI, copiar para outro workspace na mão | Definição em YAML versionada no Git |
| Configuração diferente por ambiente feita manualmente | `targets` + variáveis com override por ambiente |
| Difícil de revisar e reproduzir | Revisão via PR, deploy repetível pela CLI |
| Sem rastro de quem mudou o quê | Histórico no Git, deploy automatizado no CI |

## 3. Estrutura de um bundle

```
meu_projeto/
├── databricks.yml          # arquivo raiz OBRIGATÓRIO
├── resources/
│   ├── etl_job.yml         # definição do Job
│   └── vendas_pipeline.yml # definição do pipeline declarativo
└── src/
    ├── bronze.py
    └── silver.sql
```

```yaml
# databricks.yml
bundle:
  name: vendas_etl

include:
  - resources/*.yml

variables:
  catalog:
    description: Catálogo de destino
    default: dev_catalog
  db_password:
    description: Segredo, NUNCA com valor no arquivo

targets:
  dev:
    mode: development     # prefixa recursos com [dev usuario] e pausa schedules
    default: true
    workspace:
      host: https://adb-111.azuredatabricks.net
  prod:
    mode: production
    workspace:
      host: https://adb-222.azuredatabricks.net
    variables:
      catalog: prod_catalog   # override do valor por ambiente
```

```yaml
# resources/etl_job.yml
resources:
  jobs:
    etl_vendas:
      name: etl-vendas-${bundle.target}
      tasks:
        - task_key: bronze
          notebook_task:
            notebook_path: ../src/bronze.py
            base_parameters:
              catalog: ${var.catalog}
```

Mapeamentos de topo que você precisa reconhecer: `bundle`, `include`, `variables`, `resources`, `targets`, `workspace`, `artifacts`, `permissions`, `run_as`.

### Variáveis: ordem de precedência (do maior para o menor)
1. Flag na CLI: `databricks bundle deploy -t prod --var="catalog=prod_catalog"`
2. Variável de ambiente **`BUNDLE_VAR_<nome>`**, por exemplo `BUNDLE_VAR_db_password` vinda do GitHub Secrets
3. Valor definido no `targets.<alvo>.variables` (override)
4. `default` da variável

Referência: `${var.<nome>}`. Substituições prontas: `${bundle.target}`, `${bundle.name}`, `${workspace.current_user.userName}`.

> 🎯 **Pegadinha clássica:** "como manter a senha fora do `databricks.yml` e do código?" → declarar a variável **sem default** e passá-la por `BUNDLE_VAR_db_password` (ou usar Databricks **secrets**). Nunca escrever o valor no YAML.

### `mode: development` x `mode: production`
| development | production |
|---|---|
| Recursos ganham prefixo `[dev <usuário>]` | Nomes como definidos |
| Schedules e triggers ficam **pausados** | Schedules ativos |
| Cada dev tem sua cópia isolada | Recomenda-se `run_as` com service principal |

## 4. Databricks CLI

```bash
databricks auth login --host https://adb-111.azuredatabricks.net   # autenticação (OAuth U2M)
databricks bundle init                  # cria projeto a partir de um template
databricks bundle validate -t dev       # checa sintaxe e configuração (não faz deploy)
databricks bundle deploy -t dev         # envia arquivos e cria/atualiza recursos
databricks bundle run -t dev etl_vendas # executa um job/pipeline do bundle
databricks bundle summary -t prod       # mostra o que está implantado
databricks bundle destroy -t dev        # remove os recursos do bundle
databricks bundle generate job --existing-job-id 123  # converte job da UI em YAML
```

- Perfis de autenticação ficam em `~/.databrickscfg`. No CI, use **service principal com OAuth M2M** (variáveis `DATABRICKS_HOST`, `DATABRICKS_CLIENT_ID`, `DATABRICKS_CLIENT_SECRET`).
- A CLI também gerencia o workspace fora dos bundles: `databricks jobs list`, `databricks workspace import`, `databricks secrets put-secret`, etc.

## 5. Fluxo de CI/CD de referência

```
dev (Git folder) → commit & push → Pull Request → CI roda "bundle validate" + testes
       → merge na main → CD roda "bundle deploy -t staging" → testes → "bundle deploy -t prod"
```

---

## ✅ Checklist do domínio
- [ ] Sei onde se cria o PR (provedor Git) e o que se faz na UI do Git folder
- [ ] Sei o papel do `databricks.yml`, `resources`, `targets`, `variables`
- [ ] Sei sobrescrever variável por target, por `--var` e por `BUNDLE_VAR_`
- [ ] Sei a diferença entre `mode: development` e `mode: production`
- [ ] Sei a ordem `validate` → `deploy` → `run`

## 📚 Fontes oficiais
- [Declarative Automation Bundles / Asset Bundles](https://learn.microsoft.com/azure/databricks/dev-tools/bundles/)
- [Variáveis de bundle](https://learn.microsoft.com/azure/databricks/dev-tools/bundles/variables)
- [Comandos `bundle` da CLI](https://learn.microsoft.com/azure/databricks/dev-tools/cli/bundle-commands)
- [Git folders](https://learn.microsoft.com/azure/databricks/repos/)
