# DDM — Datatoms Deploy Manager

**Implante e opere suas aplicações nos seus próprios servidores**, com um painel web, HTTPS automático, bancos de
dados, backups, firewall e um Mapa visual da sua infraestrutura. **Grátis para instalar e usar.**

🌐 Site e documentação: **https://ddm.datatoms.com** · 📦 Downloads: [Releases](https://github.com/brenosandro/ddm-releases/releases)

**Versão atual: [1.0.9](https://github.com/brenosandro/ddm-releases/releases/latest).** Já tem o DDM instalado?
Atualize com `sudo ddm upgrade --version 1.0.9`. A atualização confere a assinatura e volta sozinha se algo falhar.

Novidades recentes:

- **Aplicativos publicados por túnel da Cloudflare:** sem abrir portas, ao lado de outro painel (como o Coolify) e
  sem mexer nele.
- **Configurações reorganizadas** e telas que usam a largura toda.
- **IA conectada pelo MCP publica um app de ponta a ponta.** Criar o túnel e trocar o domínio passam pela sua
  aprovação.

Veja o histórico completo em [Releases](https://github.com/brenosandro/ddm-releases/releases).

---

## 1. Antes de começar

Você precisa de um servidor Linux (VPS, máquina na nuvem ou servidor em casa). No Windows, veja a [seção 2b](#2b-no-windows).

| Item | Requisito |
|---|---|
| Sistema | Ubuntu 22.04, 24.04, 26.04 ou Debian 12 |
| Arquitetura | amd64 (x86-64) ou arm64 |
| CPU / memória | 2 vCPU e 2 GB de RAM (4 GB recomendados) |
| Disco | 20 GB livres |
| Acesso | usuário com `sudo` |
| Domínio | um nome como `painel.suaempresa.com` apontando para o servidor (registro A) **ou** um túnel da Cloudflare |

> O instalador instala o Docker sozinho se ele ainda não existir.

## 2. Instale com um comando

Conecte no servidor por SSH e rode:

```sh
curl -fsSL https://ddm.datatoms.com/install | sudo bash
```

O que acontece:

1. Baixa a versão mais recente daqui deste repositório.
2. **Confere a assinatura digital** (minisign) e o SHA-256 de cada arquivo **antes de executar qualquer coisa**.
3. Instala os programas `ddm` e `ddm-agent`.
4. Roda o `ddm install`, que pergunta:
   - o **domínio do painel** (ex.: `painel.suaempresa.com`);
   - o **seu e-mail** (você será o dono da instalação).
5. Sobe o painel com HTTPS (Let's Encrypt) e mostra o **link de primeiro acesso**.

Leva poucos minutos. No fim aparece algo assim:

```
DDM 1.0.9 instalado em painel.suaempresa.com.
Primeiro acesso do dono — abra no navegador (vale UMA vez, por 1 hora):
  https://painel.suaempresa.com/setup?token=…
```

## 2b. No Windows

Abra o **PowerShell como administrador** (botão direito no menu Iniciar → **Terminal (Administrador)**) e rode:

```powershell
irm https://ddm.datatoms.com/install.ps1 | iex
```

O instalador pergunta o papel deste computador:

- **1) Painel neste PC** — Windows 10 22H2 ou 11. Pede o seu e-mail (você será o dono) e sobe o painel em
  **https://localhost:8443**, com o link de primeiro acesso no fim.
- **2) Servidor Windows gerenciado** — Windows Server 2022 ou 2025 ligado a um painel que já existe. Pede o endereço
  do painel e o token de **Servidores → Adicionar** (digitado sem aparecer na tela) e instala o agente como serviço.

Antes de baixar qualquer coisa ele confere os pré-requisitos e diz o que instalar quando falta algo:

| Falta | Como instalar |
|---|---|
| PowerShell 7.4 (painel) | `winget install --id Microsoft.PowerShell --source winget` |
| WSL2 (painel) | `wsl --install` como administrador e reiniciar |
| Docker Desktop (painel) | `winget install --id Docker.DockerDesktop`, abrir e deixar em contêineres Linux (WSL2) |

Como no Linux, ele **confere a assinatura digital e o SHA-256 de cada arquivo antes de executar qualquer coisa** e
para com "Nada foi instalado" se algo não bater. Para só conferir: `$env:DDM_DRY_RUN = '1'` antes do comando.
Guia completo: **https://ddm.datatoms.com/docs/pt-br/comecar/instalar/#no-windows**

## 3. Primeiro acesso

1. Abra o **link de primeiro acesso** no navegador (não use a tela de login nessa primeira vez).
2. Preencha o nome da organização, o seu nome e o e-mail que você informou.
3. Clique em **Configurar com passkey** e confirme com a biometria, o PIN do computador ou o celular.
4. Pronto: você está no painel. **Não existe senha** — nas próximas vezes use **Entrar com passkey ou chave**.

> O link venceu? Rode o mesmo `sudo ddm install …` de novo: ele gera outro e não apaga nada.

Depois, se quiser, configure em **Configurações → Instância**: login com Google (com o seu próprio cliente
OAuth), e-mail (SMTP) para convites e as políticas de quem pode entrar.

## 4. Primeiros passos no painel

1. **Servidores → Adicionar**: gere o token e rode no servidor `sudo ddm agent enroll --url https://painel.suaempresa.com`
   (o próprio servidor do painel também pode rodar aplicações).
2. **Projetos → Novo**: escolha um repositório Git, uma imagem Docker ou um modelo pronto.
3. Desenhe o **Mapa** (serviços, bancos, rotas) e clique em **Implantar**.

Guia completo: **https://ddm.datatoms.com/docs/pt-br/**

## Casos especiais

**Servidor que já tem outro painel (Coolify, Nginx…) nas portas 80/443:** instale no modo túnel, numa porta
local livre, sem mexer no firewall, e aponte o seu túnel da Cloudflare para `http://localhost:8095`:

```sh
sudo ddm install --domain painel.suaempresa.com --owner-email voce@suaempresa.com \
  --tunnel --tunnel-port 8095 --no-firewall
```

**Prefere conferir tudo à mão (sem `curl | bash`)?**

```sh
V=1.0.9; B=https://github.com/brenosandro/ddm-releases/releases/download/v$V
curl -fsSLO "$B/install.sh" -O "$B/SHA256SUMS" -O "$B/SHA256SUMS.minisig"
minisign -Vm SHA256SUMS -P RWRiJyDv3tTHmvsta4m6nFp4Rz4Bbe3bej/Ip8mVSAYNIpUeKlxOqN4Z
sha256sum --check --ignore-missing SHA256SUMS
sudo sh install.sh --version "$V" && sudo ddm install
```

**No Windows, conferindo o script antes de rodar:** baixe `install-windows.ps1`, `SHA256SUMS` e `SHA256SUMS.minisig`
da release, confira com `minisign -Vm SHA256SUMS -P <chave abaixo>` e `(Get-FileHash .\install-windows.ps1).Hash`
(igual à linha dele em `SHA256SUMS`) e rode `Get-Content -Raw .\install-windows.ps1 | iex`. Os scripts ainda não têm
assinatura Authenticode; a garantia é a assinatura minisign da lista.

Chave pública do projeto (`9AC7D4DEEF202762`): `RWRiJyDv3tTHmvsta4m6nFp4Rz4Bbe3bej/Ip8mVSAYNIpUeKlxOqN4Z`

## Comandos úteis no servidor

| Comando | Para quê |
|---|---|
| `sudo ddm status` | saúde do painel e dos serviços |
| `sudo ddm doctor` | diagnóstico (portas, DNS, Docker, disco…) |
| `sudo ddm logs api` | logs do painel |
| `sudo ddm backup` | backup cifrado do painel |
| `sudo ddm upgrade --version X.Y.Z` | atualiza com volta automática se algo falhar |

## Problemas e sugestões

Abra uma [issue](https://github.com/brenosandro/ddm-releases/issues) contando o que aconteceu (sistema, comando
rodado e a mensagem de erro — **nunca cole senhas ou tokens**).

## ❤️ Apoie

O DDM é grátis e mantido por uma pessoa. Se ele te ajuda, apoie por PIX (QR Code e copia e cola) em
**https://ddm.datatoms.com/#support**. Obrigado!

---

Este repositório contém apenas os arquivos de release (binários assinados, instaladores e notas). O código-fonte
não é público.
