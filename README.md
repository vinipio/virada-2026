# Anatomia da virada — 2º turno 2026, município a município

Site estático (HTML + JSON) com o mapa coroplético do Brasil e o detalhe de cada município:
balanço 1º × 2º turno, **saldo** (de onde saiu e para onde foi o voto), regressão ecológica
(fração de cada fonte que foi para Lula) e quartis sem modelo.

## Como regenerar os dados

Na pasta `apuracao` (precisa de `virada.json`/`virada-municipio.csv` atualizados, `dados/boletins.sqlite`
e `dados/municipios-tse-ibge.csv`):

    python virada.py          # se o armazém mudou
    python plataforma.py      # escreve site/dados/resumo.json, municipios.json e detalhe/<UF>.json

O `plataforma.py` importa `Acum`, `regressao` e `quartis` do `virada.py` e aborta se o balanço municipal
não bater exatamente com o `virada.json`. A malha (`site/dados/malha.json`, TopoJSON) vem de
`dados/malha-municipios-ibge.json` (IBGE, malha 2022, qualidade intermediária) e não precisa ser refeita.

## Ver localmente

`ABRIR-SITE.cmd` (serve a pasta `site/` em http://localhost:8000). Abrir o `index.html` direto do disco
não funciona: o navegador bloqueia `fetch` de arquivos locais.

## Publicar no GitHub Pages

1. Crie um repositório público no GitHub (ex.: `virada-2026`).
2. Dentro da pasta `site/`:

       git init
       git add .
       git commit -m "Anatomia da virada — site"
       git branch -M main
       git remote add origin https://github.com/<usuario>/virada-2026.git
       git push -u origin main

3. No GitHub: *Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: main, pasta `/ (root)` → Save*.
4. Em ~1 minuto o site fica em `https://<usuario>.github.io/virada-2026/`.

Para atualizar: rode `python plataforma.py`, depois `git add . && git commit -m "dados" && git push` dentro de `site/`.

## Estrutura

    site/
      index.html                 página (d3 + topojson-client via CDN jsdelivr)
      .nojekyll                  evita o processamento Jekyll do GitHub Pages
      dados/resumo.json          nacional, regiões, UFs, coeficientes, quartis, validação, fechamento
      dados/municipios.json      uma linha leve por município (mapa, busca)
      dados/malha.json           TopoJSON dos 5.570 municípios (id = UF|código TSE)
      dados/detalhe/<UF>.json    detalhe por município, carregado sob demanda

Links diretos para um município: `index.html#<UF>-<código TSE>` (ex.: `#SC-81051`).
