# 🧩 O que é um Resource Group

Um Resource Group é um contêiner lógico que reúne todos os recursos relacionados a um mesmo projeto, aplicação ou ambiente.

Por exemplo:
 - Você cria uma aplicação web.
 - Essa aplicação precisa de:
   - uma **máquina virtual**,
   - um **banco de dados SQL**,
   - uma **conta de armazenamento** e
   - uma **rede virtual**.

👉 Todos esses recursos podem ser colocados **dentro do mesmo Resource Group**, facilitando a **organização**, **monitoramento** e **gerenciamento**.

# 🧭 Principais características

1. **Organização** – agrupa recursos que pertencem a um mesmo projeto, aplicação ou ambiente (ex: “app-prod” ou “app-teste”).
2. **Gerenciamento em conjunt**o – você pode aplicar **políticas, tags, permissões (RBAC)** e **automação** a todos os recursos do grupo de uma vez.
3. **Implantação simplificada** – ao usar templates (como ARM ou Bicep), você pode criar ou apagar todos os recursos do grupo em um único comando.
4. **Limite lógico, não físico** – não afeta a comunicação entre recursos de grupos diferentes; é apenas uma forma de organização e controle.
5. **Localização (região)** – o Resource Group em si tem uma **região**, mas os recursos dentro dele podem estar em **outras regiões**, se desejado.

# ⚙️ Exemplo prático

Digamos que você vai criar uma aplicação web chamada **MyApp**:

 - Resource Group: `rg-myapp-prod`
 - Dentro dele:
   - App Service: `myapp-web`
   - Banco de Dados SQL: `myapp-db`
   - Storage Account: `myappstorage`
   - Virtual Network: `myapp-vnet`

Assim, se quiser **deletar o projeto inteiro**, basta deletar o `rg-myapp-prod` — e **todos os recursos dentro dele serão removidos juntos**.

# 🧱 Boas práticas

 - Use **nomes descritivos** (ex: `rg-sitecorp-dev`, `rg-sitecorp-prod`).
 - Agrupe por **ciclo de vida** (todos os recursos que devem ser criados e apagados juntos).
 - Aplique **tags** para identificar dono, ambiente, custo, etc.
 - Evite misturar recursos de **produção e teste** no mesmo grupo.
