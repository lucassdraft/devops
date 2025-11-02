Acesse\
<img width="650" height="277" alt="2025-11-01-205453" src="https://github.com/user-attachments/assets/7f8d70fd-5ee9-4401-a459-c282da3cc334" />

Clique em "Pares de chaves" (KeyPairs)\
<img width="476" height="644" alt="image" src="https://github.com/user-attachments/assets/907bc202-298b-4e74-93e3-c3448e247cc9" />

Clique em "Criar par de chaves"\
<img width="409" height="242" alt="image" src="https://github.com/user-attachments/assets/83833b21-59ff-46dc-9a6f-41f546459d53" />

Com estas configurações, clique em "Criar par de chaves"\
<img width="1652" height="647" alt="image" src="https://github.com/user-attachments/assets/0b3166a3-9cbe-4e90-9734-f3937e29ed0c" />
> Vai baixar um arquivo chamado "devops-automation.pem".\

Agora vá em "Instâncias"\
<img width="213" height="169" alt="image" src="https://github.com/user-attachments/assets/090f06a5-1353-433b-b445-5c5c59d86554" />

Clique em "Executar instâncias"\
<img width="260" height="161" alt="image" src="https://github.com/user-attachments/assets/ffd4ad38-9af5-47aa-8859-0cb772f1199d" />

Vamos fazer este primeiro teste com as seguintes configurações:\
<img width="1076" height="1834" alt="image" src="https://github.com/user-attachments/assets/faf138e5-07d9-4414-ad16-427e92639510" />
 - Definimos um nome
 - Escolhemos uma maquina Ubuntu
 - Logo abaixo tem "ami", isso quer dizer que a maquina já vem com o sistema operacional instalado.
 - Em "Par de chaves", escolhemos a chave que criamos para poder acessar a maquina.
 - Deixamos o ip publico habilitado.
 - Colocamos a opção para criar o grupo de segurança.(Estudar mais a respeito)
 - Permitimos o acesso via SSH, HTTP e HTTPS.
 - Deixamos a configuração "Qualquer lugar 0.0.0.0/0" assim qualquer ip pode acessar esta maquina.

Em "Detalhes avançados".\
<img width="463" height="229" alt="image" src="https://github.com/user-attachments/assets/280e421c-6571-489b-9b98-962d2ea2df08" />

Vamos até este textarea:\
<img width="811" height="487" alt="image" src="https://github.com/user-attachments/assets/b8aaba7c-7d70-4a61-99b9-73853c8063ff" />

E vamos adicionar o seguinte script para executar com a maquina, assim podemos testar o acesso ssh usando a chave pem e testar o acesso http usando o nginx.
```bash
#!/bin/bash
apt update -y
apt upgrade -y
apt install -y nginx
echo "<h1>Meu primeiro servidor na AWS</h1>" > /var/www/html/index.html
systemctl enable nginx
systemctl start nginx
```
Então vamos clicar em "Executar instância"
<img width="745" height="557" alt="image" src="https://github.com/user-attachments/assets/f8b56a42-ae14-442e-8594-086a18f0ced8" />

---
Vamos então para "Instâncias" novamente e clicamos no id\
<img width="738" height="350" alt="image" src="https://github.com/user-attachments/assets/2da6c03c-c177-4eb6-b613-20ba29e5a4dc" />

Clicamos então em "Conectar"
<img width="357" height="221" alt="image" src="https://github.com/user-attachments/assets/5e5a7041-eb98-4a49-8a74-7f864ce0d797" />

Vamos até a aba "cliente SSH"
<img width="746" height="425" alt="image" src="https://github.com/user-attachments/assets/44b7e91c-d477-4cec-a364-fea5015e10cf" />
 - Aqui estão todas as configurações para a conexão.
 - Ajustamos as permissões do arquivo pem
     chmod 400 "devops-automation.pem"
 - E conectamos via ssh usando a chave:
     ssh -i "devops-automation.pem" ubuntu@ec2-18-220-35-22.us-east-2.compute.amazonaws.com

Assim conectamos na nossa maquina, e podemos verificar que foi criado o arquivo do script que configuramos na instência
<img width="1020" height="1309" alt="image" src="https://github.com/user-attachments/assets/cd499ef3-1628-427c-bb73-d4733d5a5a50" />

Agora podemos ir até a aba "Conexão de instância do EC2" e pegar o ip para testar o acesso via http.
<img width="671" height="530" alt="image" src="https://github.com/user-attachments/assets/dc2945c5-29ac-4485-af54-004f397f561d" />

Acessando via http
<img width="750" height="256" alt="image" src="https://github.com/user-attachments/assets/19d6fa01-63d7-4fdd-9e94-cfee56a9b7d4" />

Agora que já brincamos com esta maquina, vamos excluir para não gerar custos.
Volte para "Instâncias" e selecione ela, clique em "Estado da instância" e então "Encerrar (excluir) instância"
<img width="1393" height="330" alt="image" src="https://github.com/user-attachments/assets/05593f49-2f0b-43b9-a0f2-f52e29bed6dd" />

Vai aparecer a confirmação e você clica em "Encerrar (excluir) Instância"
<img width="855" height="487" alt="image" src="https://github.com/user-attachments/assets/b80a13eb-e254-4adc-ae66-a0b6ff219724" />

Aguarde o encerramento
<img width="671" height="294" alt="image" src="https://github.com/user-attachments/assets/6cab44ef-d671-4d2c-ae9a-f1dec05810ae" />
Até o estado ir para "Encerrado"
<img width="686" height="233" alt="image" src="https://github.com/user-attachments/assets/cbdb6546-b40c-45a2-80bd-1efad1e213b1" />
> A AWS mantém a entrada visível por padrão por algumas horas (até 24h) apenas para fins de histórico. Após isso, ela desaparece automaticamente da lista.


Devemos também excluir o "Security group" que foi criado, ele que é configurado o acesso ssh, http e https.
<img width="1591" height="587" alt="image" src="https://github.com/user-attachments/assets/033a817f-ae03-4009-a626-56a6663d7045" />





