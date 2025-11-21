🧭 1. O que você vai aprender
=============================

*   Como funciona o Ansible
*   Como criar máquinas virtuais na Azure usando Ansible
*   Como configurar acesso SSH
*   Como organizar seu projeto Ansible
*   Como usar módulos Azure do Ansible
*   Como instalar e configurar o NGINX via playbooks

* * *

✅ 2. Pré-requisitos
===================

### **No seu computador**

*   Python 3
*   Ansible instalado:
    ```bash
    pip install ansible
    ```
*   SDK da Azure:
    ```bash
    pip install azure-cli
    ```
*   Login na Azure:
    ```bash
    az login
    ```

### **Credenciais da Azure para o Ansible**

Gerar um **Service Principal** (usuário para automação):

```bash
az ad sp create-for-rbac --name ansible-sp --role Contributor
```

Ele retornará algo assim:

```json
{
  "appId": "...",
  "displayName": "ansible-sp",
  "password": "...",
  "tenant": "..."
}
```

Guarde isso — você vai colocar no Ansible.

* * *

📁 3. Estrutura do projeto
==========================

Crie uma pasta:

```
ansible-azure/
│
├── inventory.ini
├── install-nginx.yml
├── provision-azure.yml
└── group_vars/
    └── all.yml
```

* * *

🔐 4. Configurar variáveis da Azure (group\_vars/all.yml)
=========================================================

Crie `group_vars/all.yml`:

```yaml
azure_client_id: "APPID_DO_SERVICE_PRINCIPAL"
azure_secret: "SENHA_DO_SERVICE_PRINCIPAL"
azure_tenant: "TENANT_ID"
azure_subscription: "SUA_SUBSCRIPTION_ID"
```

Pegue a subscription:

```bash
az account show --query id -o tsv
```

* * *

🌩️ 5. Criar uma VM na Azure com Ansible
========================================

(Playbook `provision-azure.yml`)

Este playbook:

*   Cria um Resource Group
*   Cria uma VNet
*   Cria uma Subnet
*   Cria IP público
*   Cria NIC
*   Cria uma VM Ubuntu
*   Configura SSH

Crie `provision-azure.yml`:

```yaml
---
- name: Provisionar infraestrutura na Azure
  hosts: localhost
  connection: local
  vars:
    vm_name: "webserver01"
    vm_user: "ubuntu"
    vm_size: "Standard_B1s"
    location: "eastus"
  tasks:

    - name: Criar Resource Group
      azure.azcollection.azure_rm_resourcegroup:
        name: rg-webserver
        location: "{{ location }}"

    - name: Criar VNet
      azure.azcollection.azure_rm_virtualnetwork:
        resource_group: rg-webserver
        name: vnet-web
        address_prefixes: "10.0.0.0/16"

    - name: Criar Subnet
      azure.azcollection.azure_rm_subnet:
        resource_group: rg-webserver
        name: subnet-web
        address_prefix: "10.0.1.0/24"
        virtual_network: vnet-web

    - name: Criar IP Público
      azure.azcollection.azure_rm_publicipaddress:
        resource_group: rg-webserver
        name: ip-web
        allocation_method: Static

    - name: Criar Interface de Rede
      azure.azcollection.azure_rm_networkinterface:
        resource_group: rg-webserver
        name: nic-web
        subnet: subnet-web
        virtual_network: vnet-web
        public_ip_name: ip-web

    - name: Criar Máquina Virtual
      azure.azcollection.azure_rm_virtualmachine:
        resource_group: rg-webserver
        name: "{{ vm_name }}"
        vm_size: "{{ vm_size }}"
        admin_username: "{{ vm_user }}"
        ssh_password_enabled: false
        ssh_public_keys:
          - path: "/home/{{ vm_user }}/.ssh/authorized_keys"
            key_data: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
        network_interfaces: nic-web
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: "20_04-lts"
          version: latest

    - name: Mostrar IP criado
      azure.azcollection.azure_rm_publicipaddress_info:
        resource_group: rg-webserver
        name: ip-web
      register: ip_info

    - debug:
        msg: "IP público: {{ ip_info.azure_publicipaddresses[0].ip_address }}"
```

* * *

📌 6. Rodar o playbook de provisionamento
=========================================

```bash
ansible-playbook provision-azure.yml
```

Ele exibirá o IP público.

Guarde esse IP e coloque em `inventory.ini`.

* * *

🗂️ 7. Configurar o inventário do Ansible
=========================================

Crie `inventory.ini`:

```
[webservers]
webserver01 ansible_host=SEU_IP_PUBLICO ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

* * *

🌐 8. Instalar NGINX e configurar site (install-nginx.yml)
==========================================================

Crie o playbook:

```yaml
---
- name: Instalar NGINX e configurar servidor web
  hosts: webservers
  become: yes
  tasks:

    - name: Atualizar pacotes
      apt:
        update_cache: yes

    - name: Instalar NGINX
      apt:
        name: nginx
        state: present

    - name: Criar página web personalizada
      copy:
        dest: /var/www/html/index.html
        content: |
          <html>
          <h1>Servidor configurado com Ansible 🚀</h1>
          <p>Parabéns! Seu NGINX está funcionando.</p>
          </html>

    - name: Iniciar e habilitar NGINX
      systemd:
        name: nginx
        enabled: yes
        state: started
```

* * *

▶️ 9. Rodar o playbook de instalação
====================================

```bash
ansible-playbook -i inventory.ini install-nginx.yml
```

* * *

🌐 10. Acessar o servidor
=========================

No navegador, abra:

```
http://SEU_IP_PUBLICO
```

Você verá:

**Servidor configurado com Ansible 🚀**

* * *

🎉 Conclusão
============

Você aprendeu:

✔ Criar infraestrutura na Azure via Ansible  
✔ Criar VM Ubuntu automatizada  
✔ Usar módulos da Azure Collection  
✔ Configurar acesso SSH  
✔ Instalar e configurar NGINX  
✔ Criar uma página web simples
