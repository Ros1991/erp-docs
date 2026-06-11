# State — SPEC-20260611-1249

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-11 12:49

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-11 13:07
**Onde tô:** Onda 8 CONCLUÍDA e arquivada. 9/9 itens entregues + bônus (categoria herdada do título). Builds limpos, mergeado e pushed.
**Próximo passo:** nenhum.
**Última decisão:** o rename Centro de Custo→Atividade é só UI (rotas/código/permissões/banco intactos); a tela de transações é read-only, então a categoria entra na GERAÇÃO da transação (herdada do título).
**Bloqueio atual:** nenhum.
**Se retomar, ler:** memory.md.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| 1-9 | Os 9 itens do usuário | concluído | 2026-06-11 13:07 | — |
| B | Bônus: categoria herdada do título | concluído | 2026-06-11 13:07 | — |

### Próximos passos

- [x] Fechar onda (docs + merge + push) (2026-06-11 13:07)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-11 12:52] `FinancialTransactionForm.tsx` é **código morto** (não roteado/importado) — a lista de transações é read-only; transações nascem de título pago/empréstimo/folha/OC. Por isso a categoria precisa ser herdada na geração (`AccountPayableReceivableService.CreateFinancialTransactionAsync`).
- [2026-06-11 12:55] Migração `033_add_whatsapp.sql` aplicada: `employee_whatsapp` + `supplier_customer_whatsapp` varchar(20). Confirmado por `--query`.
- [2026-06-11 12:58] `User` não tem nome próprio; o apelido vem do `Employee` associado (`GetMyEmployee` no front; lookup por `UserId` no `CompanyUserService`).
- [2026-06-11 13:00] Rename: 96+6 ocorrências em 15 arquivos + passada de concordância de gênero (Atividade atualizada/criada/excluída, "Selecione uma atividade", "Nova Atividade", etc.). 0 sobras.

## Inferências prováveis

- [2026-06-11 12:50] A visão master de empréstimos lista TODOS os funcionários (com dívida zerada quando não devem) para o botão `+` servir para qualquer um; filtro "Apenas com dívida" disponível.

## Dúvidas em aberto

_nenhuma_

---

## Log cronológico (APPEND-ONLY)

## 2026-06-11 12:49 — [ativação]

Onda 8 ativada a pedido do usuário (lista de 9 melhorias). Branches `feature/onda-8-melhorias`.

## 2026-06-11 13:07 — [conclusão] Onda 8 concluída e arquivada

9/9 itens + bônus. Migração de WhatsApp aplicada + `erp.sql`. Back: WhatsApp (Employee/SupplierCustomer), `EmployeeNickname` (CompanyUser), `employee-summaries` + filtro `EmployeeId` + ILike (LoanAdvance), categoria herdada do título (APR→FT). Front: Dashboard real (permission-guarded), LoanAdvances master/detalhe + form pré-selecionado, WhatsApp nos forms, apelido em Users/Header, rename Atividade (com gênero), combo gerente, default Baixa. Builds limpos. Merge + push nos 3 repos.
