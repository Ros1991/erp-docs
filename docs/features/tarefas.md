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
| SPEC-20260609-1334 | 2026-06-09 | `71bfe4b` | Onda 4: recorrência automática (gera próxima ocorrência), 1 tarefa ativa por empregado, editar hora manual, pausa geral |
| SPEC-20260611-1231 | 2026-06-11 | — | Onda 7: gestão completa na edição (`TaskManagePanel`): funcionários com todas as ações, fotos (Base64 + galeria), comentários (endpoints novos); migração 032 (urls → text) |
| SPEC-20260611-1249 | 2026-06-11 | — | Onda 8: prioridade default ao criar tarefa = **Baixa** |
| SPEC-20260611-1317 | 2026-06-11 | — | Onda 9: **Minhas Tarefas vira quadro kanban** (colunas A fazer/Em andamento/Pausada/Finalizada, drag & drop com transições mapeadas + 1-ativa, botões nos cards p/ mobile) + módulo próprio `myTasks` (canView/canExecute) separado de "Gerenciar Tarefas" (`task.*`), com guarda de propriedade no servidor |
| SPEC-20260611-1317 | 2026-06-11 | `cf3e493` | (complemento) **modal de detalhes** ao clicar no card do quadro: descrição, datas/horas, responsáveis, fotos (galeria/câmera/lightbox) e comentários; endpoints de fotos/comentários aceitam `myTasks.*` |
| SPEC-20260612-1215 | 2026-06-12 | `9dd7de7`/`715020e` | Onda 10: frequências **Trimestral/Semestral/Anual** no form; **card de funcionários na criação** (envia `AssignedEmployeeIds`, guardado por `employee.canView`); **`POST api/task/my`** cria tarefa auto-atribuída ao empregado logado + botão "Nova tarefa" no quadro Minhas Tarefas |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/task`: CRUD (`getAll`, `getPaged`, `GET {id}`, `create`, `PUT {id}`, `DELETE {id}`) + `GET my-tasks` (tarefas do empregado logado) — todos sob `task.*`. **`POST my`** (Onda 10, `myTasks.canExecute` OU `task.canCreate`) cria tarefa já atribuída ao empregado do usuário logado. `create` aceita `AssignedEmployeeIds[]` (atribuição em lote na criação — o TaskForm usa desde a Onda 10). Atribuição: `POST {taskId}/assign/{employeeId}` e `DELETE {taskId}/unassign/{employeeId}`. **Ciclo de execução por empregado** (`task.canEdit`): `POST {taskId}/employee/{employeeId}/start | pause | resume | stop | complete | reopen`. Imagens: `GET/POST {taskId}/images`, `DELETE image/{imageId}`. Frequências no form: Diária/Semanal/Quinzenal/Mensal/Trimestral/Semestral/Anual/Personalizada (`ComputeNextStartDate` já tratava todas).

**Recursos operacionais (Onda 4, SPEC-20260609-1334):**
- **Recorrência automática** — ao concluir tarefa recorrente, gera a próxima ocorrência (data por `FrequencyType`/`FrequencyDays` + dia da semana permitido) reatribuindo os mesmos empregados; reabrir apaga a ocorrência gerada se ainda não iniciada. Vínculo `task_recurrence_source_id` (migração 029).
- **Uma tarefa ativa por empregado** — `start` aceita `?onConflict=pause|finish`; sem isso, bloqueia (`active_task`) se já houver outra `EmAndamento`.
- **Editar hora manual** — `PUT {taskId}/employee/{employeeId}/time` (start/end/clearStart/clearEnd), recomputa `ActualHours`.
- **Pausa geral** — `POST {taskId}/global-pause` pausa todos os empregados (`EmAndamento`/`Pendente`) com motivo.

**Gestão completa na edição (Onda 7, SPEC-20260611-1231):**
- **Comentários** — `GET/POST api/task/{taskId}/comments` (canView) e `DELETE api/task/comment/{id}` (canEdit); autor vem do JWT; DTOs/mapper que existiam sem fiação foram ligados.
- **`TaskManagePanel`** (dentro do `TaskForm` em edição): funcionários (atribuir/remover, iniciar respeitando 1-ativa, pausar c/ motivo, parar, finalizar, reabrir, editar hora, pausa geral), fotos (upload comprimido Base64 + galeria + lightbox + excluir) e comentários (listar/criar/excluir).
- **Migração 032** — `task_image_url` e `task_comment_attachment_url` viraram `text` (Base64 não cabia em varchar 2048).

Modelo rico: `Task` (título, descrição, `Priority`, recorrência via `FrequencyDays` + flags `AllowSunday..AllowSaturday`, `StartDate`/`EndDate`, `OverallStatus`, auto-relacionamentos `TaskIdParent` e `TaskIdBlocking`) → `TaskEmployee` (atribuição: `Status`, `EstimatedHours`/`ActualHours`, datas) → `TaskStatusHistory` (histórico de transições), `TaskPause` (pausas do cronômetro), `TaskImage` (anexos), `TaskComment` (comentários). A migration `022_add_task_and_timeclock_fields` ampliou os campos.

> Última atualização: 2026-06-12 12:52 (SPEC-20260612-1215 — Onda 10)

## Decisões arquiteturais ativas

- **Execução por empregado, não pela tarefa** (origem: estado pré-SPEC, 2026-06-08 20:15) — o cronômetro/estado de trabalho vive em `TaskEmployee` (start/pause/resume/stop/complete/reopen), permitindo múltiplos atribuídos com progresso independente; a `Task` agrega o `OverallStatus`.
- **Recorrência e dependências no próprio `Task`** (origem: estado pré-SPEC, 2026-06-08 20:15) — `FrequencyDays`/flags de dias úteis modelam tarefas recorrentes; `TaskIdParent`/`TaskIdBlocking` modelam subtarefas e bloqueios.
- **Recorrência gera na CONCLUSÃO** (origem: SPEC-20260609-1334, 2026-06-09) — a próxima ocorrência nasce ao concluir (espelha `GeraProximaTarefa`), não é pré-agendada; idempotente via `task_recurrence_source_id`; reabrir apaga a gerada se `ActualStartDate == null`.
- **Editar-hora só corrige timestamps** (origem: SPEC-20260609-1334, 2026-06-09) — não muda status nem dispara recorrência (escopo previsível, diferente do antigo).

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Todas as transições de execução usam `task.canEdit`** (2026-06-08 20:15) — não há permissão separada para "executar tarefa"; start/pause/complete etc. exigem `canEdit`, e a tela "Minhas Tarefas" usa `task.canView`.
- **Histórico de status é append (`TaskStatusHistory`)** (2026-06-08 20:15) — cada mudança de status do `TaskEmployee` gera um registro; não sobrescrever.
- **CS0104 em `TaskService.cs`** (2026-06-09, SPEC-20260609-1334) — o arquivo usa `using ERP.Domain.Entities;` SEM alias de `Task`; ao referenciar a entidade em parâmetros/`new`, qualificar `ERP.Domain.Entities.Task` (bare `Task` colide com `System.Threading.Tasks.Task`).
- **"Tarefa ativa" = `Status == "EmAndamento"`** (2026-06-09, SPEC-20260609-1334) — a regra de 1 ativa por empregado e a pausa geral dependem desse valor exato de status.
- **Listar empregados exige `employee.canView`** (2026-06-12 12:52, SPEC-20260612-1215) — TaskForm (criação) e TaskManagePanel consultam `/employee/getPaged`; sem a permissão o 403 é tratado pelo interceptor global como perda de acesso à empresa (redirect p/ /select-company). O card de criação só renderiza/busca com `hasPermission('employee.canView')` — manter esse guard em telas novas.

## Estado congelado (se houver)

_(nenhum)_
