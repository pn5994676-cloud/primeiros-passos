# 🧪 Laboratório de Cibersegurança — Kali + Metasploitable 2 + Juice Shop

Laboratório **local, isolado e próprio** para praticar segurança ofensiva e defensiva do zero.
Tudo aqui roda na minha máquina, em rede fechada, com alvos que eu mesmo subi — nenhum alvo real, nenhum sistema de terceiros.

> **Por que um lab:** não se aprende a atacar (nem a defender) lendo. Você precisa de um lugar onde **errar não custa nada** e onde **reverter ao estado limpo** leva 10 segundos. Esse lugar é este lab.

---

## 🗺️ Arquitetura

```
┌──────────────────────────────────────────────────────────────┐
│  HOST — Windows (8 GB RAM)                                   │
│                                                              │
│  ┌───────────────────────────┐   ┌────────────────────────┐  │
│  │ VirtualBox                │   │ Docker Desktop         │  │
│  │                           │   │                        │  │
│  │  ┌─────────────────────┐  │   │  ┌──────────────────┐  │  │
│  │  │ Kali Linux          │  │   │  │ Juice Shop       │  │  │
│  │  │ (atacante)          │  │   │  │ 127.0.0.1:3000   │  │  │
│  │  └─────────┬───────────┘  │   │  └──────────────────┘  │  │
│  │            │              │   │                        │  │
│  │   rede interna isolada    │   └────────────────────────┘  │
│  │            │              │                               │
│  │  ┌─────────┴───────────┐  │                               │
│  │  │ Metasploitable 2    │  │                               │
│  │  │ (alvo vulnerável)   │  │                               │
│  │  └─────────────────────┘  │                               │
│  └───────────────────────────┘                               │
└──────────────────────────────────────────────────────────────┘
             ✗ sem bridge para a rede doméstica
```

Duas frentes de treino, com objetivos diferentes:

| Frente | Alvo | Tecnologia | Treina |
|---|---|---|---|
| **Rede / infra** | Metasploitable 2 | VM VirtualBox | recon, enumeração, exploração de serviços, pós-exploração |
| **Aplicação web** | OWASP Juice Shop | container Docker | falhas de aplicação (OWASP Top 10), análise de request/response |

---

## 🧰 Ambiente

| Item | Configuração |
|---|---|
| Host | Windows, **8 GB de RAM** |
| Hypervisor | **VirtualBox** |
| Atacante | **Kali Linux** (VM) |
| Alvo de rede | **Metasploitable 2** (VM) |
| Alvo web | **OWASP Juice Shop** (`bkimminich/juice-shop`, Docker) |
| Rede das VMs | **rede interna isolada** do VirtualBox — sem bridge com a rede doméstica |
| Rede do Juice Shop | bind em **`127.0.0.1`** apenas — não escuta na rede |
| Foco | Red Team, Blue Team, redes, forense e fundamentos |

### Por que rede interna e não NAT/bridged

O Metasploitable 2 é **deliberadamente vulnerável**. Ele não pode ter rota para a internet nem para a rede da casa, senão vira um alvo exposto de verdade (e um possível vetor para os outros dispositivos da rede).

| Modo de rede | Pode? | Por quê |
|---|:---:|---|
| **Internal Network** (rede interna nomeada) | ✅ | só as VMs daquele nome se veem; sem saída para internet nem para o host |
| **NAT Network** nomeada | ⚠️ | isola do host, mas dá internet — usar só na VM atacante para baixar ferramentas |
| **NAT** (padrão) | ⚠️ | a VM fica atrás do NAT do VirtualBox; aceitável só para o Kali atualizar |
| **Bridged** | ❌ | joga a VM na rede doméstica. **Jamais** para o Metasploitable |
| **Host-only** | ⚠️ | isola bem, mas o host Windows passa a ter interface na mesma rede do alvo |

Regra prática, e a que uso:

1. **Adaptador 1** de todas as VMs → *Internal Network* `lab` (ex.: `10.10.10.0/24`).
2. **Adaptador 2** só no Kali → *NAT*, ligado **apenas** quando preciso instalar pacote. Depois, desligo.
3. O Metasploitable **nunca** tem adaptador com NAT.

---

## ⚠️ Regras do lab (não negociáveis)

1. **Só se ataca o que é seu.** Estes alvos são VMs minhas, na minha máquina, em rede fechada. Aplicar qualquer técnica deste repositório em sistema de terceiros sem autorização escrita é crime (Lei 12.737/2012, art. 154-A do Código Penal).
2. **O alvo é descartável — a evidência não é.** Screenshots, saídas de comando e `.pcap` vão para a **máquina host**, nunca para dentro do alvo (que vai ser revertido).
3. **Reverter o snapshot antes e depois de cada sessão.** Alvo sempre começa limpo.
4. **Uma VM por vez.** Com 8 GB de RAM, rodar Kali + Metasploitable + stack Docker inteira ao mesmo tempo faz o host começar a usar swap e a sessão vira luta contra o travamento.
5. **Sem `--privileged`** em container e sem montar o socket do Docker dentro de container. O isolamento real vem do bind em `127.0.0.1`.

---

## 📚 Conteúdo

| Arquivo | O que tem |
|---|---|
| **[01 — Instalação no VirtualBox](01-instalacao-virtualbox.md)** | subir o Kali e o Metasploitable 2, configurar a rede interna, criar o snapshot `limpo`, descobrir o IP do alvo |
| **[02 — Juice Shop no Docker](02-juice-shop-docker.md)** | subir o Juice Shop com bind local, versão com compose, reset do progresso, panorama das falhas |
| **[03 — Roteiro de treino](03-roteiro-treino.md)** | trilha em 4 fases com checkboxes: fundamentos → recon → exploração → relatório |
| **[docker-compose.yml](docker-compose.yml)** | stack opcional de alvos web (Juice Shop + DVWA) e perfis de captura |

---

## ✅ Checklist rápido de sessão

Antes de abrir o terminal:

- [ ] Metasploitable revertido para o snapshot `limpo`
- [ ] Kali e alvo na mesma rede interna
- [ ] IP do alvo reconferido (`nmap -sn` ou `ip a` no alvo) — **ele muda a cada boot**
- [ ] Pasta de evidências do dia criada no host
- [ ] Escopo da sessão escrito: *o que eu vou tentar hoje*

Depois:

- [ ] Snapshot revertido de novo
- [ ] Inventário preenchido (fato + evidência)
- [ ] Se houve achado: virou relatório com severidade, impacto e recomendação

---

## ⚖️ Aviso

Material **exclusivamente educacional**, executado em laboratório próprio e isolado. Nada aqui é instrução para atacar sistemas de terceiros.
