# Feature: auth

**Keywords:** autenticação, login, jwt, token, refresh-token, senha, reset-password, sessão
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/AuthController.cs
  - erp-back/-2-Application/Services/authService.cs
  - erp-back/-4-WebApi/Middlewares/JwtMiddleware.cs
  - erp-back/-5-CrossCutting/IoC/JwtConfiguration.cs
  - erp-back/-5-CrossCutting/Services/TokenService.cs
  - erp-back/-5-CrossCutting/Services/PasswordHashService.cs
  - erp-back/-1-Domain/Entities/userToken.cs
  - erp-front/src/services/authService.ts
  - erp-front/src/contexts/AuthContext.tsx
  - erp-front/src/pages/auth/
**Resumo:** Autenticação por JWT (login por e-mail/telefone/CPF + senha), emissão/renovação de tokens, fluxo de recuperação de senha, e o endpoint que entrega o mapa de permissões do usuário na empresa atual.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| — | — | — | _(nenhuma SPEC ainda — feature documentada por engenharia reversa do estado atual)_ |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/auth` (rotas públicas, fora do gate multi-tenant): `register`, `login`, `logout`, `forgot-password`, `reset-password`, `refresh-token` e `GET permissions` (autenticado). `AuthService` valida credenciais contra `User.PasswordHash` (via `PasswordHashService`) e emite JWT via `TokenService`; tokens/refresh-tokens são persistidos em `UserToken` (campos `Token`, `RefreshToken`, `ExpiresAt`, `IsRevoked`). O reset de senha usa `User.ResetToken` + `ResetTokenExpiresAt`.

Duas camadas de autenticação coexistem: o `JwtBearer` padrão (configurado em `JwtConfiguration.AddJwtAuthentication`, habilita `[Authorize]`) **e** o `JwtMiddleware` custom, que decodifica o bearer e injeta `UserId`/`Email`/`Phone`/`Cpf` em `HttpContext.Items` para uso pelos controllers/`BaseController`. `GET api/auth/permissions` retorna o `UserPermissions` (isAdmin, isSystemRole, modules) consumido pelo `PermissionContext` do frontend.

No frontend, `AuthContext` orquestra login → carregar empresas → selecionar empresa → carregar permissões; o token, o usuário e a empresa selecionada ficam em `localStorage` e são injetados pelo interceptor do `services/api.ts`.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **JWT com middleware custom além do JwtBearer padrão** (origem: estado pré-SPEC, 2026-06-08 20:15) — `JwtMiddleware` popula `HttpContext.Items` (UserId/Email/Phone/Cpf) para o `BaseController`, enquanto o `JwtBearer` cuida do `[Authorize]`. Redundância proposital: claims ficam acessíveis sem reparsear o token.
- **Tokens persistidos em `UserToken`** (origem: estado pré-SPEC, 2026-06-08 20:15) — permite revogação (`IsRevoked`) e refresh-token com expiração própria.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Segredo JWT tem fallback hardcoded** (2026-06-08 20:15) — se `Jwt:Secret` não estiver configurado, `JwtMiddleware`/`JwtConfiguration` usam `"your-very-secure-secret-key-min-32-chars"`. Conveniente para dev local, **risco de segurança em produção** — `Jwt:Secret` DEVE ser setado no ambiente Railway.
- **Login aceita múltiplos identificadores** (2026-06-08 20:15) — o campo é `credential` (e-mail, telefone ou CPF), não apenas e-mail.
- **`api/auth/*` é rota excluída do `CompanyContextMiddleware`** — não exige header `X-Company-Id`. Ver [[empresas-multitenancy]].

## Estado congelado (se houver)

_(nenhum)_
