# 🗺️ Roadmap de Cibersegurança

Trilha de estudo seguida neste repositório, organizada por fase. Cada item marcado como ✅ tem um dia documentado em [`../README.md`](../README.md).

![Progresso](https://img.shields.io/badge/progresso-3%20de%2030%20dias-blue)

---

## Fase 1 — Fundamentos (em andamento)

| # | Tópico | Dia | Status |
|:---:|---|:---:|:---:|
| 1 | Terminal Linux: navegação e arquivos | [01](../dia-01-fundamentos-linux.md) | ✅ |
| 2 | Permissões, usuários e privilégios | [02](../dia-02-permissoes-usuarios-privilegios.md) | ✅ |
| 3 | Redes, IP, portas e reconhecimento | [03](../dia-03-redes-reconhecimento.md) | ✅ |
| 4 | Enumeração de serviços com Nmap (`-sS`, `-sV`, `-p-`) | — | ⏳ |
| 5 | Processos, serviços e `systemd` | — | ⏳ |
| 6 | Manipulação de texto: `grep`, `awk`, `sed`, pipes | — | ⏳ |
| 7 | Bash scripting e automação | — | ⏳ |

## Fase 2 — Segurança ofensiva

| # | Tópico | Status |
|:---:|---|:---:|
| 8 | Metodologia de pentest e escopo/autorização | ⏳ |
| 9 | Enumeração de rede com Netcat e SMB | ⏳ |
| 10 | Vulnerability scanning com Nuclei / OpenVAS | ⏳ |
| 11 | Metasploit Framework: exploração básica | ⏳ |
| 12 | Exploração web: OWASP Top 10 | ⏳ |
| 13 | Escalação de privilégio Linux | ⏳ |
| 14 | Escalação de privilégio Windows | ⏳ |
| 15 | Pós-exploração e movimentação lateral | ⏳ |
| 16 | Coleta de evidências e relatório de pentest | ⏳ |

## Fase 3 — Defesa e especialização

| # | Tópico | Status |
|:---:|---|:---:|
| 17 | Hardening de Linux e Windows | ⏳ |
| 18 | Blue Team: detecção e resposta a incidentes | ⏳ |
| 19 | MITRE ATT&CK e mapeamento de ameaças | ⏳ |
| 20 | Análise de logs e SIEM | ⏳ |
| 21 | Forense digital básica | ⏳ |
| 22 | Análise de malware (introdução) | ⏳ |
| 23 | Criptografia aplicada e TLS | ⏳ |
| 24 | Segurança em nuvem (AWS/Azure fundamentals) | ⏳ |
| 25 | Active Directory: ataque e defesa | ⏳ |

## Fase 4 — Consolidação

| # | Tópico | Status |
|:---:|---|:---:|
| 26 | Laboratório caseiro com múltiplas VMs | ⏳ |
| 27 | Máquinas CTF (TryHackMe / Hack The Box) | ⏳ |
| 28 | Projeto próprio documentado de ponta a ponta | ⏳ |
| 29 | Certificação: eJPT / CompTIA Security+ | ⏳ |
| 30 | Portfólio público e relatórios de pentest | ⏳ |

---

## 🧰 Ferramentas a dominar

| Categoria | Ferramentas |
|---|---|
| Rede | `nmap`, `netcat`, `Wireshark`, `tcpdump` |
| Web | Burp Suite, `ffuf`, `gobuster`, `nikto` |
| Exploração | Metasploit, `searchsploit` |
| Pós-exploração | `linpeas`, `winpeas`, Mimikatz, Impacket |
| Enumeração | `enum4linux`, `smbclient`, `ldapsearch` |
| Defesa | `fail2ban`, AppArmor, `auditd`, Splunk |

## 📎 Plataformas de prática

- [TryHackMe](https://tryhackme.com/) — trilhas guiadas, ideal para começar
- [Hack The Box](https://www.hackthebox.com/) — máquinas desafiadoras
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) — web gratuita e excelente
- [OverTheWire](https://overthewire.org/wargames/) — fundamentos de Linux via jogos
- [VulnHub](https://www.vulnhub.com/) — VMs vulneráveis para baixar

---

## ⚖️ Ética

Praticar segurança ofensiva exige **autorização explícita**. Todo estudo deste roadmap é feito em:

- laboratório próprio e isolado
- plataformas que existem para isso (TryHackMe, HTB)
- ambientes com escopo e permissão por escrito

Sem autorização, o mesmo comando que ensina é **crime**.
