# Instruções para GitHub Copilot

Estas são as diretrizes para trabalhar no repositório `tinyplate`.

## Preferências Gerais e Workflow

- **Runtime e Pacotes**: Sempre use `bun` em vez de `npm`.
- **Linguagem**: Prefira `TypeScript` sobre JavaScript.
- **Qualidade de Código**:
  - Antes de qualquer commit, garanta que os testes e linters passem: `bun run test && bun run lint`.
  - Mantenha o código limpo e idiomático.

## Base de Conhecimento (Contexto)

O projeto possui documentação detalhada na pasta `.github/` que você deve consultar quando solicitado:

### Skills (Funcionalidades Específicas)
Local: `.github/skills/`
Consulte o arquivo `SKILL.md` dentro de cada pasta para detalhes de implementação.

- **PostHog** (`.github/skills/posthog`): Analytics, feature flags, session replay.
- **SEO Technical** (`.github/skills/seo-technical`): Sitemaps, structured data, vitals.
- **Marketing Copy** (`.github/skills/marketing-copy`): Framework "Elevated Direct Response".
- **UX Design** (`.github/skills/ux-design`): Princípios Apple/Jobs, progressive disclosure.
- **Stripe** (`.github/skills/stripe`): Checkout, webhooks, subscriptions (Internacional).
- **AbacatePay** (`.github/skills/abacatepay`): PIX, billing sessions (Brasil).
- **Cloudflare** (`.github/skills/cloudflare`): DNS, Email routing, R2.
- **Favicon** (`.github/skills/favicon`): Geração de icons e manifest.

### Workflows e Comandos
Local: `.github/commands/`
Consulte estes arquivos para entender o processo de release e git workflow do time.
- `ship.md`: Workflow de deploy rápido (Commit + Push + PR).
- `pr.md`, `commit.md`, `push.md`: Padrões específicos.

### Agentes Especializados
Local: `.github/agents/`
- `security-auditor.md`: Checklist para auditoria de segurança.

## Segurança

Ao escrever código relacionado a backend, API ou dados:
- Auditar endpoints e segurança de API.
- Verificar RLS (Row Level Security) e acesso ao banco de dados.
- Validar Autenticação e Autorização.
- Prevenir exposição acidental de dados sensíveis.
