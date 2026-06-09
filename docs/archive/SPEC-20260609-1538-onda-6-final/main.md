# SPEC-20260609-1538: Onda 6 (final) — Notificações + Template de rateio + Desligamento

**Status:** done
**Criada:** 2026-06-09 15:38
**Ativada:** 2026-06-09 15:38
**Concluída:** 2026-06-09 15:52
**Pausada em:** —
**Commit final:** erp-back `385b415` / erp-front `cd8c4a1`
**Keywords:** notificação, sino, desligamento, soft-delete, redirecionar tarefas, subordinados, template de rateio, centro de custo
**Features:** notificacoes, empresas-multitenancy, centros-de-custo
**Branch:** feature/onda-6-final (erp-back + erp-front)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** 7/7 (ÚLTIMA) — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (#15 desligamento, #18 notificações, #22 template de rateio; #20 mapa e #23 anexos deferidos); usuário em 2026-06-09 (mandato de execução autônoma)
**Resumo:** Itens transversais finais: módulo de notificações (sino no header), template de rateio nomeado e fluxo de desligamento/desativação de empregado com redirecionamento de tarefas e subordinados.

## Objetivo

Fechar o programa de migração com os itens transversais: **notificações** (#18, módulo inexistente), **template de rateio nomeado** (#22) e **desligamento** (#15, evita órfãos ao redirecionar tarefas/subordinados antes de tirar o acesso).

## Escopo

**DENTRO:**
- **A** — Notificações (#18): `Notification` + `tb_notification` + CRUD `api/notification` (`mine`, `unread-count`, `{id}/read`, `read-all`, `DELETE`) por usuário logado; `NotificationBell` no Header (contador + dropdown + marcar lida/todas).
- **B** — Template de rateio (#22): `CostCenterAllocationTemplate` + linhas + CRUD `api/cost-center-allocation-template` (valida soma = 100%); tela `CostCenterAllocationTemplates` (cadastro com linhas).
- **C** — Desligamento (#15): `CompanyUser.is_active` (soft-delete) + `EmployeeService.DeactivateAsync` — redireciona tarefas em aberto (`ReassignOpenTasksAsync`) e subordinados (`ReassignSubordinatesAsync`) ao destino e remove o acesso (`DeactivateAccessAsync`); `POST api/employee/{id}/deactivate`; botão "Desligar" + modal de redirecionamento na lista de Empregados.

**FORA (deferido — transparente, R.6.2):**
- **Mapa Leaflet (#20)**: camada visual de geocerca — exige dependência nova (`leaflet`/`react-leaflet`) e renderização não verificável em ambiente headless; a regra de geocerca (Haversine) **já funciona** e é melhor que a do antigo. Fica para uma SPEC dedicada (com verificação visual).
- **Gerenciador de anexos genérico (#23)**: o Base64 já é o padrão e os gaps reais de anexo (justificativa) foram fechados nas Ondas 0/1. Um manager genérico (ver/baixar/remover unificado, decisão Base64 vs S3) é um refactor de arquitetura → SPEC futura.

## Implementação

Migração 031 (aditiva: `tb_notification`, `tb_cost_center_allocation_template` + linhas, coluna `company_user_is_active`) aplicada na Railway via `node .migrate/apply.js` + `erp.sql`. Backend: 2 slices CRUD (Notification, CostCenterAllocationTemplate) registrados + desligamento via `ExecuteUpdateAsync` (reatribuições atômicas por operação) no Employee/Task/CompanyUser. Frontend: sino + tela de templates + modal de desligar. `dotnet build` + `npm run build` limpos.

## Critério de aceite

- [x] **A** — notificações (módulo + sino) (2026-06-09 15:52, `385b415`/`cd8c4a1`)
- [x] **B** — template de rateio (CRUD + tela) (2026-06-09 15:52, `385b415`/`cd8c4a1`)
- [x] **C** — desligamento (soft-delete + redirecionamento + UI) (2026-06-09 15:52, `385b415`/`cd8c4a1`)
- [x] migração 031 aplicada na Railway + `erp.sql` atualizado (2026-06-09 15:44)
- [x] erp-back compila e erp-front builda (2026-06-09 15:52)
- [x] **Features tocadas (notificacoes, empresas-multitenancy, centros-de-custo) atualizadas** com timestamp e referência a esta SPEC (2026-06-09 15:52)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 15:52)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 15:52)
- [x] **PUSH FINAL** das mains (erp-back, erp-front, erp-docs) — fechamento do programa (2026-06-09 15:52)
