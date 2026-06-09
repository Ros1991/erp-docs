# State — SPEC-20260609-1334

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 13:34

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 15:11
**Onde tô:** Onda 4 CONCLUÍDA e arquivada. Back+front buildam limpo, mergeados na main LOCAL (erp-back `7c3c120`, erp-front `985700b`; commits da feature `71bfe4b`/`9d309eb`). Sem push.
**Próximo passo:** Onda 5 — Pessoal (resumo de empréstimos por empregado, benefício %/mês de admissão, abono por dia, período de férias).
**Última decisão:** recorrência gera a próxima ocorrência ao CONCLUIR (espelha GeraProximaTarefa do antigo); vínculo via `task_recurrence_source_id`; reabrir apaga a gerada se `ActualStartDate == null`.
**Bloqueio atual:** nenhum. Execução autônoma.
**Se retomar, ler:** este TL;DR + tabela de fases.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| P | Recorrência (gerar próxima + reopen apaga) | concluído | 2026-06-09 15:11 | `71bfe4b`/`9d309eb` |
| Q | 1 tarefa ativa por empregado (onConflict) | concluído | 2026-06-09 15:11 | `71bfe4b`/`9d309eb` |
| R | Editar hora manual | concluído | 2026-06-09 15:11 | `71bfe4b`/`9d309eb` |
| S | Pausa geral | concluído | 2026-06-09 15:11 | `71bfe4b`/`9d309eb` |

### Próximos passos

- [ ] Onda 5 (Pessoal)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 13:40] A entidade `Task` JÁ tinha os campos de recorrência (`FrequencyType`, `FrequencyDays`, `Allow{Sun..Sat}`, `StartDate`) — faltava só a LÓGICA de gerar a próxima (comparativo: "meia-feature"). O núcleo (start/pause/resume/stop/complete/reopen, histórico, pausas, subtarefas) já era equivalente/superior ao antigo.
- [2026-06-09 14:55] Migração `029_add_task_recurrence_source.sql` aplicada na Railway: `tb_task` ganhou `task_recurrence_source_id bigint NULL`. Confirmado por `--query`.
- [2026-06-09 15:00] ARMADILHA CS0104: `TaskService.cs` NÃO tem `using Task = ...`; helpers que recebem a entidade precisam qualificar `ERP.Domain.Entities.Task` (bare `Task` colide com `System.Threading.Tasks.Task`).

## Inferências prováveis

- [2026-06-09 13:40] Status do TaskEmployee: Pendente → EmAndamento → Pausada → Finalizada (reopen→Pendente). "Ativa" = `Status == "EmAndamento"`.

## Dúvidas em aberto

- [2026-06-09 13:40] Editar Término no antigo dispara geração/remoção da próxima; aqui o editar-hora só ajusta timestamps (não muda status nem recorrência) — decisão de escopo p/ previsibilidade.

---

## Log cronológico (APPEND-ONLY)

## 2026-06-09 13:34 — [ativação]

Onda 4 ativada (programa 5/7). Branches `feature/onda-4-tarefas`. Comparativo Tier 2 #14 confirma as 4 lacunas; sistema antigo: GeraProximaTarefa na conclusão, alerta de "uma tarefa em andamento", EditTime modal, Pausa Geral (status 4 p/ todos).

## 2026-06-09 15:11 — [conclusão] Onda 4 concluída e arquivada

Critérios P,Q,R,S marcados. Migração 029 + `erp.sql`. Back: helpers de recorrência + `GenerateNextOccurrenceAsync` (idempotente via `GetGeneratedOccurrenceAsync`), `StartTaskAsync(onConflict)` + `GetActiveByEmployeeAsync`, `EditTaskEmployeeTimeAsync`, `GlobalPauseAsync`; endpoints novos no `TaskController`. Front: `MyTasks` (start com prompt de conflito, modal editar hora, botão pausa geral) + `taskService`. `dotnet build` 0 erros; `npm run build` ✓. Feature `tarefas` atualizada (R.7). Commits feature `71bfe4b`/`9d309eb`; merge na main LOCAL erp-back `7c3c120` / erp-front `985700b` (sem push). Pasta `active/`→`archive/`.

**Entregue na Onda 4:** geração automática da próxima tarefa recorrente (com remoção ao reabrir); regra de uma tarefa em andamento por empregado com resolução pausar/finalizar; edição manual das horas de início/término; pausa geral de toda a equipe de uma tarefa.
