# Atualização e Upgrade do Sistema e Pacotes no Linux (APT — Ubuntu/Debian)

Em distribuições Linux baseadas em Debian e Ubuntu, o gerenciamento de software é feito pelo sistema **APT (Advanced Package Tool)**. Ele permite instalar, remover, atualizar e atualizar a versão de pacotes de forma centralizada e segura.

Este material explica:

- onde o sistema busca atualizações
- arquivos de configuração de repositórios
- diferença entre update e upgrade
- comandos de atualização
- boas práticas de manutenção

---

# Conceitos Básicos

O APT trabalha com:

- **repositórios** → servidores que armazenam pacotes
- **listas de pacotes** → índice local das versões disponíveis
- **pacotes instalados** → programas no sistema

O processo de atualização ocorre em duas etapas:

1️⃣ Atualizar a lista de pacotes disponíveis  
2️⃣ Atualizar os pacotes instalados

---

# Arquivos de Repositórios


Os endereços dos repositórios ficam em arquivos dentro de:
```
/etc/apt/


Arquivo principal — sources.list
Contém os repositórios oficiais configurados.

Exemplo:

deb http://archive.ubuntu.com/ubuntu jammy main restricted
deb http://archive.ubuntu.com/ubuntu jammy-updates main restricted
deb http://security.ubuntu.com/ubuntu jammy-security main restricted
``` 

Cada linha define:

- tipo de repositório
- endereço
- versão da distribuição
- componentes

---

## 📁 Diretório adicional de fontes
```
/etc/apt/sources.list.d/
```
Contém arquivos extras de repositórios de terceiros.

Exemplo:

google-chrome.list
docker.list 

_______________________________________________________ 
# Atualizando Lista de Pacotes

## 🔄 `apt update`

Atualiza o índice local de pacotes disponíveis nos repositórios.

```
sudo apt update

O comando:

- consulta repositórios

- baixa listas de versões

- não instala nada

- apenas sincroniza informações

- Use sempre antes de instalar ou atualizar pacotes.
``` 

## Atualizando Pacotes Instalados

### ⬆️ apt upgrade
```
Atualiza todos os pacotes instalados para as versões mais recentes disponíveis sem remover dependências.

sudo apt upgrade
Características:

- método seguro

- não remove pacotes

- não instala dependências novas complexas

- Upgrade Completo do Sistema

### 🚀 apt full-upgrade (ou dist-upgrade)

# sudo apt full-upgrade
ou (compatível):

sudo apt dist-upgrade

Diferença:

- pode remover pacotes antigos

- instala dependências novas

- resolve mudanças de versão

- usado em upgrades maiores

- Atualizar e Limpar Pacotes Antigos

###🧹 Remover dependências não usadas
```
sudo apt autoremove
🗑️ Limpar cache de pacotes baixados
sudo apt clean
```
Remove arquivos .deb armazenados em:

- /var/cache/apt/archives/

### Atualizar Pacote Específico
```
sudo apt install nome-do-pacote
```
Se houver versão nova disponível, ele atualiza.

Exemplo:
```
sudo apt install openssh-server

Ver Pacotes Atualizáveis
apt list --upgradable
Mostra quais pacotes possuem atualização disponível.
```

## Ciclo Correto de Atualização (Rotina de Servidor)
Procedimento recomendado:
```
sudo apt update
sudo apt upgrade
sudo apt autoremove
```
- Atualizações de Segurança
- Repositórios de segurança são definidos no sources.list:

security.ubuntu.com
Eles fornecem:

- correções de falhas

- patches críticos

- atualizações de segurança

- Automatização de Atualizações

Pacote:

- unattended-upgrades
Permite aplicar atualizações de segurança automaticamente.

Instalar:
```
sudo apt install unattended-upgrades
```
Ver Histórico de Atualizações
Logs do APT:

/var/log/apt/history.log
/var/log/apt/term.log
Visualizar:

less /var/log/apt/history.log
Boas Práticas em Ambiente de Servidor
sempre rodar apt update antes de upgrade

revisar lista de pacotes antes de confirmar

preferir upgrade em produção

usar full-upgrade com cautela

manter repositórios oficiais

evitar fontes desconhecidas

registrar mudanças importantes



