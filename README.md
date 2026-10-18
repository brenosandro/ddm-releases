# DDM — Datatoms Deploy Manager (downloads)

Releases públicas e assinadas do **DDM**, o painel para implantar e operar aplicações nos seus próprios servidores.
O DDM é **grátis** para instalar e usar. Site e documentação: **https://ddm.datatoms.com**.

## Instalar (Ubuntu 24.04 / Debian 12, como root)

```sh
curl -fsSL https://ddm.datatoms.com/install | sudo bash
```

O instalador confere a assinatura minisign de cada release antes de executar qualquer coisa. Chave pública do
projeto (`9AC7D4DEEF202762`):

```
RWRiJyDv3tTHmvsta4m6nFp4Rz4Bbe3bej/Ip8mVSAYNIpUeKlxOqN4Z
```

Conferência manual de uma release:

```sh
V=1.0.0; B=https://github.com/brenosandro/ddm-releases/releases/download/v$V
curl -fsSLO "$B/install.sh" -O "$B/SHA256SUMS" -O "$B/SHA256SUMS.minisig"
minisign -Vm SHA256SUMS -P RWRiJyDv3tTHmvsta4m6nFp4Rz4Bbe3bej/Ip8mVSAYNIpUeKlxOqN4Z
sha256sum --check --ignore-missing SHA256SUMS
sudo sh install.sh --version "$V" && sudo ddm install
```

## Apoie

O DDM é mantido por uma pessoa. Se ele te ajuda, apoie por PIX em https://ddm.datatoms.com/#support.

Este repositório contém só os arquivos de release; o código-fonte não é público.
