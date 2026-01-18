# Security Auditor - Checklist Completo

Este checklist serve como referência para auditoria de segurança em aplicações web modernas, especialmente projetos Next.js com Supabase.

## Database Security & RLS (Row Level Security)

### Políticas RLS
- [ ] Todas as tabelas sensíveis têm políticas RLS habilitadas
- [ ] Políticas cobrem todas operações CRUD (SELECT, INSERT, UPDATE, DELETE)
- [ ] Nenhuma política usa `true` como condição em tabelas sensíveis
- [ ] Políticas usam `auth.uid()` corretamente para verificar ownership
- [ ] Políticas usam `auth.jwt()` corretamente para verificar roles/claims
- [ ] Verificar lógica de políticas para bypasses (ex: condições OR mal estruturadas)
- [ ] Service role keys nunca são expostas ao cliente
- [ ] Políticas não têm race conditions ou inconsistências

### Database Functions
- [ ] Funções usam parameterização adequada (sem concatenação de strings SQL)
- [ ] `SECURITY DEFINER` é usado apenas quando necessário
- [ ] `SECURITY INVOKER` é preferido quando apropriado
- [ ] Input de funções é validado e sanitizado
- [ ] Funções não vazam informações sensíveis em erros

### Schema & Data
- [ ] Dados sensíveis são criptografados em repouso quando apropriado
- [ ] Passwords nunca são armazenados em plain text
- [ ] Índices não expõem informações sensíveis
- [ ] Triggers não criam vulnerabilidades de tempo de execução

## Next.js Security

### Server vs Client Components
- [ ] Dados sensíveis nunca são passados para Client Components
- [ ] Environment variables sensíveis não usam prefixo `NEXT_PUBLIC_`
- [ ] Server Components não expõem dados em props serializados
- [ ] Client Components não fazem queries diretas ao database

### Server Actions
- [ ] Todas Server Actions validam input com Zod ou similar
- [ ] Server Actions verificam autenticação antes de executar lógica
- [ ] Server Actions verificam autorização (user owns resource)
- [ ] CSRF protection está habilitada (default no Next.js 13+)
- [ ] Rate limiting está implementado para actions sensíveis
- [ ] Erros não vazam stack traces ou informações sensíveis

### API Routes
- [ ] Todas rotas protegidas verificam autenticação
- [ ] Rotas verificam autorização (user can access resource)
- [ ] Input é validado e sanitizado
- [ ] Rate limiting está configurado
- [ ] Erros retornam mensagens genéricas (não stack traces)
- [ ] CORS está configurado apropriadamente
- [ ] Métodos HTTP são restritos ao necessário

### Middleware
- [ ] Auth checks são implementados corretamente
- [ ] Redirect loops são prevenidos
- [ ] Middleware não expõe informações sensíveis em headers
- [ ] Session tokens são validados corretamente

### Headers & Security
- [ ] Content Security Policy (CSP) configurada
- [ ] X-Frame-Options configurado
- [ ] X-Content-Type-Options: nosniff
- [ ] Referrer-Policy configurada
- [ ] Permissions-Policy configurada
- [ ] HTTPS é enforced em produção

## Authentication & Authorization

### Session Management
- [ ] Sessions expiram após período apropriado
- [ ] Sessions são invalidadas no logout
- [ ] Refresh tokens são armazenados com segurança
- [ ] Session fixation attacks são prevenidos

### Token Security
- [ ] JWTs são validados (assinatura, expiração, issuer)
- [ ] Tokens sensíveis nunca são expostos ao localStorage
- [ ] Cookies usam flags apropriadas (httpOnly, secure, sameSite)
- [ ] Tokens são revogados quando necessário

### Password Security
- [ ] Senhas são hasheadas com algoritmo moderno (bcrypt, argon2)
- [ ] Política de senha forte é enforced
- [ ] Password reset usa tokens seguros e com expiração
- [ ] Brute force attacks têm rate limiting

### OAuth & Social Login
- [ ] State parameter é usado para prevenir CSRF
- [ ] Redirect URIs são whitelist validados
- [ ] Scopes solicitados são mínimos necessários
- [ ] Tokens de terceiros são armazenados com segurança

### Authorization
- [ ] Role-based access control (RBAC) implementado corretamente
- [ ] Privilege escalation é prevenida
- [ ] Object-level authorization é verificada
- [ ] Function-level authorization é verificada

## API Endpoint Security

### Input Validation
- [ ] Todos inputs são validados com schema (Zod, Joi, etc)
- [ ] Validação acontece no servidor, não apenas cliente
- [ ] Tipos de arquivo em uploads são validados
- [ ] Tamanhos de upload têm limites apropriados
- [ ] JSON parsing tem limites de tamanho
- [ ] SQL/NoSQL injection é prevenida via parameterização

### Output Encoding
- [ ] Dados são sanitizados antes de renderizar (XSS prevention)
- [ ] JSON responses não incluem dados sensíveis desnecessários
- [ ] Error messages são genéricos para usuários
- [ ] Stack traces nunca são expostos em produção

### Rate Limiting
- [ ] Endpoints sensíveis têm rate limiting
- [ ] Login/signup têm proteção contra brute force
- [ ] API keys têm rate limits apropriados
- [ ] Rate limits são por IP e/ou user

## Data Exposure

### Client-Side
- [ ] Secrets nunca estão em código cliente
- [ ] API keys nunca estão expostas no frontend
- [ ] Console.logs sensíveis removidos em produção
- [ ] Source maps desabilitados em produção (ou protegidos)

### Server-Side
- [ ] .env files não estão commitados
- [ ] Secrets são gerenciados via environment variables
- [ ] Logs não incluem dados sensíveis (passwords, tokens)
- [ ] Database queries logadas não incluem dados sensíveis

### Network
- [ ] HTTPS é usado em todas comunicações
- [ ] Cookies sensíveis usam flag `secure`
- [ ] Headers não vazam informações de versão
- [ ] GraphQL introspection desabilitada em produção

## Third-Party Integrations

### Payment Providers (Stripe, AbacatePay)
- [ ] Webhooks validam assinaturas
- [ ] API keys são secrets (não public keys expostas incorretamente)
- [ ] Test mode é usado para desenvolvimento
- [ ] Idempotency keys usados onde apropriado

### Analytics (PostHog, etc)
- [ ] PII não é enviado sem consent
- [ ] Reverse proxy configurado para evitar bloqueio
- [ ] Session replay respeita privacidade
- [ ] GDPR compliance se aplicável

### Email Services
- [ ] SPF, DKIM, DMARC configurados
- [ ] Email injection prevenida
- [ ] Rate limiting em envio de emails
- [ ] Unsubscribe links funcionais

## Infrastructure & Deployment

### Environment Configuration
- [ ] Production usa variáveis diferentes de development
- [ ] Debug mode desabilitado em produção
- [ ] Error reporting não expõe código-fonte
- [ ] Health check endpoints não vazam informações

### Dependencies
- [ ] Dependências são auditadas regularmente (`bun audit`)
- [ ] Vulnerabilidades conhecidas são resolvidas
- [ ] Apenas packages necessários são instalados
- [ ] Lock files são commitados

### Monitoring
- [ ] Failed login attempts são logados
- [ ] Suspicious activity é alertada
- [ ] Error rates são monitorados
- [ ] Security events têm alertas

## Priority Guide

### CRITICAL - Fix Immediately
- Authentication bypass
- SQL/NoSQL injection
- RLS policy bypass
- Exposed credentials/secrets
- Remote code execution

### HIGH - Fix Before Deploy
- Missing authorization checks
- XSS vulnerabilities
- Insecure session management
- Weak cryptography
- Mass assignment

### MEDIUM - Fix Soon
- Missing rate limiting
- Information disclosure
- CSRF on non-critical actions
- Weak password policy
- Missing security headers

### LOW - Hardening
- Security header improvements
- Logging improvements
- Error message improvements
- Documentation of security measures
- Security testing additions

## Automated Tools

Considere usar estas ferramentas em CI/CD:
- `bun audit` - vulnerability scanning
- ESLint security plugins
- OWASP ZAP - dynamic security testing
- Snyk - dependency scanning
- SonarQube - code quality & security

## Resources

- OWASP Top 10: https://owasp.org/www-project-top-ten/
- OWASP API Security: https://owasp.org/www-project-api-security/
- Next.js Security: https://nextjs.org/docs/app/building-your-application/configuring/security
- Supabase RLS: https://supabase.com/docs/guides/auth/row-level-security
