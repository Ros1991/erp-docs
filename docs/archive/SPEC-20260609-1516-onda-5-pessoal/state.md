# State — SPEC-20260609-1516

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 15:16

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 15:33
**Onde tô:** Onda 5 CONCLUÍDA e arquivada. Back+front buildam limpo, mergeados na main LOCAL (erp-back `0b0b9fa`, erp-front `f1a4ab8`; commits da feature `34f5246`/`efd91b4`). Sem push.
**Próximo passo:** Onda 6 (ÚLTIMA) — desligamento, notificações, mapa Leaflet, template de rateio, gerenciador de anexos. AO FINAL: PUSH das mains.
**Última decisão:** benefício % é additive/default-off na folha (não altera cálculo existente); buckets de empréstimo vêm do `DiscountSource` já existente (sem migração p/ o resumo); abono/férias entregues como cadastro (consumo na folha deferido).
**Bloqueio atual:** nenhum. Execução autônoma.
**Se retomar, ler:** este TL;DR + tabela de fases.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| A | Resumo de empréstimos (#13) | concluído | 2026-06-09 15:33 | `34f5246`/`efd91b4` |
| B | Benefício percentual (#24) | concluído | 2026-06-09 15:33 | `34f5246`/`efd91b4` |
| C | Abonos Dias/Horas (#24) | concluído | 2026-06-09 15:33 | `34f5246`/`efd91b4` |
| D | Período de férias (#24) | concluído | 2026-06-09 15:33 | `34f5246`/`efd91b4` |

### Próximos passos

- [ ] Onda 6 (última) + PUSH final

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 15:20] `LoanAdvance.DiscountSource` JÁ é o bucket (Onde_Aplicar). Códigos: `TODOS`/`SALARIO`/`13SAL`/`FERIAS` (constants/discountSource.ts). Por isso o resumo NÃO precisou de migração.
- [2026-06-09 15:20] `Employee` NÃO tem data de admissão — admissão = `StartDate` do contrato ativo; salário = `Contract.Value` (centavos).
- [2026-06-09 15:24] Migração `030_onda5_pessoal.sql` aplicada na Railway: 2 colunas em `tb_contract_benefit_discount` (is_percentage, percentage) + `tb_employee_abono` + `tb_vacation_period`. Confirmado por `--query`.
- [2026-06-09 15:28] `PayrollService` consome benefício por `Amount` com proporcionalidade; o `salaryAmount` (base) está em escopo no laço de benefícios → o % é `(long)Math.Round(salaryAmount * pct/100)`. Default-off (IsPercentage=false) mantém o cálculo byte-a-byte.

## Inferências prováveis

- [2026-06-09 15:20] Abono/Férias usam permissões `employee.canView`/`canEdit` (são registros do empregado).

## Dúvidas em aberto

- [2026-06-09 15:20] "Mês Admissão" no benefício precisa de filtro período-aware na folha — deferido (risco no motor de cálculo).

---

## Log cronológico (APPEND-ONLY)

## 2026-06-09 15:16 — [ativação]

Onda 5 ativada (programa 6/7). Branches `feature/onda-5-pessoal`. Comparativo Tier 2 #13 (resumo de empréstimos) + Tier 3 #24 (% benefício, abono em dias, período de férias). Sistema antigo: `Empregados Debitos e Creditos` (Onde_Aplicar), `Empregados_Abonos` (Dias/Horas), `Lancar Ferias`.

## 2026-06-09 15:33 — [conclusão] Onda 5 concluída e arquivada

Critérios A,B,C,D marcados. Migração 030 + `erp.sql`. Back: `LoanAdvanceService.GetEmployeeSummaryAsync` + endpoint; `ContractBenefitDiscount` +IsPercentage/+Percentage + consumo na folha (default-off) + ContractService/ContractMapper; slices CRUD `EmployeeAbono` (`api/employee-abono`) e `VacationPeriod` (`api/vacation-period`) registrados. Front: telas `LoanSummary`/`Abonos`/`VacationPeriods` + serviços; `BenefitDiscountList` modo percentual + propagação. `dotnet build` 0 erros; `npm run build` ✓. Features tocadas (R.7): emprestimos-adiantamentos, empregados-contratos, folha-pagamento, abonos-ferias (nova). Commits feature `34f5246`/`efd91b4`; merge na main LOCAL erp-back `0b0b9fa` / erp-front `f1a4ab8` (sem push). Pasta `active/`→`archive/`.

**Entregue na Onda 5:** resumo de dívida de empréstimos por empregado (buckets + % do salário + alerta); benefício como % do salário (consumido na folha, default-off); cadastro de abonos em Dias/Horas; cadastro de períodos de férias (aquisitivo + gozo). Deferidos transparentes: "Mês Admissão" na folha, consumo de abono no ponto/folha, auto-integração férias→folha.
