# State — SPEC-20260609-1309

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 13:09

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 13:28
**Onde tô:** Onda 3 CONCLUÍDA e arquivada. Back+front buildam limpo, mergeados na main LOCAL (erp-back `2858e49`, erp-front `a1ec365`; commits da feature `a9edd34`/`f45202c`). Sem push.
**Próximo passo:** Onda 4 — tarefas (recorrência, regra de 1 ativa, editar hora, pausa geral).
**Última decisão:** banco de horas = ledger de lançamentos manuais; saldo do período computado posta-se no banco via "Lançar saldo do período" (sem motor de auto-recálculo nesta onda). Espelho cobre TODOS os dias do período (folga=0, falta=−previsto).
**Bloqueio atual:** nenhum. Execução autônoma.
**Se retomar, ler:** este TL;DR + tabela de fases.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| M | Espelho de ponto (GetTimeMirrorAsync + tela) | concluído | 2026-06-09 13:28 | `a9edd34`/`f45202c` |
| N | Resumo do período (GetPeriodSummaryAsync + tela) | concluído | 2026-06-09 13:28 | `a9edd34`/`f45202c` |
| O | Banco de horas (TimeBankAdjustment + CRUD + painel) | concluído | 2026-06-09 13:28 | `a9edd34`/`f45202c` |

### Próximos passos

- [ ] Onda 4 (tarefas)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 13:18] Migração `028_create_time_bank_adjustment.sql` aplicada na Railway: `tb_time_bank_adjustment` (company_id, employee_id, adjustment_date, minutes [assinado], adjustment_type, reason + audit) + índices por employee/company. Confirmado por `--query`.
- [2026-06-09 13:28] `TimeEntryRepository.GetByEmployeeAndDateRangeAsync` usa fim EXCLUSIVO (`< endUtc`); o espelho passa `end + 1 dia` para incluir o último dia inteiro.
- [2026-06-09 13:28] Pareamento Entrada/Saída e cálculo do previsto agora são helpers únicos (`ComputeMinutesWorked`, `BuildExpectedMinutesResolverAsync`) reusados por resumo-do-dia/espelho/resumo-do-período (sem duplicar bug). `GetExpectedDailyMinutes` virou delegação ao resolver pré-carregado.

## Inferências prováveis

- [2026-06-09 13:09] Espelho usa `timeClock.canView` (empregado vê o seu; gestor vê de todos via seletor) seguindo o padrão lax já existente em `GetTodaySummaryAsync`; resumo do período e CRUD do banco usam `timeClock.canManage`.

## Dúvidas em aberto

- [2026-06-09 13:09] Auto-recálculo do banco (RefreshBancoHoras) vs lançamento manual: decidido manual nesta onda; auto-recálculo é iteração futura.

---

## Log cronológico (APPEND-ONLY)

## 2026-06-09 13:09 — [ativação]

Onda 3 ativada (programa 4/7, depende de SPEC-20260609-1248 jornada já concluída/mergeada). Branches `feature/onda-3-ponto-banco-horas`. Comparativo confirma Tier 2 #11 (relatórios de ponto) + Tier 3 #17 (banco de horas). Sistema antigo: espelho/Resumo do Período (Prev/Trab/Saldo dia-a-dia) + `Empregados_banco_horas` (minutos assinados) + `Empregado_acerto_horas` (Pago/Descontado).

## 2026-06-09 13:28 — [conclusão] Onda 3 concluída e arquivada

Todos os critérios M,N,O marcados. Migração 028 aplicada + `erp.sql`. Back: `TimeBankAdjustment` CRUD canônico (`api/time-bank-adjustment`, canManage) registrado; `TimeClockService.GetTimeMirrorAsync`/`GetPeriodSummaryAsync` + endpoints `mirror` (canView) e `period-summary` (canManage); refactor dos helpers + guarda multi-tenant no espelho. Front: telas `TimeMirror` (espelho + banco) e `PeriodSummary` + `timeClockService.getMirror/getPeriodSummary` + `timeBankAdjustmentService` + rotas/sidebar. `dotnet build` 0 erros; `npm run build` ✓. Features tocadas (R.7): ponto-eletronico (espelho/resumo/banco), banco-de-horas (nova), jornada-trabalho (consumo no espelho). Commits feature `a9edd34`/`f45202c`; merge na main LOCAL erp-back `2858e49` / erp-front `a1ec365` (sem push). Pasta `active/`→`archive/`.

**Entregue na Onda 3:** espelho de ponto (dia a dia, previsto×trabalhado×saldo, batidas e justificativas); resumo do período por empregado (visão gestor); banco de horas com lançamentos manuais (crédito/débito/acerto) e saldo acumulado, com atalho para lançar o saldo computado do período.
