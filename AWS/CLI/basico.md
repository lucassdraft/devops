## 🧱 Configuração Inicial

Configurar credenciais e região padrão
```bash
aws configure
```

Ver informações do usuário atual

```bash
aws sts get-caller-identity
```

## ☁️ EC2 (Instâncias)

Listar instâncias EC2

```bash
aws ec2 describe-instances
```
Criar uma instância EC2


```bash
aws ec2 run-instances --image-id ami-xxxxxx --instance-type t2.micro --key-name MinhaChave --security-group-ids sg-xxxxxx --subnet-id subnet-xxxxxx
```
Parar uma instância


```bash
aws ec2 stop-instances --instance-ids i-xxxxxx
```
Iniciar uma instância


```bash
aws ec2 start-instances --instance-ids i-xxxxxx
```
Excluir (terminar) uma instância


```bash
aws ec2 terminate-instances --instance-ids i-xxxxxx
```
Listar tipos de instância disponíveis


```bash
aws ec2 describe-instance-types
```
## 🧰 S3 (Buckets e Objetos)
Listar buckets


```bash
aws s3 ls
```
Criar bucket


```bash
aws s3 mb s3://meu-bucket
```
Enviar arquivo para bucket


```bash
aws s3 cp arquivo.txt s3://meu-bucket/
```
Baixar arquivo do bucket


```bash
aws s3 cp s3://meu-bucket/arquivo.txt
``` .
Excluir arquivo


```bash
aws s3 rm s3://meu-bucket/arquivo.txt
```
Excluir bucket


```bash
aws s3 rb s3://meu-bucket --force
```
## 🧭 IAM (Usuários e Políticas)
Listar usuários


```bash
aws iam list-users
```
Criar usuário


```bash
aws iam create-user --user-name nomeusuario
```
Excluir usuário


```bash
aws iam delete-user --user-name nomeusuario
```
Anexar política a usuário


```bash
aws iam attach-user-policy --user-name nomeusuario --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```
Listar políticas disponíveis


```bash
aws iam list-policies
```
## 🏗️ VPC (Redes Virtuais)
Listar VPCs


```bash
aws ec2 describe-vpcs
```
Criar VPC


```bash
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```
Listar sub-redes


```bash
aws ec2 describe-subnets
```
Listar tabelas de rotas


```bash
aws ec2 describe-route-tables
```
## 🧩 Security Groups
Listar grupos de segurança


```bash
aws ec2 describe-security-groups
```
Criar grupo de segurança


```bash
aws ec2 create-security-group --group-name MeuSG --description "Grupo de teste" --vpc-id vpc-xxxxxx
```
Adicionar regra de entrada (Inbound)


```bash
aws ec2 authorize-security-group-ingress --group-id sg-xxxxxx --protocol tcp --port 22 --cidr 0.0.0.0/0
```
Excluir grupo de segurança


```bash
aws ec2 delete-security-group --group-id sg-xxxxxx
```
## 📦 EBS (Volumes e Snapshots)
Listar volumes EBS


```bash
aws ec2 describe-volumes
```
Criar volume EBS


```bash
aws ec2 create-volume --availability-zone us-east-1a --size 10
```
Listar snapshots


```bash
aws ec2 describe-snapshots --owner-ids self
```
Criar snapshot


```bash
aws ec2 create-snapshot --volume-id vol-xxxxxx --description "Backup automático"
```
## 🔄 CloudFormation
Listar stacks


```bash
aws cloudformation list-stacks
```
Criar stack


```bash
aws cloudformation create-stack --stack-name MinhaStack --template-body file://template.yaml
```
Excluir stack


```bash
aws cloudformation delete-stack --stack-name MinhaStack
```
## 📡 CloudWatch
Listar métricas


```bash
aws cloudwatch list-metrics
```
Obter alarmes


```bash
aws cloudwatch describe-alarms
```
Criar alarme


```bash
aws cloudwatch put-metric-alarm --alarm-name CPUAlta --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 80 --comparison-operator GreaterThanThreshold --dimensions Name=InstanceId,Value=i-xxxxxx --evaluation-periods 2 --alarm-actions arn:aws:sns:us-east-1:123456789012:meu-topico
```
## 🧠 Outras Utilidades
Ver versão do AWS CLI


```bash
aws --version
```
Obter ajuda sobre um comando


```bash
aws ec2 help
```
📄 Dica: Use --query e --output table para formatar os resultados:


```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId,State.Name]" --output table
```

