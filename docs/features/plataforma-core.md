# Feature: plataforma-core

**Keywords:** arquitetura, clean-architecture, baseresponse, basecontroller, unitofwork, efcore, npgsql, exception-filter, swagger, docker, railway, axios
**Arquivos principais:**
  - erp-back/Program.cs
  - erp-back/-4-WebApi/Controllers/Base/BaseController.cs
  - erp-back/-5-CrossCutting/Filters/GlobalExceptionFilter.cs
  - erp-back/-5-CrossCutting/Exceptions/DomainExceptions.cs
  - erp-back/-3-Infrastructure/UnitOfWork/ErpUnitOfWork.cs
  - erp-back/-3-Infrastructure/Data/ErpContext.cs
  - erp-back/-3-Infrastructure/Extensions/QueryableExtensions.cs
  - erp-back/-5-CrossCutting/IoC/ServiceConfiguration.cs
  - erp-front/src/services/api.ts
  - erp-front/src/contexts/ToastContext.tsx
  - erp-front/src/routes/index.tsx
  - erp-front/src/components/ui/
**Resumo:** O esqueleto compartilhado por todas as features: Clean Architecture em camadas, padrão Controller→Service→UnitOfWork→Repository→ErpContext, envelope `BaseResponse<T>`, tratamento global de exceções, paginação, e a fundação do frontend (axios + interceptors + guardas de rota + UI).

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

**Backend (.NET 9, Clean Architecture/DDD)** — um único projeto `erpApi.csproj` com camadas em pastas `-1-Domain` … `-5-CrossCutting` (namespaces `ERP.Domain`/`Application`/`Infrastructure`/`WebApi`/`CrossCutting`). Cada feature de negócio é uma fatia vertical idêntica: `Controller : BaseController` (fino, lê contexto, delega) → `IXxxService` (regra, mapeia via `XxxMapper` estático) → `IUnitOfWork.XxxRepository` (filtra por tenant, não salva) → `ErpContext`. O service chama `SaveChangesAsync`; o `ErpUnitOfWork` expõe todos os repositórios + controle de transação.

Respostas usam o envelope **`BaseResponse<T>`** (`code`, `message`, `data`, `errors`). `BaseController` oferece helpers (`ExecuteAsync`, `ValidateAndExecuteCreateAsync`, `ExecuteBooleanAsync`, `GetCompanyId`, `GetCurrentUserId`). `GlobalExceptionFilter` converte `DomainExceptions` (`EntityNotFoundException`, `ValidationException`, …) no status HTTP correto + envelope. Paginação por `PagedResult<T>` + `XxxFilterDTO` (`Skip`/`Take`/`Search`/`OrderBy`/`IsAscending`), com ordenação dinâmica via `QueryableExtensions.OrderByProperty`. `ErpContext` é `partial`, configurado por Fluent API manual, schema `erp`, **sem migrations do EF** (SQL cru — ver [[plataforma-core]] gotchas e o diretório `-1-Domain/database/`).

**Frontend (React 19 + Vite + TS)** — `services/api.ts` é a instância axios compartilhada; o request interceptor injeta `Authorization` + `X-Company-Id`, o response interceptor trata 401 (→ login) e 403 (→ seleção de empresa). `ToastContext` para feedback, `routes/index.tsx` para roteamento, `components/ui/` para primitivos (Radix + Tailwind + CVA).

**Deploy** — Dockerfiles em ambos os repos, Railway. Backend honra `PORT` e `ALLOWED_ORIGINS`; expõe `/health` e `/api/info`; Swagger só em Development.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Mappers manuais estáticos (sem AutoMapper)** (origem: estado pré-SPEC, 2026-06-08 20:15) — cada `XxxMapper` converte entidade↔DTO explicitamente; previsível e sem reflexão.
- **Repositórios não dão `SaveChanges`** (origem: estado pré-SPEC, 2026-06-08 20:15) — eles só `Add`/`Remove`/mutam entidades rastreadas; o commit é do service via `UnitOfWork`, permitindo transações multi-repositório.
- **Schema versionado por SQL cru, não EF Migrations** (origem: estado pré-SPEC, 2026-06-08 20:15) — `-1-Domain/database/erp.sql` + `migrations/*.sql` aplicados manualmente. Ver gotcha.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Sem EF Migrations** (2026-06-08 20:15) — alterar uma entidade NÃO gera migration. É obrigatório atualizar `-1-Domain/database/erp.sql` e adicionar um `migrations/NNN_*.sql` numerado, aplicado à mão no Postgres (Railway).
- **Campos de auditoria em português** (2026-06-08 20:15) — `CriadoPor`, `CriadoEm`, `AtualizadoPor`, `AtualizadoEm` em quase toda entidade; `CriadoPor`/`AtualizadoPor` recebem o `currentUserId` propagado do controller.
- **`Npgsql.EnableLegacyTimestampBehavior = true`** (2026-06-08 20:15) — setado em `Program.cs`; `DateTime` com `Kind=Unspecified` é tratado como UTC. Vários campos de data/timestamp são modelados como `string` no `ErpContext`.
- **Pastas de camada começam com `-`** (2026-06-08 20:15) — `-1-Domain` etc. quebram glob/`find`; sempre cite caminhos com `./-1-Domain/...` e prefira as ferramentas de busca.

## Estado congelado (se houver)

_(nenhum)_
