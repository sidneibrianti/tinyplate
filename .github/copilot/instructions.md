# GitHub Copilot Instructions

Este arquivo fornece instruções ao GitHub Copilot quando trabalhando neste repositório.

## Instruções Gerais

- Sempre use `bun` em vez de `npm`
- Execute testes antes de commit: `bun run test && bun run lint`
- Prefira TypeScript sobre JavaScript

## Prompts Disponíveis

### Skills de Integração

| Prompt | Descrição |
|--------|-----------|
| `posthog.prompt.md` | Analytics, feature flags, session replay |
| `seo-technical.prompt.md` | SEO técnico para Next.js |
| `marketing-copy.prompt.md` | Copywriting Direct Response |
| `ux-design.prompt.md` | UX Design (princípios Apple) |
| `stripe.prompt.md` | Pagamentos internacionais |
| `abacatepay.prompt.md` | Pagamentos PIX (Brasil) |
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

## Como Usar os Prompts

No VS Code com GitHub Copilot Chat, use `@workspace` seguido de `/` para acessar os prompts customizados, ou referencie os arquivos diretamente.

### PostHog - Analytics & Feature Flags
Implementa analytics com:
- Event tracking
- Feature flags
- Session replay
- Reverse proxy para evitar ad blockers

### SEO Technical
Configura SEO completo:
- Sitemaps e robots.txt
- Meta tags e OpenGraph
- Structured data (JSON-LD)
- Performance (Core Web Vitals)

### Marketing Copy
Escreve copy usando:
- Framework Elevated Direct Response
- Tom contrarian educator
- Hooks e CTAs otimizados

### UX Design
Design de UX com:
- Princípios da era Jobs (Apple)
- Progressive disclosure
- Anticipatory design

### Stripe
Integração de pagamentos:
- Checkout sessions
- Webhooks
- Subscriptions
- Customer portal

### AbacatePay
Pagamentos PIX para Brasil:
- Billing sessions
- QR Code PIX
- Webhooks
- Subscriptions

### Cloudflare
Gerenciamento de infraestrutura:
- DNS records
- Email routing
- R2 storage
- Vercel integration

### Favicon
Geração de ícones:
- Favicons para browser
- Apple touch icons
- PWA icons
