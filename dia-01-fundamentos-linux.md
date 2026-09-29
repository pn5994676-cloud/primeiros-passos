# Dia 01 — Fundamentos do terminal Linux

> **Objetivo:** aprender a navegar e manipular arquivos no Kali Linux, e internalizar a mentalidade de investigação acima da memorização.

![Dia](https://img.shields.io/badge/dia-01-blue)
![Tema](https://img.shields.io/badge/tema-Linux-informational)

---

## 🧠 Mentalidade (o mais importante)

O erro clássico de quem começa é tentar **decorar** comandos. Isso não escala — existem milhares.

O que se decora é isto:

> **"Como eu descubro o que eu não sei?"**

Na prática, se você não sabe usar um comando:

```bash
# Abre o manual completo do comando
man <comando>

# Versão rápida: resumo das opções
<comando> --help
```

Esses dois caminhos resolvem 90% das dúvidas sem internet e sem decorar nada.

---

## 🧭 Navegação pelo sistema de arquivos

| Comando | O que faz |
|---|---|
| `pwd` | **P**rint **W**orking **D**irectory — mostra onde você está |
| `ls` | **L**i**s**t — lista os arquivos e pastas do diretório atual |
| `cd <pasta>` | **C**hange **D**irectory — entra na pasta indicada |
| `cd ..` | Volta um nível (uma pasta acima) |

```bash
pwd          # /home/kali
ls           # lista o conteúdo
cd redteam   # entra na pasta "redteam"
cd ..        # volta para /home/kali
```

**Por que isso importa:** todo ataque começa com **reconhecimento do ambiente**. Antes de atacar, você precisa saber onde está e o que existe ali.

## 📁 Criando pastas e arquivos

| Comando | O que faz |
|---|---|
| `mkdir <nome>` | **M**a**k**e **Dir**ectory — cria uma pasta |
| `touch <nome>` | Cria um arquivo de texto vazio |

```bash
mkdir redteam           # cria a pasta
touch nota1.txt         # cria um arquivo vazio
touch nota1.txt nota2.txt nota3.txt   # cria vários de uma vez
```

---

## 🧪 Missão 01

Executada no Kali Linux:

```bash
mkdir redteam            # 1. cria a pasta
cd redteam               # 2. entra nela
touch nota1.txt nota2.txt nota3.txt   # 3. cria os 3 arquivos
ls                       # 4. lista tudo
rm nota1.txt             # 5. apaga um arquivo
ls                       # 6. confirma que sobrou nota2 e nota3
```

**Resultado:** ✅ pasta criada, 3 arquivos criados, listagem correta, 1 arquivo removido com sucesso.

> ⚠️ **Atenção ao `rm`:** ele **não manda para a lixeira**, apaga de verdade e não tem "desfazer". Em servidores de produção, um `rm` errado é incidente. Por isso hoje se usa cada vez mais `rm -i` (pede confirmação) ou ambientes com snapshot.

---

## ✅ O que aprendi no Dia 01

- Navegação completa: `pwd`, `ls`, `cd`, `cd ..`
- Criação de pasta e arquivo: `mkdir`, `touch`
- Remoção: `rm` (e que ela é irreversível)
- **Auto-didatismo:** `man` e `--help` respondem sozinhos
- **Mentalidade:** entender o *porquê* vale mais que decorar o *como*

➡️ Próximo: [Dia 02 — Permissões, usuários e privilégios](dia-02-permissoes-usuarios-privilegios.md)
