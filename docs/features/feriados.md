# Feature: feriados

**Keywords:** feriado, holiday, calendário, dia útil, recorrente, anual
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/holiday.cs
  - erp-back/-4-WebApi/Controllers/HolidayController.cs
  - erp-back/-2-Application/Services/HolidayService.cs
  - erp-back/-1-Domain/database/migrations/024_create_holiday.sql
  - erp-front/src/services/holidayService.ts
  - erp-front/src/pages/holidays/
**Resumo:** Cadastro de feriados por empresa (descrição, data, recorrente/anual). Base para futuras regras de dia útil / jornada / folha.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1212 | 2026-06-09 | `a71fb83` | Onda 1: cadastro de feriados (entidade + CRUD api/holiday + UI) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

`Holiday` (tabela `tb_holiday`, por `CompanyId`): `Description`, `Date` (date), `IsRecurring` (anual). API `api/holiday` CRUD canônico (getAll/getPaged/{id}/create/PUT/DELETE) sob permissões `companySettings.canView`/`canEdit`. Front: lista `Holidays` + `HolidayForm` em `/holidays`, item no Sidebar (Cadastros). Ainda **não é consumido** pelo cálculo de ponto/folha — fica para ondas futuras (jornada/banco de horas).

> Última atualização: 2026-06-09 (SPEC-20260609-1212)

## Decisões arquiteturais ativas

- **Permissões reusam `companySettings.*`** (origem: SPEC-20260609-1212, 2026-06-09) — feriado é configuração da empresa; evita criar um módulo de permissão novo.

## Alternativas consideradas e rejeitadas

- _(nenhuma registrada ainda)_

## Gotchas

- **Feriado ainda não afeta cálculos** (2026-06-09, SPEC-20260609-1212) — é só cadastro; ponto/folha não consultam feriados ainda.

## Estado congelado (se houver)

_(nenhum)_
