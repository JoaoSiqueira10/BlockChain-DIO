# README — Gerador simples de carteira Bitcoin (Node.js)

Projeto mínimo que gera uma **seed (mnemonic)**, deriva chaves HD (BIP32/BIP39) e imprime um **endereço Bitcoin** + **chave privada (WIF)**. O código utiliza `bip32`, `bip39` e `bitcoinjs-lib` e, no exemplo, está configurado para **testnet**.

---

## O que este projeto faz

* Gera um mnemonic (seed) usando BIP-39.
* Converte o mnemonic para seed binária.
* Cria a raiz HD (BIP-32) a partir da seed.
* Deriva um caminho (`m/49'/1'/0'/0`) e obtém uma chave pública/privada.
* Calcula um endereço Bitcoin e imprime o endereço, a chave privada (WIF) e o mnemonic.

> Atenção: é um exemplo educacional — **não use em produção nem para valores reais sem revisar cuidadosamente**.

---

## Tecnologias / Dependências

* Node.js (recomenda-se >= 12)
* npm
* pacotes:

  * `bip32`
  * `bip39`
  * `bitcoinjs-lib`

---

## Instalação rápida

```bash
# inicializar projeto (se necessário)
npm init -y

# instalar dependências
npm install bip32 bip39 bitcoinjs-lib
```

Coloque o código em um arquivo, por exemplo `index.js`, e rode:

```bash
node index.js
```

---

## Uso

O script padrão (exemplo fornecido) imprime no terminal:

* `Carteira gerada`
* `Endereço: <endereço>`
* `Chave privada: <WIF>`
* `Seed: <mnemonic de 12/24 palavras>`

Exemplo (saída fictícia):

```
Carteira gerada
Endereço: 2Nxyz... (testnet)
Chave privada: cV... (WIF)
Seed: basket actual ... twelve words ...
```

---

## Explicação técnica (resumo)

* **Rede**: o projeto define `const network = bitcoin.networks.testnet`.

  * Para usar mainnet altere para `bitcoin.networks.bitcoin` (com extremo cuidado).
* **Derivação (path)**: `m/49'/1'/0'/0`

  * `49'` normalmente associa-se ao BIP49 (endereços P2SH-wrapped-segwit).
  * `1'` indica `coin_type` testnet (padrão BIP-44/49 para testnet).
  * **Observação importante**: no código você está usando `bitcoin.payments.p2pkh(...)`, que produz um endereço legacy (P2PKH). O caminho `m/49'/...` costuma ser usado com `p2sh(p2wpkh(...))` (BIP49).

    * Se você quer um endereço compatível com BIP49, ajuste para, por exemplo:

      ```js
      const {payments} = require('bitcoinjs-lib')
      const {address} = payments.p2sh({
        redeem: payments.p2wpkh({ pubkey: node.publicKey, network })
      })
      ```
    * Se quer usar endereço legacy (P2PKH) e BIP44-style, considere usar `m/44'/1'/0'/0/0`.
* **Chave privada**: `node.toWIF()` retorna a chave em formato WIF (Wallet Import Format).

---

## Segurança — Leia com atenção

* **NUNCA** compartilhe seu `mnemonic` ou `WIF` com terceiros. Quem tiver esses dados controla a carteira.
* **NÃO** use mainnet para testar. Use *testnet* enquanto estiver desenvolvendo.
* Este script **imprime** a seed e a chave no terminal — isso é inseguro para uso real. Em produção, armazene chaves com segurança (HSM, cofres, KMS).
* Se você já expôs um mnemonic ou WIF, considere mover fundos para uma nova carteira imediatamente.

---

## Sugestões / melhorias

* Adicionar parâmetros CLI para escolher `network` (mainnet/testnet) e `path`.
* Substituir impressão em console por gravação segura (ex.: arquivo criptografado).
* Validar e suportar outros tipos de output: `p2wpkh`, `p2sh(p2wpkh)`, etc.
* Integração com wallet libraries ou serviços de broadcast de transações (apenas para testnet inicialmente).
* Não gerar seed no servidor sem proteção — gerar no cliente ou em ambiente seguro.

---

## Arquivos sugeridos

* `index.js` — seu script atual.
* `.gitignore` — ignore `node_modules/` e arquivos sensíveis (`*.env`, `secrets/`, etc.)

  ```gitignore
  node_modules/
  *.log
  secrets/
  ```

---

## Como contribuir

* Faça um fork, crie uma branch com sua feature/fix, abra um PR.
* Sugestões: melhorias na geração de endereço compatível com BIP49/BIP84, adicionar testes unitários, CLI.

---

## Licença

Escolha uma licença adequada (por exemplo MIT).
Exemplo curto em `LICENSE` (MIT) se quiser permissividade.

---

Se quiser, eu posso:

* Gerar um `README.md` já formatado pronto para copiar para seu repositório;
* Ajustar o código para produzir endereços BIP49/BIP84 corretamente;
* Criar um exemplo com argumentos CLI (`--network`, `--path`). Qual prefere?
