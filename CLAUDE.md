# CLAUDE.md — Meu Gestor

---

## AVISO IMPERATIVO PRA IA — LER PRIMEIRO

Este é um projeto **SPEC-driven v3**. A documentação em `docs/` **não é para humanos** — é para você (IA) ter contexto rico ao retomar, continuar ou mexer em qualquer parte do projeto.

- **PROIBIDO** ler arquivos Nível 1+ (features, SPECs) sem **confirmação explícita do usuário**.
- **OBRIGATÓRIO** seguir o protocolo de escalação do [docs/RULES.md](docs/RULES.md) §4.
- **PROIBIDO ABSOLUTO** decidir sozinho que um critério de aceite da SPEC é "OK pra deixar pra depois", "gap aceitável", "fica pra SPEC futura" ou qualquer racionalização equivalente. **Só o usuário pode aceitar entrega incompleta.** Ver [docs/RULES.md R.6.2](docs/RULES.md).

### Primeira ação obrigatória em QUALQUER sessão

Antes de qualquer código, qualquer resposta substantiva:

1. Ler este `CLAUDE.md`
2. Ler [docs/RULES.md](docs/RULES.md) — processo completo (fonte da verdade)
3. Ler [docs/INDEX.md](docs/INDEX.md) — mapa de features
4. Listar `docs/active/` (SPECs ativas na branch local — pode estar vazio em `main`)
5. Confirmar por texto que leu:
   > *"Li CLAUDE.md, docs/RULES.md v3, docs/INDEX.md (X features). Active local: [lista]. Como posso ajudar?"*
6. Aguardar o prompt do usuário e seguir o protocolo de escalação do RULES §4.

**Se houver SPEC ativa e o prompt conectar ao escopo dela:** pode ler `docs/active/SPEC-X/main.md` (contrato) após confirmar classificação. **NÃO** leia `state.md` ou `memory.md` da SPEC sem ANTES informar o usuário e pedir confirmação.

### Resumo do processo (fonte da verdade: [docs/RULES.md](docs/RULES.md))

1. **Documentação é para IA**, não humanos. Exceção: `main.md` (contrato humano-validado).
2. **SPEC** = pasta `SPEC-<YYYYMMDD-HHMM>-<slug>/` com `main.md` + `state.md` + `memory.md`.
3. **Toda SPEC se vincula a 1+ feature** (criando se não existir — com confirmação explícita do usuário por R.13).
4. **Invariante:** `docs/active/` em `main` é sempre VAZIO. SPECs ativas vivem em branches.
5. **Timestamps `YYYY-MM-DD HH:MM` em TODA atualização** (checkbox, status, decisão).
6. **Ao concluir SPEC**: checklist obrigatório R.5.3 + R.7 (features tocadas atualizadas, snapshot do state.md refletindo conclusão, memory.md TL;DR final, critérios marcados).
7. **Programas de SPECs** declaram dependência bidirecional `Depende de` / `Bloqueia` + `Ordem no programa` (R.14).
8. **Protocolo de escalação**: Nível 0 (auto) → Nível 1 (sob confirmação) → Nível 2 (sob pergunta) → Nível 3 (casos especiais).

Qualquer dúvida operacional → [docs/RULES.md](docs/RULES.md).

---

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Workspace layout

"Meu Gestor" is a multi-tenant ERP split into **two independent git repositories** sitting side-by-side under this workspace folder (the workspace root itself is *not* a git repo — each subfolder has its own `.git`):

- **`erp-back/`** — .NET 9 Web API (Clean Architecture + DDD), single project `erpApi.csproj`. PostgreSQL via EF Core + Npgsql.
- **`erp-front/`** — React 19 + TypeScript + Vite SPA (Tailwind, axios, react-router 7, Radix UI, recharts).

`.vscode/` at the workspace root drives both (compound launch "🚀 Full Stack"). Commit changes inside the relevant subfolder, not the root.

## Commands

### Backend (run from `erp-back/`)
- Build: `dotnet build` (CI builds `--configuration Release`)
- Run: `dotnet run` (profiles in `Properties/launchSettings.json`: `http` → :8000, `https` → :8148)
- There are **no unit tests / no `.sln`**; CI (`.github/workflows/ci.yml`) only does a Release build.

### Frontend (run from `erp-front/`)
- Install: `npm install` (CI uses `npm ci`)
- Dev server: `npm run dev` (Vite, :5173)
- Build: `npm run build` (`tsc -b && vite build` — type errors fail the build)
- Lint: `npm run lint` (ESLint flat config)

### Local full-stack debug
Use VS Code Run-and-Debug → select **"🚀 Full Stack"** then F5 (selecting just "Backend" runs only the API). The backend launch config injects `DATABASE_URL` (Railway **public** proxy URL) + `ASPNETCORE_ENVIRONMENT=Development` directly, so it works regardless of User Secrets. If F5 fails with `database "erp_new" does not exist`, a stale `dotnet` process is still holding :8000 — kill it (`Get-NetTCPConnection -LocalPort 8000 -State Listen | %{ Stop-Process -Id $_.OwningProcess -Force }`) and always Stop the debug session before relaunching.

## Backend architecture

### Layer folders (note the naming gotcha)
Layers live in one project as folders prefixed `-1-` … `-5-` to force ordering. **These leading dashes break `find`/glob/shell expansion** — quote paths and use `./-1-Domain/...` or the Read/Grep tools instead of `find -1-Domain`. Each folder maps to a namespace:

- **`-1-Domain`** — `ERP.Domain`: POCO `Entities` (partial classes), `Enums`, `Models`, and `database/` (raw SQL — see DB section).
- **`-2-Application`** — `ERP.Application`: `DTOs` (input/output/filter), `Interfaces` (`Services`, `Repositories`, `Base`), static `Mappers`, and `Services` (business logic).
- **`-3-Infrastructure`** — `ERP.Infrastructure`: `Data/ErpContext.cs` (partial DbContext), `Repositories` (one per aggregate), `UnitOfWork/ErpUnitOfWork.cs`, `Extensions/QueryableExtensions.cs` (`OrderByProperty` dynamic sort).
- **`-4-WebApi`** — `ERP.WebApi`: `Controllers` (extend `BaseController`), `Middlewares`, `Attributes`, `Extensions/HttpContextExtensions.cs`, `Configuration/modules-configuration.json`.
- **`-5-CrossCutting`** — `ERP.CrossCutting`: `IoC` (DI wiring), `Services` (Permission/Token/PasswordHash/Email/ModuleConfiguration), `Filters/GlobalExceptionFilter.cs`, `Exceptions/DomainExceptions.cs`, `Helpers`.

### Per-feature vertical slice (the pattern to copy)
Every domain (CostCenter is a clean reference) is wired the same way:

`Controller → IXxxService → IUnitOfWork.XxxRepository → ErpContext`

- **Controller** is thin: reads context (`GetCompanyId()`, `GetCurrentUserId()`), delegates to the service via `BaseController` helpers (`ExecuteAsync`, `ValidateAndExecuteCreateAsync`, `ExecuteBooleanAsync`), and guards each action with `[RequirePermissions("module.action")]`.
- **Service** holds business logic, depends only on `IUnitOfWork`, maps with the static `XxxMapper`, and calls `await _unitOfWork.SaveChangesAsync()` (repositories themselves do *not* save — they only `Add`/`Remove`/mutate tracked entities).
- **Repository** filters by tenant explicitly: `.Where(a => a.CompanyId == companyId)`. Paginated lists return `PagedResult<T>` built from an `XxxFilterDTO` (`Skip`/`Take`/`Search`/`OrderBy`/`IsAscending`).
- **`IUnitOfWork`** exposes every repository as a property plus transaction control (`BeginTransactionAsync`/`Commit`/`Rollback`). Register new services/repos in `-5-CrossCutting/IoC/ServiceConfiguration.cs` and add the repo to `IUnitOfWork`.

`CompanyId` and the current user id are **passed explicitly as method params** down the stack (sourced from the request header + JWT) — there is no global EF query filter, so a missing `.Where(CompanyId == ...)` silently leaks cross-tenant data.

### Request pipeline & multi-tenancy
Order in `Program.cs`: `UseAuthentication` → `JwtMiddleware` → `CompanyContextMiddleware` → `UseAuthorization`.

- **JWT** — standard `[Authorize]` plus a custom `JwtMiddleware` that parses the bearer token and stashes `UserId`/`Email`/`Phone`/`Cpf` into `HttpContext.Items`. `Jwt:Secret` falls back to a hardcoded default if unset, so auth works locally with no config.
- **Multi-tenant** — every authenticated request (except paths excluded in `CompanyContextMiddleware`: `/api/auth/*`, `/api/company/`, `/swagger`, `/health`) **must send an `X-Company-Id` header**. The middleware verifies the user has access to that company (`IPermissionService.UserHasAccessToCompanyAsync`) and stores it in `HttpContext.Items["CompanyId"]`; controllers read it via `GetCompanyId()`. Missing/invalid header → 400; no access → 403.
- **Permissions** — `[RequirePermissions("role.canView", ...)]` = OR logic; `"module.*"` means all CRUD perms of that module; `IsAdmin`/`IsSystemRole` bypass everything. A role's permissions are stored as **JSONB** in `Role.Permissions`. The catalog of modules/actions is `-4-WebApi/Configuration/modules-configuration.json` (copied to output via the csproj) — add new permission keys there.

### Responses & errors
All endpoints return the `BaseResponse<T>` envelope (`code`, `message`, `data`, `errors`). Throw domain exceptions from `DomainExceptions.cs` (e.g. `EntityNotFoundException`, `ValidationException`) — `GlobalExceptionFilter` converts them to the right HTTP status + envelope. Model-state validation is reshaped into the same envelope in `Program.cs`.

### Database conventions
- PostgreSQL, schema **`erp`** (`builder.HasDefaultSchema("erp")`). DB tables use `tb_`/snake_case; entities are PascalCase.
- **No EF Core migrations.** The schema is hand-written SQL in `-1-Domain/database/`: `erp.sql` (full schema) + numbered `migrations/*.sql` applied **manually** against the DB. When you change an entity, update `erp.sql` *and* add a new numbered migration (see the `README_*.md` notes there, e.g. cascade-delete rules).
- Connection: `Program.cs` reads `DATABASE_URL` (Railway URI form) and auto-builds the Npgsql string with `SearchPath=erp;SSL Mode=Require`; otherwise falls back to `ConnectionStrings:ErpConnection` (appsettings / User Secrets, `UserSecretsId` in csproj). Use Railway's **public** `*.proxy.rlwy.net` host locally, never `*.railway.internal`.
- Audit columns on most entities are **Portuguese**: `CriadoPor`, `CriadoEm`, `AtualizadoPor`, `AtualizadoEm`. Several columns are JSONB (`Role.Permissions`, `Payroll.Snapshot`, `PayrollItem.CalculationDetails`).
- `AppContext.SetSwitch("Npgsql.EnableLegacyTimestampBehavior", true)` is set — `DateTime` with `Unspecified` kind is treated as UTC.

## Frontend architecture

- **`src/services/`** — one module per backend domain, all built on the shared axios instance `services/api.ts`. That instance's request interceptor auto-attaches `Authorization: Bearer <token>` and `X-Company-Id` from `localStorage`; the response interceptor redirects on 401 (→ `/login`, clears storage) and 403 / "sem acesso" (→ `/select-company`, drops company). New API calls should go through a domain service, not raw axios.
- **`src/contexts/`** — `AuthContext` (login, company selection, `selectedCompany`), `PermissionContext` (loads `/auth/permissions`, caches in `localStorage`, exposes `hasPermission`/`hasAnyPermission` mirroring the backend `module.action` + `module.*` rules), `ToastContext`. `PermissionProvider` must wrap `AuthProvider` (AuthContext consumes it).
- **`src/routes/index.tsx`** — central route table. The standard guard nesting is `ProtectedRoute` (authenticated) → `CompanyProtectedRoute` (a company is selected) → `PermissionProtectedRoute requires="module.canX"` (permission). Match this nesting when adding routes.
- **`src/pages/`** one folder per domain (list + form components); **`src/components/ui/`** holds the shared primitives.
- Env: `VITE_API_URL` (in `erp-front/.env.local`, e.g. `http://localhost:8000/api`); code fallback is `https://localhost:8148/api`.

## Deployment
Both repos deploy to **Railway** via their `Dockerfile`. Backend honors `PORT` (binds `0.0.0.0:$PORT`) and `ALLOWED_ORIGINS` (comma-separated → restricted "Production" CORS policy; otherwise CORS is AllowAll). Frontend is served by nginx (`nginx.conf`). CI on both runs on push/PR to `main`.
