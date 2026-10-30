# 02 — OWASP Juice Shop em Docker

O **Juice Shop** é a aplicação web deliberadamente vulnerável do OWASP: mais de 100 desafios reais, com placar de pontos e níveis de dificuldade. É o alvo ideal para treinar falhas de **aplicação**, que o Metasploitable não cobre bem.

> Diferença de foco: o Metasploitable treina **serviço/infra** (versão antiga, senha fraca, shell exposto). O Juice Shop treina **aplicação** (injeção, XSS, controle de acesso, lógica de negócio).

---

## 1. Subir em 30 segundos

```bash
# 1. baixar a imagem (~250 MB)
docker pull bkimminich/juice-shop

# 2. subir com bind APENAS no localhost
docker run -d --name juice-shop \
  -p 127.0.0.1:3000:3000 \
  bkimminich/juice-shop

# 3. conferir
docker ps
curl -sI http://127.0.0.1:3000 | head -3
```

Abrir: **http://127.0.0.1:3000**

### Por que `127.0.0.1:3000:3000` e não `3000:3000`

`-p 3000:3000` publica a porta em **todas** as interfaces do host — inclusive na rede Wi-Fi da casa. O Juice Shop é vulnerável de propósito; expô-lo à rede doméstica é entregar um alvo aberto para qualquer dispositivo da rede.

`-p 127.0.0.1:3000:3000` faz o Docker escutar **só no loopback**. Nada de fora da máquina alcança a aplicação.

O `ufw` não resolve isso: o Docker escreve as regras de porta direto no `iptables` e passa por cima do firewall. **O bind em `127.0.0.1` é o que realmente protege.**

---

## 2. Versão com `docker-compose`

Arquivo [`docker-compose.yml`](docker-compose.yml) deste repositório:

```bash
docker compose up -d          # sobe Juice Shop + DVWA
docker compose ps
docker compose logs -f juice-shop
docker compose down           # derruba tudo (mantém nada)
```

Alvos disponíveis:

| Alvo | URL | Para treinar |
|---|---|---|
| Juice Shop | http://127.0.0.1:3000 | OWASP Top 10, com placar automático |
| DVWA | http://127.0.0.1:8081 | SQLi, XSS, upload, LFI em 3 níveis de dificuldade |

---

## 3. Resetar o progresso

O estado fica **dentro do container**. Recriar o container = aplicação limpa, desafios zerados:

```bash
docker rm -f juice-shop
docker run -d --name juice-shop -p 127.0.0.1:3000:3000 bkimminich/juice-shop
```

Equivalente no compose:

```bash
docker compose down --volumes
docker compose up -d
```

> Se você quiser **manter** o progresso entre sessões, ignore esta parte — o container parado com `docker stop` preserva o estado.

---

## 4. Como jogar

1. Abra a aplicação e navegue como usuário comum — antes de qualquer ferramenta.
2. Crie uma conta (`Register`). Muito desafio depende de estar logado.
3. O placar fica em **`/#/score-board`** (em versões recentes o link fica no menu lateral, após login).
4. Cada desafio tem **nome, categoria e dificuldade**, mas não a solução. A resolução é o trabalho.
5. Use o **DevTools do navegador** (F12 → Network / Console / Application) o tempo todo. A metade das falhas está no que o frontend confia no cliente.

### Ferramentas por categoria

| Categoria | Ferramentas |
|---|---|
| Injeção (SQL/NoSQL/OS) | Burp Suite, `sqlmap`, DevTools |
| XSS | Burp, DevTools, `dalfox` |
| Controle de acesso / IDOR | Burp (Repeater + Intruder), manipulação de JWT |
| Autenticação / JWT | `jwt.io`, `hashcat`, Burp |
| Lógica de negócio | caneta, papel e paciência (não é ferramenta, é raciocínio) |
| Recon da app | `ffuf`, `gobuster`, `whatweb` |

### Fluxo de trabalho recomendado

```
requisição capturada no Burp
        │
        ├─▶ o que o servidor confia que vem do cliente?
        ├─▶ o que acontece se eu mudar 1 byte? (id, role, price, token)
        └─▶ a resposta mudou de forma? então há uma hipótese a testar
```

Registre **a requisição original e a alterada** lado a lado. É isso que vira evidência no relatório.

---

## 5. Blue Team no Juice Shop

Nem só de ataque vive o lab. O Juice Shop gera tráfego com assinatura clara de ataque — ótimo para treinar detecção:

```bash
# capturar o tráfego do lab enquanto ataca (num terminal separado)
docker run --rm --net=host \
  -v "$PWD/lab/capturas:/capturas" \
  nicolaka/netshoot \
  tcpdump -i lo -w /capturas/juice-$(date +%Y%m%d-%H%M).pcap 'port 3000'
```

Depois abra o `.pcap` no Wireshark e responda:

- Quantas requisições por segundo um ataque de força bruta gera? (compare com navegação normal)
- O payload de SQLi aparece em claro no `GET /rest/products/search?q=`?
- Qual regra Suricata/Snort pegaria `' OR 1=1--` num parâmetro?

Isso vira a regra `SE <padrão> ENTÃO <alerta> PORQUE <impacto>` do roteiro de Blue Team.

---

## 6. Troubleshooting

| Sintoma | Causa | Correção |
|---|---|---|
| `port is already allocated` | porta 3000 ocupada | `docker ps` e derrube o container antigo, ou use `-p 127.0.0.1:3001:3000` |
| Página não abre | container morreu | `docker logs juice-shop` |
| `permission denied` em `/var/run/docker.sock` | usuário fora do grupo `docker` (Linux) | `sudo usermod -aG docker $USER` e relogar |
| Docker Desktop não sobe | Hyper-V/WSL2 conflitando com VirtualBox | não rodar VM e Docker ao mesmo tempo (8 GB) |
| Desafio não "conta" | estado do navegador/cache | recarregar com *hard reload*, conferir se está logado |

---

## 7. Checklist

- [ ] Container no ar e acessível só em `127.0.0.1:3000`
- [ ] Conta criada e score board aberto
- [ ] Burp configurado como proxy (ou DevTools dominado)
- [ ] Pelo menos 3 desafios resolvidos **com a requisição documentada**
- [ ] Captura `.pcap` de um ataque salva na pasta de evidências
