## Configurando RSAT 


### 1. Requisitos Prévios
A máquina Windows deve estar obrigatoriamente ingressada no domínio que você criou no Samba.

Você deve estar logado no Windows com uma conta que tenha privilégios de Domain Admin (geralmente o usuário Administrator ou Administrador criado no provisionamento do Samba).

### 2. Instalação do RSAT (Windows 10 ou 11)
Nas versões modernas do Windows, o RSAT não é mais um instalador baixado do site da Microsoft, mas um "Recurso Opcional".

No Windows, clique no Menu Iniciar e vá em Configurações (ícone de engrenagem).

Vá em Aplicativos > Recursos Opcionais.

Clique no botão Exibir recursos (ou "Adicionar um recurso").

Na barra de busca, digite RSAT.

Selecione os seguintes itens (essenciais para o seu caso):

RSAT: Active Directory Domain Services e Lightweight Directory Services Tools (Para gerenciar usuários e grupos).

RSAT: Ferramentas de Gerenciamento de Política de Grupo (Para criar as GPOs de bloqueio de CMD e Painel de Controle).

RSAT: Servidor DHCP e RSAT: Servidor DNS (Opcional, para gerenciar os serviços que você já montou).

Clique em Próximo e depois em Instalar. Aguarde a conclusão (exige internet).

### 3. Acessando as Ferramentas
Após instalar, as ferramentas aparecerão no Menu Iniciar dentro de Ferramentas Administrativas (ou "Ferramentas do Windows").

Para gerenciar usuários:
Abra o Usuários e Computadores do Active Directory.

O console deverá abrir já conectado ao seu servidor Samba. Se não abrir, clique com o botão direito em "Usuários e Computadores..." e selecione "Conectar ao Domínio".

Para bloquear o CMD e Painel de Controle (GPO):
Abra o Gerenciamento de Política de Grupo (gpmc.msc).

Navegue em: Floresta: seu.dominio > Domínios > seu.dominio.

Clique com o botão direito em Default Domain Policy (ou crie uma nova GPO clicando com o botão direito no domínio) e selecione Editar.

Para o CMD: Vá em Configuração do Usuário > Políticas > Modelos Administrativos > Sistema. Dê dois cliques em Impedir acesso ao prompt de comando, selecione Habilitado e clique em OK.

Para o Painel de Controle: Vá em Configuração do Usuário > Políticas > Modelos Administrativos > Painel de Controle. Ative a opção Proibir acesso às configurações do PC e ao Painel de Controle.

### 4. Validando no Cliente
Depois de configurar a GPO no console RSAT, vá até a máquina do usuário final e force a atualização:

Abra o "Executar" (Win + R).

Digite gpupdate /force e dê Enter.

O Windows baixará as políticas do seu servidor Ubuntu/Samba.

Tente abrir o CMD ou o Painel de Controle; o Windows deverá exibir uma mensagem dizendo que a operação foi cancelada devido a restrições.

⚠️ Dica de Troubleshooting (DNS)
Se o RSAT não conseguir encontrar o domínio, o problema quase sempre é o DNS da máquina Windows.

Certifique-se de que o único servidor DNS configurado na placa de rede do Windows seja o IP do seu servidor Ubuntu. Se houver um DNS secundário (como 8.8.8.8), o Windows pode se perder e não achar o Active Directory do Samba.
