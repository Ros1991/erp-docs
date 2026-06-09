# Feature: jornada-trabalho

**Keywords:** jornada, escala, work-schedule, minutos previstos, intervalo, horas por dia, ponto
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/workSchedule.cs
  - erp-back/-4-WebApi/Controllers/WorkScheduleController.cs
  - erp-back/-2-Application/Services/WorkScheduleService.cs
  - erp-back/-2-Application/Services/TimeClockService.cs (GetExpectedDailyMinutes)
  - erp-back/-1-Domain/database/migrations/027_create_work_schedule.sql
  - erp-front/src/services/workScheduleService.ts
  - erp-front/src/pages/work-schedules/
**Resumo:** Cadastro de jornadas de trabalho (escalas) com minutos por dia da semana + intervalo, vinculável ao contrato do empregado. O ponto calcula os minutos previstos do dia a partir da jornada do contrato ativo.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1248 | 2026-06-09 | `d9100bd` | Onda 2: WorkSchedule (entidade + CRUD api/work-schedule + UI) + FK no contrato + destrave dos minutos previstos do ponto |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | Banco de horas | Saldo acumulado (previsto vs. trabalhado) — Onda 3 |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

`WorkSchedule` (tabela `tb_work_schedule`, por `CompanyId`): `Name`, `Type` (`Normal`/`Escala`/`Flexivel`, rótulo), `MinutesMon..Sun` (minutos esperados por dia da semana; 0 = folga), `BreakMinutes` (intervalo descontado do previsto), `IsActive`. API `api/work-schedule` CRUD canônico (getAll/getPaged/{id}/create/PUT/DELETE) sob permissões `companySettings.canView`/`canEdit`. Front: lista `WorkSchedules` + `WorkScheduleForm` (horas por dia, intervalo, tipo, total semanal, "igualar seg→sex") em `/work-schedules`, item "Jornadas" no Sidebar (Cadastros).

Vínculo: `tb_contract.work_schedule_id` (nullable) — seletor de jornada no `ContractForm`. O empregado herda a jornada do **contrato ativo**.

Consumo no ponto: `TimeClockService.GetExpectedDailyMinutes(employeeId, companyId, date)` → jornada do contrato ativo: `minutos_do_dia_da_semana − intervalo` (mín. 0); fallback `WeeklyHoursDefault×60/5` → `480`.

> Última atualização: 2026-06-09 (SPEC-20260609-1248)

## Decisões arquiteturais ativas

- **Minutos por dia da semana + intervalo único** (origem: SPEC-20260609-1248, 2026-06-09) — modelo simples que cobre jornada fixa e folgas (dia = 0); escalas cíclicas reais (12x36 etc.) ficam para iteração futura.
- **Jornada herdada do contrato ativo** (origem: SPEC-20260609-1248, 2026-06-09) — `work_schedule_id` no contrato, não no empregado; segue o ciclo de vida do contrato.
- **Permissões reusam `companySettings.*`** (origem: SPEC-20260609-1248, 2026-06-09) — jornada é configuração da empresa (mesmo critério de feriados/plano de contas).

## Alternativas consideradas e rejeitadas

- **Jornada direto no empregado** (2026-06-09, SPEC-20260609-1248) — rejeitada: a jornada muda com o contrato; manter no contrato evita inconsistência ao desligar/recontratar.

## Gotchas

- **Folga = 0 minutos previstos é resultado VÁLIDO** (2026-06-09, SPEC-20260609-1248) — não tratar 0 como "sem jornada"/fallback.
- **`work_schedule_type` é só rótulo** (2026-06-09, SPEC-20260609-1248) — sem lógica de ciclo associada por ora.
- **Feriados ainda não abatem do previsto** (2026-06-09, SPEC-20260609-1248) — integração feriado↔previsto fica para o futuro.

## Estado congelado (se houver)

_(nenhum)_
