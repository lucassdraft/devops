# 🖥️ Criando e Configurando Sua Primeira Instância EC2 na AWS
## 1. Acessando o painel do EC2

Acesse o serviço **EC2** no console da **AWS**.\
<kbd><img width="650" height="277" alt="2025-11-01-205453" src="https://github.com/user-attachments/assets/7f8d70fd-5ee9-4401-a459-c282da3cc334" /></kbd>

## 2. Criando um Par de Chaves (Key Pair)

Antes de criar uma instância, precisamos de um par de chaves para autenticação SSH.

Clique em "Pares de chaves" (Key Pairs).\
<kbd><img width="476" height="644" alt="image" src="https://github.com/user-attachments/assets/907bc202-298b-4e74-93e3-c3448e247cc9" /></kbd>

Em seguida, clique em "Criar par de chaves".\
<kbd><img width="409" height="242" alt="image" src="https://github.com/user-attachments/assets/83833b21-59ff-46dc-9a6f-41f546459d53" /></kbd>

Utilize as seguintes configurações e clique em "Criar par de chaves".\
<kbd><img width="1652" height="647" alt="image" src="https://github.com/user-attachments/assets/0b3166a3-9cbe-4e90-9734-f3937e29ed0c" /></kbd>

Um arquivo chamado devops-automation.pem será baixado automaticamente.
Guarde-o com segurança — ele será usado para acessar sua instância via SSH.

## 3. Criando uma Instância EC2

Vá até “Instâncias” no menu lateral.\
<kbd><img width="213" height="169" alt="image" src="https://github.com/user-attachments/assets/090f06a5-1353-433b-b445-5c5c59d86554" /></kbd>

Clique em “Executar instâncias”.\
<kbd><img width="260" height="161" alt="image" src="https://github.com/user-attachments/assets/ffd4ad38-9af5-47aa-8859-0cb772f1199d" /></kbd>

## 4. Configurando a Instância

Utilize as configurações abaixo:\
<kbd><img width="1076" height="1834" alt="image" src="https://github.com/user-attachments/assets/faf138e5-07d9-4414-ad16-427e92639510" /></kbd>

**Nome:** defina um nome para a instância.\
**Imagem (AMI):** escolha Ubuntu (a AMI já traz o sistema operacional pré-instalado).\
**Par de chaves:** selecione o par criado anteriormente (devops-automation.pem).\
**IP público:** mantenha habilitado.\
**Grupo de segurança:** crie um novo (você pode estudar mais sobre isso depois).\
**Regras de acesso:** permita SSH (22), HTTP (80) e HTTPS (443).\
**Origem:** deixe como “Qualquer lugar (0.0.0.0/0)”, permitindo acesso de qualquer IP.

## 5. Adicionando Script de Inicialização (User Data)

Em "Detalhes avançados", role até a seção de script de inicialização.\
<kbd><img width="463" height="229" alt="image" src="https://github.com/user-attachments/assets/280e421c-6571-489b-9b98-962d2ea2df08" /></kbd>

Localize o campo User Data (textarea):\
<kbd><img width="811" height="487" alt="image" src="https://github.com/user-attachments/assets/b8aaba7c-7d70-4a61-99b9-73853c8063ff" /></kbd>

Adicione o seguinte script:

```bash
#!/bin/bash
apt update -y
apt upgrade -y
apt install -y nginx
echo "<h1>Meu primeiro servidor na AWS</h1>" > /var/www/html/index.html
systemctl enable nginx
systemctl start nginx
```

Esse script será executado automaticamente na criação da instância, instalando o Nginx e exibindo uma mensagem de teste via HTTP.

Clique em “Executar instância”.\
<kbd><img width="745" height="557" alt="image" src="https://github.com/user-attachments/assets/f8b56a42-ae14-442e-8594-086a18f0ced8" /></kbd>

## 6. Conectando-se à Instância

Vá até “Instâncias” novamente e clique no ID da instância.\
<kbd><img width="738" height="350" alt="image" src="https://github.com/user-attachments/assets/2da6c03c-c177-4eb6-b613-20ba29e5a4dc" /></kbd>

Clique em “Conectar”.\
<kbd><img width="357" height="221" alt="image" src="https://github.com/user-attachments/assets/5e5a7041-eb98-4a49-8a74-7f864ce0d797" /></kbd>

Abra a aba “Cliente SSH”.\
<kbd><img width="746" height="425" alt="image" src="https://github.com/user-attachments/assets/44b7e91c-d477-4cec-a364-fea5015e10cf" /></kbd>

Siga as instruções:

Ajuste as permissões da chave:

```bash
chmod 400 devops-automation.pem
```

Conecte-se via SSH:

```bash
ssh -i "devops-automation.pem" ubuntu@ec2-18-220-35-22.us-east-2.compute.amazonaws.com
```

Após conectar, você poderá verificar o Nginx e o arquivo criado pelo script de inicialização:\
<kbd><img width="1020" height="1309" alt="image" src="https://github.com/user-attachments/assets/cd499ef3-1628-427c-bb73-d4733d5a5a50" /></kbd>

## 7. Testando o Acesso HTTP

Na aba “Conexão de instância do EC2”, copie o endereço IP público.\
<kbd><img width="671" height="530" alt="image" src="https://github.com/user-attachments/assets/dc2945c5-29ac-4485-af54-004f397f561d" /></kbd>

Acesse o IP no navegador (HTTP):\
<kbd><img width="750" height="256" alt="image" src="https://github.com/user-attachments/assets/19d6fa01-63d7-4fdd-9e94-cfee56a9b7d4" /></kbd>

Se aparecer a mensagem configurada, o servidor está funcionando corretamente 🎉

## 8. Encerrando e Limpando Recursos

Após os testes, é importante encerrar a instância para evitar custos.

Volte em “Instâncias”, selecione a instância, clique em “Estado da instância” → “Encerrar (excluir) instância”.\
<kbd><img width="1393" height="330" alt="image" src="https://github.com/user-attachments/assets/05593f49-2f0b-43b9-a0f2-f52e29bed6dd" /></kbd>

Confirme em “Encerrar (excluir) Instância”.\
<kbd><img width="855" height="487" alt="image" src="https://github.com/user-attachments/assets/b80a13eb-e254-4adc-ae66-a0b6ff219724" /></kbd>

Aguarde o status mudar para “Encerrado”:\
<kbd><img width="671" height="294" alt="image" src="https://github.com/user-attachments/assets/6cab44ef-d671-4d2c-ae9a-f1dec05810ae" /></kbd>
<kbd><img width="686" height="233" alt="image" src="https://github.com/user-attachments/assets/cbdb6546-b40c-45a2-80bd-1efad1e213b1" /></kbd>

⚠️ A AWS mantém o registro da instância encerrada por algumas horas (até 24h) apenas para fins de histórico. Após esse período, ela desaparece automaticamente da lista.

## 9. Excluindo o Grupo de Segurança

Por fim, exclua o Security Group criado automaticamente, responsável pelas regras de acesso SSH, HTTP e HTTPS.\
<kbd><img width="1591" height="587" alt="image" src="https://github.com/user-attachments/assets/033a817f-ae03-4009-a626-56a6663d7045" /></kbd>

## ✅ Conclusão

Você acabou de:

 - Criar um **par de chaves SSH**
 - Configurar e executar uma **instância EC2 Ubuntu**
 - Instalar e testar o **Nginx automaticamente**
 - Encerrar e limpar os recursos com segurança
 - Esse é o fluxo básico para criar e testar um servidor simples na **AWS EC2**. 🚀
