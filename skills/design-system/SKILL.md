---
name: design-system
description: >
  A partir de uma imagem de referência (post, feed, print de marca, arte), extrai um
  design system completo e entrega DUAS saídas: (1) design-system.yaml — os tokens que o
  resto do pipeline consome, e (2) design-system.html — uma APRESENTAÇÃO VISUAL navegável
  do design system (paleta, tipografia, sistema de cartelas, voz e slides de exemplo) que
  o usuário abre no navegador e vê. Use sempre que o usuário enviar uma imagem e pedir
  "cria o design system", "extrai o estilo dessa arte", "monta a identidade visual",
  "quero ver o design system", "transforma essa referência em design system" ou estiver
  no Estágio 1 do pipeline de carrossel. Por padrão, gere SEMPRE as duas saídas — o
  usuário quer ver, não só ler um YAML.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
---

# Design System — do print à apresentação visual

Você recebe uma imagem de referência e devolve um design system que (a) outras etapas
conseguem ler como dados e (b) o usuário consegue VER. As duas saídas são obrigatórias.
Entregar só o YAML é considerado incompleto: a pessoa pediu para enxergar o sistema.

## Passo 1 — Analisar a referência

Leia a imagem com atenção e extraia, observando de fato (não invente):

- **Cores**: identifique 4-6 cores reais. Separe fundo(s), acento(s), cor de texto e
  cores decorativas. Estime os hex e marque que são estimativas — a marca pode ter
  valores oficiais.
- **Tipografia**: família(s) aparente(s) e, principalmente, o *padrão* de uso (ex.: título
  em peso regular com uma palavra-chave em bold; tudo em caixa alta; serifa para número).
- **Direção de arte**: meio (foto, ilustração, flat, 3D), formas/texturas recorrentes,
  tratamento de fotografia, presença de mockups, posição do logo.
- **Voz**: pegue frases reais da peça como amostras de tom.

## Passo 2 — Gravar design-system.yaml

Grave em `brand/design-system.yaml`. Inclua: `primitives.colors`, `tokens.typography`
(com o `headline_pattern`), `hero_stage` (direção de arte), `voice` e o bloco derivado
`art_direction.style_prompt` — um parágrafo único em inglês que descreve paleta + mood +
textura + composição + tipografia + o que evitar. Esse parágrafo é o que alimenta a
geração de imagem no Higgsfield, então seja concreto.

## Passo 3 — Gerar design-system.html (a apresentação visual)

Esta é a parte que o usuário quer ver. Construa um HTML **autocontido e abrível direto no
navegador** (sem depender de `fetch` de arquivos locais — fontes podem vir de CDN). Use os
tokens reais extraídos, não placeholders. O arquivo deve ter, nesta ordem, seções
navegáveis:

1. **Hero** — fundo na cor principal da marca, logo/lockup reconstruído, nome do sistema
   e a frase de filosofia. Aplique já o padrão de headline da marca aqui.
2. **Paleta** — swatches em grade, cada um com nome, hex e uso ("fundo escuro", "acento").
3. **Tipografia** — espécimes das famílias + um bloco destacando o padrão de headline com
   exemplos reais (ex.: título com uma palavra em bold).
4. **Sistema de composição** — mostre as cartelas/estruturas típicas da marca (ex.: card
   escuro vs. claro, formas decorativas) como blocos reais renderizados.
5. **Voz e tom** — as amostras de frase + tags de personalidade.
6. **Aplicação** — 5-6 mini-mockups no formato da marca (ex.: slides 4:5) montados com a
   paleta e a tipografia, provando que o sistema funciona em peça real.
7. **Rodapé** — origem (extraído da referência) e o aviso de hex estimado.

Regras de qualidade do HTML:
- Use CSS inline/`<style>` com variáveis para as cores extraídas — nada hardcoded espalhado.
- Reconstrua formas de marca (blobs, ondas, grids) com SVG inline, não imagens externas.
- Carregue fontes via Google Fonts (`<link>`), com fallback. Se a fonte exata for paga,
  use a aproximação mais próxima e diga isso no rodapé.
- Responsivo o suficiente para abrir em qualquer largura.
- Estética coerente com a marca — o documento em si deve parecer feito pela marca.

Grave em `brand/design-system.html`.

## Passo 4 — Entregar e validar

Apresente o `design-system.html` ao usuário (é o artefato principal) e mostre o
`style_prompt` derivado. Pergunte se as cores e o tom ficaram fiéis antes de seguir para a
copy/geração — os hex são estimativas e a pessoa pode ter os valores oficiais.

## Por que duas saídas

O YAML é para a máquina (o Higgsfield e o orquestrador leem os tokens). O HTML é para a
pessoa decidir se a identidade está certa antes de gastar créditos gerando arte. Um sem o
outro trava o pipeline: sem YAML o próximo estágio não roda; sem HTML o usuário aprova no
escuro.

## Anti-padrões

- Entregar só o YAML "porque é mais rápido" — o usuário pediu para ver.
- HTML que depende de `fetch()` de arquivos locais → abre em branco via file://. Tudo inline.
- Inventar hex que não estão na imagem, ou prometer a fonte exata sem confirmar.
- Apresentação genérica que não parece a marca — o style guide tem que vestir a identidade.
