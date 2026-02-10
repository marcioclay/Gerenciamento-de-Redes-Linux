# Controle de Processos no Linux — Visualizar e Encerrar

Em sistemas Linux, todo programa em execução é tratado como um **processo**. Quando você abre um editor, executa um serviço ou roda um comando no terminal, o sistema cria um processo correspondente. No gerenciamento de servidores, é essencial saber **visualizar processos ativos** e **encerrar processos problemáticos** de forma segura.

Este material apresenta os principais comandos usados para monitorar e finalizar processos.

---

# O que é um Processo

Um processo é uma instância de um programa em execução, contendo:

- código do programa
- dados em memória
- recursos associados
- identificador único (**PID — Process ID**)

Cada processo possui:

- PID
- usuário dono
- consumo de CPU
- consumo de memória
- comando executado

---

# Visualização de Processos

## 🔎 Comando `ps`

O comando `ps` mostra processos em execução no momento da consulta.

### Uso básico

```bash
ps

Mostra apenas processos ligados ao terminal atual.
```

## Uso completo (mais usado em servidores)
```
ps aux
```

Exibe:

- todos os processos do sistema

- usuário dono

- PID

- uso de CPU

- uso de memória

- comando

Exemplo:
```
USER   PID  %CPU %MEM COMMAND
root     1   0.0  0.1 systemd
mysql  820   1.2  3.5 mysqld
```

📊 Comando top

Monitor de processos em tempo real.
```
top
```

Mostra continuamente:

- processos ativos

- consumo de CPU

- consumo de memória

- carga do sistema

Atalhos úteis dentro do top:
```
k → encerrar processo

q → sair

P → ordenar por CPU

M → ordenar por memória
```
## 🌳 Comando pstree

Mostra processos em formato de árvore (relação pai–filho).
```
pstree
```

## Útil para entender quem iniciou cada processo.

🎯 Encontrar PID por nome — pidof

Retorna o PID de um programa.
```
pidof apache2
```

## Encerramento de Processos

Encerrar processos é necessário quando:

- programa travou

- consumo excessivo de recursos

- serviço precisa ser reiniciado

- aplicação entrou em erro

## 🛑 Encerrar processo do terminal atual

Se o programa está rodando no terminal:
```
Ctrl + C


Interrompe imediatamente.

💀 Comando kill

Encerra processo usando o PID.

Sintaxe
kill PID

Exemplo
kill 1234
```

Envia sinal de encerramento normal (SIGTERM).

Encerramento forçado

Quando o processo não responde:
```
kill -9 1234
```

Envia SIGKILL (término imediato).

⚠️ Use apenas quando o encerramento normal falhar.

🔪 Comando killall

Encerra processos pelo nome.
```
killall firefox

```
```
Encerra todas as instâncias com esse nome.

☠️ Comando killall5

Encerra quase todos os processos do sistema.

sudo killall5
```

## ⚠️ Uso administrativo — pode derrubar serviços críticos.

🚦 Sinais de Encerramento

Processos recebem sinais de controle:

Sinal	Número	Função
- SIGTERM	15	encerramento normal
- SIGKILL	9	encerramento forçado
- SIGSTOP	—	pausa
- SIGCONT	—	continuar

Exemplos:
```
kill -15 PID
kill -9 PID
```

🔍 Identificar Processo que Usa Recurso — fuser

Mostra qual processo está usando um arquivo ou porta.
```
fuser arquivo.txt
fuser 80/tcp
```

Encerrar diretamente:
```
fuser -k 80/tcp
```

Boas Práticas em Servidor

Antes de encerrar um processo:

- Confirme o PID

- Verifique impacto no serviço

- Tente SIGTERM primeiro

- Use SIGKILL apenas se necessário

Observe se o serviço reinicia automaticamente

