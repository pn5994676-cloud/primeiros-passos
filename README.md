# 🛡️ Primeiros Passos em Cibersegurança

Diário de estudos práticos de **Linux**, **redes** e **segurança ofensiva (Red Team)**, documentado dia a dia durante minha entrada na área de cibersegurança.

O foco aqui não é decorar comandos — é desenvolver a pergunta que sustenta tudo:
**"como eu descubro o que eu não sei?"**

![Status](https://img.shields.io/badge/status-em%20andamento-yellow)
![Dias](https://img.shields.io/badge/dias-03-blue)
![Foco](https://img.shields.io/badge/foco-Red%20Team-red)
![Lab](https://img.shields.io/badge/lab-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)

---

## 🎯 Objetivo

Construir uma base sólida e rastreável em segurança ofensiva, documentando cada avanço com **comandos reais executados**, **erros cometidos** e **correções aplicadas** — porque é no erro que o aprendizado acontece.

Cada arquivo deste repositório é uma sessão de estudo real, não um tutorial copiado.

## 🧭 Metodologia

Cada dia segue sempre a mesma estrutura:

1. **Conceito** — entender *o que* é e *por que* existe
2. **Prática** — rodar os comandos no laboratório
3. **Missão** — desafio obrigatório para fixar
4. **Erro & correção** — o que deu errado e por quê
5. **Lição** — a mentalidade profissional por trás da técnica

> **Regra de ouro:** se não sei usar um comando, existe `man <comando>` ou `<comando> --help`.

## 📚 Índice dos dias

| Dia | Tema | Competências trabalhadas | Status |
|:---:|---|---|:---:|
| [**01**](dia-01-fundamentos-linux.md) | Fundamentos do terminal Linux | `pwd` `ls` `cd` `mkdir` `touch` `man` | ✅ |
| [**02**](dia-02-permissoes-usuarios-privilegios.md) | Permissões, usuários e privilégios | `ls -l` `chmod` `whoami` `sudo` | ✅ |
| [**03**](dia-03-redes-reconhecimento.md) | Redes e reconhecimento de alvos | `ip a` `ip route` `nmap` | ✅ |

## 🧰 Ambiente de laboratório

| Item | Detalhe |
|---|---|
| Distribuição | Kali Linux |
| Virtualização | Laboratório isolado / rede *host-only* |
| Rede do lab | `192.168.56.0/24` |
| Shell | `bash` |

Todo teste é feito em máquina própria e isolada. Nenhum alvo é real.

## 🗺️ Roadmap

A trilha completa de estudo, com o que já foi coberto e o que vem a seguir, está em
**[`recursos/roadmap-cybersecurity.md`](recursos/roadmap-cybersecurity.md)**.

## ⚖️ Aviso legal e ética

Este repositório tem **finalidade exclusivamente educacional**.

Todo conteúdo é estudado em ambiente de laboratório próprio e isolado, com ferramentas legítimas e consentidas. Práticas de segurança ofensiva aplicadas a sistemas de terceiros **sem autorização por escrito** são crime, e este material não incentiva nem apoia isso.

Segurança ofensiva só faz sentido com ética: **entender o ataque para construir a defesa**.

## 📎 Recursos de estudo

- [`man` pages](https://man7.org/linux/man-pages/) — a fonte de verdade do Linux
- [Nmap Reference Guide](https://nmap.org/book/man.html) — documentação oficial
- [OWASP](https://owasp.org/) — segurança de aplicações
- [MITRE ATT&CK](https://attack.mitre.org/) — táticas e técnicas de atacantes reais
- [TryHackMe](https://tryhackme.com/) / [Hack The Box](https://www.hackthebox.com/) — laboratórios práticos

---

<sub>Repositório de estudos em evolução contínua. Cada commit é um dia de aprendizado.</sub>
