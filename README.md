# 🚀 Primeiros Passos — Cibersegurança & Ciência de Dados

Portfólio de estudos práticos em **duas trilhas**, documentado dia a dia durante minha entrada nas áreas de **Cibersegurança** e **Ciência de Dados**.

O foco aqui não é decorar comandos ou sintaxe — é desenvolver a pergunta que sustenta tudo:
**"como eu descubro o que eu não sei?"**

![Status](https://img.shields.io/badge/status-em%20andamento-yellow)
![Trilhas](https://img.shields.io/badge/trilhas-2-blue)
![Cyber](https://img.shields.io/badge/cyber-03%20dias-red)
![Dados](https://img.shields.io/badge/dados-01%20dia-blueviolet)
![Lab](https://img.shields.io/badge/lab-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)

---

## 🎯 Objetivo

Construir uma base sólida e rastreável nas duas áreas, documentando cada avanço com **código e comandos reais executados**, **erros cometidos** e **correções aplicadas** — porque é no erro que o aprendizado acontece.

Cada arquivo deste repositório é uma sessão de estudo real, não um tutorial copiado.

## 🧭 Metodologia

Cada dia segue sempre a mesma estrutura:

1. **Conceito** — entender *o que* é e *por que* existe
2. **Prática** — rodar os comandos/código no ambiente
3. **Missão** — desafio obrigatório para fixar
4. **Erro & correção** — o que deu errado e por quê
5. **Lição** — a mentalidade profissional por trás da técnica

> **Regra de ouro:** se não sei usar um comando, existe `man <comando>` ou `<comando> --help`.

---

## 🛡️ Trilha 1 — Cibersegurança

Linux, redes e segurança ofensiva (Red Team). Roadmap completo em **[`recursos/roadmap-cybersecurity.md`](recursos/roadmap-cybersecurity.md)**.

| Dia | Tema | Competências trabalhadas | Status |
|:---:|---|---|:---:|
| [**01**](dia-01-fundamentos-linux.md) | Fundamentos do terminal Linux | `pwd` `ls` `cd` `mkdir` `touch` `man` | ✅ |
| [**02**](dia-02-permissoes-usuarios-privilegios.md) | Permissões, usuários e privilégios | `ls -l` `chmod` `whoami` `sudo` | ✅ |
| [**03**](dia-03-redes-reconhecimento.md) | Redes e reconhecimento de alvos | `ip a` `ip route` `nmap` | ✅ |

## 📊 Trilha 2 — Ciência de Dados

Python, SQL, análise de dados e machine learning. Roadmap completo em **[`ciencia-de-dados/roadmap-ciencia-de-dados.md`](ciencia-de-dados/roadmap-ciencia-de-dados.md)**.

| Dia | Tema | Competências trabalhadas | Status |
|:---:|---|---|:---:|
| [**01**](ciencia-de-dados/dia-01-fundamentos-python-dados.md) | Python para dados: tipos e estruturas | `int` `float` `str` `list` `dict` `sum` `len` | ✅ |

---

## 🧪 Laboratório de treino

O que sustenta a trilha de cibersegurança: um lab **local e isolado**, montado na minha própria máquina, com alvos que eu mesmo subi. Nenhum alvo real, nenhum sistema de terceiros.

| Alvo | Tecnologia | Serve para treinar |
|---|---|---|
| **Kali Linux** | VM VirtualBox | máquina atacante |
| **Metasploitable 2** | VM VirtualBox | recon, enumeração, exploração de serviços e pós-exploração |
| **OWASP Juice Shop** | container Docker | falhas de aplicação (OWASP Top 10) |

Rede das VMs: **interna isolada**, sem bridge para a rede doméstica. Alvos web com bind **apenas em `127.0.0.1`**.

📄 **[Documentação completa do lab → `lab/`](lab/README.md)**

| Arquivo | Conteúdo |
|---|---|
| [Arquitetura e regras](lab/README.md) | diagrama, modos de rede, regras de isolamento, checklist de sessão |
| [Instalação no VirtualBox](lab/01-instalacao-virtualbox.md) | Kali + Metasploitable 2, rede interna, snapshot `limpo`, troubleshooting |
| [Juice Shop no Docker](lab/02-juice-shop-docker.md) | subir, resetar, jogar, capturar tráfego, Blue Team |
| [Roteiro de treino](lab/03-roteiro-treino.md) | 4 fases com critério de conclusão: fundamentos → recon → exploração → relatório |

Princípio do lab: **snapshot revertido antes e depois de cada sessão**, e **evidência sempre no host** — o alvo é descartável, o que ele prova não é.

---

## 🧰 Ambiente

| Item | Detalhe |
|---|---|
| Cibersegurança | Kali Linux + Metasploitable 2 (VirtualBox) e Juice Shop (Docker), em laboratório isolado / rede interna |
| Ciência de Dados | Python 3, Jupyter / Google Colab, VS Code |

Na trilha de cibersegurança, todo teste é feito em máquina própria e isolada — nenhum alvo é real.

## 🗺️ Roadmaps

- 🛡️ [Roadmap de Cibersegurança](recursos/roadmap-cybersecurity.md) — 30 dias em 4 fases
- 📊 [Roadmap de Ciência de Dados](ciencia-de-dados/roadmap-ciencia-de-dados.md) — 30 dias em 4 fases

## ⚖️ Aviso legal e ética

Este repositório tem **finalidade exclusivamente educacional**.

- Na cibersegurança, todo conteúdo é estudado em ambiente de laboratório próprio e isolado. Práticas ofensivas aplicadas a sistemas de terceiros **sem autorização por escrito** são crime, e este material não incentiva nem apoia isso.
- Na ciência de dados, só uso **dados que tenho direito de usar** (públicos, próprios ou com consentimento), respeitando a **LGPD** no tratamento de dados pessoais.

Segurança ofensiva só faz sentido com ética: **entender o ataque para construir a defesa**.

## 📎 Recursos de estudo

**Cibersegurança**
- [`man` pages](https://man7.org/linux/man-pages/) — a fonte de verdade do Linux
- [Nmap Reference Guide](https://nmap.org/book/man.html) — documentação oficial
- [OWASP](https://owasp.org/) — segurança de aplicações
- [MITRE ATT&CK](https://attack.mitre.org/) — táticas e técnicas de atacantes reais
- [TryHackMe](https://tryhackme.com/) / [Hack The Box](https://www.hackthebox.com/) — laboratórios práticos

**Ciência de Dados**
- [Kaggle](https://www.kaggle.com/) — datasets e competições
- [Google Colab](https://colab.research.google.com/) — notebooks na nuvem
- [SQLBolt](https://sqlbolt.com/) — SQL interativo do zero
- [pandas docs](https://pandas.pydata.org/docs/) — documentação oficial

---

<sub>Repositório de estudos em evolução contínua. Cada commit é um dia de aprendizado.</sub>
