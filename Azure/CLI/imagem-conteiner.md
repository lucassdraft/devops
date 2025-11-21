# Repositório privado de imagens de contêiner

### 📌 O que exatamente ele faz?

Quando você executa `az acr create`, o Azure CLI:

1.  **Cria um registro de contêiner** em uma **Resource Group** especificada.
2.  **Configura um nome único global** para o ACR.
3.  Define o **SKU** (como Basic, Standard ou Premium), que determina recursos e limites.
4.  (Opcional) Configura:
    *   **Admin user** habilitado ou não.
    *   **Network rules**.
    *   **Replicação geográfica** (em SKUs superiores).
    *   **Zona de disponibilidade** (se suportado).

* * *

### 📌 Exemplo simples

```bash
az acr create \
  --resource-group meuRG \
  --name meuRegistroAcr \
  --sku Basic
```

### 📌 Resultado

Após rodar esse comando, você terá um registro do tipo:

*   **Login server:** `meuregistroacr.azurecr.io`  
    (usado para enviar/puxar imagens Docker)

* * *

### 📌 Para que serve o ACR?

Com o ACR você pode:

*   Armazenar imagens Docker privadas
*   Fazer push/pull usando pipelines CI/CD (Azure DevOps, GitHub Actions etc.)
*   Gerenciar tags e webhooks
*   Integrar com AKS, Web Apps, Container Apps, Functions etc.

* * *

Se quiser, posso te mostrar **todas as opções disponíveis do comando**, ou montar um **guia passo a passo com exemplos**.

## Response:
Aqui está um **guia completo e direto** com **todas as opções úteis** do comando **`az acr create`**, incluindo explicações curtas para cada parâmetro e exemplos práticos.

* * *

🧭 Guia Completo: `az acr create`
=================================

O comando **`az acr create`** é utilizado para **criar um registro de contêiner privado (ACR)** dentro do Azure.

* * *

✅ Sintaxe básica
================

```bash
az acr create --resource-group <RG> --name <NOME_ACR> --sku <SKU>
```

* * *

📌 Principais parâmetros e o que cada um faz
============================================

### **Obrigatórios**

| Parâmetro | Explicação |
| --- | --- |
| `--resource-group` | Resource Group onde o ACR será criado. |
| `--name` | Nome do registro ACR (único global). |
| `--sku` | Tipo de plano: `Basic`, `Standard`, `Premium`. |

* * *

📌 Parâmetros mais usados
=========================

### **Habilitar usuário admin**

Permite login com usuário/senha (não é recomendado para produção).

```bash
--admin-enabled true
```

### **Localização**

Define a região onde o ACR será criado.

```bash
--location eastus
```

### **Tags**

Adiciona tags para organização e governança.

```bash
--tags projeto=app ambiente=dev
```

* * *

📌 Opções mais avançadas
========================

### **Redes privadas / Acesso**

*   Permite somente redes selecionadas:

```bash
--public-network-enabled false
```

*   Permite acesso apenas a endereços IP específicos:

```bash
--ip-rules 200.180.10.1/32 200.180.10.2/32
```

* * *

### **Webhooks**

Cria webhooks automaticamente no ACR:

```bash
--workspace <id-do-log-analytics>
```

* * *

### **Criptografia de chave gerenciada (CMK)**

```bash
--encryption-key-name <key>
--encryption-key-version <version>
--encryption-key-vault <resource-id-keyvault>
```

* * *

### **Replicação geográfica** (somente SKU Premium)

```bash
--zone-redundancy Enabled
```

* * *

### **Habilitar logging do diagnóstico**

```bash
--workspace <ID_LOG_ANALYTICS>
```

* * *

🧪 Exemplos práticos
====================

1️⃣ Criar um ACR básico
-----------------------

```bash
az acr create \
  --resource-group MeuRG \
  --name meuacrteste123 \
  --sku Basic
```

* * *

2️⃣ Criar ACR com admin habilitado
----------------------------------

```bash
az acr create \
  --resource-group MeuRG \
  --name meuacrdev \
  --sku Standard \
  --admin-enabled true
```

* * *

3️⃣ ACR com bloqueio de rede pública
------------------------------------

```bash
az acr create \
  --resource-group ProdRG \
  --name acrprotegido \
  --sku Premium \
  --public-network-enabled false
```

* * *

4️⃣ ACR Premium com replicação e encryption
-------------------------------------------

```bash
az acr create \
  --resource-group GlobalRG \
  --name acrglobalpremium \
  --sku Premium \
  --zone-redundancy Enabled \
  --encryption-key-name minhaKey \
  --encryption-key-version v1 \
  --encryption-key-vault /subscriptions/.../keyvaults/meuKV
```

* * *

📚 Como verificar o ACR depois de criar
=======================================

```bash
az acr show --name meuacr --resource-group MeuRG
```

Listar os registries:

```bash
az acr list --output table
```

* * *

📦 Comandos úteis após criar o ACR
==================================

### Fazer _login_ no ACR

```bash
az acr login --name meuacr
```

### Obter o login server

```bash
az acr show --name meuacr --query "loginServer" -o tsv
```

### Fazer _push_ de uma imagem Docker

```bash
docker tag nginx:latest meuacr.azurecr.io/nginx:latest
docker push meuacr.azurecr.io/nginx:latest
```
