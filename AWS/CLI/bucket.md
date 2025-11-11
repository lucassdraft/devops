Criar e Gerenciar um Bucket S3 com AWS CLI
==========================================

Este guia mostra como criar um bucket, copiar um arquivo para ele e listar os arquivos usando o AWS CLI.

* * *

1\. Criar um bucket S3
----------------------

Cria um bucket chamado **meu-bucket-exemplo** na região **us-east-1**:

```
aws s3api create-bucket --bucket meu-bucket-exemplo --region us-east-1
```

* * *

2\. Copiar um arquivo local para o bucket
-----------------------------------------

Envia o arquivo **teste.txt** para dentro do bucket S3 criado:

```
aws s3 cp teste.txt s3://meu-bucket-exemplo/
```

* * *

3\. Listar os arquivos do bucket
--------------------------------

Exibe todos os arquivos armazenados dentro do bucket:

```
aws s3 ls s3://meu-bucket-exemplo/
```

* * *

Dica
----

Antes de executar os comandos, configure suas credenciais AWS com:

```
aws configure
```

Isso define o **Access Key**, **Secret Key**, **região padrão** e o **formato de saída**.
