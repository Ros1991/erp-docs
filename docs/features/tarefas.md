# Feature: tarefas

**Keywords:** tarefa, task, atribuição, execução, pausa, cronômetro, recorrência, status, imagem, comentário, minhas-tarefas
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/TaskController.cs
  - erp-back/-2-Application/Services/TaskService.cs
  - erp-back/-1-Domain/Entities/task.cs
  - erp-back/-1-Domain/Entities/taskEmployee.cs
  - erp-back/-1-Domain/Entities/taskStatusHistory.cs
  - erp-back/-1-Domain/Entities/taskPause.cs
  - erp-back/-1-Domain/Entities/taskImage.cs
  - erp-back/-1-Domain/Entities/taskComment.cs
  - erp-front/src/pages/tasks/
  - erp-front/src/pages/my-tasks/
**Resumo:** Gestão de tarefas com atribuição por empregado e ciclo de execução cronometrado (iniciar/pausar/retomar/parar/concluir/reabrir), recorrência por dias da semana, dependências entre tarefas, e anexos de imagem/comentários.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| — | — | — | _(nenhuma SPEC ainda — feature documentada por engenharia reversa do estado atual)_ |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/task`: CRUD (`getAll`, `getPaged`, `GET {id}`, `create`, `PUT {id}`, `DELETE {id}`) + `GET my-tasks` (tarefas do empregado logado) — todos sob `task.*`. Atribuição: `POST {taskId}/assign/{employeeId}` e `DELETE {taskId}/unassign/{employeeId}`. **Ciclo de execução por empregado** (`task.canEdit`): `POST {taskId}/employee/{employeeId}/start | pause | resume | stop | complete | reopen`. Imagens: `GET/POST {taskId}/images`, `DELETE image/{imageId}`.

Modelo rico: `Task` (título, descrição, `Priority`, recorrência via `FrequencyDays` + flags `AllowSunday..AllowSaturday`, `StartDate`/`EndDate`, `OverallStatus`, auto-relacionamentos `TaskIdParent` e `TaskIdBlocking`) → `TaskEmployee` (atribuição: `Status`, `EstimatedHours`/`ActualHours`, datas) → `TaskStatusHistory` (histórico de transições), `TaskPause` (pausas do cronômetro), `TaskImage` (anexos), `TaskComment` (comentários). A migration `022_add_task_and_timeclock_fields` ampliou os campos.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Execução por empregado, não pela tarefa** (origem: estado pré-SPEC, 2026-06-08 20:15) — o cronômetro/estado de trabalho vive em `TaskEmployee` (start/pause/resume/stop/complete/reopen), permitindo múltiplos atribuídos com progresso independente; a `Task` agrega o `OverallStatus`.
- **Recorrência e dependências no próprio `Task`** (origem: estado pré-SPEC, 2026-06-08 20:15) — `FrequencyDays`/flags de dias úteis modelam tarefas recorrentes; `TaskIdParent`/`TaskIdBlocking` modelam subtarefas e bloqueios.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Todas as transições de execução usam `task.canEdit`** (2026-06-08 20:15) — não há permissão separada para "executar tarefa"; start/pause/complete etc. exigem `canEdit`, e a tela "Minhas Tarefas" usa `task.canView`.
- **Histórico de status é append (`TaskStatusHistory`)** (2026-06-08 20:15) — cada mudança de status do `TaskEmployee` gera um registro; não sobrescrever.

## Estado congelado (se houver)

_(nenhum)_
