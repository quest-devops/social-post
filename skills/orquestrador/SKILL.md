---
name: carousel-pipeline
description: >
  Orquestrador de carrossel para redes sociais. Encadeia 3 estágios: (1) extrair um design
  system de uma imagem de referência (skill design-system → yaml + html visual), (2) montar
  a copy do carrossel (skill slide-copy, que pergunta formato(s) e se o conteúdo é pronto ou
  a criar), (3) gerar a arte de cada slide via Higgsfield MCP usando o design system + a
  copy. Use quando o usuário disser "criar design system desta imagem", "fazer a copy do
  carrossel", "gerar o carrossel", "rodar o pipeline de carrossel" ou enviar uma imagem de
  referência pedindo artes no mesmo estilo.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
---

# Orquestrador de Carrossel
# INSTALAÇÃO: mova este arquivo para .claude/skills/carousel-pipeline/SKILL.md

Você coordena três skills já instaladas. NÃO reimplementa o trabalho delas —
delega a cada uma e cola as saídas seguindo o contrato de dados do `CLAUDE.md`.

Skills consumidas:
- `design-system` — referência visual → `design-system.yaml` + `design-system.html` (apresentação visual)
- `slide-copy` — conteúdo → `slides.yaml` (copy de cada slide)
- `higgsfield` — prompt → imagem (MCP, 30+ modelos)

## Os 3 comandos

### A) "Criar design system a partir desta imagem"
Gatilhos: usuário envia uma imagem de referência / "extrair estilo" / "design system desta arte" / "quero ver o design system".

1. Salve a imagem em `reference/ref.png`.
2. Invoque a skill **design-system** apontando para essa imagem. Ela produz DUAS saídas
   obrigatórias: `brand/design-system.yaml` (tokens) e `brand/design-system.html`
   (apresentação visual navegável — paleta, tipografia, sistema de cartelas, voz e
   slides de exemplo).
3. O `design-system.yaml` já inclui o `art_direction.style_prompt` (parágrafo que será
   prependido em toda geração de imagem).
4. **Apresente o `design-system.html`** ao usuário (é o artefato principal — ele quer ver)
   e mostre o `style_prompt`. Só siga para a copy/geração após aprovação, lembrando que os
   hex são estimativas da imagem.

### B) "Fazer a copy / montar os slides do carrossel"
Gatilhos: "monta os slides" / "faz a copy" / "tenho um conteúdo pronto" / "cria o conteúdo".

Invoque a skill **slide-copy**, que SEMPRE começa por um intake de duas perguntas:

1. **Formato(s)** — qual(is) formato(s) o carrossel terá; pode escolher mais de um
   (4:5 retrato, 1:1 quadrado, 9:16 stories…). Vão para `formats:` no slides.yaml.
2. **Origem do conteúdo** — bifurcação obrigatória:
   - **Conteúdo pronto** → o usuário cola o texto; a skill só refina (sem inventar) e gera
     o `image_prompt` de cada slide. Pula a criação.
   - **Criar conteúdo** → a skill faz primeiro as perguntas estratégicas de social media
     (objetivo, público, CTA, diferencial, dor, provas, restrições, tom) e só então escreve.

A skill grava `content/<projeto>/slides.yaml` (com `formats`, `content_source` e, por slide,
`art_brief` + `image_prompt`). Mostre para revisão. NÃO gere arte ainda.

### C) "Gerar o carrossel"
Gatilhos: "gera as artes" / "roda o Higgsfield" / "exporta o carrossel".

Pré-condições: existir `brand/design-system.yaml` (com `style_prompt`) e `slides.yaml`.

1. `select_workspace` (uma vez por sessão) e cheque `balance`.
2. Modelo: `gpt_image_2` (texto nítido, suporta 1:1/3:4/9:16) por padrão; `nano_banana_pro`
   para 4K. Rode `models_explore action=get` para confirmar `aspect_ratios` e `roles`.
   Obs.: GPT Image 2 não tem 4:5 — use **3:4** como retrato.
3. **Cote o custo com `get_cost: true`** (preflight) e multiplique por
   nº de slides × nº de formatos. Some, compare com o saldo e ESPERE "ok" explícito.
4. Para cada **formato** × cada **slide**, use o `image_prompt` já pronto do slides.yaml
   (ele já tem o `style_prompt` + texto on-image) e chame `generate_image({ model, prompt,
   aspect_ratio, resolution, quality })`. Se for usar a referência como âncora de estilo,
   importe-a com `media_import_url` e passe `medias:[{value: media_id, role:'image'}]`.
5. Geração é assíncrona: o retorno traz `job_id`/status `pending`. Faça poll com
   `job_display(id)` até `status: completed` e pegue `results.rawUrl`.
   **Plano free = 1 job concorrente** → gere em SÉRIE (um slide por vez), não em paralelo.
6. Apresente cada resultado com `job_display` e registre os `rawUrl` num
   `content/<projeto>/out/manifest.md` por formato.
7. Ao final, mostre o carrossel e ofereça regenerar slides específicos (1 dimensão por vez).
   Lembre o usuário de conferir a renderização do texto (IA erra acento/letra às vezes).

## Schema do slides.yaml

```yaml
project: "lancamento-x"
formats: ["4:5", "1:1"]   # um ou mais, escolhidos no intake da slide-copy
n_slides: 6
content_source: "criado"   # "criado" | "pronto"
slides:
  - i: 1
    role: hook
    headline: "Pare de perder seguidor no slide 1"
    bold_word: "perder"
    body: ""
    art_brief: "close de um grafico despencando, foco no headline"
    image_prompt: "<style_prompt> + layout do slide 1 + texto on-image + proporcao"
  # ... slides de valor ...
  - i: 6
    role: cta
    headline: "Salva esse post"
    body: "Compartilha com quem