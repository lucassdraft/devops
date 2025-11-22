O `actions/checkout@v3` é uma **ação oficial do GitHub** usada em praticamente todos os workflows porque ela **clona o repositório no ambiente da execução do GitHub Actions**.

### 📌 Para que serve?

Quando um workflow inicia, **o runner não possui o código do repositório**.  
O `actions/checkout@v3` faz o **download (checkout)** do código para dentro do runner, permitindo que você:

*   execute builds
*   execute testes
*   acesse arquivos do repo (YAML, código, configs etc.)
*   faça deploy baseado nos arquivos do repositório
*   execute ferramentas que precisam do código (Terraform, Node, Docker, etc.)

### ✔️ Exemplo simples

```yaml
steps:
  - name: Checkout do código
    uses: actions/checkout@v3
```

### 🧠 O que ele faz internamente?

*   Faz clone do repositório na versão do commit que disparou o workflow.
*   Coloca o código no diretório padrão `GITHUB_WORKSPACE`.
*   Suporta checkout de branches, tags, commits específicos.
*   Pode ser configurado para baixar submódulos, fazer fetch completo etc.

### 💡 Sem o checkout…

Quase todos os workflows quebram, porque **não haveria código disponível** para rodar qualquer ferramenta.