🧭 **Tutorial: Criando um Cluster AKS + Helm + KEDA com Terraform**
===================================================================

📌 **Objetivo**
---------------

Você aprenderá a criar:

*   Uma infraestrutura Kubernetes no Azure usando **AKS**
*   Configurar **Helm** via Terraform
*   Instalar o **NGINX ingress controller** usando Helm
*   Configurar o **KEDA** usando Terraform
*   Entender como funciona a estrutura de arquivos do Terraform
*   Entender o que são **required\_providers**

* * *

🧱 **Como funciona o Terraform (em linguagem simples)**
=======================================================

Terraform é uma ferramenta de **infraestrutura como código (IaC)**.  
Você descreve tudo o que quer criar usando arquivos `.tf`, e o Terraform:

1.  Lê seus arquivos
2.  Compara com o que existe na nuvem
3.  Cria / atualiza / remove recursos para que tudo fique igual ao seu código

* * *

📂 **Estrutura dos arquivos do projeto**
========================================

```
├── main.tf
├── provider.tf
├── variables.tf
├── output.tf
├── keda.tf
├── helm-ingress.tf
```

Cada arquivo tem um propósito. Assim você evita um “arquivo gigante” e mantém tudo organizado.

* * *

🧩 **provider.tf**
==================

O que é um _provider_?
----------------------

Um **provider** é um plugin que permite o Terraform conversar com algum serviço.  
Exemplo:

*   Provider **azurerm** → fala com a Azure
*   Provider **helm** → instala charts no Kubernetes
*   Provider **kubernetes** → cria recursos Kubernetes
*   Provider **keda** (na verdade é helm + CRDs do KEDA)

📌 _required\_providers_: O que são?
------------------------------------

É a seção onde você diz:

*   Quais providers o seu projeto usa
*   De onde eles vêm (repositório)
*   Quais versões devem ser usadas

Isso é **muito importante** porque garante que sua configuração seja **reproduzível**.

### Exemplo claro:

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
    helm = {
      source  = "hashicorp/helm"
      version = "~> 2.12"
    }
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = "~> 2.30"
    }
  }

  required_version = ">= 1.5.0"
}

provider "azurerm" {
  features {}
}

provider "helm" {
  kubernetes {
    config_path = "~/.kube/config"
  }
}

provider "kubernetes" {
  config_path = "~/.kube/config"
}
```

* * *

🧩 **variables.tf**
===================

Todas as variáveis usadas no projeto:

```hcl
variable "resource_group_name" {
  type        = string
  description = "Nome do Resource Group"
}

variable "location" {
  type        = string
  default     = "eastus"
}

variable "aks_name" {
  type        = string
  default     = "meu-aks"
}
```

* * *

🧩 **main.tf**
==============

Aqui criamos:

*   Resource group
*   AKS
*   Acesso ao kubeconfig

### Exemplo simples:

```hcl
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_kubernetes_cluster" "aks" {
  name                = var.aks_name
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = "meucluster"

  default_node_pool {
    name       = "nodepool1"
    node_count = 1
    vm_size    = "Standard_B2s"
  }

  identity {
    type = "SystemAssigned"
  }
}

# Salva o kubeconfig localmente
resource "local_file" "kubeconfig" {
  content  = azurerm_kubernetes_cluster.aks.kube_config_raw
  filename = "${path.module}/kubeconfig"
}
```

Após o apply, você terá o arquivo `kubeconfig`.

* * *

🧩 **output.tf**
================

Serve para mostrar informações úteis ao final:

```hcl
output "kube_config" {
  value = azurerm_kubernetes_cluster.aks.kube_config_raw
  sensitive = true
}
```

* * *

🧩 **helm-ingress.tf**
======================

Aqui instalamos o **NGINX ingress controller** usando Helm.

🧠 O que é Helm?
----------------

**Helm é o “apt-get” do Kubernetes”.**

Ele instala aplicações completas no cluster usando **charts**, que contêm:

*   deployments
*   services
*   configmaps
*   RBAC
*   ingress
*   etc

Ou seja, ao invés de você criar 20 YAMLs, basta usar um único comando.

Aqui vamos instalar o **NGINX ingress**.

### Explicação simples do código:

1.  Criamos o repositório Helm (_helm\_repository_)
2.  Criamos o release (_helm\_release_)

### Arquivo completo:

```hcl
resource "helm_repository" "ingress_nginx" {
  name = "ingress-nginx"
  url  = "https://kubernetes.github.io/ingress-nginx"
}

resource "helm_release" "ingress_nginx" {
  name       = "ingress-nginx"
  repository = helm_repository.ingress_nginx.url
  chart      = "ingress-nginx"
  namespace  = "ingress-nginx"

  create_namespace = true

  values = [
    file("${path.module}/values-ingress.yaml")
  ]
}
```

Crie também um arquivo **values-ingress.yaml** (opcional):

```yaml
controller:
  replicaCount: 1
```

* * *

🧩 **keda.tf**
==============

🧠 O que é o KEDA?
------------------

KEDA = **Kubernetes Event Driven Autoscaler**

É um autoscaler baseado em **eventos externos**, como:

*   fila do RabbitMQ
*   fila do Azure Service Bus
*   fila do Kafka
*   tamanho do banco Redis
*   métricas personalizadas

O HPA do Kubernetes só escala baseado em:

*   CPU
*   memória

O KEDA escala baseado em **qualquer evento que você quiser**.

Instalando KEDA com Helm:
-------------------------

```hcl
resource "helm_repository" "keda" {
  name = "kedacore"
  url  = "https://kedacore.github.io/charts"
}

resource "helm_release" "keda" {
  name             = "keda"
  repository       = helm_repository.keda.url
  chart            = "keda"
  namespace        = "keda"
  create_namespace = true
}
```

* * *

🚀 Passo a passo para rodar tudo
================================

1\. Inicializar Terraform
-------------------------

```
terraform init
```

Ele vai:

*   baixar os providers
*   preparar o ambiente

2\. Ver o que será criado
-------------------------

```
terraform plan
```

Mostra tudo antes de criar.

3\. Criar a infraestrutura
--------------------------

```
terraform apply
```

* * *

🎉 **Pronto!**
==============

Você acabou de:

✔ Criar um AKS do zero  
✔ Instalar e configurar providers  
✔ Instalar NGINX via Helm  
✔ Instalar o KEDA via Helm  
✔ Entender a estrutura do Terraform  
✔ Aprender sobre required\_providers