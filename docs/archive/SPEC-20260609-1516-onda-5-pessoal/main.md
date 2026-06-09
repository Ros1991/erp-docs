# SPEC-20260609-1516: Onda 5 — Pessoal (empréstimos, benefícios, abonos, férias)

**Status:** done
**Criada:** 2026-06-09 15:16
**Ativada:** 2026-06-09 15:16
**Concluída:** 2026-06-09 15:33
**Pausada em:** —
**Commit final:** erp-back `34f5246` / erp-front `efd91b4`
**Keywords:** empréstimo, dívida, bucket, 13º, férias, benefício, percentual, abono, dias, horas, período aquisitivo, salário comprometido
**Features:** emprestimos-adiantamentos, empregados-contratos, folha-pagamento, abonos-ferias
**Branch:** feature/onda-5-pessoal (erp-back + erp-front)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** 6/7 — programa "Migração Fazenda → Meu Gestor"
**Origem:** comparativo [docs/comparativo-sistema-antigo.md](../../comparativo-sistema-antigo.md) (Tier 2 #13 resumo de empréstimos; Tier 3 #24 % no benefício, abono em dias, período de férias); usuário em 2026-06-09 (mandato de execução autônoma)
**Resumo:** Recursos de Pessoal/RH que faltavam: resumo de dívida de empréstimos por empregado (buckets + % do salário), benefício como percentual do salário, cadastro de abonos (Dias/Horas) e de períodos de férias (aquisitivo + gozo).

## Objetivo

Fechar lacunas de RH do comparativo: o **resumo de empréstimos** (dívida por bucket Mês/13º/Férias + alerta de comprometimento do salário — #13), o **benefício percentual** do salário (#24), o **abono em dias** como cadastro próprio (#24) e o **período de férias** com período aquisitivo (#24).

## Escopo

**DENTRO:**
- **A** — Resumo de empréstimos por empregado: `GET api/loan-advance/employee/{id}/summary` (buckets via `DiscountSource`: Mês/13º/Férias + dívida total + parcela mensal + % do salário do contrato ativo + alerta ≥30%) + tela `LoanSummary`. Sem migração (usa dados existentes).
- **B** — Benefício percentual: `ContractBenefitDiscount` ganha `IsPercentage` + `Percentage`; a folha (`PayrollService`) calcula `% × salário base` quando marcado (aditivo, **default-off** preserva o comportamento atual); UI no `BenefitDiscountList`. Migração 030 (2 colunas).
- **C** — Abonos (Dias/Horas): entidade `EmployeeAbono` + `tb_employee_abono` + CRUD `api/employee-abono` + tela `Abonos`. Migração 030.
- **D** — Período de férias: entidade `VacationPeriod` + `tb_vacation_period` (aquisitivo início/fim, dias de direito, início do gozo, dias gozados, pago) + CRUD `api/vacation-period` + tela `VacationPeriods`. Migração 030.

**FORA (deferido — transparente, R.6.2):**
- **"Mês Admissão" no benefício (consumo na folha)**: exige filtro período-aware no motor de folha (incluir o benefício só no mês de admissão). Risco no motor de cálculo → fica para SPEC futura. (O modo **percentual** foi entregue.)
- **Consumo de abono no ponto/folha** (abater banco de horas / proventos): nesta onda o abono é cadastro (espelha o `Empregados_Abonos` standalone do antigo); o consumo automático fica para futuro.
- **Auto-integração do período de férias com a folha**: a folha **já calcula** férias (1/3, INSS/IRRF) ao lançar na folha; aqui entregamos o **registro** do período aquisitivo/gozo. A ligação automática período→folha fica para futuro.

## Implementação

Migração 030 (aditiva: 2 colunas em `tb_contract_benefit_discount` + 2 tabelas novas) aplicada na Railway via `node .migrate/apply.js` + `erp.sql`. Backend: 2 slices CRUD canônicos (EmployeeAbono, VacationPeriod) registrados; método de resumo no `LoanAdvanceService` + endpoint; campos no benefício + consumo na folha. Frontend: 3 telas + serviços + benefício % no `BenefitDiscountList`. `dotnet build` + `npm run build` limpos.

## Critério de aceite

- [x] **A** — resumo de empréstimos (endpoint + tela `LoanSummary`) (2026-06-09 15:33, `34f5246`/`efd91b4`)
- [x] **B** — benefício percentual (colunas + folha default-off + UI) (2026-06-09 15:33, `34f5246`/`efd91b4`)
- [x] **C** — abonos Dias/Horas (`EmployeeAbono` CRUD + tela) (2026-06-09 15:33, `34f5246`/`efd91b4`)
- [x] **D** — período de férias (`VacationPeriod` CRUD + tela) (2026-06-09 15:33, `34f5246`/`efd91b4`)
- [x] migração 030 aplicada na Railway + `erp.sql` atualizado (2026-06-09 15:24)
- [x] erp-back compila e erp-front builda (2026-06-09 15:33)
- [x] **Features tocadas (emprestimos-adiantamentos, empregados-contratos, folha-pagamento, abonos-ferias) atualizadas** com timestamp e referência a esta SPEC (2026-06-09 15:33)
- [x] `state.md` com entrada `[conclusão]` (2026-06-09 15:33)
- [x] `memory.md` com TL;DR final atualizado (2026-06-09 15:33)
