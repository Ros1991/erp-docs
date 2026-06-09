# Memory — SPEC-20260609-1538 (Onda 6 final — Notificações + Template + Desligamento)

> Main: [main.md](./main.md) · State: [state.md](./state.md)

## TL;DR final

**Onda 6 (ÚLTIMA) entregue (2026-06-09 15:52) — programa encerrado.** 3 itens transversais:
- **Notificações (#18)**: `tb_notification` + CRUD `api/notification` (mine/unread-count/read/read-all/delete) por usuário; `NotificationBell` no Header (badge + dropdown + poll 60s).
- **Template de rateio (#22)**: `tb_cost_center_allocation_template` (+ linhas) + CRUD `api/cost-center-allocation-template` (valida soma=100%); tela de cadastro.
- **Desligamento (#15)**: `CompanyUser.is_active` (soft-delete) + `EmployeeService.DeactivateAsync` — redireciona tarefas em aberto e subordinados ao destino (via `ExecuteUpdateAsync`) e remove o acesso; `POST api/employee/{id}/deactivate` + modal na lista de Empregados.
Migração 031 (3 objetos). Commits feature `385b415`/`cd8c4a1`; main local `40ccb67`/`bf46eef`; **push final** das 3 mains.

## Contexto / decisões

- **Notificações são pessoais** — só `[Authorize]` (sem `RequirePermissions`); cada usuário vê/gerencia as suas (filtro por `UserId`+`CompanyId`). A criação (`create`) existe para gatilhos do sistema; os gatilhos automáticos ficam para futuro.
- **Desligamento via `ExecuteUpdateAsync`** (EF Core 9) — escrita em lote imediata, sem depender de tracking; cada reatribuição é uma operação. Exige empregado de destino se houver tarefas em aberto (senão lança ValidationException). Subordinados podem ir para `null`.
- **Template valida soma=100%** no service (espelha a validação de rateio já existente).
- **Deferidos (R.6.2)**: mapa Leaflet (#20) — dependência nova + render não-verificável headless; gerenciador de anexos (#23) — Base64 já é padrão, gaps reais já fechados (Ondas 0/1).

## Armadilhas

- `ExecuteUpdateAsync` executa imediatamente (não participa do `SaveChangesAsync` do UnitOfWork) — ok para a ação discreta de desligamento; as 3 reatribuições não são uma transação única (aceitável aqui).
- `TaskEmployee.EmployeeId` é `long` (não-nulável) → reatribuição de tarefas EXIGE destino; `Employee.EmployeeIdManager` é `long?` → subordinados podem ficar sem gerente.

## Referências

- Feature docs: [notificacoes](../../features/notificacoes.md), [empresas-multitenancy](../../features/empresas-multitenancy.md), [centros-de-custo](../../features/centros-de-custo.md).
- Sistema antigo: Desativar Usuário (redireciona tarefas/subordinados), Notificações (sino), Template de rateio.
