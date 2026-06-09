# Memory — SPEC-20260609-1309 (Onda 3 — Espelho de ponto + Banco de horas)

> Main: [main.md](./main.md) · State: [state.md](./state.md)

## TL;DR final

**Onda 3 entregue (2026-06-09 13:28).** **Espelho de ponto** por empregado/período (`GET api/time-clock/mirror`, canView): dia a dia previsto×trabalhado×saldo + batidas + justificativas, totais e saldo do período. **Resumo do período** consolidado por empregado (`GET api/time-clock/period-summary`, canManage). **Banco de horas** = ledger de lançamentos manuais (`tb_time_bank_adjustment`, CRUD `api/time-bank-adjustment`, canManage); saldo do banco = Σ lançamentos até a data; o saldo computado do período pode ser postado via "Lançar saldo do período". Telas `TimeMirror` + `PeriodSummary`. Migração 028 aditiva. Commits feature `a9edd34`/`f45202c`; main local `2858e49`/`a1ec365` (sem push).

## Contexto / decisões

- **Previsto vem da jornada (Onda 2).** Reuso de `BuildExpectedMinutesResolverAsync` (pré-carrega a jornada do contrato ativo UMA vez → função por dia; evita N queries no loop do período). Folga = 0 previsto é válido.
- **Espelho cobre TODOS os dias do período** (não só os trabalhados): folga = 0, falta = saldo negativo (−previsto). É a função do espelho.
- **Banco de horas = lançamentos manuais** (assinados em minutos) + tipo rótulo (`Credito|Debito|Pago|Folga|Ajuste`). Saldo acumulado = Σ minutos até `PeriodEnd`. SEM motor de auto-recálculo nesta onda (decisão consciente; ver "Itens deferidos" no main.md).
- **Fim de range é EXCLUSIVO** em `TimeEntryRepository.GetByEmployeeAndDateRangeAsync` → passar `end.AddDays(1)`.
- **Guarda multi-tenant** adicionada no espelho (`employee.CompanyId == companyId`), fechando vazamento cross-tenant via `employeeId` na query (o `GetTodaySummaryAsync` legado ainda tem o padrão lax — não tocado nesta onda).

## Armadilhas

- `ERP.Domain.Entities.Task` vs `System.Threading.Tasks.Task` — não ocorreu aqui, mas vigiar.
- `SumAsync(a => (long)a.Minutes)` sobre conjunto vazio → 0 (long não-nulo). OK.
- Timestamps em UTC; agrupamento por `Timestamp.Date` (UTC) consistente com `DateTime.UtcNow.Date` usado no resto do ponto.

## Referências

- Padrão CRUD: CostCenter / Holiday / FinancialCategory / WorkSchedule.
- Feature docs: [ponto-eletronico](../../features/ponto-eletronico.md), [banco-de-horas](../../features/banco-de-horas.md), [jornada-trabalho](../../features/jornada-trabalho.md).
- Sistema antigo: `Empregados_banco_horas` (minutos assinados/dia), `Empregado_acerto_horas` (Pago/Descontado), Resumo do Período/Relatório Semanal/Mensal.
