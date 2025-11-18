🌐 **Criar VM na Azure**
=============================================================

📁 Estrutura dos Arquivos
-------------------------

Crie uma pasta do projeto e dentro dela os seguintes arquivos:

```
/azure-terraform/
   ├── main.tf
   ├── provider.tf
   ├── variables.tf
   └── outputs.tf
```

* * *

1️⃣ **provider.tf — Configurando o Provider da Azure**
======================================================

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

* * *

2️⃣ **variables.tf — Variáveis para facilitar reuso**
=====================================================

```hcl
variable "location" {
  type    = string
  default = "eastus"
}

variable "resource_group_name" {
  type    = string
  default = "rg-terraform-demo"
}

variable "vm_size" {
  type    = string
  default = "Standard_B1s"
}

variable "admin_username" {
  type    = string
  default = "azureuser"
}

variable "admin_password" {
  type      = string
  sensitive = true
}
```

> **Obs:** Você pode passar `admin_password` via `terraform.tfvars` ou no comando `-var`.

* * *

3️⃣ **main.tf — Recursos principais**
=====================================

Abaixo segue um exemplo completo com todos os recursos pedidos.

* * *

🔹 **Resource Group**
---------------------

```hcl
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}
```

* * *

🔹 **Data Source — Buscar info existente (ex: imagem Ubuntu)**
--------------------------------------------------------------

```hcl
data "azurerm_linux_virtual_machine_image" "ubuntu" {
  location  = var.location
  publisher = "Canonical"
  offer     = "0001-com-ubuntu-server-focal"
  sku       = "20_04-lts"
}
```

Esse data source pega a imagem mais recente do Ubuntu 20.04.

* * *

🔹 **IP Público**
-----------------

```hcl
resource "azurerm_public_ip" "public_ip" {
  name                = "vm-public-ip"
  resource_group_name = azurerm_resource_group.rg.name
  location            = var.location
  allocation_method   = "Static"
  sku                 = "Standard"
}
```

* * *

🔹 **Virtual Network (VNet)**
-----------------------------

```hcl
resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-demo"
  resource_group_name = azurerm_resource_group.rg.name
  location            = var.location
  address_space       = ["10.0.0.0/16"]
}
```

* * *

🔹 **Subnet**
-------------

```hcl
resource "azurerm_subnet" "subnet" {
  name                 = "subnet-demo"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}
```

* * *

🔹 **Network Security Group (NSG)**
-----------------------------------

Com portas **22 (SSH)** e **8081** liberadas.

```hcl
resource "azurerm_network_security_group" "nsg" {
  name                = "nsg-demo"
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name

  security_rule {
    name                       = "allow-ssh"
    priority                   = 1001
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "22"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  security_rule {
    name                       = "allow-8081"
    priority                   = 1002
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "8081"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }
}
```

* * *

🔹 **Network Interface (NIC)**
------------------------------

```hcl
resource "azurerm_network_interface" "nic" {
  name                = "nic-demo"
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "nic-ipconfig"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.public_ip.id
  }
}
```

Associar NIC com o NSG:

```hcl
resource "azurerm_network_interface_security_group_association" "nic_nsg" {
  network_interface_id      = azurerm_network_interface.nic.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}
```

* * *

🔹 **Virtual Machine (Linux)**
------------------------------

```hcl
resource "azurerm_linux_virtual_machine" "vm" {
  name                = "vm-demo"
  resource_group_name = azurerm_resource_group.rg.name
  location            = var.location
  size                = var.vm_size
  admin_username      = var.admin_username
  admin_password      = var.admin_password

  disable_password_authentication = false

  network_interface_ids = [
    azurerm_network_interface.nic.id
  ]

  source_image_reference {
    publisher = data.azurerm_linux_virtual_machine_image.ubuntu.publisher
    offer     = data.azurerm_linux_virtual_machine_image.ubuntu.offer
    sku       = data.azurerm_linux_virtual_machine_image.ubuntu.sku
    version   = data.azurerm_linux_virtual_machine_image.ubuntu.version
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
}
```

* * *

4️⃣ **outputs.tf — Mostrar infos úteis**
========================================

```hcl
output "public_ip" {
  value = azurerm_public_ip.public_ip.ip_address
}

output "vm_id" {
  value = azurerm_linux_virtual_machine.vm.id
}
```

* * *

▶️ **Como rodar o Terraform**
=============================

Dentro da pasta do projeto, execute:

```bash
terraform init
```

Verificar se tudo está certo:

```bash
terraform plan
```

Aplicar:

```bash
terraform apply -auto-approve
```

Quando quiser destruir:

```bash
terraform destroy -auto-approve
```

* * *

✔️ Pronto!
==========

Você agora tem um template completo do Terraform para Azure, com:

✔ Separação em arquivos  
✔ Resource group  
✔ Data source  
✔ IP público  
✔ VNet  
✔ Subnet  
✔ Network Interface  
✔ NSG com portas 22 e 8081  
✔ VM Linux