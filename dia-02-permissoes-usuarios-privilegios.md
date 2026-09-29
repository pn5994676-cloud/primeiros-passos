# Dia 02 — Permissões, usuários e privilégios

> **Objetivo:** entender como o Linux controla quem pode fazer o quê — e por que isso é a base da escalação de privilégios.

![Dia](https://img.shields.io/badge/dia-02-blue)
![Tema](https://img.shields.io/badge/tema-Permiss%C3%B5es%20%2F%20Privilege%20Escalation-orange)

---

## Parte 1 — Como as permissões são lidas

```bash
ls -l
```

Saída típica:

```
-rw-r--r-- 1 user user arquivo.txt
```

Lendo caractere por caractere:

| Bloco | Valor | Significado |
|---|---|---|
| `-` | tipo | `-` = arquivo comum, `d` = diretório, `l` = link |
| `rw-` | dono (*user*) | pode **ler** e **escrever** |
| `r--` | grupo (*group*) | só pode **ler** |
| `r--` | outros (*others*) | só pode **ler** |

### A tradução simples

| Símbolo | Valor | Significa |
|:---:|:---:|---|
| `r` | 4 | **R**ead — ler |
| `w` | 2 | **W**rite — escrever |
| `x` | 1 | e**X**ecute — executar |

### Os valores octais do `chmod`

O Linux usa **base octal** (base 8). Cada dígito é a **soma** das permissões:

| Soma | Permissão | Código |
|:---:|---|---|
| 4 + 2 + 1 | `rwx` | **7** — total |
| 4 + 2 | `rw-` | **6** |
| 4 + 1 | `r-x` | **5** |
| 4 | `r--` | **4** |
| 0 | `---` | **0** — nenhum acesso |

Os **três dígitos** são na ordem **dono → grupo → outros**. Então `754` significa: dono faz tudo (`7`), grupo lê e executa (`5`), outros só leem (`4`).

> 💡 **Insight:** existem só **8 combinações possíveis** por dígito (0 a 7). Entender a soma é entender todo o `chmod`.

---

## Parte 2 — Mudando permissões na prática

```bash
touch teste.sh       # cria o arquivo
ls -l                # por padrão vem 644: -rw-r--r--

chmod 777 teste.sh   # permissão TOTAL para todo mundo
ls -l                # -rwxrwxrwx  ← PERIGOSO

chmod 000 teste.sh   # ninguém acessa
ls -l                # ----------
```

### ⚠️ Por que `chmod 777` é um problema de segurança

`777` libera **leitura, escrita e execução para qualquer usuário do sistema**. Em um servidor:

- qualquer usuário pode **alterar** o script
- se ele for executado por `root`, o invasor **herda privilégio de root** (é uma das falhas mais exploradas em CTFs e pentests reais)
- viola o **princípio do menor privilégio**

**Regra profissional:** nunca use `777`. Use o mínimo necessário — scripts executáveis normalmente ficam `755` ou `750`.

Verificação:

```bash
stat -c "%a %n" teste.sh   # mostra a permissão em octal
```

---

## Parte 3 — O que é privilégio

No Linux existem dois níveis essenciais:

| Nível | Usuário | Poderes |
|---|---|---|
| **Comum** | `kali` | limitado ao que é dele |
| **Root** | `root` (UID 0) | faz **absolutamente tudo** |

### Descobrindo seu nível

```bash
whoami          # quem eu sou agora?
sudo whoami     # quem eu sou ao pedir elevação?
```

- `whoami` → `kali` (usuário comum)
- `sudo whoami` → `root` (você subiu de nível)

### Por que isso é o centro da segurança ofensiva

Grande parte dos ataques segue **exatamente** esta sequência:

```
1. Entrar como usuário comum (phishing, senha fraca, serviço vulnerável)
2. Procurar falhas de configuração
3. Virar root         ← privilege escalation
4. Ter controle total da máquina
```

Entender as permissões de hoje é entender o **passo 2** dessa corrente.

---

## 🧪 Missão 02 — Laboratório de permissões

Objetivo: praticar a lógica octal com três níveis de acesso diferentes.

```bash
mkdir laboratorio
cd laboratorio

# 1. cria os três arquivos
touch publico.txt privado.txt secreto.txt

# 2. configura as permissões conforme a regra
chmod 644 publico.txt    # todos podem LER, só o dono escreve
chmod 600 privado.txt    # só o dono lê e escreve
chmod 000 secreto.txt    # ninguém acessa (nem o dono)

# 3. confere o resultado
ls -l
```

Resultado esperado:

```
-rw-r--r-- 1 kali kali  0 ... publico.txt
-rw------- 1 kali kali  0 ... privado.txt
---------- 1 kali kali  0 ... secreto.txt
```

**Aprendizado:** `000` é tão extremo quanto `777` — bloqueia até o dono. Na prática, `600` (privado) e `640` (grupo lê) é o que você vai usar de verdade.

---

## ✅ O que aprendi no Dia 02

- Ler a string de permissões do `ls -l` símbolo por símbolo
- Calcular octal na mão: `r=4`, `w=2`, `x=1`, somando por bloco
- Aplicar `chmod` para restringir e liberar acesso
- Diferença entre usuário comum e `root`, e o conceito de **escalação de privilégio**
- **Segurança na prática:** `777` é quase sempre um erro e um vetor de ataque

⬅️ Anterior: [Dia 01 — Fundamentos do terminal Linux](dia-01-fundamentos-linux.md)
➡️ Próximo: [Dia 03 — Redes e reconhecimento de alvos](dia-03-redes-reconhecimento.md)
