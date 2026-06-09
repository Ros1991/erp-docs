# Memory — SPEC-20260609-1334 (Onda 4 — Tarefas)

> Main: [main.md](./main.md) · State: [state.md](./state.md)

## TL;DR final

**Onda 4 entregue (2026-06-09 15:11).** 4 recursos operacionais de Tarefas:
- **Recorrência**: ao concluir tarefa recorrente, `GenerateNextOccurrenceAsync` cria a próxima ocorrência (data por `FrequencyType`/`FrequencyDays` + `SnapToAllowedDay`), reatribuindo os mesmos empregados; idempotente (`GetGeneratedOccurrenceAsync`); reabrir apaga a gerada se `ActualStartDate == null`. Vínculo: `task_recurrence_source_id` (migração 029).
- **1 ativa por empregado**: `StartTaskAsync(..., onConflict)` — se já há outra `EmAndamento` (`GetActiveByEmployeeAsync`), lança `active_task`; `onConflict=pause|finish` resolve a atual e inicia.
- **Editar hora**: `PUT api/task/{taskId}/employee/{employeeId}/time` (start/end/clearStart/clearEnd) recomputa `ActualHours`.
- **Pausa geral**: `POST api/task/{taskId}/global-pause` pausa todos os empregados `EmAndamento`/`Pendente` com motivo.
Front: `MyTasks` (prompt de conflito, modal editar-hora datetime-local, botão pausa geral) + `taskService`. Commits feature `71bfe4b`/`9d309eb`; main local `7c3c120`/`985700b` (sem push).

## Contexto / decisões

- **O núcleo de tarefas já existia** (start/pause/resume/stop/complete/reopen, histórico, pausas, subtarefas, `CompletesAll`). Esta onda só adicionou os 4 recursos operacionais — sem reescrever o workflow.
- **Recorrência gera na CONCLUSÃO** (não agenda no futuro), espelhando `GeraProximaTarefa`. `FrequencyType` é string livre — `ComputeNextStartDate` normaliza (diaria/semanal/quinzenal/mensal/trimestral/semestral/anual) e cai em `FrequencyDays` p/ customizada.
- **Editar-hora é puramente correção de timestamps** (não muda status nem dispara recorrência) — escopo enxuto e previsível (diferente do antigo, que ao editar Término podia gerar/remover a próxima). Registrado.
- **Permissão**: tudo sob `task.canEdit` (segue os endpoints de workflow existentes).

## Armadilhas

- **CS0104**: `TaskService.cs` usa `using ERP.Domain.Entities;` SEM alias de `Task` → qualquer parâmetro/`new` da entidade precisa de `ERP.Domain.Entities.Task` (senão colide com `System.Threading.Tasks.Task`). Os métodos `async Task<...>` continuam sendo o tipo de tarefa do .NET.
- **Idempotência da geração**: sempre checar `GetGeneratedOccurrenceAsync(sourceTaskId)` antes de gerar; reabrir/concluir em ciclo não duplica.
- **Front detecta o conflito** por `data.errors['active_task']` ou mensagem contendo "andamento".

## Referências

- Feature doc: [tarefas](../../features/tarefas.md).
- Sistema antigo: `04 - Tarefas/` (Nova Tarefa, Ver Tarefa, Editar Hora da Tarefa, Pausa Geral, Minhas Tarefas).
- Arquivos: `TaskService.cs`, `TaskRepository.cs`, `TaskController.cs`, `task.cs`, front `MyTasks.tsx`, `taskService.ts`.
