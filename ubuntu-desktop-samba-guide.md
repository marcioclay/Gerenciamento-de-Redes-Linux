# Instalação do Samba AD DC no Ubuntu Desktop

## ✅ Ubuntu Desktop é Adequado?

**SIM!** Ubuntu Desktop funciona perfeitamente para:
- Laboratórios e estudos
- Ambiente de testes
- Aprendizado
- Pequenas redes domésticas ou de escritório (até ~50 usuários)

### Vantagens do Ubuntu Desktop:
- ✅ Interface gráfica facilita o gerenciamento
- ✅ Mais fácil para iniciantes
- ✅ Ferramentas gráficas disponíveis
- ✅ Mesmo kernel e pacotes do Ubuntu Server
- ✅ Consome mais recursos, mas PCs modernos suportam bem

### Desvantagens:
- ❌ Consome mais RAM (interface gráfica)
- ❌ Processos em segundo plano desnecessários
- ❌ Não é considerado "profissional" para produção

---

## 📋 Pré-requisitos

### Hardware Mínimo:
- **CPU:** 2 cores (4 recomendado)
- **RAM:** 4GB (8GB recomendado)
- **HD:** 40GB livres (SSD recomendado)
- **Rede:** Placa de rede cabeada

### Software:
- Ubuntu Desktop 22.04 LTS ou 24.04 LTS
- Acesso à Internet para download de pacotes
- Privilégios de administrador (sudo)

---

## 🚀 MÉTODO 1: Instalação Nativa (Direto no Ubuntu)

Esta é a forma mais tradicional e recomendada para aprendizado.

### Passo 1: Preparação do Sistema

```bash
# Atualizar sistema
sudo apt update
sudo apt upgrade -y

# Instalar ferramentas básicas
sudo apt install -y \
    vim \
    net-tools \
    dnsutils \
    curl \
    wget
```

### Passo 2: Configurar Hostname e Rede

```bash
# Definir hostname (IMPORTANTE!)
sudo hostnamectl set-hostname dc01

# Verificar
hostnamectl

# Configurar IP estático (via interface gráfica)
# 1. Abra Configurações
# 2. Vá em Rede
# 3. Clique na engrenagem da sua conexão
# 4. Vá em IPv4
# 5. Selecione "Manual"
# 6. Configure:
#    Endereço: 192.168.1.10 (exemplo)
#    Máscara: 255.255.255.0
#    Gateway: 192.168.1.1 (seu roteador)
#    DNS: 127.0.0.1 (importante!)
#    DNS secundário: 8.8.8.8
```

**Alternativa via terminal:**

```bash
# Identificar interface de rede
ip addr

# Editar netplan (Ubuntu Desktop também usa)
sudo nano /etc/netplan/01-network-manager-all.yaml

# Cole esta configuração:
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp0s3:  # Substitua pelo nome da sua interface
      dhcp4: no
      addresses:
        - 192.168.1.10/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 127.0.0.1
          - 8.8.8.8

# Aplicar configuração
sudo netplan apply

# Verificar
ip addr show
```

### Passo 3: Configurar /etc/hosts

```bash
sudo nano /etc/hosts

# Adicione estas linhas (ajuste conforme seu IP/domínio):
127.0.0.1       localhost
192.168.1.10    dc01.exemplo.local dc01

# Salvar: Ctrl+O, Enter
# Sair: Ctrl+X
```

### Passo 4: Instalar Samba e Dependências

```bash
# Remover pacotes conflitantes (se existirem)
sudo systemctl disable --now smbd nmbd winbind
sudo apt remove -y samba samba-common smbclient

# Instalar Samba AD DC e dependências
sudo apt install -y \
    samba \
    smbclient \
    winbind \
    krb5-user \
    krb5-config \
    libpam-krb5 \
    libpam-winbind \
    libnss-winbind

# Durante a instalação do krb5, quando perguntar:
# - Default Kerberos realm: EXEMPLO.LOCAL (maiúsculas!)
# - Kerberos servers: dc01.exemplo.local
# - Administrative server: dc01.exemplo.local
```

### Passo 5: Parar Serviços e Fazer Backup

```bash
# Parar serviços
sudo systemctl stop smbd nmbd winbind

# Desabilitar serviços antigos
sudo systemctl disable smbd nmbd winbind

# Fazer backup da configuração antiga
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.backup 2>/dev/null || true
```

### Passo 6: Provisionar o Domínio

```bash
# Limpar configurações antigas
sudo rm -rf /var/lib/samba/*
sudo rm -rf /etc/samba/*

# Provisionar domínio interativamente
sudo samba-tool domain provision --use-rfc2307 --interactive

# Responda as perguntas:
# Realm: EXEMPLO.LOCAL
# Domain: EXEMPLO
# Server Role: dc
# DNS backend: SAMBA_INTERNAL
# DNS forwarder IP: 8.8.8.8
# Administrator password: (senha forte, mínimo 8 caracteres)
```

**OU provisionar direto (não-interativo):**

```bash
sudo samba-tool domain provision \
    --use-rfc2307 \
    --realm=EXEMPLO.LOCAL \
    --domain=EXEMPLO \
    --adminpass='SenhaForte123!' \
    --server-role=dc \
    --dns-backend=SAMBA_INTERNAL \
    --option="dns forwarder = 8.8.8.8"
```

### Passo 7: Configurar Kerberos

```bash
# Copiar configuração do Kerberos gerada pelo Samba
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf

# Verificar arquivo
cat /etc/krb5.conf
```

### Passo 8: Configurar Resolução DNS

```bash
# Configurar resolv.conf
sudo nano /etc/resolv.conf

# Cole:
search exemplo.local
nameserver 127.0.0.1
nameserver 8.8.8.8

# Salvar e sair
```

**Para evitar que NetworkManager sobrescreva:**

```bash
sudo nano /etc/NetworkManager/NetworkManager.conf

# Adicione na seção [main]:
[main]
dns=none

# Reiniciar NetworkManager
sudo systemctl restart NetworkManager
```

### Passo 9: Iniciar Serviços

```bash
# Habilitar e iniciar Samba AD DC
sudo systemctl unmask samba-ad-dc
sudo systemctl enable samba-ad-dc
sudo systemctl start samba-ad-dc

# Verificar status
sudo systemctl status samba-ad-dc

# Se tudo OK, deve mostrar "active (running)"
```

### Passo 10: Verificar Instalação

```bash
# 1. Verificar nível do domínio
samba-tool domain level show

# 2. Testar DNS
host -t A exemplo.local
host -t A dc01.exemplo.local

# 3. Testar autenticação Kerberos
kinit administrator@EXEMPLO.LOCAL
# Digite a senha do Administrator

# Listar tickets
klist

# 4. Testar SMB
smbclient -L localhost -U%
smbclient -L localhost -U administrator

# 5. Listar usuários
samba-tool user list
```

---

## 🐳 MÉTODO 2: Instalação com Docker (No Ubuntu Desktop)

Se preferir usar Docker no Ubuntu Desktop:

### Passo 1: Instalar Docker

```bash
# Atualizar sistema
sudo apt update

# Instalar Docker
sudo apt install -y docker.io docker-compose

# Adicionar seu usuário ao grupo docker
sudo usermod -aG docker $USER

# IMPORTANTE: Fazer logout e login novamente para o grupo ter efeito
# Ou use: newgrp docker

# Verificar instalação
docker --version
docker-compose --version
```

### Passo 2: Usar os Arquivos Docker Fornecidos

```bash
# Extrair o arquivo que forneci anteriormente
tar xzf samba-docker-lab.tar.gz
cd samba-docker-lab

# Editar configurações
nano .env
# Ajuste IP, domínio e senha

# Construir e iniciar
docker-compose build
docker-compose up -d

# Verificar
docker-compose logs -f
```

### Passo 3: Gerenciar com o Script

```bash
./manage.sh
```

---

## 🖥️ MÉTODO 3: Interface Gráfica (Cockpit)

Para quem prefere interface gráfica para gerenciar o servidor:

### Instalar Cockpit

```bash
# Instalar Cockpit
sudo apt install -y cockpit

# Habilitar e iniciar
sudo systemctl enable --now cockpit.socket

# Acessar via navegador
# https://localhost:9090
# ou
# https://192.168.1.10:9090

# Login com seu usuário Ubuntu
```

**Nota:** Cockpit não gerencia diretamente o Samba AD, mas ajuda a:
- Monitorar recursos do sistema
- Gerenciar serviços
- Ver logs
- Gerenciar usuários do sistema

---

## 📊 Gerenciamento Diário

### Via Terminal (Comandos Principais)

```bash
# Criar usuário
sudo samba-tool user create joao Senha123!

# Listar usuários
sudo samba-tool user list

# Criar grupo
sudo samba-tool group add TI

# Adicionar usuário a grupo
sudo samba-tool group addmembers TI joao

# Resetar senha
sudo samba-tool user setpassword joao

# Desabilitar usuário
sudo samba-tool user disable joao

# Criar OU
sudo samba-tool ou create "OU=Departamentos,DC=exemplo,DC=local"

# Ver logs
sudo tail -f /var/log/samba/log.samba
```

### Via Interface Gráfica (RSAT)

Você pode gerenciar de uma máquina Windows com RSAT instalado:

1. **No Windows:**
   - Instale RSAT (Remote Server Administration Tools)
   - Configure DNS para apontar para o servidor Ubuntu (192.168.1.10)
   - Abra "Active Directory Users and Computers"
   - Conecte-se ao domínio

2. **Gerenciamento visual:**
   - Criar/editar usuários
   - Criar/editar grupos
   - Gerenciar OUs
   - Configurar GPOs (Group Policy Objects)

---

## 🔧 Ferramentas Gráficas Linux

### LAM (LDAP Account Manager)

Interface web para gerenciar LDAP/AD:

```bash
# Instalar Apache e PHP
sudo apt install -y apache2 php libapache2-mod-php php-ldap php-xml

# Instalar LAM
sudo apt install -y ldap-account-manager

# Acessar via navegador
# http://localhost/lam
# http://192.168.1.10/lam

# Configurar LAM para conectar no Samba AD
```

### Samba Web Administration Tool (SWAT)

**Nota:** SWAT foi descontinuado, mas há alternativas:

```bash
# Webmin (alternativa moderna)
wget http://prdownloads.sourceforge.net/webadmin/webmin_2.105_all.deb
sudo dpkg -i webmin_2.105_all.deb
sudo apt install -f

# Acessar: https://localhost:10000
```

---

## 🔒 Configurações de Segurança

### Firewall (UFW)

```bash
# Habilitar firewall
sudo ufw enable

# Permitir SSH (se usar)
sudo ufw allow 22/tcp

# Permitir Samba
sudo ufw allow 53/tcp    # DNS
sudo ufw allow 53/udp    # DNS
sudo ufw allow 88/tcp    # Kerberos
sudo ufw allow 88/udp    # Kerberos
sudo ufw allow 135/tcp   # RPC
sudo ufw allow 137/udp   # NetBIOS
sudo ufw allow 138/udp   # NetBIOS
sudo ufw allow 139/tcp   # NetBIOS
sudo ufw allow 389/tcp   # LDAP
sudo ufw allow 445/tcp   # SMB
sudo ufw allow 464/tcp   # Kerberos Password
sudo ufw allow 636/tcp   # LDAPS
sudo ufw allow 3268/tcp  # Global Catalog
sudo ufw allow 3269/tcp  # Global Catalog SSL

# Verificar status
sudo ufw status verbose

# OU permitir tudo de uma vez (apenas para LAB!)
sudo ufw allow from 192.168.1.0/24
```

### Políticas de Senha

```bash
# Configurar complexidade de senha
sudo samba-tool domain passwordsettings set --complexity=on
sudo samba-tool domain passwordsettings set --history-length=12
sudo samba-tool domain passwordsettings set --min-pwd-age=1
sudo samba-tool domain passwordsettings set --max-pwd-age=90
sudo samba-tool domain passwordsettings set --min-pwd-length=8

# Ver configurações
sudo samba-tool domain passwordsettings show
```

---

## 🔄 Backup Automatizado

### Script de Backup Simples

```bash
# Criar diretório de backups
mkdir -p ~/backups

# Criar script
nano ~/backup-samba.sh

# Cole:
#!/bin/bash
BACKUP_DIR="$HOME/backups"
DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_FILE="samba-backup-$DATE.tar.gz"

echo "Iniciando backup do Samba AD DC..."

# Parar serviços temporariamente (opcional)
# sudo systemctl stop samba-ad-dc

# Fazer backup
sudo tar czf "$BACKUP_DIR/$BACKUP_FILE" \
    /var/lib/samba \
    /etc/samba \
    /etc/krb5.conf

# Reiniciar serviços
# sudo systemctl start samba-ad-dc

echo "Backup concluído: $BACKUP_FILE"

# Manter apenas últimos 7 backups
cd "$BACKUP_DIR"
ls -t samba-backup-*.tar.gz | tail -n +8 | xargs rm -f 2>/dev/null

# Salvar e tornar executável
chmod +x ~/backup-samba.sh

# Testar
~/backup-samba.sh
```

### Agendar Backup (Cron)

```bash
# Editar crontab
crontab -e

# Adicionar linha para backup diário às 2h da manhã:
0 2 * * * /home/seu_usuario/backup-samba.sh

# Salvar e sair
```

---

## 🧪 Testando com Cliente

### Cliente Ubuntu Desktop

Em outra máquina Ubuntu Desktop (ou na mesma para teste):

```bash
# Instalar pacotes necessários
sudo apt install -y samba winbind krb5-user libpam-winbind libnss-winbind

# Configurar DNS para apontar para o servidor
sudo nano /etc/resolv.conf
# nameserver 192.168.1.10

# Configurar /etc/samba/smb.conf
sudo nano /etc/samba/smb.conf

[global]
   workgroup = EXEMPLO
   realm = EXEMPLO.LOCAL
   security = ADS
   dns forwarder = 8.8.8.8
   idmap config * : backend = tdb
   idmap config * : range = 10000-999999
   template shell = /bin/bash
   template homedir = /home/%U
   winbind use default domain = true

# Ingressar no domínio
sudo net ads join -U administrator

# Configurar NSS
sudo nano /etc/nsswitch.conf

# Modificar linhas:
passwd:         files systemd winbind
group:          files systemd winbind

# Reiniciar winbind
sudo systemctl restart winbind

# Testar
wbinfo -u  # Lista usuários do domínio
wbinfo -g  # Lista grupos do domínio
getent passwd joao  # Informações do usuário
```

### Cliente Windows

1. Configurar DNS do Windows para apontar para 192.168.1.10
2. Ingressar no domínio:
   - Painel de Controle > Sistema > Alterar configurações
   - Botão "Alterar"
   - Selecionar "Domínio" e digitar: exemplo.local
   - Credenciais: Administrator / senha
3. Reiniciar Windows
4. Login com EXEMPLO\joao ou joao@exemplo.local

---

## 🐛 Troubleshooting Comum

### DNS não resolve

```bash
# Verificar serviço Samba
sudo systemctl status samba-ad-dc

# Testar DNS
nslookup exemplo.local 127.0.0.1
dig @127.0.0.1 exemplo.local

# Verificar logs
sudo tail -f /var/log/samba/log.samba

# Reiniciar serviço
sudo systemctl restart samba-ad-dc
```

### Autenticação falha

```bash
# Verificar Kerberos
kinit administrator@EXEMPLO.LOCAL
klist

# Resetar senha
sudo samba-tool user setpassword administrator

# Verificar configuração
sudo samba-tool testparm
```

### Porta já em uso

```bash
# Verificar processos usando portas
sudo netstat -tulpn | grep -E ':(53|88|135|389|445)'

# Se houver conflito, parar serviços conflitantes
sudo systemctl stop systemd-resolved  # Conflito com DNS
```

---

## 📚 Próximos Passos

1. **Compartilhamentos de Rede**
   - Criar shares
   - Configurar permissões
   - Mapear unidades de rede

2. **Políticas de Grupo (GPO)**
   - Instalar ferramentas RSAT
   - Criar políticas
   - Aplicar a OUs

3. **DHCP Server**
   - Instalar ISC DHCP
   - Integrar com DNS dinâmico
   - Configurar escopos

4. **Monitoramento**
   - Configurar Nagios/Zabbix
   - Alertas por email
   - Dashboard de status

---

## ✅ Checklist de Instalação

- [ ] Ubuntu Desktop instalado e atualizado
- [ ] IP estático configurado
- [ ] Hostname configurado corretamente
- [ ] /etc/hosts configurado
- [ ] Samba e dependências instalados
- [ ] Domínio provisionado com sucesso
- [ ] DNS funcionando (testes com host/nslookup)
- [ ] Kerberos funcionando (kinit e klist)
- [ ] SMB respondendo (smbclient)
- [ ] Firewall configurado
- [ ] Backup configurado
- [ ] Cliente teste conectado

---

**Está pronto para começar! O Ubuntu Desktop funciona perfeitamente para seu laboratório Samba AD DC! 🎉**
