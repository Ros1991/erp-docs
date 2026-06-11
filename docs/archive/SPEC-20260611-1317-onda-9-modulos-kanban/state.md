# State — SPEC-20260611-1317

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-11 13:17

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-11 13:31
**Onde tô:** Onda 9 CONCLUÍDA e arquivada. Módulos granulares + kanban + fix do header. Builds limpos, mergeado e pushed.
**Próximo passo:** nenhum.
**Última decisão:** controllers aceitam novo OU antigo (cargos existentes não quebram); a migração 034 copia o acesso antigo→novo no JSONB; o front usa as chaves novas.
**Bloqueio atual:** nenhum.
**Se retomar, ler:** memory.md.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| A-E | Módulos granulares (catálogo, migração, controllers, guarda, front) | concluído | 2026-06-11 13:31 | — |
| F | Quadro kanban em Minhas Tarefas | concluído | 2026-06-11 13:31 | — |
| G | Fix do header (h-16) | concluído | 2026-06-11 13:31 | — |

### Próximos passos

- [x] Fechar onda (docs + merge + push) (2026-06-11 13:31)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-11 13:20] JSONB de permissões do cargo: `{"modules": {"<key>": {"canView":bool,...,"extraPermissions":{"canManage":bool}}}, "isAdmin":bool, "isSystemRole":bool}` (camelCase; gerado pelo RoleForm; lido case-insensitive pelo `PermissionService`). Ações não-CRUD (canManage/canPunch/canExecute) vivem em `extraPermissions`.
- [2026-06-11 13:22] Migração 034 aplicada: dos 8 cargos, 3 tinham `task` configurado → ganharam `myTasks`; os com `timeClock` ganharam `timeBank`; nenhum tinha `companySettings`/`employee`/`accountPayableReceivable`/`costCenter` configurados → cópias corretamente não geradas (sem fonte = sem acesso antes).
- [2026-06-11 13:25] `IPermissionService.UserHasPermissionAsync(userId, companyId, "task", "edit")` — a ação é sem o prefixo `can` (constantes `Actions`).
- [2026-06-11 13:28] **Header**: layout reserva 64px (`pt-16`/`top-16`) mas o header tinha `py-4` + logo `h-10` (≈72px) → cobria o topo. Fix: `h-16` fixo no container interno.
- [2026-06-11 13:29] erp-front tem `"type": "module"` no package.json — scripts Node soltos precisam de extensão `.cjs`. Rotas/arquivos têm CRLF — casar strings com `\r\n` ou buscar por índice.

## Inferências prováveis

- [2026-06-11 13:20] O RoleForm renderiza os módulos a partir do catálogo (`/module-configuration`) — os 8 módulos novos aparecem na tela de cargos automaticamente, sem mudança no front de cargos.

## Dúvidas em aberto

_nenhuma_

---

## Log cronológico (APPEND-ONLY)

## 2026-06-11 13:17 — [ativação]

Onda 9 ativada (3 pedidos do usuário: módulos por tela p/ cargos, Minhas Tarefas em quadro, header engolindo layout). Branches `feature/onda-9-modulos-kanban`. Inventário completo de RequirePermissions/sidebar/rotas feito por agente.

## 2026-06-11 13:31 — [conclusão] Onda 9 concluída e arquivada

A-G entregues. Catálogo: +8 módulos (`myTasks`, `holiday`, `workSchedule`, `financialCategory`, `allocationTemplate`, `abono`, `vacation`, `timeBank`) + renames (`task`→"Gerenciar Tarefas", `costCenter`→"Atividades"). Migração 034 (dados) copiou acesso antigo→novo nos cargos. 40+ atributos de controller com OR (novo, antigo); guarda de propriedade p/ `myTasks.canExecute` (403 fora das próprias tarefas). Front: 7 itens de sidebar, 13 rotas e 7 checks de página nas chaves novas. `MyTasks` reescrito como quadro kanban (4 colunas, drag & drop com transições mapeadas + regra de 1-ativa, botões nos cards p/ mobile, busca, editar hora). Header `h-16`. Builds limpos; merge + push nos 3 repos.
