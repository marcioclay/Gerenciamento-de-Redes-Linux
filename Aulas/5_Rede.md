# Configuração de Interface de Rede no Ubuntu (Server)


O Netplan aplica configurações usando `systemd-networkd` ou `NetworkManager`.

# 📍 1. Verificar Interfaces de Rede

Antes de configurar, identifique o nome da interface:

'''
ip a
'''

/etc/netplan/ 

## Exemplo de Interface
```
ens33
enp0s3
eth0
```
📂 2. Arquivo de Configuração

Os arquivos normalmente possuem nomes como:
```
/etc/netplan/00-installer-config.yaml
ou

/etc/netplan/01-netcfg.yaml

Editar com:

sudo nano /etc/netplan/00-installer-config.yaml
```

🌐 3. Configuração com IP Dinâmico (DHCP)

Exemplo:
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: true
```
Explicação:
```
version: 2 → versão do Netplan

renderer: networkd → mecanismo de gerenciamento

ens33 → nome da interface

dhcp4: true → recebe IP automaticamente
```

🌐 4. Configuração com IP Fixo (Estático)

Exemplo:
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
Explicação:
```
addresses → IP + máscara CIDR

gateway4 → gateway padrão

nameservers → servidores DNS
```

⚙️ 5. Aplicar Configuração

Após salvar o arquivo:

```
sudo netplan apply
```
Para testar antes de aplicar definitivamente:
```
sudo netplan try
🔎 6. Verificar Configuração Aplicada
ip a
ip route

Testar conectividade:

ping 8.8.8.8
ping google.com
```
⚠️ Cuidados Importantes

YAML é sensível à indentação (usar espaços, não TAB).

Erros de identação impedem aplicação.

Sempre testar com netplan try em ambiente remoto.



