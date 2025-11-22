📘 Documentação: Uso do `actions/checkout@v3` no GitHub Actions
===============================================================

🔎 O que é o `actions/checkout@v3`?
-----------------------------------

`actions/checkout@v3` é uma **ação oficial do GitHub** utilizada para **clonar o repositório dentro do runner** durante a execução de um workflow.

O runner sempre inicia **vazio**, sem o código do repositório.  
O checkout é responsável por disponibilizar os arquivos necessários para:

*   builds
*   testes
*   validações
*   execução de ferramentas (Terraform, Docker, Node, etc.)
*   deploys
*   leituras de arquivos do repositório

* * *

🧠 O que acontece internamente?
-------------------------------

Quando o checkout é executado:

*   O GitHub baixa o repositório no estado do commit que disparou o workflow.
*   Os arquivos são colocados no diretório `GITHUB_WORKSPACE`.
*   Todos os steps seguintes do **mesmo job** passam a ter acesso ao código.
*   É possível configurar:
    *   branch específica
    *   tag
    *   commit
    *   submódulos
    *   fetch completo

* * *

❗ É necessário um checkout para cada step?
------------------------------------------

**Não.**

Dentro de um **job**, você precisa rodar o checkout **apenas uma vez**.  
Todos os steps seguintes usam o mesmo diretório e têm acesso aos mesmos arquivos.

### ✔️ Exemplo correto (checkout apenas 1 vez por job)

```yaml
steps:
  - uses: actions/checkout@v3
  - run: npm install
  - run: npm test
  - run: npm run build
```

### ❌ Exemplo incorreto (checkout repetido sem necessidade)

```yaml
steps:
  - uses: actions/checkout@v3
  - run: npm install
  - uses: actions/checkout@v3  # Desnecessário
  - run: npm test
```

Repetir o checkout **dentro do mesmo job** aumenta o tempo e não traz benefício.

* * *

✔️ Quando o checkout realmente precisa ser repetido?
----------------------------------------------------

Existem casos específicos:

### 1\. **Jobs diferentes**

Cada job roda em um **runner diferente**, completamente isolado.

**O checkout do Job 1 não é herdado pelo Job 2.**

Por isso, **cada job precisa do seu próprio `actions/checkout@v3`**.

### 2\. Uso de mais de um repositório

Ex.:

```yaml
- uses: actions/checkout@v3
- uses: actions/checkout@v3
  with:
    repository: org/infra-modules
    path: modules
```

### 3\. O workflow remove o diretório manualmente

Ex.:

```bash
rm -rf ./*
```

* * *

📌 Caso prático mostrado: workflow Terraform com 2 checkouts
------------------------------------------------------------

O usuário apresentou o seguinte workflow simplificado:

```
jobs:
  validate:
    steps:
      - uses: actions/checkout@v3
      ...
  deploy:
    needs: validate
    steps:
      - uses: actions/checkout@v3
      ...
```

### ❓ Isso é desnecessário?

➡️ **Não. É necessário.**

### ✔️ Explicação

*   `validate` é um job.
*   `deploy` é outro job.

Cada job:

*   roda em uma máquina separada
*   começa totalmente vazia
*   não compartilha arquivos com o outro job

Por isso, cada job **precisa do seu próprio checkout**.

### 🔥 Regra de ouro:

> **Um checkout por job**  
> **Nunca mais de um por job (exceto casos especiais)**

* * *

📑 Resumo geral
---------------

| Cenário | Checkout necessário? |
| --- | --- |
| Steps dentro do mesmo job | ❌ Não |
| Jobs diferentes | ✔️ Sim |
| Workflow baixa outro repositório | ✔️ Sim |
| Diretório é apagado | ✔️ Sim |
| Apenas múltiplos steps normais | ❌ Não |