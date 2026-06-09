# Feature: empresas-multitenancy

**Keywords:** empresa, company, multi-tenant, x-company-id, companyuser, vínculo, configurações, company-setting
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/CompanyController.cs
  - erp-back/-4-WebApi/Controllers/CompanyUserController.cs
  - erp-back/-4-WebApi/Controllers/CompanySettingController.cs
  - erp-back/-4-WebApi/Middlewares/CompanyContextMiddleware.cs
  - erp-back/-5-CrossCutting/Services/PermissionService.cs
  - erp-back/-1-Domain/Entities/company.cs
  - erp-back/-1-Domain/Entities/companyUser.cs
  - erp-back/-1-Domain/Entities/companySetting.cs
  - erp-front/src/pages/companies/CompanySelect.tsx
  - erp-front/src/pages/company-settings/
**Resumo:** Isolamento multi-tenant do ERP: empresas (`Company`), o vínculo usuário↔empresa↔cargo (`CompanyUser`), as configurações operacionais da empresa (`CompanySetting`) e o middleware que exige/valida o header `X-Company-Id` em toda requisição autenticada.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1538 | 2026-06-09 | `385b415` | Onda 6: desligamento/desativação de empregado — `CompanyUser.is_active` (soft-delete) + redireciona tarefas em aberto e subordinados antes de remover o acesso (`POST api/employee/{id}/deactivate`) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

O ERP é **multi-tenant por `CompanyId`**. Toda requisição autenticada (exceto rotas excluídas: `api/auth/*`, `api/company/`, `swagger`, `health`) precisa do header **`X-Company-Id`**. O `CompanyContextMiddleware` valida o header, confirma via `PermissionService.UserHasAccessToCompanyAsync(userId, companyId)` que o usuário pertence à empresa e grava `CompanyId` em `HttpContext.Items` (lido nos controllers por `GetCompanyId()`). Header ausente/ inválido → 400; sem acesso → 403.

`Company` (api/company) é o cadastro da empresa (dono via `UserId`). `CompanyUser` (api/companyuser, permissões `user.*`) liga `User` + `Company` + `Role` — é a tabela de associação que concede acesso e cargo. `CompanySetting` (api/company-setting, permissões `companySettings.canView/canEdit`) guarda parâmetros operacionais que alimentam outras features: dia/dia-de-fechamento da folha, tolerância de ponto em minutos, horas semanais default, gerente geral, regras de hora extra/almoço, fim de semana, e as distribuições de centro de custo default (`DefaultCostCenterDistribution`, endpoint `default-distributions`).

No frontend, após o login o usuário escolhe a empresa em `CompanySelect`; a escolha vai para `localStorage` (`selectedCompanyId`) e é injetada como `X-Company-Id` pelo interceptor do `api.ts`. As guardas de rota `CompanyProtectedRoute` exigem empresa selecionada; 403 derruba a empresa e redireciona para a seleção.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Tenant via header `X-Company-Id` + validação por requisição** (origem: estado pré-SPEC, 2026-06-08 20:15) — em vez de um claim fixo no JWT, a empresa ativa é um header trocável, permitindo um login alternar entre empresas sem relogar. Trade-off: cada request paga a checagem de acesso (mitigada por cache de permissões na request).
- **Sem global query filter de tenant no EF** (origem: estado pré-SPEC, 2026-06-08 20:15) — o `CompanyId` é propagado explicitamente até cada repositório (`.Where(x => x.CompanyId == companyId)`). Ver gotcha. Relacionado a [[plataforma-core]].

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Isolamento de tenant é manual em cada repositório** (2026-06-08 20:15) — não existe filtro global de `CompanyId` no `ErpContext`. Esquecer o `.Where(x => x.CompanyId == companyId)` em uma query vaza dados entre empresas silenciosamente. Toda nova query DEVE filtrar por tenant.
- **`CompanyController` não tem `[RequirePermissions]`** (2026-06-08 20:15) — é acessado antes da seleção de empresa (rota excluída do gate), então o controle de acesso é por posse/vínculo do usuário, não por módulo de permissão.

## Estado congelado (se houver)

_(nenhum)_
