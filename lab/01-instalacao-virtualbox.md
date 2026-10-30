# 01 — Montando o lab no VirtualBox

Do zero até a primeira sessão de recon funcionando: Kali como atacante, Metasploitable 2 como alvo, rede interna isolada e snapshot de restauração.

> Tempo estimado: 40–60 min. Disco: ~10 GB. RAM: 8 GB no host.

---

## 1. Pré-requisitos

| Item | Versão / tamanho | Onde |
|---|---|---|
| VirtualBox | 7.x | virtualbox.org |
| Kali Linux (imagem de VM) | ~4 GB | kali.org/get-kali → *Virtual Machines* |
| Metasploitable 2 | ~865 MB (`.zip` → `.vmdk`) | SourceForge, projeto `metasploitable` |
| Espaço em disco | ≥ 15 GB livres | — |

No Windows, mantenha o **Hyper-V desligado** se for usar VirtualBox (conflito de virtualização). Verifique com `systeminfo` → "Virtualização baseada em Hyper-V: Um hipervisor foi detectado". Se aparecer isso, desative o Hyper-V em *Recursos do Windows* e reinicie.

---

## 2. Kali Linux (VM atacante)

### 2.1 Importar

1. Baixe a imagem de VM do Kali (`.7z` com `.vbox` + `.vdi`).
2. Descompacte e dê duplo clique no `.vbox` — o VirtualBox registra a VM sozinho.
3. **Configurações → Sistema → Placa-mãe:** RAM **2048 MB** (4096 se o host tiver folga), 2 CPUs.
4. **Configurações → Vídeo:** 128 MB, controlador VMSVGA.

### 2.2 Rede

**Configurações → Rede:**

| Adaptador | Modo | Nome |
|---|---|---|
| 1 | **Internal Network** | `lab` |
| 2 | **NAT** | (padrão) — desligue quando não estiver instalando pacotes |

> O adaptador NAT é o "cabo de internet" do Kali. Ele **não** dá ao Metasploitable nenhuma rota — o alvo continua fora.

### 2.3 Primeiro boot

Credenciais padrão da imagem do Kali: `kali` / `kali`.

```bash
# trocar a senha imediatamente
passwd

# confirmar as interfaces e o IP na rede do lab
ip -brief a
ip route
```

Esperado: uma interface `eth0` com IP na faixa do lab (ex.: `10.10.10.20/24`) e outra `eth1` com IP `10.0.2.x` (NAT).

Se as interfaces estiverem trocadas, confira qual é qual e ajuste a ordem dos adaptadores.

### 2.4 Ver se o laboratório está pronto

```bash
nmcli device status          # interfaces gerenciadas
sudo systemctl status ssh    # acesso remoto (opcional, útil para copiar arquivos)
sudo apt update              # só com o adaptador NAT ligado
```

---

## 3. Metasploitable 2 (VM alvo)

### 3.1 Criar a VM a partir do disco

O Metasploitable 2 é distribuído como `.vmdk` avulso — **não** é um `.ova`. Caminho mais confiável:

1. Baixe e descompacte `Metasploitable2-Linux.zip` → arquivo `Metasploitable.vmdk`.
2. No VirtualBox: **Novo**.
   - Nome: `Metasploitable2`
   - Tipo: **Linux**, Versão: **Ubuntu (32-bit)** — o sistema é antigo e 32 bits
   - RAM: **512 MB** (é mais que suficiente)
   - Disco: **Usar um arquivo de disco rígido virtual existente** → selecione o `Metasploitable.vmdk`
3. **Configurações → Rede → Adaptador 1:** *Internal Network* `lab`. **Nenhum outro adaptador.**

> Confira sempre: se aparecer "Bridged" ou "NAT" no adaptador do Metasploitable, **pare e corrija**. É a única coisa neste lab que pode virar um problema de verdade.

### 3.2 Primeiro boot e IP

Login padrão: **`msfadmin` / `msfadmin`**.

```bash
# dentro do Metasploitable
ifconfig
```

Anote o IP (algo como `10.10.10.100`). **Ele muda a cada reinício**, porque vem por DHCP — reconfira sempre antes de cada sessão.

Alternativa mais rápida, do lado do Kali:

```bash
sudo nmap -sn 10.10.10.0/24
```

O alvo é o host que responde com uma porrada de portas abertas (22, 23, 80, 139, 445, 3306, 5432, 5900, 8180…).

### 3.3 Snapshot `limpo` — o passo mais importante

Sem snapshot, o Metasploitable vira lixo em duas sessões. Com snapshot, ele volta ao estado original em segundos.

Com a VM **desligada** (ou em estado consistente):

1. Selecione a VM → **Snapshots** → **Tirar snapshot**.
2. Nome: **`limpo`**. Descrição: *"estado pós-instalação, sem nenhuma alteração de exploração"*.

Ciclo de uso:

```
snapshot limpo  ──▶  treinar  ──▶  reverter para limpo  ──▶  treinar de novo
        ▲                                      │
        └──────────────────────────────────────┘
```

Reverter **antes e depois** de cada fase de exploração — não só no fim. Assim, se algo quebrar, você sabe exatamente qual passo quebrou.

O snapshot **não se consome**: um único `limpo` serve para sempre. Só o apaga quem exclui o snapshot, apaga o disco, recria/converte a VM ou sofre corrupção.

---

## 4. Teste de integração

No Kali, com as duas VMs ligadas:

```bash
ALVO=10.10.10.100          # troque pelo IP real que você anotou

ping -c 2 $ALVO            # 1. o alvo responde na rede interna?
sudo nmap -Pn -p 21,22,23,80,139,445,3306,5432,5900,8180 $ALVO   # 2. portas
sudo nmap -sn 10.10.10.0/24                                      # 3. quem mais está lá
```

Resultado esperado no passo 2: quase todas as portas **abertas**. Isso não é um erro de configuração — é o propósito da máquina.

Próximo passo: identificar os serviços por trás das portas → **[03 — Roteiro de treino](03-roteiro-treino.md)**

---

## 5. Erros comuns

| Sintoma | Causa | Correção |
|---|---|---|
| VM não inicia / erro de VT-x | Hyper-V ativo no Windows | desativar Hyper-V e reiniciar |
| Kali não enxerga o alvo | adaptadores em redes diferentes | os dois no mesmo nome de *Internal Network* |
| Metasploitable sem IP | DHCP do VirtualBox não entrega na rede interna | subir o servidor DHCP do VirtualBox ou configurar IP fixo: `sudo ifconfig eth0 10.10.10.100 netmask 255.255.255.0 up` |
| IP do alvo "sumiu" | mudou por DHCP após reiniciar | reconferir com `nmap -sn` ou `ifconfig` |
| Tela do Metasploitable em 640x480 sem login visível | resolução mínima do console | login via SSH do Kali: `ssh msfadmin@10.10.10.100` (senha `msfadmin`) |
| Kali sem internet | adaptador NAT desligado | ligar o adaptador 2 temporariamente |
| Host travando | duas VMs + Docker juntos | uma VM por vez; fechar o Docker Desktop |

---

## 6. RAM: quanto cada peça come

| Componente | RAM típica |
|---|---|
| Windows + navegador | ~3 GB |
| Kali em uso (nmap, Burp) | ~2 GB |
| Metasploitable 2 | ~0,5 GB |
| Juice Shop (container) | ~0,3 GB |
| DVWA (container) | ~0,4 GB |

Com 8 GB, o cenário confortável é **Kali + Metasploitable** (≈ 5,5 GB) **ou** **Kali + alguns containers** — não os dois mundos ao mesmo tempo.
