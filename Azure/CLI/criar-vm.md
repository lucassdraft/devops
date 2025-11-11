# Guia Azure CLI - Criação e Gerenciamento de Recursos

Este guia demonstra, passo a passo, como criar e gerenciar recursos no
**Azure CLI**, incluindo **Resource Group**, **Máquinas Virtuais
(VMs)**, **IPs**, **Discos**, **Interfaces de Rede** e **Network
Security Groups (NSGs)**.

------------------------------------------------------------------------

## 🔹 1. Criar um Resource Group

Cria um grupo de recursos chamado `MeuResourceGroup` na região `eastus`.

``` bash
az group create --name MeuResourceGroup --location eastus
```

------------------------------------------------------------------------

## 🔹 2. Criar duas Máquinas Virtuais

Cria duas VMs (VM1 e VM2) com o Ubuntu 22.04 e autenticação via SSH.

``` bash
az vm create   --resource-group MeuResourceGroup   --name VM1   --image Ubuntu2204   --admin-username azureuser   --generate-ssh-keys
```

``` bash
az vm create   --resource-group MeuResourceGroup   --name VM2   --image Ubuntu2204   --admin-username azureuser   --generate-ssh-keys
```

------------------------------------------------------------------------

## 🔹 3. Listar as Máquinas Virtuais

Lista todas as VMs do grupo de recursos em formato de tabela.

``` bash
az vm list --resource-group MeuResourceGroup -o table
```

------------------------------------------------------------------------

## 🔹 4. Mostrar os IPs das VMs

Mostra os endereços IP (públicos e privados) das VMs.

``` bash
az vm list-ip-addresses --resource-group MeuResourceGroup -o table
```

------------------------------------------------------------------------

## 🔹 5. Mostrar apenas os IPs públicos

Lista apenas os IPs públicos associados às VMs.

``` bash
az network public-ip list --resource-group MeuResourceGroup -o table
```

------------------------------------------------------------------------

## 🔹 6. Listar Discos

Lista os discos gerenciados existentes no grupo de recursos.

``` bash
az disk list --resource-group MeuResourceGroup -o table
```

------------------------------------------------------------------------

## 🔹 7. Listar Interfaces de Rede

Mostra todas as interfaces de rede (NICs) criadas no grupo de recursos.

``` bash
az network nic list --resource-group MeuResourceGroup -o table
```

------------------------------------------------------------------------

## 🔹 8. Mostrar Network Security Groups (NSGs)

Exibe todos os **Network Security Groups** do grupo de recursos.

``` bash
az network nsg list --resource-group MeuResourceGroup -o table
```

------------------------------------------------------------------------

## ✅ Conclusão

Esses comandos permitem **criar, listar e gerenciar** os principais
recursos de uma infraestrutura básica no Azure, utilizando o **Azure
CLI** de forma simples e prática.
