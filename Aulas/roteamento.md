### Funcionamento do servidor Ubuntu como um gateway e servidor DNS (BIND) entre a rede interna e a internet (NAT):
- interfaces de rede,
- o encaminhamento de pacotes (IP Forwarding)
- configuração básica do BIND.

### 1. Configuração de Rede (Netplan)No Ubuntu, a configuração é feita via Netplan. Identifique suas placas (geralmente enp0s3 e enp0s8) com ip link. Edite o arquivo: sudo nano /etc/netplan/01-netcfg.yaml (o nome pode variar).YAMLnetwork:
```
version: 2
  renderer: networkd
  ethernets:
    enp0s3:            # Interface NAT (Externa)
      dhcp4: true
    enp0s8:            # Interface Rede Interna
      addresses:
        - 192.168.10.1/24
   ```     
### Aplique as mudanças:
```
sudo netplan apply
```
### 2. Ativar Internet para a Rede Interna (IP Forwarding)Como a rede interna precisa acessar a internet via placa NAT, o Ubuntu deve atuar como roteador. Habilitar o encaminhamento:
Edite: 
```
sudo nano /etc/sysctl.conf
```
Descomente a linha: net.ipv4.ip_forward=1

Aplique: 
```
sudo sysctl -p
```
Configurar o NAT (Masquerade) com iptables: Para garantir que o tráfego da rede interna saia pela NAT:
```
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
```
(Para tornar essa regra persistente, instale o pacote iptables-persistent).
### 3. Configuração do BIND9Com o BIND instalado (sudo apt install bind9), você deve configurar quem pode fazer consultas e para onde os pedidos externos devem ser enviados (Forwarders).
Edite o arquivo de opções: sudo nano /etc/bind/named.conf.options
```
DNS Zone file
acl "trusted" {
        127.0.0.0/8;
        192.168.10.0/24;  # Sua rede interna
};

options {
        directory "/var/cache/bind";

        recursion yes;
        allow-query { trusted; };

        # Encaminha consultas que ele não conhece (ex: google.com) para o DNS do Google
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };

        dnssec-validation auto;
        listen-on-v6 { any; };
};
```
Reinicie o serviço:
```
sudo systemctl restart bind9
```
4. Resumo da Topologia
<img width="512" height="142" alt="image" src="https://github.com/user-attachments/assets/ee253589-1fc5-4ceb-b75a-a098f5a94952" />


