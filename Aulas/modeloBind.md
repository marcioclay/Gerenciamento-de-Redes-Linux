#Modelo Bind9

## redes.com

## Configuração pensada para servidor DNS autoritativo interno, em rede local 192.168.1.0/24, com servidor:

192.168.1.100

1️⃣ Instalar o BIND9
sudo apt update
sudo apt install bind9 bind9utils -y

2️⃣ Configuração Global

Arquivo:

/etc/bind/named.conf.options
```
options {
    directory "/var/cache/bind";

    recursion yes;
    allow-query { any; };

    forwarders {
        8.8.8.8;
        8.8.4.4;
    };

    dnssec-validation auto;

    listen-on { any; };
    listen-on-v6 { any; };
};
```
Explicação técnica:

- recursion yes → permite resolver domínios externos

- forwarders → encaminha consultas para DNS externo

- allow-query → libera consultas da rede

- listen-on → escuta em todas interfaces

3️⃣ Declarar Zona Direta

Arquivo:

/etc/bind/named.conf.local

Adicionar:
```
zone "redes.com" {
    type master;
    file "/etc/bind/db.redes.com";
};
```
4️⃣ Criar Arquivo de Zona Direta

Criar:

/etc/bind/db.redes.com

Conteúdo:
```
$TTL 604800
@   IN  SOA ns1.redes.com. admin.redes.com. (
        2026022401  ; Serial
        604800      ; Refresh
        86400       ; Retry
        2419200     ; Expire
        604800 )    ; Negative Cache TTL

; Servidor DNS
@       IN  NS      ns1.redes.com.

; Registros A
ns1     IN  A       192.168.1.100
www     IN  A       192.168.1.101
server  IN  A       192.168.1.100
mail    IN  A       192.168.1.102

; Registro MX
@       IN  MX 10   mail.redes.com.
```
5️⃣ Zona Reversa (Recomendado)

Ainda em:

/etc/bind/named.conf.local

Adicionar:
```
zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192";
};
```

Criar arquivo reverso

/etc/bind/db.192

Conteúdo:
```
$TTL 604800
@   IN  SOA ns1.redes.com. admin.redes.com. (
        2026022401
        604800
        86400
        2419200
        604800 )

@       IN  NS      ns1.redes.com.

100     IN  PTR     ns1.redes.com.
101     IN  PTR     www.redes.com.
102     IN  PTR     mail.redes.com.
```

6️⃣ Permissões (Boa prática)
sudo chown bind:bind /etc/bind/db.redes.com
sudo chown bind:bind /etc/bind/db.192

7️⃣ Verificação

Verificar sintaxe geral:

sudo named-checkconf

Verificar zona direta:
```
sudo named-checkzone redes.com /etc/bind/db.redes.com
```

Verificar zona reversa:
```
sudo named-checkzone 1.168.192.in-addr.arpa /etc/bind/db.192
```

8️⃣ Reiniciar Serviço
sudo systemctl restart bind9

9️⃣ Liberar Firewall
sudo ufw allow 53

🔟 Testes
Teste resolução direta:
- dig @192.168.1.100 www.redes.com
- Teste reverso:
- dig -x 192.168.1.101

Teste cliente

No cliente Linux:

- nano /etc/resolv.conf

Adicionar:
```
nameserver 192.168.1.100
📌 Resultado Esperado
Nome	IP
ns1.redes.com	192.168.1.100
www.redes.com
	192.168.1.101
mail.redes.com	192.168.1.102
````
📚 Arquivos Envolvidos
- /etc/bind/named.conf
- /etc/bind/named.conf.options
- /etc/bind/named.conf.local
- /etc/bind/db.redes.com
/etc/bind/db.192
