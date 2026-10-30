# 03 — Roteiro de treino

Trilha em 4 fases para o lab (Kali + Metasploitable 2 + Juice Shop). Cada fase termina com um **critério de conclusão** — se o critério não está cumprido, a fase não terminou.

> Regra que organiza tudo: **recon termina com inventário cheio e zero achados confirmados.** Achado só existe depois de testado e evidenciado.

---

## Fase 1 — Fundamentos e higiene de lab

Objetivo: saber **onde estou** antes de saber o que atacar.

```bash
ip -brief a                   # minhas interfaces
ip route                      # minhas rotas
sudo nmap -sn 10.10.10.0/24   # quem está na minha rede
```

Depois, no alvo (`ssh msfadmin@10.10.10.100`):

```bash
ifconfig ; netstat -tulpn ; whoami ; id
```

**Entregável:** um `inventario-fase1.md` com sua interface, seu IP, o IP do alvo e os hosts vivos da rede.

- [ ] Sei explicar a diferença entre rede interna, NAT e bridged
- [ ] Sei reverter o snapshot `limpo` e reverter de volta
- [ ] Reconheço meu IP e o do alvo sem consultar nada

---

## Fase 2 — Reconhecimento e enumeração (Metasploitable)

Objetivo: mapear **superfície de ataque** sem tocar em nada. Nada aqui altera o alvo.

### 2.1 Descoberta de portas

```bash
ALVO=10.10.10.100

sudo nmap -sn 10.10.10.0/24                        # descoberta
sudo nmap -Pn -p- --min-rate 1000 $ALVO            # todas as portas TCP
sudo nmap -sV -sC -p 21,22,23,25,53,80,111,139,445,512,513,514,1099,1524,2049,2121,3306,5432,5900,6000,6667,8009,8180 $ALVO
```

Aprenda a ler a saída: **porta / estado / serviço / versão**. `-sV` é o que transforma "porta 21 aberta" em "vsftpd 2.3.4".

### 2.2 Enumeração por serviço

```bash
whatweb http://$ALVO                 # tecnologias web
smbclient -L //$ALVO -N              # shares SMB sem senha
showmount -e $ALVO                   # NFS exportado
enum4linux -a $ALVO                  # usuários e shares
nmap -p 80,8180 --script http-enum,http-title $ALVO
```

### 2.3 Inventário × hipótese × achado

Três coisas diferentes, nunca misturadas:

| O que é | Definição | Exemplo |
|---|---|---|
| **Inventário** | fato observado, com a evidência do comando | *"porta 21/TCP aberta, `vsftpd 2.3.4`"* |
| **Hipótese de risco** | suspeita testável derivada do fato | *"`vsftpd 2.3.4` na 21 sugere backdoor conhecido, porque essa versão tem CVE documentado. **Não testado**"* |
| **Achado** | hipótese **testada e confirmada**, com evidência | *"backdoor confirmado: comando X retornou shell root (print anexo)"* |

Toda hipótese entra junto do **teste que a confirma ou derruba**. Hipótese não vira achado por parecer óbvia.

**Entregável:** `inventario-fase2.md` com a tabela completa de portas/serviços/versões e as hipóteses marcadas como hipóteses.

- [ ] Tabela de portas completa (TCP e UDP relevante)
- [ ] Versões identificadas dos serviços principais
- [ ] Zero achados confirmados (recon não confirma nada)
- [ ] Cada hipótese tem o teste que a valida escrito ao lado

---

## Fase 3 — Exploração controlada

Objetivo: transformar **uma** hipótese em **um** achado confirmado. Uma por sessão, não dez.

### 3.1 Antes de qualquer coisa

1. Snapshot `limpo` confirmado (reverter agora, para começar do zero).
2. Pasta de evidências criada **no host**: `~/lab/evidencias/<data>-<modulo>/`.
3. Escopo escrito: *"hoje eu vou testar a hipótese X na porta Y"*.

### 3.2 Ordem de tentativa (do mais simples ao mais complexo)

| Alvo | Serviço | O que testar |
|---|---|---|
| Metasploitable | 1524 (ingreslock) | shell root **sem autenticação** — confirma em um comando |
| Metasploitable | 21 (vsftpd 2.3.4) | backdoor da versão |
| Metasploitable | 23 / 512–514 (r-services) | `rlogin`/`rexec` com confiança indevida |
| Metasploitable | 139/445 (Samba) | share com escrita e execução |
| Metasploitable | 3306 (MySQL) | root sem senha |
| Metasploitable | 5900 (VNC) | senha fraca / ausente |
| Metasploitable | 80, 8180 | DVWA, Mutillidae, Tomcat manager |
| Juice Shop | 3000 | OWASP Top 10, com placar |

### 3.3 Depois de cada tentativa

Reverter o snapshot **imediatamente**, mesmo que tenha dado certo. Assim o próximo teste começa de um estado conhecido — e você sabe qual passo quebrou o quê.

**Formato da evidência (sempre os três):**

```
1. Comando exato executado
2. Saída literal (recorte relevante)
3. Uma frase de interpretação: o que isso prova
```

**Entregável:** `evidencias/<data>-<modulo>/` com comando + saída + print por achado.

- [ ] Pelo menos 1 achado confirmado com evidência completa
- [ ] Snapshot revertido depois da exploração
- [ ] Sei explicar **por que** a falha existe (não só que existe)

---

## Fase 4 — Relatório, defesa e forense

Objetivo: virar o ataque em **defesa escrita**. Quem só explora é curioso; quem documenta é profissional.

### 4.1 Estrutura do relatório

```
1. Escopo e autorização        → o que foi testado, com que permissão
2. Metodologia                 → ferramentas e fases (recon → enum → exploração)
3. Achados                     → severidade, impacto, evidência, recomendação
4. Superfície de ataque        → anexo com o inventário de portas/serviços
5. Limitações                  → o que NÃO foi testado
```

Cada achado, obrigatoriamente com: **severidade · impacto no negócio · evidência · recomendação de correção**.

### 4.2 Mesma falha, dois lados

| Falha | Ataque | Defesa correspondente |
|---|---|---|
| root sem senha no MySQL | acesso total ao banco | `mysql_secure_installation`, bind em `127.0.0.1`, usuário sem `GRANT ALL` |
| SMB com escrita anônima | upload de payload | desabilitar null session, mínimo privilégio, SMB signing |
| Juice Shop: SQLi no busca | dump da base | query parametrizada, validação de entrada, WAF como camada extra |

### 4.3 Forense e captura

```bash
# capturar o tráfego da sessão inteira
sudo tcpdump -i lo -w capturas/sessao-$(date +%F).pcap

# no host Windows: coleta de evidências da VM
# (sempre FORA do alvo, que vai ser revertido)
```

Analise no Wireshark: handshake TCP, HTTP em claro, assinatura de ataque.

**Entregável:** `relatorio-<data>.md` completo + `capturas/*.pcap`.

- [ ] Relatório com todos os campos obrigatórios preenchidos
- [ ] Cada achado tem a defesa correspondente
- [ ] Limitações declaradas (honestidade é parte do método)
- [ ] `.pcap` aberto e interpretado

---

## Progresso

| Fase | Tema | Status |
|:---:|---|:---:|
| 1 | Fundamentos e higiene de lab | ⬜ |
| 2 | Recon e enumeração | ⬜ |
| 3 | Exploração controlada | ⬜ |
| 4 | Relatório, defesa e forense | ⬜ |

> Atualize esta tabela conforme avança — ela é o painel do lab.

---

## Regras que não mudam

1. **Autorização**: só se ataca o que é seu ou com autorização escrita. O lab é meu, o alvo é descartável.
2. **Evidência fora do alvo**: o alvo vai ser revertido; o que fica dentro dele desaparece.
3. **Reverter sempre**: antes e depois de cada fase.
4. **Um achado por vez**: exploração controlada com escopo escrito vale mais que dez tentativas aleatórias.
5. **Documentar o erro**: o que deu errado é mais útil que o que deu certo.
