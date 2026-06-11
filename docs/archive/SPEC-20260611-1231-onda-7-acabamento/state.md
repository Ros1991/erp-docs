# State — SPEC-20260611-1231

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-11 12:31

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-11 13:05
**Onde tô:** Onda 7 CONCLUÍDA e arquivada. Back+front buildam limpo, mergeados na main e pushed.
**Próximo passo:** nenhum (acabamento entregue). Futuro: anexo no comentário; relatório por categoria sobre transações.
**Última decisão:** "Aplicar template" vive DENTRO do `CostCenterDistribution` (todos os 5 formulários ganham de graça); relatório por categoria agrupa TÍTULOS (APR) por vencimento — é onde as categorias vivem desde a Onda 1.
**Bloqueio atual:** nenhum.
**Se retomar, ler:** memory.md.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| A | Aplicar template de rateio | concluído | 2026-06-11 13:05 | — |
| B | Categoria em transações | concluído | 2026-06-11 13:05 | — |
| C | Relatório por categoria | concluído | 2026-06-11 13:05 | — |
| D | Gestão completa da tarefa | concluído | 2026-06-11 13:05 | — |

### Próximos passos

- [x] Fechar onda (docs + merge + push) (2026-06-11 13:05)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-11 12:35] TaskComment tinha entidade + DTOs + mapper **sem** repo/service/controller (scaffold morto). A fiação reusa os DTOs existentes; `UserId` do comentário agora vem do JWT (não do payload).
- [2026-06-11 12:38] `task_image_url` e `task_comment_attachment_url` eram varchar(2048) → migração 032 vira text (Base64 não cabia; mesma natureza da 026).
- [2026-06-11 12:36] `tb_financial_transaction.financial_category_id` JÁ existia no banco (025) — item B não precisou de migração; só entity/DTOs/mapper/UI.
- [2026-06-11 12:50] `User` não tem Name — autor do comentário exibe email/telefone/cpf.

## Inferências prováveis

- [2026-06-11 12:31] Comentar exige só `task.canView` (colaboração); excluir comentário `task.canEdit`; excluir foto `task.canDelete` (segue o DELETE de imagem já existente).

## Dúvidas em aberto

_nenhuma_

---

## Log cronológico (APPEND-ONLY)

## 2026-06-11 12:31 — [ativação]

Onda 7 (acabamento) ativada a pedido do usuário: gaps das telas de template/plano de contas + edição de tarefa pobre. Branches `feature/onda-7-acabamento`.

## 2026-06-11 13:05 — [conclusão] Onda 7 concluída e arquivada

Critérios A,B,C,D marcados. Migração 032 + `erp.sql`. Back: TaskComment ligado (repo+registro+3 endpoints), FT.FinancialCategoryId, `GetCategoryReportAsync` + `GET api/report/category`. Front: "Aplicar template..." no `CostCenterDistribution` (5 formulários), dropdown de categoria no `FinancialTransactionForm`, página `CategoryReport` (+rota+sidebar), `TaskManagePanel` no `TaskForm` (funcionários com iniciar/pausar/parar/finalizar/reabrir/editar-hora/pausa-geral/atribuir/remover; fotos com upload comprimido + lightbox + excluir; comentários listar/criar/excluir) + `taskService` comments. Builds limpos. Features tocadas atualizadas (R.7). Merge na main + push.
