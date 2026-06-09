# Feature: cargos-permissoes

**Keywords:** cargo, role, permissão, rbac, modules-configuration, RequirePermissions, autorização, isAdmin
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/RoleController.cs
  - erp-back/-4-WebApi/Controllers/ModuleConfigurationController.cs
  - erp-back/-4-WebApi/Attributes/RequirePermissionsAttribute.cs
  - erp-back/-5-CrossCutting/Services/PermissionService.cs
  - erp-back/-5-CrossCutting/Services/ModuleConfigurationService.cs
  - erp-back/-4-WebApi/Configuration/modules-configuration.json
  - erp-back/-1-Domain/Entities/role.cs
  - erp-front/src/contexts/PermissionContext.tsx
  - erp-front/src/components/permissions/PermissionProtectedRoute.tsx
  - erp-front/src/pages/roles/
**Resumo:** Controle de acesso por papéis (RBAC): cargos (`Role`) guardam um mapa de permissões em JSONB; o atributo `[RequirePermissions("module.action")]` protege endpoints e o frontend espelha as mesmas regras para esconder/bloquear telas.

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

`Role` (api/role, permissões `role.*`) tem `CompanyId`, `Name`, flag `IsSystem` e o campo **`Permissions` (JSONB)** com a estrutura `module → {canView, canCreate, canEdit, canDelete, ...extras}`. O catálogo de módulos/ações é declarado em `modules-configuration.json` (18 módulos: role, user, account, employee, accountPayableReceivable, costCenter, loanAdvance, financialTransaction, location, purchaseOrder, supplierCustomer, task, payroll, report, timeClock, justification, companySettings — alguns com ações extras como `purchaseOrder.canProcess`, `payroll.canClose/canReopen`, `timeClock.canPunch/canManage`, `justification.canManage`).

Backend: `RequirePermissionsAttribute` (IAuthorizationFilter) resolve as permissões do usuário via `PermissionService` (com cache por request em `HttpContext.Items`), aceita **OR** entre múltiplas permissões e a hierarquia `module.*` (todas as ações). `IsAdmin` ou `IsSystemRole` fazem bypass total. `ModuleConfigurationController` (api/ModuleConfiguration) expõe o catálogo e mapeia permissão→endpoints.

Frontend: `PermissionContext` carrega o mapa de `GET api/auth/permissions`, cacheia em `localStorage` e expõe `hasPermission`/`hasAnyPermission`/`hasAllPermissions` com a **mesma** semântica (`module.action`, `module.*`, bypass admin/system). `PermissionProtectedRoute` envolve cada rota; componentes usam o hook `usePermission` para esconder ações.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Permissões como JSONB no `Role`, não tabela normalizada** (origem: estado pré-SPEC, 2026-06-08 20:15) — flexível e barato de ler de uma vez; o catálogo canônico de chaves vive em `modules-configuration.json`.
- **Regra de permissão duplicada back+front com semântica idêntica** (origem: estado pré-SPEC, 2026-06-08 20:15) — `RequirePermissionsAttribute` (autoridade) e `PermissionContext` (UX). O backend é a fonte de verdade; o front só melhora a experiência.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Adicionar uma ação nova exige editar `modules-configuration.json`** (2026-06-08 20:15) — o arquivo é copiado para o output via `erpApi.csproj`; chaves de permissão usadas em `[RequirePermissions]` devem existir no catálogo para o frontend renderizar o checkbox correspondente.
- **`module.*` exige TODAS as ações do módulo verdadeiras** (2026-06-08 20:15) — tanto no back quanto no front, a hierarquia `*` é um AND de canView/canCreate/canEdit/canDelete, não um "qualquer uma".

## Estado congelado (se houver)

_(nenhum)_
