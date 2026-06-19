---
name: slide-copy
description: >
  Prepara a copy que vai DENTRO de cada slide de um carrossel de rede social e a grava em
  slides.yaml (hook, slides de valor, CTA + art_brief + prompt de imagem por slide). Sempre
  começa por um intake: (1) pergunta o(s) FORMATO(S) do post — pode escolher mais de um — e
  (2) pergunta se o usuário JÁ TEM o conteúdo pronto (aí só refina e gera os prompts de
  imagem, pulando a criação) ou se quer que a skill CRIE o conteúdo (aí faz primeiro as
  perguntas estratégicas que um social media faria sobre a marca). Use sempre que o usuário
  pedir "monta os slides", "faz a copy do carrossel", "quebra esse conteúdo em slides",
  "transforma esse texto em carrossel", "melhora esse conteúdo pra carrossel", "tenho um
  conteúdo pronto" ou estiver rodando o pipeline de carrossel e precisar do slides.yaml.
  NÃO escreve a legenda (caption) do post — isso é da skill social-captions; aqui é só o
  texto que aparece nas imagens.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Slide Copy — texto que vai dentro do carrossel

Carrossel não é um texto corrido fatiado. É uma sequência onde cada slide tem **um
único trabalho**: o primeiro segura o scroll, os do meio entregam um ponto cada, e o
último converte (salvar/compartilhar/seguir). A copy precisa funcionar **lida em silêncio,
no celular, em 1 segundo por slide**: headline curta, uma ideia por tela, nada de parágrafo.

## Passo 0 — Intake (sempre fazer ANTES de escrever)

Faça duas perguntas. Use múltipla escolha quando a ferramenta permitir; não escreva nada
antes de ter as respostas.

### Pergunta 1 — Formato(s) do post

Pergunte **qual formato** o carrossel terá e deixe escolher **mais de um** (multi-seleção).
Cada formato vira um conjunto de slides na proporção certa. Opções padrão:

| Formato | Proporção | Uso |
|---------|-----------|-----|
| Instagram Carrossel (retrato) | 4:5 (1080×1350) → no Higgsfield use 3:4 | padrão de feed, mais real estate vertical |
| Feed quadrado | 1:1 (1080×1080) | feed clássico, LinkedIn |
| Stories / Reels capa | 9:16 (1080×1920) | tela cheia, muito espaço; cuidado com zona segura |
| LinkedIn / documento | 4:5 ou 1:1 | conteúdo B2B |

Se o usuário escolher vários, a copy é a MESMA; o que muda é a proporção e as notas de
layout (em 9:16 há mais espaço vertical e zona segura no topo/rodapé). Registre todos em
`formats:` no slides.yaml.

### Pergunta 2 — Origem do conteúdo

Pergunte: **"Você já tem o conteúdo pronto, ou quer que eu crie com base no que sei da
marca?"** Há dois caminhos:

- **A) Conteúdo pronto** → o usuário cola/aponta o texto. Você **não inventa nada**: refina,
  encurta, corrige, e estrutura no formato de slides. Pule a criação de conteúdo e vá direto
  para o Passo 2.
- **B) Criar conteúdo** → faça primeiro as **perguntas estratégicas** abaixo (Passo 1), só
  então escreva.

## Passo 1 — Perguntas estratégicas (só no caminho B, "criar conteúdo")

Antes de criar, entenda a marca como um social media faria num briefing. Pergunte só o que
você ainda **não consegue inferir** do `design-system.yaml` ou do contexto da conversa —
não floode o usuário. As perguntas que importam:

1. **Objetivo** — o que esse carrossel precisa fazer? (vender, educar, gerar autoridade,
   engajar, anunciar lançamento)
2. **Público** — pra quem é? (quem você quer alcançar e em que momento)
3. **Ação final (CTA)** — o que a pessoa deve fazer no último slide?
4. **Oferta / diferencial** — qual é o ponto principal da marca neste tema? O que te torna
   diferente?
5. **Dor ou desejo** — qual problema do público esse conteúdo resolve, ou qual desejo ativa?
6. **Provas** — tem número, resultado, depoimento ou exemplo real pra dar credibilidade?
7. **Restrições** — algo que NÃO pode dizer? (compliance, promessas proibidas, palavras a evitar)
8. **Tom** — confirmar se segue o `voice` do design system ou se há ajuste.

Agrupe num conjunto curto. Com as respostas, você tem o briefing para escrever.

## Passo 1-alt — Refinar conteúdo pronto (caminho A)

Quando o usuário traz o conteúdo, seu trabalho é **melhorar, não reescrever do zero**:
- Mantenha a mensagem e os fatos do usuário; não invente dados, números ou claims.
- Encurte para caber em slide (uma ideia por tela), aplique o padrão de headline da marca,
  conserte hook fraco e CTA empilhado.
- Se faltar um slide óbvio (ex.: não há CTA), proponha — mas avise que foi adição sua.

## Passo 2 — Ler o tom (se houver design system)

Procure `brand/design-system.yaml`. Se existir, leia `voice` (tom, cadência, samples) e
espelhe o ritmo. Leia também `art_direction.style_prompt` — você vai usá-lo no prompt de
imagem de cada slide (Passo 4). Sem design system, use tom profissional e direto e siga.

## Passo 3 — Estrutura

Conte os **pontos reais** do conteúdo. Cada ponto vira um slide de valor.
- Estrutura: 1 hook + N de valor + 1 CTA.
- Default 6 (1+4+1). Faixa 3 a 8. Com 3, é o mínimo (hook + 1 valor + CTA) — ótimo para
  carrosséis enxutos.
- Mais pontos que o teto → escolha os mais fortes; retenção cai a cada slide.

## Passo 4 — Escrever slide a slide

- **Hook**: parar o scroll e prometer valor (tensão, curiosidade, número). É a frase mais
  trabalhada do carrossel.
- **Valor**: uma ideia por slide; `headline` carrega a ideia, `body` (1-2 linhas) dá a
  evidência. Mantenha paralelismo entre os slides.
- **CTA**: uma ação só (priorize salvar/compartilhar). Sem "curte, salva, comenta, segue".

Para cada slide produza:
- `art_brief`: o que aparece visualmente (assunto + elemento + onde fica o espaço do texto).
- `image_prompt`: o prompt PRONTO para gerar a imagem = `art_direction.style_prompt` (se
  houver) + a descrição do slide + o texto on-image entre aspas + a proporção. É isto que o
  Higgsfield consome. No caminho A (conteúdo pronto), entregar o `image_prompt` é o ponto
  principal — o usuário quer o prompt de imagem.

## Passo 5 — Gravar o slides.yaml

```yaml
project: "nome-curto"
formats: ["4:5", "1:1"]      # um ou mais — escolhidos no intake
n_slides: 6
content_source: "criado"      # "criado" (caminho B) ou "pronto" (caminho A)
slides:
  - i: 1
    role: hook                # hook | value | cta
    headline: "Frase que segura o scroll"
    bold_word: "palavra"      # a palavra-chave em bold (assinatura da marca)
    body: ""
    art_brief: "o que aparece + onde fica o espaço do texto"
    image_prompt: "<style_prompt> + layout do slide + texto on-image + proporção"
  # ...
  - i: 6
    role: cta
    headline: "Salva esse carrossel"
    body: "Manda pra quem precisa ver isso."
    art_brief: "fundo na cor de acento, CTA centralizado"
    image_prompt: "..."
```

Regras: `n_slides` bate com a contagem; `i` sequencial desde 1; primeiro slide `hook`,
último `cta`. Idioma da copy: português, s