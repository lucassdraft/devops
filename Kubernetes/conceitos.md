📘 Documentação – Introdução e Conceitos Fundamentais do Kubernetes
===================================================================

1\. O que é Kubernetes?
-----------------------

Kubernetes (ou **K8s**) é uma plataforma open-source para **orquestração de contêineres**, criada pelo Google e mantida pela CNCF.  
Ele automatiza:

*   Deploy de aplicações em contêineres
*   Escalabilidade automática
*   Balanceamento de carga
*   Atualizações sem downtime (rolling updates)
*   Recuperação automática (self-healing)
*   Gestão de armazenamento e volumes

O objetivo é garantir que aplicativos distribuídos rodem de forma **confiável, escalável e resiliente**.

* * *

2\. Por que usar Kubernetes?
----------------------------

Kubernetes resolve problemas presentes em ambientes com muitos contêineres:

*   Como garantir que contêineres sempre voltem após falhas?
*   Como escalar automaticamente quando o tráfego aumenta?
*   Como fazer atualizações sem parar o serviço?
*   Como organizar serviços distribuídos?

Ele se tornou o padrão da indústria por oferecer **alto nível de automação** e **portabilidade**.

* * *

3\. Conceitos e Componentes Fundamentais
========================================

3.1 **Cluster**
---------------

Um **cluster Kubernetes** é formado por:

*   **Master Nodes (Control Plane)** → gerenciam o cluster
*   **Worker Nodes** → executam aplicações (contêineres)

* * *

3.2 **Node**
------------

Um **Node** é uma máquina (VM ou física) onde os contêineres rodam.  
Cada node executa:

### Componentes do Node

*   **kubelet** – agente que conversa com o Control Plane
*   **kube-proxy** – gerencia regras de rede e serviços
*   **container runtime** – Docker, containerd etc.

* * *

3.3 Control Plane (Master Nodes)
--------------------------------

É o cérebro do cluster.  
Componentes:

### **API Server**

*   Porta de entrada para toda comunicação
*   Exposto via `kubectl` e clientes externos

### **Scheduler**

*   Decide em qual node um Pod deve rodar

### **Controller Manager**

*   Mantém o estado desejado (ex.: se um pod cai, recria outro)

### **etcd**

*   Banco de dados distribuído chave-valor
*   Guarda o estado do cluster

* * *

4\. Objetos Fundamentais do Kubernetes
======================================

4.1 **Pod**
-----------

É a menor unidade executável.  
Um Pod:

*   Contém **um ou mais contêineres**
*   Compartilham rede e armazenamento
*   É efêmero — deve ser criado e destruído automaticamente

**Você nunca gerencia Pods diretamente em produção**, e sim controllers que gerenciam pods.

* * *

4.2 **ReplicaSet**
------------------

Garante que um número desejado de Pods esteja sempre rodando.  
Exemplo: manter sempre **5 réplicas**.

* * *

4.3 **Deployment**
------------------

É o recurso mais usado.

Controla:

*   ReplicaSets
*   Rollouts (atualizações)
*   Rollbacks

Permite atualizações sem downtime.

* * *

4.4 **StatefulSet**
-------------------

Usado para aplicações com **estado** persistente, como:

*   Bancos de dados
*   Sistemas distribuídos (Kafka, Cassandra etc.)

Propriedades:

*   Nomes fixos dos Pods
*   Ordem previsível de inicialização

* * *

4.5 **DaemonSet**
-----------------

Garante que cada Node tenha **exatamente um** Pod rodando.  
Usado para:

*   Log collectors
*   Monitoramento
*   Nodes storage

* * *

4.6 **Job e CronJob**
---------------------

*   **Job** → executa tarefas únicas (ex.: migração de banco)
*   **CronJob** → tarefas agendadas (ex.: backups diários)

* * *

5\. Rede e Exposição de Serviços
================================

5.1 **Service**
---------------

Objetivo: expor Pods dentro ou fora do cluster.

Tipos:

### **ClusterIP (padrão)**

*   Acessível apenas dentro do cluster
*   Para comunicação interna

### **NodePort**

*   Exposto via porta alta (30000-32767) nos nodes

### **LoadBalancer**

*   Exposto via um load balancer externo (Cloud Providers)

### **Headless Service**

*   Usado com StatefulSets para fornecer DNS individual para cada Pod

* * *

5.2 **Ingress**
---------------

*   Exposição HTTP/HTTPS avançada
*   Permite rotas como `/api`, `/app`, domínios, TLS etc.

Requer um **Ingress Controller** (ex.: NGINX, Traefik).

* * *

6\. Armazenamento no Kubernetes
===============================

6.1 **Volume**
--------------

Fornece armazenamento ao Pod. Não é persistente por padrão.

* * *

6.2 **PersistentVolume (PV)**
-----------------------------

Recurso de armazenamento provisionado externamente:

*   NFS
*   EBS
*   Azure Disk
*   Google Persistent Disk
*   Ceph

* * *

6.3 **PersistentVolumeClaim (PVC)**
-----------------------------------

É o “pedido” de armazenamento.  
O Pod acessa o volume por meio do PVC.

* * *

7\. Configuração e Segredos
===========================

7.1 **ConfigMap**
-----------------

Armazena **configurações não sensíveis**, como:

*   URLs
*   Variáveis de ambiente
*   Arquivos de configuração

* * *

7.2 **Secret**
--------------

Armazena dados sensíveis:

*   Senhas
*   Tokens
*   Certificados

* * *

8\. Escalabilidade
==================

8.1 Escalabilidade Horizontal (HPA)
-----------------------------------

Horizontal Pod Autoscaler:

*   Escala Pods automaticamente
*   Baseado em: CPU, RAM, métricas customizadas etc.

* * *

8.2 Cluster Autoscaler
----------------------

Adiciona ou remove Nodes automaticamente, conforme necessidade.

* * *

9\. Atualizações
================

Rolling Updates
---------------

Atualiza aplicações sem downtime.  
O Deployment cria novas réplicas e remove gradualmente as antigas.

Rollbacks
---------

Se algo der errado:

```
kubectl rollout undo deployment nome
```

* * *

10\. Como o Kubernetes Garante Alta Disponibilidade?
====================================================

*   Se um Pod cai → o ReplicaSet recria
*   Se um Node cai → scheduler move pods para outro node
*   LoadBalancer distribui tráfego automaticamente
*   Control Plane redundante

Kubernetes é projetado para resiliência.

* * *

11\. Ferramentas Relacionadas
=============================

*   **kubectl** → CLI para Kubernetes
*   **Helm** → gerenciamento de pacotes e templates
*   **Kustomize** → customização declarativa
*   **Metrics Server** → métricas para HPA
*   **Prometheus + Grafana** → monitoramento

* * *

12\. Conclusão
==============

Kubernetes é uma plataforma poderosa para ambientes modernos baseados em contêineres.  
Ele oferece:

*   Automação
*   Escalabilidade
*   Autocorreção
*   Flexibilidade
*   Padronização
*   Portabilidade entre clouds

Entender seus conceitos principais (Pods, Deployments, Services, Nodes, Control Plane, Volumes) é essencial para operar clusters Kubernetes com segurança e eficiência.
