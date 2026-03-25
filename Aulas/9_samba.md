## Samba em um Controlador de Domínio (AD DC)
No Ubuntu moderno, o Samba não usa apenas o arquivo smb.conf para isso; ele utiliza um processo chamado samba-tool para provisionar o domínio.

Atenção: Como um Controlador de Domínio gerencia DNS e autenticação Kerberos, ele entrará em conflito com o seu BIND9 se não for configurado para trabalharem juntos.

Converter seu servidor em um Primary Domain Controller (PDC):

### 1. Preparação (Limpando o Samba antigo)
Para que o Samba vire um AD, ele precisa de uma configuração "limpa".

```
# Para o serviço e remove o config antigo (faça backup antes!)
sudo systemctl stop smbd nmbd
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```

### 2. Provisionando o Domínio
Ele fará perguntas como o nome do domínio (ex: lab.local) e a senha do Administrador.

```
sudo samba-tool domain provision --use-rfc2307 --interactive
```

### 3. O arquivo smb.conf de um Servidor de Domínio
Após o comando acima, o Samba gerará um arquivo parecido com este. Note que não há comentários no final das linhas para evitar o erro anterior:

```
[global]
    # Nome do domínio NetBIOS (ex: LAB)
    workgroup = LAB
    
    # Nome completo do domínio DNS
    realm = LAB.LOCAL
    
    # Define o papel do servidor como Controlador de Domínio
    server role = active directory domain controller
    
    # Onde o Samba buscará nomes de internet (encaminhador)
    dns forwarder = 8.8.8.8
    
    # Configurações de ID para compatibilidade com Linux
    idmap_ldb:use rfc2307 = yes

[netlogon]
    # Pasta para scripts de logon (ex: mapear unidades ao iniciar)
    path = /var/lib/samba/sysvol/lab.local/scripts
    read only = No

[sysvol]
    # Pasta para as Políticas de Grupo (GPO) do Windows
    path = /var/lib/samba/sysvol
    read only = No

[Publico]
    # Pasta que todos do domínio podem ver
    path = /srv/samba/publico
    read only = Yes
    guest ok = Yes

[Alunos]
    # Pasta restrita a um grupo do AD
    path = /srv/samba/alunos
    read only = No
    # Apenas membros do grupo 'alunos' podem acessar
    valid users = @alunos
```

### 4. Gerenciando Usuários e Grupos via Terminal
No modo Domínio, você não usa mais smbpasswd. Você usa o samba-tool:

- Criar um usuário no domínio:
```
sudo samba-tool user create aluno1
```

- Criar um grupo:
```
sudo samba-tool group add alunos
```

- Adicionar o aluno ao grupo:
```
sudo samba-tool group addmembers alunos aluno1
```

### 5. Como colocar o Windows no Domínio
Para o Windows "enxergar" o domínio LAB.LOCAL:

DNS no Windows: O DNS da placa de rede do Windows DEVE ser o IP do seu servidor Ubuntu (192.168.10.1). 
Se estiver o DNS do Google, ele nunca achará o domínio.

Propriedades do Sistema: No Windows, vá em Sistema > Alterar Nome > Domínio, digite lab.local e use o usuário Administrator e a senha que você definiu no passo 2.

Importante: Como você já tem o BIND9 instalado, o Samba tentará rodar o seu próprio DNS interno (Samba Internal DNS). Isso causará um erro de "Porta 53 já ocupada".
