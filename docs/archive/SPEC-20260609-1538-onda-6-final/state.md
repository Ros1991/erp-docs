# State — SPEC-20260609-1538

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 15:38

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 15:52
**Onde tô:** Onda 6 (ÚLTIMA) CONCLUÍDA e arquivada. Back+front buildam limpo, mergeados na main LOCAL (erp-back `40ccb67`, erp-front `bf46eef`). PUSH FINAL das 3 mains feito — **programa encerrado**.
**Próximo passo:** nenhum — programa "Migração Fazenda → Meu Gestor" (7 ondas) concluído.
**Última decisão:** desligamento usa `ExecuteUpdateAsync` (sem depender de tracking) para reatribuir tarefas/subordinados; mapa e gerenciador de anexos deferidos (não-verificável headless / refactor).
**Bloqueio atual:** nenhum.
**Se retomar, ler:** memory.md final + comparativo (itens deferidos #20/#23).

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| A | Notificações (#18) | concluído | 2026-06-09 15:52 | `385b415`/`cd8c4a1` |
| B | Template de rateio (#22) | concluído | 2026-06-09 15:52 | `385b415`/`cd8c4a1` |
| C | Desligamento (#15) | concluído | 2026-06-09 15:52 | `385b415`/`cd8c4a1` |

### Próximos passos

- [x] Push final das 3 mains (programa encerrado) (2026-06-09 15:52)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 15:40] `CompanyUser` não tinha flag de ativo → migração 031 adicionou `company_user_is_active` (default true).
- [2026-06-09 15:44] Migração `031_onda6_final.sql` aplicada na Railway: `tb_notification`, `tb_cost_center_allocation_template` (+ linhas), `company_user_is_active`. Confirmado por `--query`.
- [2026-06-09 15:48] Reatribuições do desligamento usam `ExecuteUpdateAsync` (EF Core 9, escrita imediata sem tracking): `ReassignOpenTasksAsync` (TaskEmployee.Status != Finalizada), `ReassignSubordinatesAsync` (Employee.EmployeeIdManager), `DeactivateAccessAsync` (CompanyUser.IsActive=false).

## Inferências prováveis

- [2026-06-09 15:40] Notificações usam só `[Authorize]` (recurso pessoal — cada usuário vê as suas); template usa `costCenter.*`.

## Dúvidas em aberto

- [2026-06-09 15:40] Geração automática de notificações (ao criar justificativa / atribuir tarefa): a infra está pronta (`api/notification/create`); os gatilhos automáticos podem ser ligados numa SPEC futura.

---

## Log cronológico (APPEND-ONLY)

## 2026-06-09 15:38 — [ativação]

Onda 6 (ÚLTIMA) ativada (programa 7/7). Branches `feature/onda-6-final`. Comparativo: #15 desligamento, #18 notificações, #22 template; #20 mapa e #23 anexos deferidos.

## 2026-06-09 15:52 — [conclusão] Onda 6 concluída + PROGRAMA ENCERRADO

Critérios A,B,C marcados. Migração 031 + `erp.sql`. Back: módulos Notification e CostCenterAllocationTemplate (CRUD canônico) registrados; desligamento (`CompanyUser.IsActive` + `EmployeeService.DeactivateAsync` + reatribuições via ExecuteUpdate + endpoint). Front: `NotificationBell` no Header, tela de templates de rateio, modal de desligar na lista de Empregados. `dotnet build` 0 erros; `npm run build` ✓. Features tocadas (R.7): notificacoes (nova), empresas-multitenancy (desligamento), centros-de-custo (template). Commits feature `385b415`/`cd8c4a1`; merge na main LOCAL erp-back `40ccb67` / erp-front `bf46eef`. Pasta `active/`→`archive/`. **PUSH FINAL** das 3 mains (erp-back, erp-front, erp-docs) — único push do programa.

**Entregue na Onda 6:** notificações com sino no header; template de rateio nomeado; desligamento de empregado com redirecionamento de tarefas/subordinados + soft-delete do acesso. Deferidos transparentes: mapa Leaflet (#20), gerenciador de anexos genérico (#23).
