# Primeira VPC

Pesquisa por VPC\
<kbd><img width="721" height="202" alt="image" src="https://github.com/user-attachments/assets/1e9a1f80-9305-420e-96d7-6c21c1b37bc8" /></kbd>

Clique em "Criar VPC"\
<kbd><img width="369" height="160" alt="image" src="https://github.com/user-attachments/assets/996c9037-b5de-459f-a5b4-e6a9f4f3acf0" /></kbd>

Tela de criação\
<kbd><img width="1625" height="1720" alt="image" src="https://github.com/user-attachments/assets/ed1964d9-500a-4300-80a6-9796e8c9385b" /></kbd>

# Guia Explicativo: Tela “Criar VPC” na AWS

## 1. Visão geral

A **VPC (Virtual Private Cloud)** é uma rede virtual isolada dentro da AWS, onde você pode lançar recursos como instâncias EC2, bancos de dados e outros serviços.  
A tela **“Criar VPC”** permite configurar rapidamente toda a estrutura de rede — incluindo sub-redes, tabelas de rotas e conexões de Internet — de forma automatizada.

---

## 2. Configurações da VPC

### 🧱 Recursos a serem criados
Define o escopo da criação:

- **Somente VPC**: cria apenas a VPC (sem sub-redes, tabelas de rotas ou gateways).
- **VPC e muito mais**: cria automaticamente uma estrutura completa de rede, com sub-redes públicas e privadas, tabelas de rotas e conexões de rede.  
  > No exemplo, a opção selecionada é **“VPC e muito mais”**.

---

### 🏷️ Geração automática da etiqueta de nome
Cria automaticamente a **tag “Name”** para todos os recursos.  
- Campo “Gerar automaticamente”: gera nomes baseados no valor informado.  
- Valor informado: **vpc-devops**  
  → Assim, todos os recursos serão nomeados como `vpc-devops-*`.

---

### 🌐 Bloco CIDR IPv4
Define o intervalo de endereços IP da VPC em **notação CIDR (Classless Inter-Domain Routing)**.

- Valor: `10.0.0.0/16`
- Total de IPs: 65.536  
- Regra: o tamanho deve estar entre **/16 (maior rede)** e **/28 (menor rede)**.

Esse bloco determina o espaço de endereçamento disponível para todas as sub-redes dentro da VPC.

---

### 🌍 Bloco CIDR IPv6
Permite criar uma VPC com suporte a IPv6.

Opções:
- **Nenhum bloco CIDR IPv6** (padrão)
- **Bloco CIDR IPv6 fornecido pela Amazon** – a AWS aloca automaticamente um bloco IPv6 global.

> No exemplo, foi mantido **“Nenhum bloco CIDR IPv6”**.

---

### 🏠 Locação
Define o tipo de locação de instâncias dentro da VPC:

- **Padrão** – permite instâncias compartilhadas (multi-tenant).  
- **Dedicada** – usa hardware dedicado (opção mais cara).

> No exemplo, permanece **Padrão**.

---

## 3. Sub-redes e disponibilidade

### 🏗️ Número de zonas de disponibilidade (AZs)
Controla em quantas **Zonas de Disponibilidade** (regiões físicas separadas dentro da mesma região AWS) as sub-redes serão criadas.

- Opções: **1, 2 ou 3**  
- Recomendação: usar **pelo menos 2 AZs** para alta disponibilidade.

> No exemplo, estão selecionadas **2 AZs (us-east-2a e us-east-2b)**.

---

### 🌐 Número de sub-redes públicas
Define quantas **sub-redes públicas** serão criadas.  
- Sub-redes públicas possuem rota para a Internet via **Internet Gateway (IGW)**.  
- São usadas para servidores e aplicações que precisam ser acessadas externamente (como sites ou APIs).

> No exemplo: **2 sub-redes públicas** (uma em cada AZ).

---

### 🔒 Número de sub-redes privadas
Define quantas **sub-redes privadas** serão criadas.  
- Sub-redes privadas **não têm acesso direto à Internet**, ideais para bancos de dados e sistemas internos.  
- Podem usar um **NAT Gateway** para acessar a Internet apenas para saída.

> No exemplo: **2 sub-redes privadas** (uma em cada AZ).

---

### ⚙️ Personalizar blocos CIDR de sub-redes
Permite definir manualmente o bloco de IP de cada sub-rede.  
Se não for usado, a AWS divide automaticamente o bloco principal (`10.0.0.0/16`) entre as sub-redes criadas.
<img width="462" height="321" alt="image" src="https://github.com/user-attachments/assets/966a91d9-faa7-4b49-94ca-fb27a93a8546" />


---

## 4. Conectividade e endpoints

### 🌉 Gateways NAT (Network Address Translation)
Usado para permitir que **instâncias em sub-redes privadas** acessem a Internet (para atualizações, downloads, etc.), sem ficarem expostas.

Opções:
- **Nenhuma** – sem acesso externo.  
- **Em 1 AZ** – cria um único NAT Gateway.  
- **1 por AZ** – cria um NAT Gateway para cada zona (maior redundância, porém mais caro).

> O texto destaca: *“há cobrança para cada gateway NAT”*.

---

### 🧩 Endpoints da VPC
Conectam a VPC diretamente a serviços da AWS (como S3 e DynamoDB) **sem usar Internet**.

Opções:
- **Nenhuma** – acesso normal via Internet.  
- **Gateway do S3** – cria um endpoint direto para o Amazon S3, reduzindo custos e aumentando segurança.  

> No exemplo, a opção **Gateway do S3** está disponível, mas não marcada.

---

## 5. DNS e tags

### 🔡 Opções de DNS
Controla se os recursos dentro da VPC podem usar e resolver nomes de domínio.

- **Habilitar nomes de host DNS** – permite que instâncias recebam nomes DNS automaticamente.  
- **Habilitar resolução de DNS** – permite que as instâncias resolvam nomes para endereços IP.

> Ambas as opções estão habilitadas no exemplo.

---

### 🏷️ Tags adicionais
Permite adicionar até **49 tags personalizadas** para organização e identificação de recursos.  
A **tag Name** não deve ser definida aqui, pois é gerada automaticamente pelo campo anterior.

---

## 6. Visualização (lado direito da tela)

A seção de **previsualização** mostra como os recursos serão criados:

- **Sub-redes (4)**  
  - 2 públicas (`us-east-2a`, `us-east-2b`)  
  - 2 privadas (`us-east-2a`, `us-east-2b`)

- **Tabelas de rotas (3)**  
  - Uma para a VPC pública, conectada ao Internet Gateway.  
  - Duas para as sub-redes privadas (uma por AZ).

- **Conexões de rede (2)**  
  - `vpc-devops-igw` (Internet Gateway)  
  - `vpc-devops-vpc-3` (a própria VPC)

---

## 7. Ações disponíveis

- **Cancelar** – descarta a configuração.  
- **Código de visualização** – exibe o código JSON/Terraform equivalente à configuração.  
- **Criar VPC** – executa a criação dos recursos na conta AWS.

---

## 8. Resumo final

| Categoria | Configuração escolhida |
|------------|-----------------------|
| Tipo de criação | VPC e muito mais |
| Nome base | vpc-devops |
| IPv4 CIDR | 10.0.0.0/16 |
| IPv6 | Nenhum |
| Zonas de disponibilidade | 2 (us-east-2a, us-east-2b) |
| Sub-redes públicas | 2 |
| Sub-redes privadas | 2 |
| NAT Gateway | Não selecionado |
| Endpoint S3 | Não selecionado |
| DNS habilitado | Sim |
| Tags adicionais | Nenhuma definida |
