# Dia 03 — Redes e reconhecimento de alvos

> **Objetivo:** entender como máquinas se comunicam, descobrir os alvos na rede e executar a primeira varredura real — a fase 1 de um ataque de verdade.

![Dia](https://img.shields.io/badge/dia-03-blue)
![Tema](https://img.shields.io/badge/tema-Reconhecimento%20%2F%20Nmap-red)

---

## Parte 1 — IP, a identidade da máquina

```bash
ip a
```

Saída (resumida):

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.56.102/24 brd 192.168.56.255 scope global eth0
```

| Conceito | Definição |
|---|---|
| **IP** | o endereço da máquina na rede |
| **Rede** | conjunto de máquinas conectadas que se enxergam |
| **Gateway** | a "porta de saída" da rede |

Interpretando `192.168.56.102/24`:

- **`192.168.56.102`** → o endereço da minha máquina
- **`/24`** → máscara de sub-rede: fixa os três primeiros octetos
- **`192.168.56.0/24`** → a faixa inteira da minha rede (256 endereços, `.0` a `.255`)

> 🔑 **Conceito-chave:** eu só consigo enxergar máquinas dentro da minha própria rede. Um atacante **sempre** começa descobrindo qual é esse território.

## Parte 2 — Portas: onde os serviços vivem

| Porta | Serviço | Protocolo |
|:---:|---|---|
| **22** | SSH — acesso remoto | TCP |
| **80** | HTTP — site sem criptografia | TCP |
| **443** | HTTPS — site com TLS | TCP |
| 21 | FTP — transferência de arquivos | TCP |
| 445 | SMB — compartilhamento Windows | TCP |
| 3306 | MySQL | TCP |
| 3389 | RDP — área de trabalho remota | TCP |

**Regra mental:** porta **aberta** = serviço escutando = **possível porta de entrada**. Numa varredura, o que importa não é o que está fechado — é **o que está aberto respondendo**.

## Parte 3 — Descobrindo a rede antes de varrer

```bash
ip route
```

Saída obtida:

```
192.168.56.0/24 dev eth0 proto kernel scope link src 192.168.56.102 metric 100
```

➡️ **Minha rede é `192.168.56.0/24`.**
➡️ **Meu IP é `192.168.56.102`.**

Esse é o passo que **não pode ser pulado**: primeiro o contexto, depois a ferramenta.

---

## Parte 4 — Nmap: a primeira varredura

O [Nmap](https://nmap.org/) mapeia a rede: descobre hosts vivos e quais portas estão abertas.

### Os dois comandos usados hoje

```bash
# -sn : ping scan — NÃO varre portas, só descobre quais hosts estão VIVOS
nmap -sn 192.168.56.0/24

# -sV : service/version detection — descobre QUAIS serviços rodam nas portas abertas
nmap -sV 192.168.56.1
```

| Flag | O que faz |
|---|---|
| `-sn` | varredura de descoberta de hosts (sem portas) |
| `-sV` | detecta versão dos serviços |
| `-sS` | SYN scan (stealth, exige root) |
| `-p 22,80,443` | limita a portas específicas |
| `-oN saida.txt` | salva o resultado em arquivo |

---

## 🧪 Missão 03 — Encontrando máquinas

### ❌ O erro que eu cometi

Descobri minha rede corretamente (`192.168.56.0/24`), mas **escaneiei uma rede diferente**:

```bash
nmap -sn 192.168.0.0/24     # ❌ ERRO — rede errada
```

Resultado: **256 falhas** seguidas de `setup_target: failed to determine route to 192.168.0.X`, e no final:

```
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.01 seconds
```

<details>
<summary><strong>📄 Ver a saída completa do erro (256 linhas)</strong></summary>

```
setup_target: failed to determine route to 192.168.0.0
setup_target: failed to determine route to 192.168.0.1
setup_target: failed to determine route to 192.168.0.2
... (uma linha para cada um dos 256 endereços) ...
setup_target: failed to determine route to 192.168.0.255
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.01 seconds
```

</details>

### 🔍 O que aconteceu

| Item | Valor |
|---|---|
| Minha rede real | `192.168.56.0/24` ✅ |
| Rede que eu escaneei | `192.168.0.0/24` ❌ |
| Meu IP | `192.168.56.102` |

**O Kali só consegue "ver" `192.168.56.X`.** Como ele não tem rota para `192.168.0.X`, o Nmap não conseguiu montar nenhum alvo — daí as 256 falhas de rota.

### ✅ A correção

```bash
nmap -sn 192.168.56.0/24     # ✅ a MINHA rede
```

Resultado esperado (laboratório com rede host-only):

```
Nmap scan report for 192.168.56.1
Nmap scan report for 192.168.56.102
Nmap done: 256 IP addresses (2 hosts up) scanned
```

Interpretação:

- `192.168.56.1` → o **gateway** da rede host-only
- `192.168.56.102` → **eu**, o Kali
- **Nenhuma outra máquina** — o laboratório está isolado, e isso é o esperado

### ⚠️ Sobre os "255 IPs"

Eu **não** encontrei 255 máquinas. Eu **tentei** 256 endereços possíveis e **nenhum respondeu**. A diferença é enorme:

- `/24` = 256 endereços **possíveis**
- Hosts **vivos** = só os que responderam (2, no caso)

---

## 🎯 Lição do dia

> **Reconhecimento só funciona com contexto de rede.**

O erro de hoje é um clássico e vale como regra:

| | ❌ Iniciante | ✅ Profissional |
|---|---|---|
| Ordem | escolhe a ferramenta → roda no alvo errado | entende o ambiente → **depois** usa a ferramenta |
| Resultado | 255 erros, zero informação | resposta clara e acionável |

Ferramenta certa no alvo errado = resultado zero. **Contexto antes de comando.**

E aqui está o ponto mais importante: o erro **não foi perda de tempo**. Foi o erro que ensinou, na prática, o que significa "não ter rota" — algo que eu não aprenderia lendo sobre o assunto.

### A fase 1 de um ataque real

```
[✅] Reconhecimento      ← descobrir o território   (Dia 03)
[✅] Descoberta de alvos ← achar hosts vivos        (Dia 03)
[  ] Enumeração          ← detalhar cada serviço    (próximo)
[  ] Exploração          ← usar a vulnerabilidade
[  ] Pós-exploração      ← escalar e persistir
```

Hoje cumpri as **duas primeiras fases**. O trabalho real começa na enumeração.

---

## ✅ O que aprendi no Dia 03

- Como máquinas se comunicam e o que é um endereço IP + máscara `/24`
- O papel das portas e o que "porta aberta" significa para um atacante
- Descobrir minha própria rede com `ip a` e `ip route` **antes** de varrer
- `nmap -sn` para hosts vivos e `nmap -sV` para versão dos serviços
- **Erro real e correção real:** escanear rede sem rota → entender o porquê → corrigir

⬅️ Anterior: [Dia 02 — Permissões, usuários e privilégios](dia-02-permissoes-usuarios-privilegios.md)
