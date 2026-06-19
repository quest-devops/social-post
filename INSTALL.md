# Instalação do Pipeline de Carrossel

## 1. Crie a pasta do projeto e a estrutura

```bash
mkdir -p meu-carrossel/.claude/skills meu-carrossel/brand meu-carrossel/reference
cd meu-carrossel
```

Copie para a raiz os arquivos `CLAUDE.md` e `orquestrador.SKILL.md` deste pacote.

## 2. Clone as 3 skills

```bash
git clone https://github.com/dominikmartn/hue .claude/skills/hue
git clone https://github.com/rediumvex/social-media-caption-generator-claude .claude/skills/social-captions
git clone https://github.com/robonuggets/higgsfield-skill /tmp/hf && \
  cp -r /tmp/hf/.claude/skills/higgsfield .claude/skills/higgsfield
```

## 3. Instale o orquestrador (a cola)

```bash
mkdir -p .claude/skills/carousel-pipeline
mv orquestrador.SKILL.md .claude/skills/carousel-pipeline/SKILL.md
```

## 4. Conecte o MCP do Higgsfield (precisa de assinatura Higgsfield)

```bash
claude mcp add --transport http --scope user higgsfield https://mcp.higgsfield.ai/mcp
```
Depois rode `/mcp` e faça login. Na primeira sessão, rode `select_workspace` uma vez.

## 5. Uso

Abra o Claude Code dentro de `meu-carrossel/` e fale, em sequência:

1. (envie a imagem de referência) → "cria o design system desta imagem"
2. (cole o conteúdo) → "faz a copy do carrossel sobre isto"
3. "gera o carrossel" → ele cota os créditos, você aprova, e os PNGs saem em `content/<projeto>/out/`.

## Estrutura final esperada

```
meu-carrossel/
├── CLAUDE.md
├── .claude/skills/{hue, social-captions, higgsfield, carousel-pipeline}/
├── brand/design-system.yaml      # gerado no passo 1
├── reference/ref.png
└── content/<projeto>/{source.md, copy.md, slides.yaml, out/}
```

## Notas

- Licenças: hue (MIT), higgsfield-skill (CC BY 4.0), social-captions (ver repo). hue ok
  para uso/fork; mantenha atribuição onde exigido.
- Custo: cada slide via `nano_banana_pro` ≈ 11 créditos; `gpt_image_2` ≈ 3 créditos
  (medir no dashboard — os números mudam). Um carrossel de 6 slides = ~66 ou ~18 créditos.
- Gap custom: a quebra de "caption" em "copy de slide" é lógica do orquestrador (passo B.3).
  Funciona, mas é o ponto mais frágil — candidato a virar uma skill dedicada depois.
```
