# Pipeline de Carrossel (referência → design system → copy → arte Higgsfield)

Este projeto encadeia 3 skills para transformar uma **imagem de referência** + um
**conteúdo bruto** em um **carrossel de rede social** com artes geradas pelo Higgsfield,
mantendo um estilo visual consistente.

## Estrutura de pastas

```
carrossel-pipeline/
├── CLAUDE.md                         # este arquivo (regras persistentes)
├── .claude/skills/
│   ├── design-system/SKILL.md        # ESTÁGIO 1 — skill própria (referência → yaml + html visual)
│   ├── slide-copy/SKILL.md           # ESTÁGIO 2 — skill própria (conteúdo → slides.yaml)
│   ├── higgsfield/                   # ESTÁGIO 3 — clonado de robonuggets/higgsfield-skill
│   └── carousel-pipeline/SKILL.md    # ORQUESTRADOR (a cola)
├── brand/
│   ├── design-system.yaml            # saída 1 do Estágio 1 (tokens, reutilizável)
│   └── design-system.html            # saída 2 do Estágio 1 (apresentação visual navegável)
├── reference/
│   └── ref.png                       # a imagem de referência que você enviar
└── content/
    └── <projeto>/
        ├── source.md                 # o conteúdo que você fornecer
        ├── copy.md                   # saída do Estágio 2 (caption por plataforma)
        ├── slides.yaml               # plano slide-a-slide (copy + prompt de arte)
        └── out/                      # PNGs finais
```

## Contrato de dados entre estágios (NÃO quebrar)

1. **Estágio 1 → `brand/design-system.yaml` + `brand/design-system.html`**
   - A skill `design-system` gera SEMPRE as duas saídas. O YAML traz `primitives.colors`,
     `tokens.typography` (com `headline_pattern`), `hero_stage`, `voice` e o campo
     `art_direction.style_prompt`: um parágrafo único em inglês (paleta + mood + textura +
     composição + tipografia + o que evitar). **Esse parágrafo é reaproveitado em toda geração.**
   - O HTML é a apresentação visual navegável (paleta, tipografia, cartelas, voz, slides de
     exemplo) — é o que o usuário abre para aprovar a identidade antes de gastar créditos.

2. **Estágio 2 → `content/<projeto>/copy.md` + `slides.yaml`**
   - O social-captions escreve a caption por plataforma. O orquestrador pega a seção
     **INSTAGRAM CAROUSEL** e quebra o conteúdo em copy de slide (slide 1 = hook,
     slides 2..n-1 = valor, slide n = CTA) gravando em `slides.yaml`.

3. **Estágio 3 → `content/<projeto>/out/*.png`**
   - Para cada slide do `slides.yaml`: prompt = `art_direction.style_prompt` +
     layout do slide + texto do slide. Gera via Higgsfield MCP referenciando a
     imagem original para consistência de estilo.

## Regras do Higgsfield (do SKILL.md oficial — obrigatórias)

- Rodar `select_workspace` UMA vez no início da sessão (gerações falham em silêncio sem isso).
- SEMPRE cotar custo em créditos + saldo antes de gerar, e esperar "ok" explícito.
- Texto em imagem: usar `nano_banana_pro` ou `gpt_image_2` (renderizam texto corretamente).
  Para fundo/arte sem texto, qualquer modelo de imagem serve.
- Passagem de referência: schema `medias: [{ value, role }]`. Rodar
  `models_explore action=get` antes para confirmar os `role` aceitos pelo modelo.
- Geração é assíncrona: `generate_image` -> `job_id` -> `job_status({ job_id, sync: true })`.
  Pegar `rawUrl` (PNG), não `minUrl`.
- Reaproveitar o `media_id` da referência entre slides (não re-upar a cada slide).

## Defaults do projeto

- Formato do carrossel: 4:5 (1080x1350) para Instagram. Ajustar por pedido.
- Nº de slides padrão: 6 (1 hook + 4 valor + 1 CTA).
- Idioma da copy de slide: português. Caption longa: conforme social-captions (inglês por padrão — pedir PT se quiser).
- Sempre escrever/atualizar `slides.yaml` antes de gerar, para revisão.
