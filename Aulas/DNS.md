### Configuração BIND9 como um servidor DNS que atende a rede interna e resolve nomes da internet via placa NAT. Deve modificar três arquivos principais.

Considerando que o IP da sua placa de rede interna no Ubuntu é 192.168.10.1 (o gateway da rede 192.168.10.0/24), aqui estão as configurações:

1. /etc/bind/named.conf.options
Este arquivo define quem pode consultar o servidor e para onde as perguntas da internet devem ser enviadas.
```
acl "trusted" {
        127.0.0.0/8;
        192.168.10.0/24;  # Sua rede interna do VirtualBox
};

options {
        directory "/var/cache/bind";

        recursion yes;
        allow-query { trusted; };

        # Encaminha o que ele não conhece para o DNS do Google via placa NAT
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };

        dnssec-validation auto;
        listen-on-v6 { any; };
};
```
2. /etc/bind/named.conf.local
Aqui você declara as zonas (o seu domínio interno). Vamos supor que seu domínio seja lab.local.
```
// Zona Direta: Nome -> IP
zone "lab.local" {
    type master;
    file "/etc/bind/db.lab.local";
};

// Zona Reversa: IP -> Nome
zone "10.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192";
};

````
3. Criando os arquivos de Zona (Dados)
Você precisa criar os dois arquivos mencionados acima. Use o comando cp /etc/bind/db.local /etc/bind/db.lab.local para começar com um modelo.

Arquivo da Zona Direta: /etc/bind/db.lab.local

```
$TTL    604800
@       IN      SOA     ns1.lab.local. admin.lab.local. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.lab.local.
ns1     IN      A       192.168.10.1    ; IP do seu servidor Ubuntu
servidor IN     A       192.168.10.1
cliente1 IN     A       192.168.10.10   ; Exemplo de uma máquina Windows/Linux interna

```
Arquivo da Zona Reversa: /etc/bind/db.192

```
$TTL    604800
@       IN      SOA     ns1.lab.local. admin.lab.local. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.lab.local.
1       IN      PTR     ns1.lab.local.  ; Corresponde ao final .1 do IP 192.168.10.1
10      IN      PTR     cliente1.lab.local.

```

### Check-list de ativação:
Permissões: Garanta que o BIND consiga ler os arquivos: sudo chown bind:bind /etc/bind/db.*

Verificação de erros:

named-checkconf (Verifica o arquivo options e local)

named-checkzone lab.local /etc/bind/db.lab.local (Verifica a zona)

Reiniciar: sudo systemctl restart bind9

Dica: No VirtualBox, se as máquinas internas não estiverem navegando, verifique se você ativou o IP Forwarding no sysctl.conf e a regra de Masquerade no iptables para a placa NAT, como mencionei anteriormente.
