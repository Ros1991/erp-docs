# Memory — SPEC-20260609-1248 (Onda 2 — Jornada de trabalho)

> Main: [main.md](./main.md) · State: [state.md](./state.md)

## TL;DR final

**Onda 2 entregue (2026-06-09 13:01).** Cadastro de **jornadas de trabalho** (`tb_work_schedule`: minutos por dia da semana + intervalo + tipo rótulo) com CRUD `api/work-schedule` (permissões `companySettings.*`) e telas (lista + form com total semanal e "igualar seg→sex"). Jornada vinculada ao **contrato** (`work_schedule_id` nullable) via seletor no ContractForm. **Destrave do ponto:** `TimeClockService.GetExpectedDailyMinutes(employeeId, companyId, date)` agora deriva os minutos previstos da jornada do contrato ativo (minutos do dia − intervalo), com fallback `WeeklyHoursDefault×60/5` → `480`. Migração 027 ADITIVA aplicada na Railway. Commits feature `d9100bd`/`22aa1b7`; main local `c50fd96`/`1f556c5` (sem push). Fundação para banco de horas (Onda 3).

## Contexto / decisões

- **Modelo de jornada:** uma jornada = nome + tipo (`Normal|Escala|Flexivel`, rótulo) + **minutos por dia da semana** (`work_minutes_mon..sun`) + **intervalo único** (`work_break_minutes`) + ativo. Simples e direto; cobre jornada fixa e folgas (dia = 0). Escalas cíclicas reais ficam para depois.
- **Vínculo:** `Contract.work_schedule_id` nullable. O empregado herda a jornada do **contrato ativo**.
- **Destrave do ponto (item L):** `GetExpectedDailyMinutes(employeeId, companyId, date)` busca o contrato ativo (`ContractRepository.GetActiveByEmployeeIdAsync`), pega a jornada, retorna `minutos_do_dia − intervalo` (mín. 0). Sem jornada → fallback `WeeklyHoursDefault*60/5` → `480`.
- **Permissões:** `companySettings.canView`/`canEdit` (mesmo critério de Feriados/Plano de contas).

## Armadilhas

- `ERP.Domain.Entities.Task` colide com `System.Threading.Tasks.Task` — qualificar quando necessário (não ocorreu aqui).
- Folga (0 min previstos) é resultado VÁLIDO — não tratar como "sem jornada"/fallback.
- Migração é ADITIVA (tabela nova + coluna nullable). Nada destrutivo; prod só no push final.

## Referências

- Padrão CRUD: CostCenter / Holiday / FinancialCategory.
- Feature doc: [docs/features/jornada-trabalho.md](../../features/jornada-trabalho.md).
