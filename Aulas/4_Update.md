
# Resumo — Repositórios e Atualização de Pacotes no Ubuntu (APT)

## 📍 Localização dos Arquivos de Repositórios

Nas versões atuais do Ubuntu, os repositórios de pacotes (programas, atualizações e segurança) ficam em:

```
/etc/apt/sources.list.d/
```

Arquivo principal moderno:

```
/etc/apt/sources.list.d/ubuntu.sources
```

Arquivo legado (ainda pode existir):

```
/etc/apt/sources.list
```

O APT lê automaticamente todos os arquivos `.list` e `.sources` desse diretório.

---

## 🔄 Comandos de Atualização

### Atualizar lista de pacotes disponíveis

```bash
sudo apt update
```

- sincroniza com os repositórios
- baixa índices de versões
- não instala nada

---

### Atualizar pacotes instalados

```bash
sudo apt upgrade
```

- atualiza pacotes já instalados
- método seguro
- não remove dependências

---

### Upgrade completo (mais agressivo)

```bash
sudo apt full-upgrade
```

- pode instalar/remover dependências
- usado em upgrades maiores

---

## 🗑️ Remoção e Limpeza

### Remover dependências não utilizadas

```bash
sudo apt autoremove
```

---

### Limpar cache de pacotes baixados

```bash
sudo apt clean
```

Remove arquivos de:

```
/var/cache/apt/archives/
```

---

## ➕ Incluir Novos Repositórios

Sim — é possível adicionar novos repositórios.

### Método recomendado (atual)

Criar um novo arquivo em:

```
/etc/apt/sources.list.d/
```

Exemplo:

```bash
sudo nano /etc/apt/sources.list.d/meurepo.list
```

Adicionar linha:

```
deb http://servidor/repositorio ubuntu main
```

Depois executar:

```bash
sudo apt update
```

---

## ➕ Adicionar repositório via comando

```bash
sudo add-apt-repository ppa:nome/ppa
```

Atualizar:

```bash
sudo apt update
```

---

## ⚠️ Boas Práticas

- evitar editar vários arquivos ao mesmo tempo
- preferir criar arquivo novo em sources.list.d
- sempre rodar `apt update` após alterações
- usar apenas repositórios confiáveis
- documentar mudanças em servidores

---

## ✅ Resumo Final

- Repositórios ficam em `/etc/apt/sources.list.d/`
- Arquivo principal moderno: `ubuntu.sources`
- Atualizar lista: `apt update`
- Atualizar sistema: `apt upgrade`
- Limpar pacotes: `apt autoremove` e `apt clean`
- É permitido adicionar novos repositórios
- Sempre atualizar após incluir repositório
