---
name: your-skill-name
description: >
  [50+ palavras em TERCEIRA PESSOA. Descrever o que a skill faz + quando disparar.
  Listar 5+ TRIGGER PHRASES explÃ­citas que o usuÃ¡rio realmente digitaria â€”
  variaÃ§Ãµes de fraseado, com e sem a palavra "skill", formal e informal.
  Incluir NEGATIVE BOUNDARIES no fim: "NÃƒO use para X, Y, Z." Quanto mais
  especÃ­fico, maior a chance de acionar corretamente em Claude/GPT/Gemini.
  Exemplos: "faz X", "cria Y", "processa Z", "/nome-da-skill", "me ajuda com W".
  NÃƒO use para: [caso parecido 1], [caso parecido 2], [caso parecido 3].]
type: skill
category: your-category
status: ATIVO
version: 1.0
created: YYYY-MM-DD
last_reviewed: YYYY-MM-DD
estimated_time: 5min
model_compatible: [claude-sonnet-4, claude-opus-4, gpt-5, gpt-4o, gemini-pro]
---

# Your Skill Name

[Overview escrito para o LLM (nÃ£o pra humano): o que a skill faz, qual input
espera, qual output produz, em que contexto dispara. 3-5 linhas.]

---

## When to Use

Aciona quando:
- [CenÃ¡rio 1 â€” especÃ­fico, nÃ£o genÃ©rico]
- [CenÃ¡rio 2 â€” com variaÃ§Ã£o de fraseado]
- [CenÃ¡rio 3 â€” caso automÃ¡tico/cron se aplicÃ¡vel]

Exemplos literais de mensagens que devem disparar:
- "[exemplo real 1]"
- "[exemplo real 2]"
- "[exemplo real 3]"

## When NOT to Use

NÃƒO aciona se:
- [Caso confundÃ­vel 1] â†’ usar skill `{alternativa-1}`
- [Caso confundÃ­vel 2] â†’ usar skill `{alternativa-2}`
- [Caso fora de escopo] â†’ [o que fazer em vez disso]

---

## Inputs

| ParÃ¢metro | Tipo | ObrigatÃ³rio | DescriÃ§Ã£o |
|-----------|------|-------------|-----------|
| input_1 | string | âœ… | [o que esperar â€” formato, range, exemplo] |
| input_2 | string | âŒ | [opcional, default: X] |

## Outputs

| Campo | Tipo | DescriÃ§Ã£o |
|-------|------|-----------|
| output_1 | string | [formato exato] |
| output_2 | file | [path absoluto se gera arquivo] |

Formato de entrega: [markdown no chat / arquivo em {path} / etc]

---

## Workflow

Executar na ordem exata. Cada passo Ã© UMA aÃ§Ã£o imperativa.

1. **[Verbo imperativo] [objeto]** â€” [comando exato se houver]
   ```bash
   comando exato aqui
   ```
2. **SE [condiÃ§Ã£o explÃ­cita] â†’ [aÃ§Ã£o]**
   SENÃƒO â†’ [aÃ§Ã£o alternativa]
3. **[Ler / Extrair / Perguntar / Executar]** [o quÃª]
4. **Validar** que [resultado esperado bate com critÃ©rio X]
5. **Entregar** output no formato [especificado]

### Regras do workflow

- Voz imperativa obrigatÃ³ria: "Ler arquivo X", NÃƒO "O arquivo X deve ser lido"
- Condicionais explÃ­citas: "SE [condiÃ§Ã£o] â†’ [aÃ§Ã£o]", NÃƒO "quando apropriado"
- Linguagem BANIDA: "handle appropriately", "format nicely", "as needed",
  "quando relevante", "se fizer sentido", "adaptar conforme contexto"

---

## Edge Cases

- **Se [input faltando]** â†’ [aÃ§Ã£o: perguntar / default / abortar]
- **Se [formato errado]** â†’ [aÃ§Ã£o]
- **Se [API/dependÃªncia fora]** â†’ [fallback ou erro exato]
- **Se [condiÃ§Ã£o ambÃ­gua]** â†’ [regra de desempate]

---

## Examples

### Example 1 â€” Happy path
**Input real:** "[mensagem exata que o usuÃ¡rio digitaria]"
**Workflow executado:**
1. [passo com valores reais]
2. [passo]
3. [passo]
**Output real:**
```
[output literal, nÃ£o descriÃ§Ã£o abstrata]
```

### Example 2 â€” Edge case
**Input:** "[input quebrado / ambÃ­guo / parcial]"
**Workflow executado:**
1. [detecta problema]
2. [aplica fallback]
**Output:**
```
[como skill reage]
```

---

## Dependencies

- **APIs:** [Notion, Buffer, etc â€” "nenhuma" se vazio]
- **MCPs:** [nome do MCP server]
- **Env vars:** [VAR_NAME â€” onde estÃ¡ armazenada]
- **Files:** [paths absolutos que a skill lÃª/escreve]
- **Outras skills:** [chamadas por esta â€” ou "nenhuma"]

---

## Errors & Recovery

| Erro | Causa provÃ¡vel | Fix |
|------|----------------|-----|
| [msg exata] | [por que acontece] | [comando/aÃ§Ã£o pra resolver] |
| Timeout | [qual step] | [retry N vezes / fallback Y] |
| API 4xx | [creds expiradas / rate limit] | [revalidar / aguardar] |

---

## Notes

[ObservaÃ§Ãµes: limitaÃ§Ãµes conhecidas, decisÃµes de design, contexto histÃ³rico,
gotchas que o LLM precisa saber mas nÃ£o sÃ£o parte do workflow.]

---

## Changelog

- v1.0 (YYYY-MM-DD): VersÃ£o inicial.

---

## Estrutura de pastas

```
your-skill-name/
â”œâ”€â”€ SKILL.md              â† este arquivo
â””â”€â”€ evals/
    â””â”€â”€ evals.json        â† mÃ­nimo 2 evals: 1 happy path + 1 edge case
```

Opcionais:
- `references/` â€” docs de apoio, specs, guias
- `scripts/` â€” cÃ³digo executÃ¡vel chamado pelo workflow
- `assets/` â€” templates, fontes, arquivos estÃ¡ticos

### evals/evals.json (obrigatÃ³rio)

```json
{
  "skill_name": "your-skill-name",
  "evals": [
    {
      "id": 1,
      "prompt": "input real do Example 1",
      "expected_output": "descriÃ§Ã£o concreta do output esperado"
    },
    {
      "id": 2,
      "prompt": "input real do Example 2 â€” edge case",
      "expected_output": "como skill deve reagir"
    }
  ]
}
```


---
*Créditos originais da metodologia: [Bruno Okamoto](https://github.com/okjpg)*
