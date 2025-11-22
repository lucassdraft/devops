# Configurando o samba no Raspberry

É necessário ter o Ansible instalado e instalar o sshpass

```bash
sudo apt install sshpass -y
```

# Testar a conexão com o Raspberry

```bash
ansible raspberry -i inventory.ini -m ping
```

# Executar playbook

```bash
ansible-playbook -i inventory.ini setup-rpi.yml
```
