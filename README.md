# skill-audit

Static analysis for Claude Code / Agent SDK skills. Audits `SKILL.md` against
10 QA checks to verify a skill will trigger and execute correctly across LLMs
(Claude, GPT, Gemini).

Built from an audit of 119 production skills (score went from 5.9 → 10.0/10).

---

## Why this exists

Skills written for Claude pre-4.5 often work only in the author's context —
they have thin descriptions, vague workflows, no negative boundaries, no evals.
When you try to run them in:

- Claude 4.6+ (tighter trigger heuristics)
- GPT-5 / GPT-4o (different trigger semantics)
- Gemini Pro (stricter about imperative language)
- External agent frameworks

...the skill silently fails to trigger, or triggers in wrong contexts.

`skill-audit` diagnoses these issues before you ship.

---

## Install

Requires Python 3.8+. No dependencies (stdlib only).

```bash
git clone https://github.com/okjpg/skill-audit
cd skill-audit
```

Or install as a Claude Code skill:

```bash
cp -r skill-audit ~/.claude/skills/
```

Or via `gh skill` (when your CLI supports it):

```bash
gh skill install okjpg/skill-audit
```

---

## Usage

### Audit a single skill

```bash
python3 scripts/audit.py ./my-skill/SKILL.md
```

### Audit a whole folder (recursive)

```bash
python3 scripts/audit.py ~/.claude/skills/
```

### JSON output (for tooling)

```bash
python3 scripts/audit.py ~/.claude/skills/ --json -o audit.json
```

---

## Output

```
skill-audit: 12 skills analisadas
  Score médio: 7.1/10
  Passing (≥7): 4/12 (33%)

Report: audit-results.md
```

The markdown report includes:
- Score distribution
- Issues ranked by frequency
- Skills ranked worst → best
- Detailed per-skill breakdown

---

## The 10 QA checks

1. **Name in kebab-case** matching folder name
2. **Description** with 50+ words, third person, 5+ trigger phrases, negative boundaries
3. **Workflow** steps are single, imperative, unambiguous
4. **2+ examples** with real input → real output
5. **Edge cases** covered (3+ conditions with specific action)
6. **Output format** explicitly defined
7. **Zero vague language** (`handle appropriately`, `as needed`, etc — banned)
8. **Negative boundaries** in body (`## When NOT to Use`)
9. **Zero hardcoded secrets** (API keys, tokens)
10. **`evals/evals.json`** with 2+ cases

Full spec: [`references/qa-checklist.md`](references/qa-checklist.md).

---

## Before / After

See [`examples/before.md`](examples/before.md) (score 3/10) vs
[`examples/after.md`](examples/after.md) (score 10/10) — same skill, same
behavior, but one triggers reliably in any LLM.

---

## Template

[`templates/SKILL-TEMPLATE.md`](templates/SKILL-TEMPLATE.md) — copy-paste
skeleton for new V3-compliant skills.

---

## When NOT to use skill-audit

- Creating a skill from scratch — use [`criar-skill`](https://github.com/okjpg/skill-creator) instead
- Executing the skill being audited — run it directly
- Security audit (OWASP, production secrets) — this tool is for structural
  quality, not security posture

---

## Limitations

- Does not audit semantic quality of the workflow (whether the steps make sense
  for your domain)
- Static analysis only — does not execute the skill
- Trigger phrase heuristics can false-positive on dense technical descriptions.
  Review borderline scores (6-7) manually.

---

## Built by

[Bruno Okamoto](https://github.com/okjpg) — part of the
[Pixel AI Hub](https://pixel.ai.br) curriculum.

Related:
- [`second-brain-amora`](https://github.com/okjpg/second-brain-amora) — persistent memory system for Claude Code
- [`skill-creator`](https://github.com/okjpg/skill-creator) — generate new skills from workflows

---

## License

MIT.
