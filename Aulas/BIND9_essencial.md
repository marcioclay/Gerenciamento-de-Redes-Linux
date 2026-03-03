## BIND9 em um cenário de rede mista (Interna/Externa), você precisa manipular quatro arquivos principais. 
A lógica do BIND é modular: um arquivo chama o outro, separando as configurações globais das definições de zonas (seus domínios).

Aqui estão os arquivos e o porquê de cada um:

1. /etc/bind/named.conf.options
O que é: Configurações globais e de segurança.
Por que configurar: * Segurança: Você define quem pode fazer consultas ao seu servidor (ACLs). Sem isso, seu servidor pode sofrer ataques de amplificação de DNS.

Forwarders: Como sua máquina interna quer acessar a internet, este arquivo diz ao BIND: "Se você não souber o IP do https://www.google.com/search?q=google.com, pergunte ao DNS 8.8.8.8".

Recursão: Habilita o servidor a buscar respostas para os clientes da rede interna.

2. /etc/bind/named.conf.local
O que é: Onde você declara suas "Zonas" (seus domínios, ex: meuprojeto.lan).
Por que configurar: * É aqui que você aponta para o BIND onde estão os arquivos de banco de dados das zonas.

Você define aqui a Zona Direta (nome -> IP) e a Zona Reversa (IP -> nome).

3. /etc/bind/db.redes.com (Nome personalizado)
O que é: O arquivo de Zona Direta.
Por que configurar: * Contém os registros A (Address). É aqui que você diz que o servidor web.meuprojeto.lan corresponde ao IP 192.168.10.10.

Sem ele, seus computadores internos não conseguem se comunicar usando nomes, apenas IPs.

4. /etc/bind/db.192 (Nome personalizado)
O que é: O arquivo de Zona Reversa.
Por que configurar: * Contém os registros PTR (Pointer). Serve para quando um serviço pergunta: "Quem é o dono do IP 192.168.10.10?".

É essencial para o bom funcionamento de muitos serviços de rede e logs de segurança.
