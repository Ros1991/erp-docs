# SPEC-20260611-1317: Onda 9 — Módulos de permissão granulares + Quadro de tarefas + fix do header

**Status:** done
**Criada:** 2026-06-11 13:17
**Ativada:** 2026-06-11 13:17
**Concluída:** 2026-06-11 13:31
**Pausada em:** —
**Commit final:** erp-back `cf6fad8` / erp-front `b0d465e`
**Keywords:** módulo, permissão, cargo, granular, myTasks, kanban, quadro, drag and drop, header, layout
**Features:** cargos-permissoes, tarefas, plataforma-core
**Branch:** feature/onda-9-modulos-kanban (erp-back + erp-front)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** pós-programa (solicitado pelo usuário em 2026-06-11)
**Origem:** usuário em 2026-06-11: "criar os módulos para configurar visualização nos cargos (Gerenciar tarefas é um módulo, Minhas tarefas é outro)" + "Minhas Tarefas mais dinâmico, tipo um quadro" + bug do header engolindo sidebar/conteúdo.
**Resumo:** Catálogo de permissões granular por tela (8 módulos novos + migração de dados nos cargos + guarda de propriedade), Minhas Tarefas em quadro kanban com drag & drop, e correção do header que cobria o topo da sidebar/conteúdo.

## Objetivo

(1) Catálogo de permissões **granular por tela**: cada funcionalidade vira um módulo configurável no cargo. (2) **Minhas Tarefas** vira um **quadro kanban** (colunas por status, drag & drop + botões). (3) Corrigir o header mais alto que o espaço reservado pelo layout.

## Escopo

**DENTRO:**
- **A — 8 módulos novos no catálogo** (`modules-configuration.json`): `myTasks` (canView/canExecute), `holiday`, `workSchedule`, `financialCategory`, `allocationTemplate`, `abono`, `vacation` (CRUD) e `timeBank` (canView/canEdit). Renomeados: `task`→"Gerenciar Tarefas", `costCenter`→"Atividades".
- **B — Migração 034 (dados)**: copia o acesso dos módulos antigos para os novos no JSONB dos cargos (idempotente; ex.: `task.canView`→`myTasks.canView`, `task.canEdit`→`myTasks.canExecute`, `companySettings`→`holiday`/`workSchedule`, `timeClock.canManage`→`timeBank`...). Cargos sem o módulo-fonte não ganham nada (semântica correta).
- **C — Controllers com OR (novo + antigo)**: 7 controllers (Holiday, WorkSchedule, FinancialCategory, AllocationTemplate, TimeBank, Abono, Vacation) + TaskController (`my-tasks` aceita `myTasks.canView`; os 7 endpoints de execução aceitam `myTasks.canExecute`).
- **D — Guarda de propriedade**: quem só tem `myTasks.canExecute` (sem `task.canEdit`) só executa ações nas **próprias** tarefas (`EnsureSelfOrTaskManagerAsync` → 403 caso contrário).
- **E — Front nas chaves novas**: sidebar (7 itens), rotas (13) e checks de página (7) usando os módulos novos (páginas com `hasAnyPermission(novo, antigo)`).
- **F — Quadro kanban em Minhas Tarefas**: colunas A fazer / Em andamento / Pausada / Finalizada; drag & drop (HTML5) com mapa de transições (iniciar/pausar/finalizar/reabrir + regra de 1-ativa com resolução); botões nos cards (mobile); busca; editar hora mantido.
- **G — Fix do header**: altura fixa `h-16` (o layout `pt-16` e a sidebar `top-16` assumem 64px; o `py-4` + logo `h-10` estouravam e o header cobria o topo do conteúdo/sidebar).

**FORA:** drag & drop por toque (mobile usa os botões dos cards); reordenação dentro da coluna (sem persistência de ordem).

## Implementação

Catálogo + migração 034 (só dados, sem DDL — `erp.sql` inalterado). Backend: 40+ atributos `[RequirePermissions]` com OR; guarda de propriedade no TaskController (IPermissionService + IEmployeeService). Front: chaves novas + `MyTasks.tsx` reescrito como quadro. Builds limpos.

## Critério de aceite

- [x] **A** — 8 módulos novos no catálogo + renames (2026-06-11 13:31)
- [x] **B** — migração 034 aplicada na Railway (cópia verificada: `myTasks`/`timeBank` nos cargos com fonte) (2026-06-11 13:31)
- [x] **C** — controllers aceitando novo OU antigo (sem quebrar cargos existentes) (2026-06-11 13:31)
- [x] **D** — guarda: `myTasks.canExecute` só age nas próprias tarefas (2026-06-11 13:31)
- [x] **E** — sidebar/rotas/páginas nas chaves novas (2026-06-11 13:31)
- [x] **F** — Minhas Tarefas em quadro kanban (drag & drop + botões) (2026-06-11 13:31)
- [x] **G** — header h-16 sem engolir sidebar/conteúdo (2026-06-11 13:31)
- [x] erp-back compila e erp-front builda (2026-06-11 13:31)
- [x] **Features tocadas (cargos-permissoes, tarefas, plataforma-core) atualizadas** (2026-06-11 13:31)
- [x] `state.md` com entrada `[conclusão]` (2026-06-11 13:31)
- [x] `memory.md` com TL;DR final atualizado (2026-06-11 13:31)
