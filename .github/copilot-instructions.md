# GitHub Copilot Instructions

Este arquivo fornece instruções ao GitHub Copilot quando trabalhando neste repositório.

## Instruções Gerais

- Sempre use `bun` em vez de `npm`
- Execute testes antes de commit: `bun run test && bun run lint`
- Prefira TypeScript sobre JavaScript
- Use componentes do Next.js App Router (não Pages Router)
- Sempre implemente tratamento de erros apropriado
- Siga os princípios de segurança listados abaixo

## Segurança

Ao trabalhar com código sensível, sempre considere:

### Database Security & RLS (Row Level Security)
- Analise políticas RLS do Supabase/PostgreSQL para gaps e bypasses
- Identifique políticas RLS ausentes em tabelas sensíveis
- Verifique erros de lógica que possam permitir acesso não autorizado
- Garanta que políticas cubram todas operações CRUD apropriadamente
- Certifique-se que service role keys não sejam expostas aos clientes
- Revise funções de database para vulnerabilidades de SQL injection
- Valide que `auth.uid()` e `auth.jwt()` sejam usados corretamente em políticas

### Next.js Security
- Server Components: Garanta que dados sensíveis não vazem para client components
- Server Actions: Valide input, verifique autorização, previna CSRF
- API Routes: Autenticação, rate limiting, validação de input
- Middleware: Verificações de auth apropriadas e lógica de redirect
- Variáveis de ambiente: Verifique que uso do prefixo `NEXT_PUBLIC_` é apropriado
- Não exponha dados sensíveis em page props ou initial state

### API Endpoint Security
- Verificação de autenticação em todas rotas protegidas
- Verificações de autorização (usuário só acessa seus próprios recursos)
- Validação e sanitização de input
- Rate limiting
- Tratamento de erros apropriado (sem stack traces ou info sensível)
- Configuração CORS apropriada
- Restrições de métodos HTTP

### Authentication & Authorization
- Segurança no gerenciamento de sessão
- Armazenamento e transmissão de tokens
- Políticas de senha e hashing
- Segurança em implementação OAuth
- Validação e expiração de JWT
- Prevenção de escalação de privilégios
- Implementação de controle de acesso baseado em roles

## Skills Especializadas

Use estas referências quando trabalhar com integrações específicas:

### PostHog - Analytics & Feature Flags
**Quando usar**: Implementar analytics, feature flags, session replay, A/B testing

Configuração recomendada:
- Use reverse proxy para evitar ad blockers
- Implemente event tracking com taxonomia consistente
- Configure feature flags para experiments
- Setup de session replay com controles de privacidade

Arquivos de referência:
- `.github/copilot-skills/posthog/nextjs-implementation.md` - Setup completo
- `.github/copilot-skills/posthog/event-taxonomy.md` - Naming conventions
- `.github/copilot-skills/posthog/feature-flags.md` - Feature flags e A/B tests

### SEO Technical
**Quando usar**: Otimizar SEO técnico, meta tags, structured data

Implementar:
- Sitemaps e robots.txt
- Meta tags e OpenGraph
- Structured data (JSON-LD)
- Performance (Core Web Vitals)
- Canonical URLs
- Next.js Metadata API

Arquivos de referência:
- `.github/copilot-skills/seo-technical/metadata.md`
- `.github/copilot-skills/seo-technical/structured-data.md`

### Marketing Copy
**Quando usar**: Escrever copy para landing pages, emails, CTAs

Framework:
- Elevated Direct Response
- Tom contrarian educator
- Hooks e CTAs otimizados
- Focus em benefícios, não features
- Storytelling orientado a resultados

Arquivos de referência:
- `.github/copilot-skills/marketing-copy/framework.md`

### UX Design
**Quando usar**: Design de interfaces, fluxos de usuário, componentes

Princípios:
- Princípios da era Jobs (Apple)
- Progressive disclosure
- Anticipatory design
- Simplicidade sobre complexidade
- Feedback visual imediato

Arquivos de referência:
- `.github/copilot-skills/ux-design/principles.md`

### Stripe - Pagamentos Internacionais
**Quando usar**: Implementar pagamentos, checkout, subscriptions

Implementar:
- Checkout sessions
- Webhooks com verificação de assinatura
- Subscriptions e billing
- Customer portal
- Testes em modo test antes de production

Arquivos de referência:
- `.github/copilot-skills/stripe/implementation.md`
- `.github/copilot-skills/stripe/webhooks.md`

### AbacatePay - Pagamentos PIX (Brasil)
**Quando usar**: Implementar pagamentos PIX para mercado brasileiro

Implementar:
- Billing sessions
- QR Code PIX
- Webhooks com validação
- Subscriptions
- Tratamento de status de pagamento

Arquivos de referência:
- `.github/copilot-skills/abacatepay/implementation.md`

### Cloudflare
**Quando usar**: Configurar DNS, email routing, R2 storage

Setup:
- DNS records (Clerk, Vercel)
- Email routing e redirects
- R2 buckets (CORS, public access, custom domains)
- Verificação de propagação

Arquivos de referência:
- `.github/copilot-skills/cloudflare/setup.md`

### Favicon
**Quando usar**: Gerar favicons e app icons para PWA

Gerar:
- Todos os tamanhos (16, 32, 180, 192, 512)
- Apple touch icon e Android chrome
- Web manifest para PWA
- Configuração de metadata Next.js

Arquivos de referência:
- `.github/copilot-skills/favicon/generation.md`

## Segurança - Auditoria Contínua

Após implementar features que lidam com dados sensíveis, sempre considere:
- APIs e endpoints
- Database e RLS
- Autenticação e autorização
- Exposição de dados
- Validação de input
- Rate limiting
- CORS e CSP headers

Referência completa: `.github/copilot-skills/security-auditor/checklist.md`

## Commit Guidelines

Ao criar commits:
- Use modo imperativo ("Add feature" não "Added feature")
- Seja conciso (1-2 sentenças) focando no "por quê" não apenas "o quê"
- Siga o estilo dos commits recentes do repositório
- Agrupe mudanças relacionadas em um único commit
- Não commite secrets (.env, credentials, API keys)

## Code Style

- Use TypeScript strict mode
- Prefira functional components e hooks
- Use async/await sobre promises .then()
- Implemente error boundaries para React
- Use Zod para validação de schemas
- Prefira server components quando possível
- Use "use client" apenas quando necessário
