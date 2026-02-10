# Estrutura básica de diretórios do Sistema Linux


```
/bin Contém arquivos programas do sistema que são usados com freqüência pelos usuários.
/boot Contém arquivos necessários para a inicialização do sistema.
/cdrom Ponto de montagem da unidade de CD-ROM.
/media Ponto de montagem de dispositivos diversos do sistema (rede, pen-drives, CD-ROM
em distribuições mais novas).
/dev Contém arquivos usados para acessar dispositivos (periféricos) existentes no
computador.
/etc Arquivos de configuração de seu computador local.
/floppy Ponto de montagem de unidade de disquetes
/home Diretórios contendo os arquivos dos usuários.
/lib Bibliotecas compartilhadas pelos programas do sistema e módulos do kernel.
/lost+found Local para a gravação de arquivos/diretórios recuperados pelo utilitário fsck.ext2. Cada
partição possui seu próprio diretório lost+found.
/mnt Ponto de montagem temporário.
/proc Sistema de arquivos do kernel. Este diretório não existe em seu disco rígido, ele é
colocado lá pelo kernel e usado por diversos programas que fazem sua leitura, verificam
configurações do sistema ou modificar o funcionamento de dispositivos do sistema
através da alteração em seus arquivos.
/sys Sistema de arquivos do kernel. Este diretório não existe em seu disco rígido, ele é
colocado lá pelo kernel e usado por diversos programas que fazem sua leitura, verificam
configurações do sistema ou modificar o funcionamento de dispositivos do sistema
através da alteração em seus arquivos.
/root Diretório do usuário root.
/sbin Diretório de programas usados pelo superusuário (root) para administração e controle
do funcionamento do sistema.
/tmp Diretório para armazenamento de arquivos temporários criados por programas.
/usr Contém maior parte de seus programas. Normalmente acessível somente como leitura.
/var Contém maior parte dos arquivos que são gravados com freqüência pelos programas
do sistema, e-mails, spool de impressora, cache, etc.

```

A estrutura de diretórios também é chamada de Árvore de Diretórios porque é parecida com uma
árvore de cabeça para baixo. Cada diretório do sistema tem seus respectivos arquivos que são armazenados
conforme regras definidas pela FHS (FileSystem Hierarchy Standard - Hierarquia Padrão do Sistema de
Arquivos) versão 2.0, definindo que tipo de arquivo deve ser armazenado em cada diretório.

