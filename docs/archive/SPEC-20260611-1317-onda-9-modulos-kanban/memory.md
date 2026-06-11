# Memory — SPEC-20260611-1317 (Onda 9 — Módulos granulares + Kanban + Header)

> Main: [main.md](./main.md) · State: [state.md](./state.md)

## TL;DR final

**Onda 9 entregue (2026-06-11 13:31).**
- **Módulos granulares**: catálogo ganhou `myTasks` (canView/canExecute), `holiday`, `workSchedule`, `financialCategory`, `allocationTemplate`, `abono`, `vacation`, `timeBank`; `task` renomeado p/ "Gerenciar Tarefas" e `costCenter` p/ "Atividades". Migração **034 (só dados)** copiou o acesso antigo→novo no JSONB dos cargos. **Controllers aceitam novo OU antigo** (cargos legados não quebram); front usa as chaves novas. **Guarda de propriedade**: `myTasks.canExecute` sem `task.canEdit` → só nas próprias tarefas (403).
- **Minhas Tarefas = quadro kanban**: colunas A fazer/Em andamento/Pausada/Finalizada, drag & drop HTML5 com transições mapeadas (iniciar c/ regra de 1-ativa, pausar c/ motivo, finalizar, reabrir), botões nos cards (mobile), busca, editar hora.
- **Header**: `h-16` fixo (layout reservava 64px e o header tinha ~72px, cobrindo sidebar/conteúdo).

## Contexto / decisões

- **OR (novo, antigo) nos controllers + cópia no JSONB**: dupla proteção — o backend não quebra com cargos antigos E os cargos ganham as chaves novas para o front (que migrou). Quem configurar um cargo novo já enxerga os 8 módulos no RoleForm (renderiza do catálogo, zero mudança lá).
- **Separação Gerenciar × Minhas**: `task.*` é gestão (CRUD, atribuir, agir sobre qualquer um, pausa geral); `myTasks.canView/canExecute` é o quadro próprio. Endpoints de execução aceitam ambos, com a guarda fazendo o cerco no servidor (não só na UI).
- **Kanban sem lib**: HTML5 drag & drop (desktop) + botões contextuais (mobile/touch). Transições inválidas dão toast claro. Sem persistência de ordem dentro da coluna (fora de escopo).
- **JSONB shape**: `modules.<key>.{canView,canCreate,canEdit,canDelete, extraPermissions:{canX}}` camelCase; ações não-CRUD via `extraPermissions` (o `RequirePermissionsAttribute`/`PermissionService` resolve `canExecute` igual resolve `canManage`/`canPunch`).

## Armadilhas

- **`UserHasPermissionAsync(.., "task", "edit")`** — ação SEM o prefixo `can` (constantes `Actions`); `"canEdit"` cairia no branch de extraPermissions e falharia.
- **erp-front é `"type": "module"`** — script Node solto no repo precisa ser `.cjs`.
- **Arquivos com CRLF** — matching de strings multi-linha em scripts precisa de `\r\n` (ou busca por índice/janela, como feito).
- A migração 034 é **condicional à fonte** (`WHERE ... ? 'companySettings'`): cargo sem o módulo-fonte não ganha o novo (correto — não tinha acesso antes). Rodá-la de novo é no-op (`NOT ? 'novo'`).

## Referências

- Catálogo: `-4-WebApi/Configuration/modules-configuration.json` · Migração: `034_granular_permission_modules.sql` · Guarda: `TaskController.EnsureSelfOrTaskManagerAsync` · Quadro: `src/pages/my-tasks/MyTasks.tsx` · Header: `src/components/layout/Header.tsx`.
