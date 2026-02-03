# Gerenciamento-de-Redes-Linux

https://guiafoca.org/ 

## 🎯 Objetivo Geral

Capacitar o aluno para instalar, configurar, administrar e monitorar servidores Linux em ambiente de rede, utilizando Ubuntu, com implementação de serviços de infraestrutura, controle de acesso, segurança, automação e ferramentas de administração gráfica e via terminal.

### 📚 Conteúdo Programático
### 1️⃣ Fundamentos do Linux e Ubuntu

Arquitetura do sistema Linux (kernel, shell, systemd)

Estrutura de diretórios padrão (FHS)

Inicialização do sistema e gerenciamento de serviços

Interfaces CLI x GUI

### 2️⃣ Instalação e Configuração Inicial

Instalação do Ubuntu Server

Particionamento e sistemas de arquivos

Configuração inicial de rede

Atualização e repositórios

Gerenciamento de pacotes (APT, snap)

3️⃣ Shell e Administração via Terminal

Comandos essenciais

Manipulação de arquivos e diretórios

Pipes e redirecionamentos

Bash scripting básico

Agendamento de tarefas (cron, at)

4️⃣ Configuração de Rede no Ubuntu

Configuração IP estático e dinâmico (netplan)

DNS cliente

Testes e diagnóstico de rede

Ferramentas: ip, ss, ping, traceroute, dig, host

Configuração de rotas

Firewall (UFW / iptables — fundamentos)

5️⃣ Serviços de Rede Essenciais
🌐 DNS (Servidor de Nomes)

Conceitos de resolução de nomes

Instalação e configuração do Bind9

Zonas diretas e reversas

DNS cache

Testes de resolução

📡 DHCP

Conceitos de concessão de IP

Instalação do servidor DHCP

Escopos, reservas e leases

Integração DHCP + DNS

6️⃣ Gerenciamento de Usuários e Grupos

Criação e administração de contas

Políticas de senha

Expiração e bloqueio de contas

Grupos primários e secundários

Perfis de ambiente

7️⃣ Permissões e Controle de Acesso

Permissões Linux (rwx)

chmod, chown, chgrp

Permissões especiais (setuid, setgid, sticky)

ACLs

Cotas de disco

8️⃣ Serviços de Compartilhamento e Domínio

Servidor de arquivos com Samba

Compartilhamentos autenticados

Integração Linux–Windows

Samba como membro de domínio

Samba AD básico (conceitos)

Controle de acesso por grupo

9️⃣ Controle de Ações no Sistema

Políticas PAM

Restrições de login

Bloqueio de shell/terminal para usuários

Shell restrito

Controle de sudo

Perfis de acesso

🔟 Processos e Serviços

Gerenciamento de processos

ps, top, htop, kill, nice

systemctl

Serviços e daemons

Logs do sistema (journalctl)

11️⃣ Segurança Básica de Servidores

Hardening inicial

Segurança SSH

Chaves criptográficas

AppArmor (Ubuntu)

Auditoria básica

12️⃣ Backup e Recuperação

Conceitos de backup

Backup completo, incremental e diferencial

Ferramentas: rsync, tar, dump

Automação de backups

Restauração de dados

🖥️ Ferramentas Gráficas (Módulo Opcional)

Uso de interfaces gráficas para administração e monitoramento:

Cockpit (painel web de administração)

Webmin

Interfaces gráficas de rede (NetworkManager GUI)

Gerenciadores de usuários gráficos

Monitoramento gráfico de processos

Ferramentas visuais de logs

Comparação: gestão via CLI vs GUI — vantagens e limitações.
