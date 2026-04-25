---
name: generate-report
description: >
  Generates weekly analytics reports from a CSV file with traffic data. Ativa
  quando o usuÃ¡rio diz "gera o weekly report", "roda o report semanal", "report
  da semana", "/generate-report", "monta o resumo semanal de trÃ¡fego", ou cola
  um path de CSV pedindo anÃ¡lise. Retorna markdown com top 5 fontes, delta vs
  semana anterior, e 3 insights priorizados. NÃƒO use para: reports mensais (usa
  `monthly-report`); anÃ¡lise ad-hoc de dados arbitrÃ¡rios (usa `data-explorer`);
  geraÃ§Ã£o de grÃ¡ficos visuais.
type: skill
category: analytics
status: ATIVO
version: 1.0
created: 2026-04-19
last_reviewed: 2026-04-19
estimated_time: 2min
model_compatible: [claude-sonnet-4, claude-opus-4, gpt-5, gpt-4o, gemini-pro]
---

# Generate Report

Generates weekly traffic reports from a CSV file. Compares current week vs
previous, ranks top traffic sources, extracts 3 actionable insights. Output is
markdown, ready to paste into email or Notion.

---

## When to Use

Aciona quando:
- UsuÃ¡rio cola path de um CSV e pede anÃ¡lise semanal
- Toda segunda-feira 9h (cron job chama esta skill)
- UsuÃ¡rio pede "weekly report" / "report da semana"

Exemplos literais:
- "gera o weekly report de /data/traffic.csv"
- "roda o report semanal"
- "/generate-report"

## When NOT to Use

NÃƒO aciona se:
- UsuÃ¡rio quer report mensal â†’ `monthly-report`
- UsuÃ¡rio quer anÃ¡lise ad-hoc de dados arbitrÃ¡rios â†’ `data-explorer`
- UsuÃ¡rio quer grÃ¡fico visual â†’ `chart-builder`

---

## Inputs

| ParÃ¢metro | Tipo | ObrigatÃ³rio | DescriÃ§Ã£o |
|-----------|------|-------------|-----------|
| csv_path | string | âœ… | Path absoluto do CSV. Colunas esperadas: date, source, visits |
| weeks_back | int | âŒ | Quantas semanas comparar (default: 1) |

## Outputs

| Campo | Tipo | DescriÃ§Ã£o |
|-------|------|-----------|
| report | markdown | Report formatado, 300-500 palavras |
| top_sources | array | Top 5 fontes com visits + % |
| delta | object | VariaÃ§Ã£o vs semana anterior (visits, %) |
| insights | array | 3 strings priorizadas |

Formato de entrega: arquivo markdown em `./reports/weekly-YYYY-MM-DD.md`.

---

## Workflow

1. **Validar CSV** â€” abrir `csv_path`, confirmar colunas `date`, `source`, `visits`
2. **SE colunas faltando â†’ abortar com erro especÃ­fico**
3. **Filtrar semana atual** â€” Ãºltimos 7 dias
4. **Agrupar por source** â€” sum visits, sortear desc
5. **Filtrar semana anterior** â€” dias 8-14
6. **Calcular delta** â€” (atual - anterior) / anterior * 100
7. **Extrair top 5 sources** da lista agrupada
8. **Gerar 3 insights** baseados em: maior gain, maior loss, nova fonte
9. **Renderizar markdown** no template `reports/template.md`
10. **Salvar** em `./reports/weekly-{today}.md`
11. **Retornar** path do arquivo gerado

---

## Edge Cases

- **Se CSV vazio** â†’ retornar report com "Sem dados disponÃ­veis"
- **Se apenas 1 semana de histÃ³rico** â†’ omitir delta, marcar "sem comparaÃ§Ã£o"
- **Se mais de 50 sources** â†’ agregar cauda em "Outros"
- **Se CSV com encoding errado** â†’ tentar UTF-8 â†’ Latin-1 â†’ abortar

---

## Examples

### Example 1 â€” Happy path
**Input:** `generate-report /data/traffic.csv`
**Workflow:**
1. Abre CSV com 14 dias de dados, 8 sources
2. Top fonte: Google (5.2k visits, +12%)
3. Insight chave: novo source "ProductHunt" trouxe 800 visits

**Output:**
```markdown
# Weekly Report â€” 2026-04-19

**Total visits:** 12,340 (+8% vs semana anterior)

## Top 5 Sources
1. Google â€” 5,234 (42%)
2. Twitter â€” 2,108 (17%)
3. Direct â€” 1,890 (15%)
4. ProductHunt â€” 812 (7%) ðŸ†•
5. LinkedIn â€” 754 (6%)

## Insights
- ProductHunt Ã© nova fonte, 7% do trÃ¡fego total
- Twitter caiu 15% vs semana anterior â€” investigar
- Google cresceu +12%, provavelmente ganho de ranking
```

### Example 2 â€” Edge case (sem histÃ³rico)
**Input:** `generate-report /data/new-site.csv`
**Workflow:**
1. CSV tem sÃ³ 5 dias, nÃ£o dÃ¡ pra comparar semana
2. Retorna report sem seÃ§Ã£o de delta

**Output:**
```markdown
# Weekly Report â€” 2026-04-19

**Total visits:** 420 (primeira semana â€” sem comparaÃ§Ã£o)
...
```

---

## Dependencies

- **Libs:** Python stdlib (csv, datetime, pathlib)
- **Files:** `reports/template.md` (read-only)
- **Outras skills:** nenhuma

---

## Errors & Recovery

| Erro | Causa | Fix |
|------|-------|-----|
| `ColumnError: missing 'source'` | CSV sem coluna esperada | Verificar header do CSV |
| `UnicodeDecodeError` | Encoding nÃ£o-UTF8 | Salvar CSV como UTF-8 |
| `PermissionError: reports/` | Pasta sem write | `chmod +w reports/` |

---

## Notes

Template do report em `reports/template.md` pode ser customizado. Campos entre
`{{ }}` sÃ£o substituÃ­dos no step 9.

---

## Changelog

- v1.0 (2026-04-19): VersÃ£o inicial


---
*Créditos originais da metodologia: [Bruno Okamoto](https://github.com/okjpg)*
