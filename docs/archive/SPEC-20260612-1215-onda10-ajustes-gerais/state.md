# State — SPEC-20260612-1215

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-12 12:15

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-12 12:50
**Onde tô:** SPEC CONCLUÍDA — 11 itens implementados, revisados (4 bugs achados e corrigidos), builds verdes, código na main dos 2 repos (erp-back `9dd7de7`/merge `5c65e31`; erp-front `715020e`/merge `302ce45`).
**Próximo passo:** nenhum — arquivada. Migração `035` precisa ser aplicada manualmente no Postgres (Railway).
**Última decisão:** 13º/férias/adiantamento passam a respeitar o mês de referência do empréstimo (PeriodEndDate em todos os call sites).
**Bloqueio atual:** nenhum
**Se retomar, ler:** log de 2026-06-12 (entradas 12:25 em diante).

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| 1 | Exploração: mapear código dos 11 itens | concluído | 2026-06-12 12:25 | — |
| 2 | Implementação backend (empréstimo mês-ref, tarefa anual, feriados ano, task create c/ empregados, my-tasks) | concluído | 2026-06-12 12:40 | `9dd7de7` |
| 3 | Implementação frontend (comboboxes, lista empréstimos, expander folha, sidebar, user duplicado, forms) | concluído | 2026-06-12 12:42 | `715020e` |
| 4 | Build + revisão adversarial + correções (4 bugs) | concluído | 2026-06-12 12:48 | `9dd7de7`/`715020e` |
| 5 | Docs (R.7) + archive + commit/push | concluído | 2026-06-12 12:50 | — |

### Próximos passos

- [x] Exploração fan-out dos 11 itens (2026-06-12 12:25)
- [x] Implementar e validar item a item (2026-06-12 12:48, commits `9dd7de7`/`715020e`)
- [x] Builds verdes nos 2 repos (2026-06-12 12:48)
- [ ] (operacional, fora do repo) aplicar migração `035_loan_start_date_reference_month.sql` no Postgres Railway

### Bloqueios ativos

_(nenhum)_

---

## Fatos confirmados

- [2026-06-12 12:15] `docs/active/` estava vazio em main; repos erp-back e erp-front limpos e sincronizados com origin/main. Fonte: `git status`/`git log`.
- [2026-06-12 12:15] Padrão de entrega das ondas anteriores: branch "Onda N" nos repos de código → merge na main → push; docs commitados direto na main do repo raiz já com SPEC arquivada. Fonte: `git log --oneline` dos 3 repos.

## Inferências prováveis

- [2026-06-12 12:15] O início de cobrança do empréstimo (`StartDate`) é comparado por data na folha; mudar para comparação ano/mês deve bastar sem migração destrutiva. Validar com: leitura de `PayrollService` + `loanAdvance.cs`.

## Dúvidas em aberto

- [2026-06-12 12:15] Botão "Nova tarefa" em Minhas Tarefas: qual permissão governa (task.canCreate vs módulo myTasks)? Próxima ação: ler TaskController/modules-configuration.json e decidir na implementação.

---

## Log cronológico (APPEND-ONLY — NUNCA editar entradas antigas)

## 2026-06-12 12:15 — [ativação]

SPEC criada direto em active/ a partir de lote de 12 itens do usuário (2026-06-12 12:10). Usuário confirmou: recorte em 2 SPECs (esta + equipamentos), feature nova `equipamentos` aprovada para a outra SPEC, mapeamento de features confirmado. Plano: exploração fan-out → implementação sequencial (backend depois frontend) → builds → docs → archive → commit/push nas branches `feature/onda-10-ajustes` dos 2 repos + merge na main (padrão das ondas).

## 2026-06-12 12:25 — [descoberta] Exploração fan-out concluída (6 agentes)

Fatos-chave: (1) `TaskInputDTO` JÁ aceita `AssignedEmployeeIds[]` e `TaskService.CreateAsync` já atribui — item 8 é só frontend; (2) `ComputeNextStartDate` JÁ trata "anual"/"trimestral"/"semestral" — item 4 é só adicionar `<option>` no TaskForm; (3) bug do sidebar (item 6) é o `useEffect` de auto-expand por rota (Sidebar.tsx:75-94); (4) expander da folha: `PayrollDetails.tsx:803-807` com Chevron invertido; (5) empréstimo: `StartDate` é `date` no banco, folha filtra `StartDate <= PeriodEndDate` via `GetPendingLoansByEmployeeAsync` — normalizar para dia 1 preserva o filtro; entidade já tem `LoanDate` para a coluna do item 3; (6) bug do item 7: `CreateUserDialog` reseta o form no `useEffect([open, employeeData])` e `employeeData` é objeto inline recriado a cada re-render do pai (toast de erro → re-render → wipe).
Fonte: workflow onda10-exploracao (wf_c4d0cb35-ec1).

## 2026-06-12 12:40 — [MARCO] [decisão] Desenho dos 11 itens implementado

- **Item 1**: ordenação no backend — `EmployeeRepository.GetAllAsync` e default do `GetPagedAsync` viram `OrderBy(e => e.Nickname ?? e.FullName)` (COALESCE; combos exibem nickname||fullName). Zero mudança nos 6 consumidores sem orderBy.
- **Item 2**: `LoanAdvanceMapper.ToReferenceMonth` normaliza StartDate para dia 1 (create/update); migração `035` normaliza dados existentes + COMMENT na coluna; form usa `<input type=month>`; filtro da folha inalterado (`StartDate <= PeriodEndDate` ⇒ desconta a partir da folha cujo fim cai no mês de referência).
- **Item 3**: lista por funcionário ordena `orderBy=loanDate, isAscending=false`; 1ª coluna vira Data do Empréstimo (formatDate sem timezone p/ não voltar 1 dia em BRT).
- **Item 4**: options Trimestral/Semestral/Anual no TaskForm (backend já suportava).
- **Itens 8/9**: card de funcionários (checkboxes) no create do TaskForm enviando `assignedEmployeeIds`; novo endpoint `POST api/task/my` (`myTasks.canExecute` OU `task.canCreate`) auto-atribui via `GetByUserIdAsync`; modal "Nova tarefa" no MyTasks.
- **Item 10**: `HolidayService.GenerateYearAsync` (Páscoa por Meeus/Jones/Butcher; fixos + Carnaval seg/ter, Sexta Santa, Corpus Christi, Consciência Negra 20/11) pulando duplicados (data exata OU recorrente no mesmo dia/mês); endpoint `POST api/holiday/generate/{year}`; modal na tela.
- **Item 7**: fix do reset (só reseta na transição closed→open via ref) + novo endpoint `POST api/employee/searchUserByData` e painel "associar usuário existente" no CreateUserDialog (associa com `createCompanyUser=!hasCompanyAccess`).
- **Itens 6/11**: removido auto-expand; grupos RH / Financeiro / Cadastros / Ponto / Tarefas / Relatórios (ordem do usuário), Dashboard e Configurações soltos, grupo "Pessoal" fundido em RH; default expandido `['RH']`.

## 2026-06-12 12:42 — [nota] Builds verdes; lint pré-existente

`dotnet build` OK; `npm run build` (tsc) OK após remover `sortDirection` não usado. `npm run lint` já falha na main (190 erros `no-explicit-any` em arquivos não tocados); nos arquivos tocados: +4 erros do mesmo idioma `catch (err: any)` usado no projeto inteiro — mantido por consistência. Revisão adversarial do diff em andamento (workflow onda10-review).

## 2026-06-12 12:48 — [MARCO] [tentativa] Revisão adversarial achou 4 bugs — todos corrigidos

Workflow onda10-review (3 revisores + verificação adversarial; 0 refutados, 4 confirmados):
1. **Classificação "Adiantamento" quebrada pela normalização dia-1** (introduzido nesta SPEC): `LoanAdvanceService.CreateAsync` comparava `startDate.Date == hoje` → com mês de referência só seria Adiantamento no dia 1. Corrigido para comparar ano+mês corrente.
2. **TaskForm (create) chamava `/employee/getPaged` sem `employee.canView`** → 403 e o interceptor global derruba para /select-company. Corrigido: fetch e card guardados por `hasPermission('employee.canView')`.
3. **`IsAscending` é propriedade computada no `PagedRequest`** (sem setter) → o param era ignorado e a lista de empréstimos viria ASC. Corrigido no loanAdvanceService.ts: envia `OrderDirection=asc|desc` (padrão do financialTransactionService).
4. **13º/férias/adiantamento de férias ignoravam o mês de referência** (pré-existente, mas viola o contrato novo): 3 call sites de `GetPendingLoansByEmployeeAsync` sem `referenceDate`. Corrigido: todos passam `payroll.PeriodEndDate`.
Rebuild dos 2 repos: verde.

## 2026-06-12 12:50 — [conclusão] Onda 10 entregue

11 itens implementados, revisados e publicados. erp-back: `9dd7de7` (merge `5c65e31` na main, pushed). erp-front: `715020e` (merge `302ce45` na main, pushed). Critérios de aceite marcados no main.md; features atualizadas (R.7). Pendência operacional: aplicar migração `035` no Postgres (Railway) — normaliza `loan_advance_start_date` para dia 1.
