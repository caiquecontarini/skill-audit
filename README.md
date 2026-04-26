# 🔍 Skill Audit — Auditoria de Qualidade para Skills de IA

> **Análise estática e garantia de qualidade para skills de agentes de IA.**
> Compatível com Claude Code, OpenClaw e qualquer agente baseado em MCP.

---

## 🎯 O Que Faz?

O **Skill Audit** é uma ferramenta de controle de qualidade que analisa o arquivo `SKILL.md` de qualquer skill e verifica se ela vai funcionar corretamente em diferentes modelos de linguagem.

---

## ✅ Os 10 Critérios de Auditoria

| # | Critério | O Que Verifica |
|:---|:---|:---|
| 1 | **Gatilho Claro** | A skill tem condições de ativação bem definidas? |
| 2 | **Frontmatter Válido** | YAML de metadados está correto? |
| 3 | **Instruções Inequívocas** | As ordens são claras e sem ambiguidade? |
| 4 | **Compatibilidade Cross-LLM** | Funciona com Claude, GPT e Gemini? |
| 5 | **Segurança** | Não expõe segredos ou dados sensíveis? |
| 6 | **Tamanho Otimizado** | Não é grande demais para o contexto? |
| 7 | **Saída Definida** | O formato de resposta está especificado? |
| 8 | **Dependências** | Todas as dependências externas estão documentadas? |
| 9 | **Exemplos** | Há exemplos práticos de uso? |
| 10 | **Fallback** | Comportamento em caso de erro está definido? |

---

## 🚀 Como Usar

```bash
# Instale a skill no seu agente
# Depois peça ao agente:
"Audite a skill em /caminho/para/SKILL.md"

# O agente retornará um relatório com:
# - Score de qualidade (0-100)
# - Problemas encontrados
# - Sugestões de melhoria
```

---

## 📊 Exemplo de Relatório

```
🔍 Relatório de Auditoria — deep-research/SKILL.md
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Score Geral: 87/100

✅ Gatilho: Definido corretamente
✅ Frontmatter: Válido
⚠️  Tamanho: 8.2KB (recomendado < 6KB)
❌ Fallback: Comportamento de erro não definido

💡 Recomendações:
1. Reduzir seção de exemplos em ~2KB
2. Adicionar bloco "Em caso de erro, faça X"
```

---

### 👨‍💻 Autor
[Caíque Contarini](https://github.com/caiquecontarini) — Visual Brain Ecosystem

---
*Qualidade é o padrão, não a exceção — Abril 2026*