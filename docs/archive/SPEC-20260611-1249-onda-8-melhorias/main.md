# SPEC-20260611-1249: Onda 8 — Melhorias de usabilidade (9 itens do usuário)

**Status:** done
**Criada:** 2026-06-11 12:49
**Ativada:** 2026-06-11 12:49
**Concluída:** 2026-06-11 13:07
**Pausada em:** —
**Commit final:** erp-back `581665c` / erp-front `51106e8`
**Keywords:** dashboard, whatsapp, atividade, centro de custo, empréstimo por funcionário, apelido, case-insensitive, gerente, prioridade
**Features:** plataforma-core, empregados-contratos, fornecedores-clientes, usuarios, emprestimos-adiantamentos, centros-de-custo, tarefas, plano-de-contas
**Branch:** feature/onda-8-melhorias (erp-back + erp-front)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** pós-programa (melhorias solicitadas pelo usuário em 2026-06-11)
**Origem:** usuário em 2026-06-11 (lista de 9 melhorias de usabilidade)
**Resumo:** Nove melhorias diretas de usabilidade: dashboard real, WhatsApp em cadastros, tela de empréstimos por funcionário, rename Centro de Custo→Atividade, e refinamentos (combo gerente, apelido em usuários/header, busca case-insensitive, prioridade default).

## Objetivo

Atender a lista do usuário item a item, sem deixar meia-feature.

## Escopo

**DENTRO (os 9 itens):**
1. **Combo gerente sem "-" órfão** — `apelido - nome` só mostra o `-` quando há nome completo.
2. **Dashboard real** — contadores de funcionários e tarefas em aberto, receitas/despesas do mês (resumo financeiro), painel "Ponto hoje" (presentes/trabalhando/sem registro), "Minhas tarefas" (top 5), ações rápidas navegáveis — tudo com guarda de permissão; números fake removidos.
3. **Apelido do funcionário na lista de usuários** — `CompanyUserOutputDTO.EmployeeNickname` (preenchido por lookup 1-query no service) + exibição na tabela/cards.
4. **WhatsApp em funcionário e fornecedor** — migração 033 (`employee_whatsapp`, `supplier_customer_whatsapp`) + entity/DTOs/mappers + campo `PhoneInput` nos dois formulários.
5. **Empréstimos por funcionário** — visão master (`GET api/loan-advance/employee-summaries`: dívida total + buckets + em aberto, por funcionário) → clique abre só os empréstimos dele (filtro `EmployeeId` no getPaged) → botão `+` na linha abre o form com o funcionário pré-selecionado (`?employeeId=`, reusa o carregamento de rateio do contrato).
6. **Centro de Custo → Atividade** — rename de TODOS os textos de UI (~100 ocorrências em 15 arquivos, com correção de concordância de gênero); rotas/código/banco/permissões intactos.
7. **Busca case-insensitive em empréstimos** — `EF.Functions.ILike` no Search (fonte, apelido, descrição).
8. **"Olá" com apelido do funcionário** — Header e saudação do Dashboard usam o apelido do empregado associado (fallback: nome do usuário).
9. **Prioridade default "Baixa"** ao criar tarefa.

**BÔNUS (coerência):** a transação financeira gerada pelo pagamento de um título agora **herda a categoria** do título (fecha de verdade o fluxo do plano de contas — a tela de transações é somente leitura, então a categoria precisava vir da geração).

**FORA:** nada deferido — os 9 itens foram entregues.

## Implementação

Migração 033 aditiva (2 colunas WhatsApp). Back: WhatsApp em Employee/SupplierCustomer; `EmployeeNickname` no CompanyUser; `EmployeeLoanSummaryDTO` + `GetEmployeeLoanSummariesAsync` + endpoint; filtro `EmployeeId` + ILike no repo de empréstimos; categoria herdada no `CreateFinancialTransactionAsync` do APR. Front: Dashboard reescrito com dados reais; LoanAdvances em 2 níveis (master/detalhe) + pré-seleção no form; WhatsApp nos 2 forms; apelido em Users/Header; rename Atividade; combo gerente; default Baixa. Builds limpos.

## Critério de aceite

- [x] 1 — combo gerente sem "-" órfão (2026-06-11 13:07)
- [x] 2 — dashboard com dados reais + guardas de permissão (2026-06-11 13:07)
- [x] 3 — apelido do funcionário na lista de usuários (2026-06-11 13:07)
- [x] 4 — WhatsApp em funcionário e fornecedor (migração 033) (2026-06-11 13:07)
- [x] 5 — empréstimos por funcionário (master → detalhe → form pré-selecionado) (2026-06-11 13:07)
- [x] 6 — rename Centro de Custo → Atividade em toda a UI (0 sobras + concordância) (2026-06-11 13:07)
- [x] 7 — busca case-insensitive em empréstimos (ILIKE) (2026-06-11 13:07)
- [x] 8 — header/dashboard saúdam com o apelido do funcionário (2026-06-11 13:07)
- [x] 9 — prioridade default Baixa na nova tarefa (2026-06-11 13:07)
- [x] migração 033 aplicada na Railway + `erp.sql` atualizado (2026-06-11 12:55)
- [x] erp-back compila e erp-front builda (2026-06-11 13:07)
- [x] **Features tocadas atualizadas** (2026-06-11 13:07)
- [x] `state.md` com entrada `[conclusão]` (2026-06-11 13:07)
- [x] `memory.md` com TL;DR final atualizado (2026-06-11 13:07)
