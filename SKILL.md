---
name: skill-audit
description: >
  Audits a Claude Code / Agent SDK skill (or folder of skills) against 10 QA
  checks to verify it will trigger and execute correctly in any LLM (Claude,
  GPT, Gemini). Ativa quando o usuÃ¡rio diz "audit my skill", "roda skill-audit",
  "verifica se essa skill funciona", "check skill quality", "skill-audit ./my-skill",
  "minha skill nÃ£o dispara", "migrei do Claude antigo e quebrou", ou colar path
  de um SKILL.md. Retorna score 0-10 por skill, issues priorizados, e sugestÃµes
  de conserto. NÃƒO use para: criar uma skill nova do zero (use `criar-skill`
  ou `skill-creator`); executar a skill alvo; refatorar cÃ³digo genÃ©rico.
type: skill
category: meta
status: ATIVO
version: 1.0
created: 2026-04-19
last_reviewed: 2026-04-19
estimated_time: 2min
model_compatible: [claude-sonnet-4, claude-opus-4, gpt-5, gpt-4o, gemini-pro]
---

# skill-audit

Auditoria estÃ¡tica de `SKILL.md` contra o padrÃ£o V3 (LLM-friendly). Roda 10 QA
checks, detecta issues mecÃ¢nicos e semÃ¢nticos, entrega report priorizado. Ãštil
especialmente pra quem migrou de Claude pre-4.5 â€” skills antigas costumam
funcionar sÃ³ no contexto do autor, nÃ£o em agentes externos.

---

## When to Use

Aciona quando:
- UsuÃ¡rio tem uma skill que "nÃ£o dispara" ou "nÃ£o executa direito"
- Migrou de Claude 3.x / 4.0 pra 4.6+ e viu comportamento quebrado
- Quer rodar a skill em GPT-5, Gemini, ou outro LLM que nÃ£o seja Claude
- EstÃ¡ criando skills em massa (ex: para alunos, time, comunidade)
- Quer priorizar quais skills da coleÃ§Ã£o conserta primeiro

Exemplos literais de mensagens:
- "roda skill-audit em skills/my-skill/SKILL.md"
- "minha skill nÃ£o dispara no GPT"
- "audita essa pasta de skills pra mim"
- "skill-audit ./skills"
- "por que essa skill funciona no Claude mas nÃ£o no Gemini?"

## When NOT to Use

NÃƒO aciona se:
- UsuÃ¡rio quer CRIAR uma skill do zero â†’ usar `criar-skill` ou `skill-creator`
- UsuÃ¡rio quer EXECUTAR a skill auditada â†’ rodar a skill diretamente
- UsuÃ¡rio quer auditoria de seguranÃ§a (OWASP, secrets em produÃ§Ã£o) â†’ outra ferramenta
- Arquivo alvo nÃ£o Ã© `SKILL.md` (ex: script Python, README) â†’ refatoraÃ§Ã£o genÃ©rica

---

## Inputs

| ParÃ¢metro | Tipo | ObrigatÃ³rio | DescriÃ§Ã£o |
|-----------|------|-------------|-----------|
| path | string | âœ… | Path de um `SKILL.md` OU pasta contendo subpastas com `SKILL.md` (recursivo) |
| format | string | âŒ | `markdown` (default) ou `json` â€” formato do report |

## Outputs

| Campo | Tipo | DescriÃ§Ã£o |
|-------|------|-----------|
| score | int 0-10 | Quantos dos 10 QA checks a skill passa |
| issues | array | Lista priorizada de problemas (ex: `desc_no_triggers`, `examples_too_few`) |
| by_category | object | Score mÃ©dio e passing rate por categoria de skill |
| report | markdown | Report completo com ranking + detalhamento por skill |

Formato de entrega: arquivo markdown em `./audit-results.md` + resumo no terminal.

---

## Workflow

1. **Validar input** â€” path existe? Ã‰ arquivo `.md` ou diretÃ³rio?
2. **Coletar SKILL.md files**
   - SE path aponta pra arquivo `.md` â†’ lista Ãºnica
   - SENÃƒO â†’ `glob **/SKILL.md` recursivo no diretÃ³rio
3. **Para cada SKILL.md:**
   - Parsear frontmatter YAML (`---` ... `---`)
   - Split do body em seÃ§Ãµes H2 (ignorando H2 dentro de code fences ``` ou ~~~)
   - Rodar os 10 QA checks
   - Registrar score + issues + contagens
4. **Agregar resultados** â€” score mÃ©dio, distribuiÃ§Ã£o, issues mais comuns
5. **Gerar report markdown** em `./audit-results.md` com:
   - SumÃ¡rio global
   - DistribuiÃ§Ã£o de scores
   - Issues por frequÃªncia
   - Top 30 skills que mais precisam conserto
   - Detalhamento por skill
6. **Imprimir resumo no terminal** â€” total, score mÃ©dio, passing â‰¥7

### Os 10 QA checks

1. Nome em kebab-case e bate com pasta
2. Description: 50+ palavras, terceira pessoa, 5+ trigger phrases, negative boundaries
3. Cada passo do Workflow Ã© aÃ§Ã£o Ãºnica, imperativa, nÃ£o-ambÃ­gua
4. 2+ exemplos concretos (input real â†’ output real)
5. Edge Cases cobertos (3+ condiÃ§Ãµes)
6. Output Format explicitamente definido
7. Zero linguagem vaga (lista de palavras banidas em `references/qa-checklist.md`)
8. Negative boundaries na seÃ§Ã£o `## When NOT to Use`
9. Zero credenciais hardcoded (secrets, API keys, tokens)
10. Pasta `evals/evals.json` existe com 2+ casos

---

## Edge Cases

- **Se path nÃ£o existe** â†’ exit 1 com mensagem "Path nÃ£o encontrado: {path}"
- **Se diretÃ³rio vazio (0 SKILL.md)** â†’ exit 0 com mensagem "Nenhum SKILL.md encontrado em {path}"
- **Se SKILL.md sem frontmatter YAML** â†’ score 0, issue `missing_frontmatter`
- **Se YAML invÃ¡lido** â†’ score 0, issue `yaml_parse_error`
- **Se pasta `evals/` existe mas sem `evals.json`** â†’ issue `no_evals_folder`
- **Se SKILL.md muito grande (>350 linhas)** â†’ warning `long`, sugere mover conteÃºdo para `references/`
- **Se placeholder ref (`link`, `url`, `path`) em markdown link** â†’ ignorado (nÃ£o conta como broken ref)

---

## Examples

### Example 1 â€” Auditar uma skill Ãºnica

**Input real:** `skill-audit ./my-skill/SKILL.md`

**Workflow executado:**
1. Valida que `./my-skill/SKILL.md` existe
2. Parseia YAML â€” encontra `name: my-skill`, description de 32 palavras
3. Body split: tem `## When to Use`, falta `## When NOT to Use`
4. Roda 10 checks: description short (32w<50), no negatives, no evals folder, sÃ³ 1 Example
5. Gera report

**Output real:**
```
skill-audit: 1 skill analisada
  my-skill â€” 6/10 (232L, 32w desc, 4 triggers, 1 ex, 4 edge, evals âœ—)
  Issues: desc_too_short(32w), desc_no_negatives, examples_too_few(1), no_evals_folder

Report: ./audit-results.md
```

### Example 2 â€” Auditar pasta inteira (edge: vÃ¡rias skills)

**Input real:** `skill-audit ~/my-claude-skills/`

**Workflow executado:**
1. Valida que `~/my-claude-skills/` Ã© diretÃ³rio
2. Glob encontra 12 `SKILL.md`
3. Para cada: parseia + checks + score
4. Agrega: score mÃ©dio 7.1, 4/12 passing â‰¥7
5. Report priorizado: pior primeiro

**Output real:**
```
skill-audit: 12 skills analisadas
  Score mÃ©dio: 7.1/10
  Passing (â‰¥7): 4/12 (33%)

Top 3 issues mais comuns:
  desc_no_negatives (12, 100%)
  no_evals_folder (9, 75%)
  desc_too_short (7, 58%)

Worst offenders:
  1. legacy-skill-x â€” 3/10
  2. migrated-from-gpt â€” 4/10
  3. old-claude35-skill â€” 5/10

Report: ./audit-results.md
```

---

## Dependencies

- **Runtime:** Python 3.8+
- **Libs:** stdlib only (re, json, pathlib, argparse, collections)
- **APIs:** nenhuma â€” 100% estÃ¡tico/offline
- **Files:** `templates/SKILL-TEMPLATE.md` + `references/qa-checklist.md` (read-only)
- **Outras skills:** nenhuma â€” ferramenta standalone

---

## Errors & Recovery

| Erro | Causa provÃ¡vel | Fix |
|------|----------------|-----|
| `Path nÃ£o encontrado` | Path invÃ¡lido ou digitado errado | Verificar path absoluto; usar `ls {path}` pra confirmar |
| `yaml.YAMLError` | Frontmatter mal formado | Abrir SKILL.md, verificar indentaÃ§Ã£o e fechamento `---` |
| `UnicodeDecodeError` | SKILL.md em encoding nÃ£o-UTF8 | Salvar como UTF-8 no editor |
| `ModuleNotFoundError: yaml` | PyYAML ausente | `pip3 install pyyaml` |
| Report nÃ£o gerado | PermissÃ£o de escrita em `./audit-results.md` | Rodar de diretÃ³rio com permissÃ£o de escrita |

---

## Notes

**Sobre o score:** 7/10 Ã© o mÃ­nimo aceitÃ¡vel pra considerar uma skill "trigger-confiÃ¡vel"
em LLMs externos. 10/10 Ã© ideal. Abaixo de 7 geralmente significa que a skill foi
escrita em prosa livre e nÃ£o em contrato executÃ¡vel â€” formato comum em skills pre-V3.

**Sobre evals.json:** o check 10 nÃ£o valida conteÃºdo dos evals, sÃ³ presenÃ§a do arquivo
e estrutura mÃ­nima (2+ casos com `prompt` e `expected_output`). Qualidade dos evals Ã©
trabalho do autor da skill, nÃ£o do audit.

**LimitaÃ§Ãµes conhecidas:**
- NÃ£o audita qualidade semÃ¢ntica do workflow (se os passos fazem sentido pro domÃ­nio)
- NÃ£o executa a skill (sÃ³ anÃ¡lise estÃ¡tica)
- HeurÃ­sticas de trigger phrase podem dar falso positivo em descriptions tÃ©cnicas
  densas â€” revisar issues manualmente quando score for borderline (6-7)

---

## Changelog

- v1.0 (2026-04-19): VersÃ£o inicial. 10 QA checks baseados no padrÃ£o V3.
  Deriva de auditoria em 119 skills do workspace Amora (score mÃ©dio 5.9 â†’ 10.0).


---
*Créditos originais da metodologia: [Bruno Okamoto](https://github.com/okjpg)*
