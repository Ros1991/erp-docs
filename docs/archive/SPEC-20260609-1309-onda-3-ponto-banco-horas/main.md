# SPEC-20260609-1309: Onda 3 — Espelho de ponto + Banco de horas

**Status:** done
**Criada:** 2026-06-09 13:09
**Ativada:** 2026-06-09 13:09
**Concluída:** 2026-06-09 13:28
**Pausada em:** —
**Commit final:** erp-back `a9edd34` / erp-front `f45202c`
**Keywords:** espelho de ponto, resumo do período, banco de horas, saldo, previsto, trabalhado, lançamento manual, migração
**Features:** ponto-eletronico, banco-de-horas, jornada-trabalho
**Branch:** feature/onda-3-ponto-banco-horas (erp-back + erp-front)
**Depende de:** SPEC-20260609-1248 (jornada — fonte dos minutos previstos)
**Bloqueia:** —
**Ordem no programa:** 4/7 — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (Tier 2 #11 relatórios de ponto; Tier 3 #17 banco de horas); usuário em 2026-06-09 (mandato de execução autônoma)
**Resumo:** Relatórios de ponto (espelho dia-a-dia + resumo do período por empregado) calculando previsto×trabalhado×saldo a partir da jornada, e banco de horas com lançamentos manuais (crédito/débito/acerto).

## Objetivo

Entregar a visão consolidada do ponto que faltava: o **espelho** (por empregado/período, dia a dia: previsto, trabalhado, saldo + batidas + justificativas), o **resumo do período** (consolidado por empregado — visão gestor) e o **banco de horas** (saldo acumulado a partir de lançamentos manuais de crédito/débito/acerto). Tudo destravado pela jornada da Onda 2.

## Escopo

**DENTRO:**
- **M** — Espelho de ponto: `TimeClockService.GetTimeMirrorAsync` + `GET api/time-clock/mirror` (canView) + tela `TimeMirror` (seletor de empregado p/ gestor, mês, tabela diária, cards de totais).
- **N** — Resumo do período: `GetPeriodSummaryAsync` + `GET api/time-clock/period-summary` (canManage) + tela `PeriodSummary` (consolidado por empregado, clique abre o espelho).
- **O** — Banco de horas: entidade `TimeBankAdjustment` + `tb_time_bank_adjustment` + CRUD `api/time-bank-adjustment` (canManage) + painel de lançamentos no espelho (add/excluir + "lançar saldo do período"). Saldo do banco = Σ lançamentos até a data.
- Refactor de qualidade: pareamento Entrada/Saída e cálculo do previsto extraídos em helpers reusados (`ComputeMinutesWorked`, `BuildExpectedMinutesResolverAsync`); guarda multi-tenant no espelho.

**FORA (deferido — transparente, R.6.2):**
- **Auto-recálculo do banco de horas** (estilo `RefreshBancoHoras`: persistir os saldos diários computados): nesta onda o banco é o ledger de lançamentos manuais; o saldo do período computado pode ser postado via "Lançar saldo do período". Fica para iteração futura.
- **Abonos (Dias/Horas) e resolução de justificativa com abono**: pertencem a Pessoal → **Onda 5**.
- **Feriados abatendo do previsto**: futuro.

## Implementação

Migração 028 (aditiva) aplicada na Railway via `node .migrate/apply.js` + `erp.sql` atualizado; back no padrão canônico (entity/DTOs/mapper/repo/service/controller + registro) e métodos de relatório no `TimeClockService` (reusando `GetByEmployeeAndDateRangeAsync` por período); front com 2 telas + service. `dotnet build` + `npm run build` limpos.

## Critério de aceite

- [x] **M** — espelho de ponto (`GetTimeMirrorAsync` + `mirror` + tela `TimeMirror`) (2026-06-09 13:28, `a9edd34`/`f45202c`)
- [x] **N** — resumo do período (`GetPeriodSummaryAsync` + `period-summary` + tela `PeriodSummary`) (2026-06-09 13:28, `a9edd34`/`f45202c`)
- [x] **O** — banco de horas (`TimeBankAdjustment` + CRUD `api/time-bank-adjustment` + painel no espelho) (2026-06-09 13:28, `a9edd34`/`f45202c`)
- [x] guarda multi-tenant no espelho + helpers de cálculo reusados (2026-06-09 13:28)
- [x] migração 028 aplicada na Railway + `erp.sql` atualizado (2026-06-09 13:18)
- [x] erp-back compila e erp-front builda (2026-06-09 13:28)
- [x] **Features tocadas (ponto-eletronico, banco-de-horas, jornada-trabalho) atualizadas** com timestamp e referência a esta SPEC (2026-06-09 13:28)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 13:28)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 13:28)
