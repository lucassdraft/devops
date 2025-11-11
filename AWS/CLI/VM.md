🚀 Guia Completo: Criando uma VM (EC2) na AWS via AWS CLI
---------------------------------------------------------

### 🧩 Pré-requisitos

Antes de começar, você precisa ter:

*   AWS CLI instalado e configurado com credenciais válidas (`aws configure`).
*   Permissões adequadas para criar recursos de rede e EC2.
*   Uma região definida (ex: `us-east-1`).

* * *

1️⃣ Criar uma VPC
-----------------

```bash
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```

**Descrição:**  
Cria uma VPC (Virtual Private Cloud) com um bloco CIDR de endereços IP privados.  
O comando retorna um `VpcId`, que será usado nos próximos passos.

> ⚙️ Exemplo de saída:
> 
> ```
> {
>     "Vpc": {
>         "VpcId": "vpc-0a1b2c3d4e5f6g7h",
>         "CidrBlock": "10.0.0.0/16",
>         "State": "pending"
>     }
> }
> ```

* * *

2️⃣ Criar uma Sub-rede
----------------------

```bash
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone us-east-1a
```

**Descrição:**  
Cria uma sub-rede dentro da VPC. Use o mesmo `VpcId` retornado anteriormente.

* * *

3️⃣ Criar um Gateway da Internet
--------------------------------

```bash
aws ec2 create-internet-gateway
```

**Descrição:**  
Cria um Internet Gateway para permitir acesso à Internet.

**Anexar o Gateway à VPC:**

```bash
aws ec2 attach-internet-gateway --internet-gateway-id <igw-id> --vpc-id <vpc-id>
```

* * *

4️⃣ Criar e Associar uma Tabela de Rotas
----------------------------------------

### Criar tabela de rotas:

```bash
aws ec2 create-route-table --vpc-id <vpc-id>
```

### Criar rota padrão para a Internet:

```bash
aws ec2 create-route --route-table-id <rtb-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw-id>
```

### Associar a sub-rede à tabela de rotas:

```bash
aws ec2 associate-route-table --subnet-id <subnet-id> --route-table-id <rtb-id>
```

* * *

5️⃣ Habilitar IP público automático na sub-rede
-----------------------------------------------

```bash
aws ec2 modify-subnet-attribute --subnet-id <subnet-id> --map-public-ip-on-launch
```

**Descrição:**  
Isso garante que as instâncias criadas na sub-rede recebam IP público automaticamente.

* * *

6️⃣ Criar um Par de Chaves (PEM)
--------------------------------

```bash
aws ec2 create-key-pair --key-name MinhaChave --query 'KeyMaterial' --output text > MinhaChave.pem
```

**Descrição:**  
Cria um par de chaves que será usado para acessar a VM via SSH.  
O arquivo `.pem` é salvo localmente — **não perca este arquivo!**

> ⚠️ Dê permissão correta:
> 
> ```bash
> chmod 400 MinhaChave.pem
> ```

* * *

7️⃣ Criar um Grupo de Segurança (Security Group)
------------------------------------------------

```bash
aws ec2 create-security-group --group-name AcessoSSH --description "Permitir acesso SSH" --vpc-id <vpc-id>
```

### Liberar tráfego SSH (porta 22)

```bash
aws ec2 authorize-security-group-ingress --group-id <sg-id> --protocol tcp --port 22 --cidr 0.0.0.0/0
```

**Descrição:**  
Permite acesso SSH de qualquer IP (para produção, use um IP específico).

* * *

8️⃣ Criar a Instância EC2
-------------------------

```bash
aws ec2 run-instances \
--image-id ami-0c02fb55956c7d316 \
--count 1 \
--instance-type t2.micro \
--key-name MinhaChave \
--security-group-ids <sg-id> \
--subnet-id <subnet-id> \
--associate-public-ip-address
```

**Descrição:**

*   `--image-id`: ID da AMI (aqui é uma AMI Amazon Linux 2, na região us-east-1).
*   `--instance-type`: tipo da instância (ex: `t2.micro`, elegível para o Free Tier).
*   `--key-name`: nome do arquivo `.pem`.
*   `--associate-public-ip-address`: garante IP público.

> Após a execução, anote o `InstanceId`.

* * *

9️⃣ Verificar a Instância
-------------------------

```bash
aws ec2 describe-instances --instance-ids <instance-id>
```

**Descrição:**  
Retorna informações da instância, incluindo IP público.

> 🔍 Exemplo de trecho da saída:
> 
> ```
> "PublicIpAddress": "18.234.123.45"
> ```

* * *

🔟 Conectar via SSH
-------------------

```bash
ssh -i MinhaChave.pem ec2-user@<ip-publico>
```

**Descrição:**  
Acesse sua instância EC2 usando o arquivo `.pem` criado.

* * *

🧹 Passo Opcional: Encerrar Instância e Limpar Recursos
-------------------------------------------------------

Encerrar a instância:

```bash
aws ec2 terminate-instances --instance-ids <instance-id>
```

Excluir grupo de segurança, sub-rede, gateway e VPC:

```bash
aws ec2 delete-security-group --group-id <sg-id>
aws ec2 delete-subnet --subnet-id <subnet-id>
aws ec2 detach-internet-gateway --internet-gateway-id <igw-id> --vpc-id <vpc-id>
aws ec2 delete-internet-gateway --internet-gateway-id <igw-id>
aws ec2 delete-vpc --vpc-id <vpc-id>
```

* * *

✅ Resumo dos Recursos Criados
-----------------------------

| Recurso | Comando | Finalidade |
| --- | --- | --- |
| VPC | `create-vpc` | Rede privada isolada |
| Sub-rede | `create-subnet` | Segmento dentro da VPC |
| Internet Gateway | `create-internet-gateway` | Acesso à Internet |
| Tabela de Rotas | `create-route-table` | Define rotas da rede |
| Security Group | `create-security-group` | Controla tráfego de rede |
| Par de Chaves | `create-key-pair` | Acesso SSH |
| Instância EC2 | `run-instances` | Cria a VM |

* * *

Se quiser, posso gerar **esse mesmo conteúdo formatado em Markdown (`.md`) para download**, mantendo os blocos de código prontos para uso.  
Deseja que eu gere o arquivo?
