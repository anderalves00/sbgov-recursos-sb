# Transferências Especiais — Carteira SB (página pública)

Data: 2026-07-27

## O que é

Página estática única, autocontida, publicada via GitHub Pages.

- Repo: `anderalves00/transferencias-especiais-sb` (público)
- Link: https://anderalves00.github.io/transferencias-especiais-sb/
- Arquivo de trabalho: `transferencias-especiais-carteira-sb-v6.html` (~600 KB)
- `index.html` é só um stub de redirecionamento para a raiz do link funcionar

Origem: cópia de `anderalves00/TesteHTML`. Só o HTML foi trazido — verificado que
não há CSS/JS externo, nenhuma imagem em arquivo separado (todas embutidas como
`data:` base64) e nenhum `fetch`/XHR. As pastas `Imagens/` e as versões v2–v5 do
repo original não são usadas na geração da página.

## Decisões

**Tema.** A página já trazia as duas paletas prontas e seguia o
`prefers-color-scheme` do visitante. Agora o padrão é escuro fixo (`data-theme`
no `<html>`) com botão de alternância `#themeBtn`, preferência gravada em
`localStorage` (`sbgov-tema`) e reaplicada por um script no `<head>` para evitar
flash. O botão é `position:fixed` logo abaixo do cabeçalho, à direita.

Motivo de estar fora do cabeçalho: dentro do `.brandbar` ele era coberto pelo
`<span class="h1-main">` do título (mesmo `z-index`, `h1` depois no DOM), então o
clique nunca chegava nele. Diagnóstico confirmado com `elementFromPoint`.

**Cores.** Paleta viva padronizada, valor único por cor, igual nos dois temas e
entre KPIs e barras: azul `#365CAA`, verde `#1BB24B`, laranja `#F36C21`
(variáveis `--bar`, `--good`, `--warn`).

Houve uma tentativa de escurecer verde/laranja e usar texto quase-preto para
passar em WCAG 4.5:1. Foi **revertida por decisão de visual** — os rótulos
voltaram a branco alinhados à esquerda. Contraste atual, medido: branco sobre
verde 2,80:1 e sobre laranja 3,01:1, ambos abaixo do mínimo WCAG. É uma escolha
consciente, não um descuido.

**Trilha das barras** (`--track`): `#8E9CAD` no claro, `#3A4959` no escuro.
Branco sobre a trilha no claro: 1,38:1 → 2,80:1.

**Alinhamento dos gráficos.** Cada `.brow` é um grid próprio, então a coluna de
valores tinha largura diferente por linha e as trilhas terminavam desalinhadas.
Um script mede a maior largura de valor **de cada gráfico** e aplica como mínimo
às demais linhas daquele gráfico — independente entre gráficos. Recalcula em
novo render (`MutationObserver`), `resize` e `document.fonts.ready`.

**Logotipo.** Brasão e wordmark têm cor fixa branca, sem seguir o tema — o
cabeçalho é azul nos dois modos. Antes o brasão usava `var(--accent)`, que no
tema claro valia `#365CAA`, a própria cor do fundo, e ele sumia.

**Fundo decorativo.** Opacidade dos quatro motes virou `--mote-op`: 8% no claro,
14% no escuro (a 8% eles sumiam sobre `#10151C`). 20% foi testado e descartado
por competir com o conteúdo.

## Como verificar mudanças

Não há navegador de teste instalado (sem Playwright). O que foi usado em todas as
verificações desta sessão: Edge headless já presente no Windows.

```
"/c/Program Files (x86)/Microsoft/Edge/Application/msedge.exe" \
  --headless=new --disable-gpu --no-sandbox --window-size=1440,900 \
  --virtual-time-budget=6000 --screenshot=saida.png "file:///caminho/arquivo.html"
```

Trocando `--screenshot` por `--dump-dom` e escrevendo o resultado em
`document.title` via script anexado ao final de uma cópia do arquivo, dá para
medir contraste, larguras e clicabilidade sem navegador interativo.

## Pendência

**Responsividade — não foi feita.** Todas as verificações desta sessão rodaram em
janelas largas (1440×900, 1500×950, 780×1250). A página nunca foi aberta em
navegador real nem em tela estreita. Pontos a checar quando for retomar:

- o botão de tema é `position:fixed` com `top` dependente de `--hero-h`; em tela
  estreita o cabeçalho cresce e ele pode encostar em outra coisa
- `.brandbar` é `position:absolute` no cabeçalho, e `.hero` tem
  `padding-right:280px` fixo
- `.mote-slogan` já some abaixo de 1100px; os demais motes, não
- `.shell` é grid `300px + 1fr`, com regra para 1100px que empilha a sidebar
