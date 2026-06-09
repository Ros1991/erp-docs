# Feature: banco-de-horas

**Keywords:** banco de horas, saldo de horas, hora extra, acerto, lançamento manual, crédito, débito, espelho de ponto
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/timeBankAdjustment.cs
  - erp-back/-4-WebApi/Controllers/TimeBankAdjustmentController.cs
  - erp-back/-2-Application/Services/TimeBankAdjustmentService.cs
  - erp-back/-2-Application/Services/TimeClockService.cs (GetTimeMirrorAsync / GetPeriodSummaryAsync)
  - erp-back/-1-Domain/database/migrations/028_create_time_bank_adjustment.sql
  - erp-front/src/services/timeBankAdjustmentService.ts
  - erp-front/src/pages/time-clock/TimeMirror.tsx
**Resumo:** Saldo de horas (banco de horas) por empregado, formado por lançamentos manuais assinados (crédito/débito/acerto) e visualizado no espelho de ponto junto ao saldo computado do período.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1309 | 2026-06-09 | `a9edd34` | Onda 3: banco de horas (lançamentos manuais) + espelho/resumo do período |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | Auto-recálculo do banco | Persistir saldos diários computados (estilo RefreshBancoHoras) — hoje é manual |
| — | Abonos (Dias/Horas) | Pessoal — Onda 5 |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

`TimeBankAdjustment` (tabela `tb_time_bank_adjustment`, por `CompanyId`+`EmployeeId`): `AdjustmentDate` (date), `Minutes` (int **assinado**: + crédito / − débito), `Type` (`Credito`/`Debito`/`Pago`/`Folga`/`Ajuste`, rótulo), `Reason`. CRUD `api/time-bank-adjustment` (getPaged/{id}/create/PUT/DELETE) sob `timeClock.canManage`.

Saldo do banco = **Σ minutos dos lançamentos até a data** (`GetSumMinutesUpToAsync`). Exposto:
- no espelho (`GET api/time-clock/mirror`): `bankBalance` (acumulado) + `adjustments` (do período) + `manualAdjustmentMinutes`;
- no resumo do período (`GET api/time-clock/period-summary`): `bankBalance` por empregado.

Front: painel "Banco de horas" dentro de `TimeMirror` (lista do período + add/excluir + botão "Lançar saldo do período" que pré-preenche um lançamento com o saldo computado do período).

> Última atualização: 2026-06-09 (SPEC-20260609-1309)

## Decisões arquiteturais ativas

- **Banco = ledger de lançamentos manuais** (origem: SPEC-20260609-1309, 2026-06-09) — sem motor de auto-recálculo dos saldos diários nesta fase; o saldo computado do período é postado manualmente. Simples, auditável e suficiente para o fechamento manual.
- **Minutos assinados** (origem: SPEC-20260609-1309) — um único campo `minutes` (+/−) é a fonte do saldo; `type` é só rótulo/categoria (espelha `Empregados_banco_horas` + `Empregado_acerto_horas` do sistema antigo num só lugar).
- **Permissão `timeClock.canManage`** (origem: SPEC-20260609-1309) — gerir banco de horas é ação de gestor.

## Alternativas consideradas e rejeitadas

- **Auto-recálculo persistido (RefreshBancoHoras)** (2026-06-09, SPEC-20260609-1309) — adiado: exige motor de recálculo + data de início por empregado; o ledger manual + "lançar saldo do período" cobre o fechamento sem essa complexidade.
- **Tabelas separadas para banco vs acerto** (2026-06-09) — rejeitada: um só ledger de minutos assinados com `type` resolve ambos.

## Gotchas

- **`minutes` é assinado e não pode ser 0** (2026-06-09, SPEC-20260609-1309) — validação no service; positivo = crédito, negativo = débito.
- **Saldo do banco ≠ saldo do período** (2026-06-09) — o saldo do período é computado das batidas×jornada; o saldo do banco é a soma dos lançamentos. O espelho mostra os dois.

## Estado congelado (se houver)

_(nenhum)_
