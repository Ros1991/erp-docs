# SPEC-20260609-1334: Onda 4 — Tarefas (recorrência, 1 ativa, editar hora, pausa geral)

**Status:** done
**Criada:** 2026-06-09 13:34
**Ativada:** 2026-06-09 13:34
**Concluída:** 2026-06-09 15:11
**Pausada em:** —
**Commit final:** erp-back `71bfe4b` / erp-front `9d309eb`
**Keywords:** tarefa, recorrência, ocorrência, escala, uma tarefa ativa, editar hora, pausa geral, apontamento
**Features:** tarefas
**Branch:** feature/onda-4-tarefas (erp-back + erp-front)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** 5/7 — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (Tier 2 #14 "Tarefas — recursos operacionais"); usuário em 2026-06-09 (mandato de execução autônoma)
**Resumo:** Recursos operacionais de tarefas que faltavam no novo: geração automática da próxima ocorrência recorrente, regra de uma tarefa em andamento por empregado, edição manual da hora de início/término e pausa geral da equipe.

## Objetivo

Fechar as 4 lacunas operacionais de Tarefas (o núcleo já era equivalente/superior ao antigo): (1) gerar a próxima ocorrência ao concluir uma tarefa recorrente; (2) impedir 2 tarefas "em andamento" simultâneas para o mesmo empregado, oferecendo pausar/finalizar a atual; (3) corrigir manualmente as horas de início/término; (4) pausar de uma vez todos os empregados de uma tarefa.

## Escopo

**DENTRO:**
- **P** — Recorrência: ao concluir tarefa recorrente, gera a próxima ocorrência (próxima data por `FrequencyType`/`FrequencyDays` + snap p/ dia da semana permitido), reatribuindo os mesmos empregados; reabrir apaga a ocorrência gerada se ainda não iniciada. Coluna `task_recurrence_source_id` (migração 029) + helpers no `TaskService`.
- **Q** — Uma tarefa ativa por empregado: `StartTaskAsync(onConflict)` bloqueia se já há outra `EmAndamento`; `onConflict=pause|finish` resolve a atual antes de iniciar.
- **R** — Editar hora manual: `PUT api/task/{taskId}/employee/{employeeId}/time` (start/end + clear) recomputa `ActualHours`.
- **S** — Pausa geral: `POST api/task/{taskId}/global-pause` pausa todos os empregados (`EmAndamento`/`Pendente`) com motivo.
- Frontend (`MyTasks`): start com prompt de conflito (pausar/finalizar), modal "Editar hora", botão "Pausa geral".

**FORA (deferido — transparente, R.6.2):**
- **Modal estruturado de motivo de pausa/parada** no front (hoje a pausa geral usa `prompt` nativo; a pausa individual já existia sem motivo no front): polimento de UX → futuro.
- **Galeria/upload de imagens de tarefa com câmera** (comparativo §4.5): é o tema transversal de **Anexos/Multimídia** (gerenciador de anexos) → Onda 6.

## Implementação

Migração 029 (aditiva, 1 coluna nullable) aplicada na Railway via `node .migrate/apply.js` + `erp.sql`. Backend reusa o `TaskService`/`TaskRepository` existentes (2 métodos novos no repo, helpers de recorrência, ajustes em Start/Complete/Reopen, 2 métodos novos editar-hora/pausa-geral) + endpoints no `TaskController`. Frontend estende `taskService` e `MyTasks`. `dotnet build` + `npm run build` limpos.

## Critério de aceite

- [x] **P** — recorrência (gera próxima ao concluir + reopen apaga não-iniciada; migração 029) (2026-06-09 15:11, `71bfe4b`/`9d309eb`)
- [x] **Q** — uma tarefa ativa por empregado (`StartTaskAsync` + `onConflict` + UI de conflito) (2026-06-09 15:11, `71bfe4b`/`9d309eb`)
- [x] **R** — editar hora manual (endpoint + modal) (2026-06-09 15:11, `71bfe4b`/`9d309eb`)
- [x] **S** — pausa geral (endpoint + botão) (2026-06-09 15:11, `71bfe4b`/`9d309eb`)
- [x] migração 029 aplicada na Railway + `erp.sql` atualizado (2026-06-09 14:55)
- [x] erp-back compila e erp-front builda (2026-06-09 15:11)
- [x] **Feature tocada (tarefas) atualizada** com timestamp e referência a esta SPEC (2026-06-09 15:11)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 15:11)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 15:11)
