# tinyplate

Boilerplate de prompts do GitHub Copilot para projetos Next.js.

## O que é isso?

Uma coleção de prompts pré-configurados para o GitHub Copilot, prontos para usar em qualquer projeto.

## Como Usar

### 1. Clone o repositório

```bash
git clone git@github.com:andrehfp/tinyplate.git
cd tinyplate
```

### 2. Copie a pasta `.github/copilot/` para seu projeto

```bash
cp -r .github/copilot/ /caminho/do/seu/projeto/.github/
```

### 3. Use os prompts no VS Code

No VS Code com GitHub Copilot Chat, você pode referenciar os prompts usando `@workspace` ou abrindo os arquivos diretamente.

## Prompts Incluídos

### Skills de Integração

| Prompt | Descrição |
|--------|-----------|
| `posthog.prompt.md` | Analytics, feature flags, session replay com reverse proxy |
| `seo-technical.prompt.md` | SEO técnico: sitemaps, meta tags, structured data |
| `marketing-copy.prompt.md` | Copy usando Elevated Direct Response framework |
| `ux-design.prompt.md` | UX com princípios Jobs-era Apple |
| `stripe.prompt.md` | Checkout, webhooks, subscriptions, customer portal |
| `abacatepay.prompt.md` | Pagamentos PIX para o mercado brasileiro |
| `cloudflare.prompt.md` | DNS, domínios, email routing, R2 storage |
| `favicon.prompt.md` | Geração de favicons e app icons |

### Commands de Git

| Prompt | Descrição |
|--------|-----------|
| `commit.prompt.md` | Stage all changes e cria commit com mensagem AI |
| `push.prompt.md` | Push da branch atual para o remote |
| `pr.prompt.md` | Cria Pull Request no GitHub |
| `ship.prompt.md` | Commit + Push + PR em um só comando |

### Agents Especializados

| Prompt | Descrição |
|--------|-----------|
| `security-auditor.prompt.md` | Auditoria de segurança para APIs, database, auth |

## Estrutura

```
.github/
└── copilot/
    ├── instructions.md        # Instruções base do Copilot
    └── prompts/
        ├── posthog.prompt.md
        ├── seo-technical.prompt.md
        ├── marketing-copy.prompt.md
        ├── ux-design.prompt.md
        ├── stripe.prompt.md
        ├── abacatepay.prompt.md
        ├── cloudflare.prompt.md
        ├── favicon.prompt.md
        ├── commit.prompt.md
        ├── push.prompt.md
        ├── pr.prompt.md
        ├── ship.prompt.md
        └── security-auditor.prompt.md
```

## Personalização

### Adicionar novos prompts

1. Crie um arquivo em `.github/copilot/prompts/nome-do-prompt.prompt.md`
2. Adicione o frontmatter com `mode`, `description` e `tools`
3. Escreva as instruções do prompt

### Formato do Prompt

```markdown
---
mode: agent
description: "Descrição do que o prompt faz"
tools: ["editFiles", "codebase", "terminal", "fetch"]
---

# Título do Prompt

Instruções detalhadas aqui...
```

### Modificar prompts existentes

Edite os arquivos `.prompt.md` conforme necessário.

## Migração do Claude Code

Este repositório foi convertido do formato Claude Code (`.claude/`) para o formato GitHub Copilot (`.github/copilot/`).

| Claude Code | GitHub Copilot |
|-------------|----------------|
| `.claude/CLAUDE.md` | `.github/copilot/instructions.md` |
| `.claude/skills/*/SKILL.md` | `.github/copilot/prompts/*.prompt.md` |
| `.claude/commands/*.md` | `.github/copilot/prompts/*.prompt.md` |
| `.claude/agents/*.md` | `.github/copilot/prompts/*.prompt.md` |

## Requisitos

- [VS Code](https://code.visualstudio.com/)
- [GitHub Copilot Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [GitHub Copilot Chat Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)

## Licença

MIT
