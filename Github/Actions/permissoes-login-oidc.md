📘 **Documentação Completa – GitHub Actions, Permissões e Login OIDC (AWS / Azure / GCP)**
==========================================================================================

* * *

\# 1. **Permissões no GitHub Actions**
======================================

No GitHub Actions, o bloco **`permissions:`** define quais permissões o `GITHUB_TOKEN` terá durante a execução do workflow.

Exemplo:

```yaml
permissions:
  id-token: write
  contents: read
```

🔐 **1.1. `id-token: write`**
-----------------------------

Permite ao workflow **gerar um token OIDC (OpenID Connect)**.

Isso é utilizado para realizar autenticação em serviços externos **sem necessidade de secrets fixos**, como:

*   AWS (AssumeRole via OIDC)
*   Azure (App Registration + Federated Credentials)
*   GCP (Workload Identity Federation)
*   HashiCorp Vault
*   Outras plataformas compatíveis com identidade federada

Sem essa permissão, o GitHub Actions **não pode gerar o ID Token** necessário para autenticação.

* * *

📄 **1.2. `contents: read`**
----------------------------

Permite ao workflow **ler conteúdos do repositório**, incluindo:

*   Código-fonte
*   Arquivos YAML
*   Dependências
*   Histórico (commits, tags, releases)

Essa permissão é usada, por exemplo, quando o workflow precisa fazer:

```yaml
- uses: actions/checkout@v4
```

Como é apenas leitura, **não permite criar commits, tags ou releases**.

* * *

\# 2. **Como funciona o Login OIDC no GitHub Actions**
======================================================

OIDC (OpenID Connect) permite que o GitHub emita um **ID Token assinado** para autenticação segura em provedores de nuvem.

Com isso, não é necessário salvar secrets como:

*   AWS Access Key / Secret Key
*   Azure Client Secret
*   Chaves JSON do GCP

Tudo passa a ser **temporário, seguro e controlado**.

* * *

\## 2.1. **Fluxo OIDC – Visão Geral**
=====================================

1.  O workflow solicita um token OIDC, habilitado por:
    ```yaml
    permissions:
      id-token: write
    ```
2.  O GitHub gera um token com informações:
    *   Repositório
    *   Branch
    *   Commit SHA
    *   Workflow
    *   Dados de segurança
3.  O workflow envia esse token para AWS, Azure ou GCP.
4.  A nuvem valida o token através do provedor OIDC:
    *   `https://token.actions.githubusercontent.com`
5.  Se for válido, a nuvem retorna **credenciais temporárias**.
6.  O workflow utiliza essas credenciais para realizar o deploy ou outras operações.

* * *

\# 3. **OIDC na AWS**
=====================

A AWS utiliza o OIDC através do método `sts:AssumeRoleWithWebIdentity`.

🔧 3.1. Passos necessários
--------------------------

### **1\. Criar o OIDC Provider no IAM**

Apontando para:

```
https://token.actions.githubusercontent.com
```

### **2\. Criar um IAM Role com trust policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<ACCOUNT-ID>:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:sub": "repo:usuario/repositorio:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

### **3\. Utilizar no workflow:**

```yaml
permissions:
  id-token: write
  contents: read

- uses: aws-actions/configure-aws-credentials@v2
  with:
    role-to-assume: arn:aws:iam::123456789012:role/MyGitHubRole
    aws-region: us-east-1
```

✔️ Resultado
------------

O GitHub consegue assumir o role e recebe credenciais temporárias válidas por minutos, **sem usar Access Keys**.

* * *

\# 4. OIDC na Azure
===================

A Azure utiliza **Federated Identity Credentials** dentro de um App Registration.

🔧 4.1. Passos necessários
--------------------------

### **1\. Criar um App Registration**

### **2\. Criar uma Federated Credential**, com:

*   issuer: `https://token.actions.githubusercontent.com`
*   subject: `repo:usuario/repositorio:ref:refs/heads/main`

### **3\. Usar no GitHub Actions:**

```yaml
permissions:
  id-token: write

- name: Azure Login
  uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

> Não precisa de **client secret**.

✔️ Resultado
------------

O workflow faz login no Azure AD e obtém um **token temporário**, eliminando secrets.

* * *

\# 5. OIDC no GCP
=================

A GCP utiliza **Workload Identity Federation**, permitindo login sem chave JSON.

🔧 5.1. Passos necessários
--------------------------

### **1\. Criar um Workload Identity Pool**

### **2\. Criar um Provider OIDC**:

```
https://token.actions.githubusercontent.com
```

### **3\. Vincular a um Service Account**

### **4\. Usar no workflow:**

```yaml
permissions:
  id-token: write

- name: Authenticate to Google Cloud
  uses: google-github-actions/auth@v2
  with:
    workload_identity_provider: "projects/123456789/locations/global/workloadIdentityPools/github/providers/github"
    service_account: "deploy-sa@meu-projeto.iam.gserviceaccount.com"
```

✔️ Resultado
------------

GCP devolve credenciais temporárias sem usar chaves JSON.

* * *

\# 6. Comparação entre AWS vs Azure vs GCP
==========================================

| Provedor | Mecanismo | Recurso usado |
| --- | --- | --- |
| **AWS** | AssumeRoleWithWebIdentity | IAM Role + OIDC Provider |
| **Azure** | Federated Identity Credentials | App Registration |
| **GCP** | Workload Identity Federation | Identity Pool + Provider |

* * *

\# 7. Benefícios do Login OIDC
==============================

✔️ Elimina secrets permanentes  
✔️ Credenciais temporárias, mais seguras  
✔️ Permissões granularizadas por branch  
✔️ Evita vazamento de chaves  
✔️ Melhor prática recomendada pelos 3 provedores

* * *

\# 8. Conclusão
===============

O uso de OIDC com GitHub Actions tornou-se a forma **mais segura, moderna e recomendada** para autenticação em nuvens como AWS, Azure e GCP.  
As permissões `id-token: write` e `contents: read` servem exatamente para permitir esse fluxo de autenticação federada e acesso seguro ao repositório durante a execução do workflow.
