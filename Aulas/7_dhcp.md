## Instalação do Servidor DHCP

Pacote utilizado:

isc-dhcp-server

Instalar:
```
sudo apt update
sudo apt install isc-dhcp-server
Configurar Interface DHCP
```
Arquivo:
```
/etc/default/isc-dhcp-server
```
Definir interface:
```
INTERFACESv4="enp0s3"
```
Configurar Escopo DHCP

Arquivo principal:
```
/etc/dhcp/dhcpd.conf
```
Exemplo de configuração:
```
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;
  option routers 192.168.1.1;
  option domain-name-servers 192.168.1.10;
  option domain-name "empresa.local";
  default-lease-time 600;
  max-lease-time 7200;
}
```

## Explicação:

```
range → faixa de IP distribuída

routers → gateway

domain-name-servers → IP do DNS

lease-time → tempo de concessão
```
Reiniciar serviço:
```
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
```
