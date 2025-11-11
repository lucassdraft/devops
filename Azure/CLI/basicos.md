# 📘 Guia de Comandos Mais Utilizados no Azure CLI

> **Objetivo:** servir como **consulta rápida** com comandos curtos e explicações resumidas.

---

## 🔧 Configuração e Login

Autentica no Azure.
```bash
az login
```

Mostra informações da conta atual.
```bash
az account show
```

Lista todas as contas disponíveis.
```bash
az account list
```

Seleciona a assinatura a ser usada.
```bash
az account set --subscription "<nome-ou-id>"
```

Define padrões (grupo, região, etc).
```bash
az configure --defaults group=<resource-group> location=<região>
```

---

## 🗂️ Resource Groups

Cria um novo grupo de recursos.
```bash
az group create --name <nome> --location <região>
```

Lista grupos existentes.
```bash
az group list
```

Exibe detalhes de um grupo.
```bash
az group show --name <nome>
```

Remove um grupo de recursos.
```bash
az group delete --name <nome> --yes
```

---

## ☁️ Máquinas Virtuais (VMs)

Cria uma VM Ubuntu.
```bash
az vm create --resource-group <grupo> --name <nome> --image UbuntuLTS
```

Lista VMs no grupo atual.
```bash
az vm list
```

Mostra detalhes de uma VM.
```bash
az vm show --name <nome>
```

Inicia uma VM.
```bash
az vm start --name <nome>
```

Para uma VM.
```bash
az vm stop --name <nome>
```

Desaloca a VM (para economizar custos).
```bash
az vm deallocate --name <nome>
```

Exclui uma VM.
```bash
az vm delete --name <nome> --yes
```

---

## 🌐 Redes (VNet, Subnets, IPs, NSG)

Cria uma rede virtual e uma sub-rede.
```bash
az network vnet create --name <vnet> --resource-group <grupo> --subnet-name <subnet>
```

Lista VNets.
```bash
az network vnet list
```

Lista sub-redes de uma VNet.
```bash
az network vnet subnet list --vnet-name <vnet>
```

Cria um IP público.
```bash
az network public-ip create --name <ip-name> --resource-group <grupo>
```

Cria um grupo de segurança de rede.
```bash
az network nsg create --name <nsg> --resource-group <grupo>
```

Cria regra para permitir acesso SSH (porta 22).
```bash
az network nsg rule create --nsg-name <nsg> --name <regra> --priority 100 --protocol Tcp --destination-port-ranges 22 --access Allow
```

---

## 💾 Armazenamento (Storage Accounts, Blobs)

Cria uma conta de armazenamento.
```bash
az storage account create --name <nome> --resource-group <grupo> --location <região> --sku Standard_LRS
```

Lista contas de armazenamento.
```bash
az storage account list
```

Cria um container de blob.
```bash
az storage container create --name <container> --account-name <conta>
```

Faz upload de um arquivo.
```bash
az storage blob upload --account-name <conta> --container-name <container> --name <arquivo> --file <caminho>
```

Lista blobs em um container.
```bash
az storage blob list --container-name <container> --account-name <conta>
```

---

## ⚙️ Azure Kubernetes Service (AKS)

Cria um cluster AKS.
```bash
az aks create --resource-group <grupo> --name <cluster> --node-count 2 --enable-addons monitoring --generate-ssh-keys
```

Configura o `kubectl` para acessar o cluster.
```bash
az aks get-credentials --resource-group <grupo> --name <cluster>
```

Lista clusters AKS.
```bash
az aks list
```

Remove um cluster AKS.
```bash
az aks delete --resource-group <grupo> --name <cluster> --yes
```

---

## 🧩 App Services

Cria um plano de hospedagem.
```bash
az appservice plan create --name <plano> --resource-group <grupo> --sku B1 --is-linux
```

Cria um app web.
```bash
az webapp create --resource-group <grupo> --plan <plano> --name <app> --runtime "NODE|18-lts"
```

Lista aplicativos web.
```bash
az webapp list
```

Para o app web.
```bash
az webapp stop --name <app>
```

Inicia o app web.
```bash
az webapp start --name <app>
```

---

## 🔄 Monitoramento e Logs

Exibe métricas de um recurso.
```bash
az monitor metrics list --resource <id-recurso>
```

Lista logs de atividade recentes.
```bash
az monitor activity-log list
```

Lista workspaces do Log Analytics.
```bash
az monitor log-analytics workspace list
```

---

## 🧰 Diversos

Mostra a versão do Azure CLI.
```bash
az version
```

Mostra exemplos de uso de um comando.
```bash
az find "vm create"
```

Lista extensões disponíveis.
```bash
az extension list-available
```

Atualiza o Azure CLI.
```bash
az upgrade
```

---

## 🧹 Limpeza

Lista todos os recursos.
```bash
az resource list
```

Remove um recurso específico.
```bash
az resource delete --ids <id>
```
