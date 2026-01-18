# tinyplate

Boilerplate de skills para GitHub Copilot em projetos Next.js.

## O que é isso?

Uma coleção de skills e instruções pré-configuradas para GitHub Copilot, prontas para usar em qualquer projeto Next.js. Também inclui a estrutura original para Claude Code (`.claude/`) para máxima compatibilidade.

## Como Usar

### Para GitHub Copilot (VS Code)

1. Clone o repositório:

```bash
git clone git@github.com:andrehfp/tinyplate.git
cd tinyplate
```

2. Copie a pasta `.github/` para seu projeto:

```bash
cp -r .github/ /caminho/do/seu/projeto/
```

3. Abra o projeto no VS Code com GitHub Copilot ativado

4. As instruções em `.github/copilot-instructions.md` serão automaticamente lidas pelo Copilot

5. Use as skills mencionando-as nas suas conversas:
   - "Implementar PostHog analytics com reverse proxy"
   - "Configurar SEO técnico com sitemaps"
   - "Escrever copy usando framework Direct Response"
   - "Integrar Stripe com webhooks"
   - "Auditar segurança do código"

### Para Claude Code (CLI)

1. Clone o repositório:

```bash
git clone git@github.com:andrehfp/tinyplate.git
cd tinyplate
```

2. Copie a pasta `.claude/` para seu projeto:

```bash
cp -r .claude/ /caminho/do/seu/projeto/
```

3. Abra o Claude Code no seu projeto:

```bash
cd /caminho/do/seu/projeto
claude
```

4. Use as skills:

```bash
/posthog        # Analytics & Feature Flags
/seo-technical  # SEO para Next.js
/marketing-copy # Copywriting Direct Response
/ux-design      # UX Design
/stripe         # Pagamentos Stripe
/abacatepay     # Pagamentos PIX
```

## Skills Incluídas

| Skill | Descrição |
|-------|-----------|
| `posthog` | Analytics, feature flags, session replay com reverse proxy |
| `seo-technical` | SEO técnico: sitemaps, meta tags, structured data |
| `marketing-copy` | Copy usando Elevated Direct Response framework |
| `ux-design` | UX com princípios Jobs-era Apple |
| `stripe` | Checkout, webhooks, subscriptions, customer portal |
| `abacatepay` | Pagamentos PIX para o mercado brasileiro |
| `cloudflare` | DNS, email routing, R2 storage |
| `favicon` | Geração de favicons e app icons para PWA |
| `security-auditor` | Auditoria de segurança para APIs, database, auth |

## Estrutura

### GitHub Copilot
```
.github/
├── copilot-instructions.md    # Instruções principais
├── copilot-skills/
│   ├── posthog/
│   │   ├── README.md
│   │   └── references/
│   ├── seo-technical/
│   ├── marketing-copy/
│   ├── ux-design/
│   ├── stripe/
│   ├── abacatepay/
│   ├── cloudflare/
│   ├── favicon/
│   └── security-auditor/
└── workflows/
    ├── commit-push.yml
    ├── create-pr.yml
    └── ship.yml
```

### Claude Code
```
.claude/
├── CLAUDE.md              # Instruções base
├── settings.local.json    # Configurações do Claude Code
├── skills/
│   ├── posthog/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── seo-technical/
│   ├── marketing-copy/
│   ├── ux-design/
│   ├── stripe/
│   ├── favicon/
│   └── security-auditor/
└── workflows/
    ├── commit-push.yml
    ├── create-pr.yml
    └── ship.yml
```

### Claude Code
```
.claude/
├── CLAUDE.md              # Instruções base
├── settings.local.json    # Configurações do Claude Code
├── skills/
│   ├── posthog/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── seo-technical/
│   ├── marketing-copy/
│   ├── ux-design/
│   ├── stripe/
│   ├── abacatepay/
│   ├── cloudflare/
│   └── favicon/
├── agents/
│   └── security-auditor.md
└── commands/
    ├── commit.md
    ├── push.md
    ├── pr.md
    └── ship.md
```

## GitHub Actions Workflows

O repositório inclui workflows automatizados:

| Workflow | Descrição |
|----------|-----------|
| `commit-push.yml` | Stage e commit de todas as mudanças + push |
| `create-pr.yml` | Cria Pull Request para a branch atual |
| `ship.yml` | Commit + Push + PR em um único workflow |

Para usar, vá em Actions > escolha o workflow > Run workflow

## Personalização

### GitHub Copilot

Edite `.github/copilot-instructions.md` para modificar instruções gerais, ou adicione/edite arquivos em `.github/copilot-skills/` para skills específicas.

### Claude Code

1. Crie uma pasta em `.claude/skills/nome-da-skill/`
2. Adicione um arquivo `SKILL.md` com as instruções
3. Opcionalmente, adicione uma pasta `references/` com documentação

## Requisitos

### Para GitHub Copilot
- [Visual Studio Code](https://code.visualstudio.com/)
- [GitHub Copilot](https://github.com/features/copilot) subscription
- GitHub Copilot extension instalada no VS Code

### Para Claude Code
- [Claude Code CLI](https://claude.ai/code)
- Conta Anthropic com acesso ao Claude Code

## Licença

MIT
