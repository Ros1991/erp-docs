# Feature: abonos-ferias

**Keywords:** abono, dias, horas, férias, período aquisitivo, gozo, vacation, Empregados_Abonos
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/employeeAbono.cs
  - erp-back/-1-Domain/Entities/vacationPeriod.cs
  - erp-back/-4-WebApi/Controllers/EmployeeAbonoController.cs
  - erp-back/-4-WebApi/Controllers/VacationPeriodController.cs
  - erp-back/-2-Application/Services/EmployeeAbonoService.cs
  - erp-back/-2-Application/Services/VacationPeriodService.cs
  - erp-back/-1-Domain/database/migrations/030_onda5_pessoal.sql
  - erp-front/src/pages/personnel/Abonos.tsx
  - erp-front/src/pages/personnel/VacationPeriods.tsx
**Resumo:** Cadastro de abonos (Dias/Horas) e de períodos de férias (aquisitivo + gozo) por empregado — registros de RH portados do sistema antigo.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1516 | 2026-06-09 | `34f5246` | Onda 5: cadastro de abonos (Dias/Horas) + períodos de férias (aquisitivo/gozo) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | Consumo de abono no ponto/folha | Hoje é cadastro; abater banco de horas/proventos é futuro |
| — | Auto-integração férias→folha | A folha já calcula férias ao lançar; ligar o período automaticamente é futuro |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

**Abonos** — `EmployeeAbono` (tabela `tb_employee_abono`, por `CompanyId`+`EmployeeId`): `AbonoDate`, `Type` (`Dias`/`Horas`), `Quantity` (numeric), `Description`. CRUD `api/employee-abono` (`getPaged`, `employee/{id}`, `GET {id}`, `create`, `PUT {id}`, `DELETE {id}`) sob `employee.canView`/`canEdit`. Tela `Abonos` (`/abonos`): seletor de empregado + lista + form inline.

**Férias** — `VacationPeriod` (tabela `tb_vacation_period`): `AcquisitionStart`/`AcquisitionEnd` (período aquisitivo), `DaysEntitled` (dias de direito), `VacationStart` (início do gozo), `DaysTaken`, `IsPaid`, `Notes`. CRUD `api/vacation-period` sob `employee.canView`/`canEdit`. Tela `VacationPeriods` (`/vacation-periods`).

Ambos no sidebar, seção **Pessoal**.

> Última atualização: 2026-06-09 (SPEC-20260609-1516)

## Decisões arquiteturais ativas

- **Cadastro standalone** (origem: SPEC-20260609-1516, 2026-06-09) — espelha os cadastros do antigo (`Empregados_Abonos`, `Lancar Ferias`); o consumo no ponto/folha é deferido (documentado), evitando tocar no motor de folha nesta fase.
- **Abono com tipo Dias OU Horas** (origem: SPEC-20260609-1516) — o novo só tinha "horas" (via justificativa); o cadastro de abono cobre os dois (resolve o gap "abono em dias").
- **Permissões `employee.*`** (origem: SPEC-20260609-1516) — são registros do empregado.

## Alternativas consideradas e rejeitadas

- **Reusar a justificativa (hoursGranted) para abono em dias** (2026-06-09, SPEC-20260609-1516) — rejeitada: justificativa é de ponto/falta; o abono Dias/Horas é um cadastro de RH próprio (como no antigo).

## Gotchas

- **Abono/Férias não afetam cálculos ainda** (2026-06-09, SPEC-20260609-1516) — são cadastros; ponto/folha não os consomem automaticamente.
- **Férias na folha já existe** (2026-06-09) — o motor de folha calcula férias (1/3, INSS/IRRF) ao lançar; `VacationPeriod` é o registro do período, não substitui o cálculo.

## Estado congelado (se houver)

_(nenhum)_
