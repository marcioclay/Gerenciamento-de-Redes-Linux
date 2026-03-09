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
### 2. Para configurar o seu servidor Ubuntu como um roteador (fazendo a ponte entre a rede interna e a internet via NAT), você precisa de dois passos: ativar o encaminhamento de pacotes no núcleo (kernel) e configurar a tabela de roteamento (NAT).

1. Ativar o IP Forwarding (Kernel)
Este comando diz ao Linux que ele pode receber um pacote em uma placa e enviá-lo por outra.

Para ativar imediatamente (temporário):

```
sudo sysctl -w net.ipv4.ip_forward=1
```
Para tornar permanente (após reiniciar):

Edite o arquivo: sudo nano /etc/sysctl.conf

Procure a linha #net.ipv4.ip_forward=1 e remova o # (descomente).

Salve (Ctrl+O, Enter) e saia (Ctrl+X).

Aplique com: sudo sysctl -p

2. Configurar o NAT (Masquerade) no Firewall
O IP Forwarding sozinho não é suficiente porque a internet não sabe como devolver pacotes para o seu IP privado (192.168.10.x). O Masquerade "mascara" o IP interno com o IP da sua placa NAT externa.

Assumindo que sua placa externa (NAT) seja enp0s3:

```
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
```
3. Tornar as regras de IPTables permanentes
Por padrão, o iptables apaga as regras ao reiniciar. Para salvar:

Instale o utilitário:

```
sudo apt update && sudo apt install iptables-persistent
```
Durante a instalação, selecione "Yes" para salvar as regras atuais de IPv4.

Dica de Diagnóstico: Se as máquinas internas ainda não navegarem, verifique se elas estão com o Gateway Padrão configurado como o IP da placa interna do seu servidor (192.168.10.1).
4. Resumo da Topologia
<img width="512" height="142" alt="image" src="https://github.com/user-attachments/assets/ee253589-1fc5-4ceb-b75a-a098f5a94952" />


