# Pipeline de Carrossel — índice

Tudo o que construímos está nesta pasta (`Projetos\Design\carrossel-pipeline`).

## Onde estão os documentos das skills

| Skill | Documento (SKILL.md) | Papel |
|-------|----------------------|-------|
| **design-system** | `skills/design-system/SKILL.md` | Estágio 1 — recebe uma imagem de referência e gera o design system: `design-system.yaml` (tokens) + `design-system.html` (apresentação visual navegável). |
| **slide-copy** | `skills/slide-copy/SKILL.md` | Estágio 2 — recebe um conteúdo e escreve a copy de cada slide em `slides.yaml` (hook, valor, CTA + art_brief). Foi a skill que testamos (100% no benchmark). |
| **orquestrador** (carousel-pipeline) | `skills/orquestrador/SKILL.md` | A "cola" — dispara os 3 estágios na ordem e cuida do contrato de dados entre eles. |
| **higgsfield** | (clonado de robonuggets/higgsfield-skill na instalação) | Estágio 3 — gera a arte de cada slide via Higgsfield MCP usando o design system + a copy. |

> Os arquivos `design-system.SKILL.md` e `orquestrador.SKILL.md` na raiz são cópias soltas
> dos mesmos documentos — a versão "oficial" para instalar é a de dentro de `skills/`.

## Como as peças se distribuem (fluxo)

```
imagem de referência ─▶ [1] design-system ─▶ brand/design-system.yaml + .html
conteúdo bruto ───────▶ [2] slide-copy ─────▶ content/<projeto>/slides.yaml
slides.yaml + design system ─▶ [3] higgsfield ─▶ content/<projeto>/out/  (PNGs)
                         (tudo coordenado pelo orquestrador)
```

## Configuração e regras

- `CLAUDE.md` — regras persistentes do projeto + contrato de dados entre estágios.
- `INSTALL.md` — comandos para clonar as skills e conectar o MCP do Higgsfield.

## Saídas já geradas (exemplo NeoBank)

- `brand/design-system.yaml` — tokens do NeoBank (paleta, tipografia, voz, style_prompt).
- `brand/design-system.html` — a apresentação visual do design system (abra no navegador).
- `content/neobank-trocar-banco/slides.yaml` — copy do carrossel "por que trocar de banco" (3 slides).
- `content/neobank-trocar-banco/out/manifest.md` — links das 3 artes geradas no Higgsfield.

## Como usar de novo

Abra esta pasta no Claude Code (ou Cowork apontando para ela) e diga, em sequência:
1. (envie a imagem) "cria o design system desta imagem"
2. (cole o conteúdo) "faz a copy do carrossel sobre isto"
3. "gera o carrossel"
