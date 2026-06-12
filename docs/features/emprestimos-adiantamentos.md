# Feature: emprestimos-adiantamentos

**Keywords:** empréstimo, adiantamento, loan, advance, parcela, desconto em folha, vale
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/LoanAdvanceController.cs
  - erp-back/-2-Application/Services/LoanAdvanceService.cs
  - erp-back/-3-Infrastructure/Repositories/LoanAdvanceRepository.cs
  - erp-back/-1-Domain/Entities/loanAdvance.cs
  - erp-front/src/services/loanAdvanceService.ts
  - erp-front/src/pages/loan-advances/
**Resumo:** Empréstimos e adiantamentos a empregados (`LoanAdvance`: valor, parcelas, origem do desconto), descontados na folha de pagamento ao longo das parcelas.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260609-1111 | 2026-06-09 | `a4dd879` | Onda 0: bloqueio de editar/excluir empréstimo/adiantamento com parcela já paga (`InstallmentsPaid>0`) |
| SPEC-20260609-1516 | 2026-06-09 | `34f5246` | Onda 5: resumo de dívida por empregado (buckets Mês/13º/Férias via `DiscountSource` + % do salário comprometido + alerta) — `GET api/loan-advance/employee/{id}/summary` |
| SPEC-20260611-1249 | 2026-06-11 | — | Onda 8: tela em 2 níveis — **visão master por funcionário** (`GET api/loan-advance/employee-summaries`: dívida total + buckets + em aberto) → clique abre os empréstimos dele (filtro `EmployeeId`) → `+` na linha abre o form pré-selecionado (`?employeeId=`). Busca **case-insensitive** (ILIKE) |
| SPEC-20260612-1215 | 2026-06-12 | `9dd7de7`/`715020e` | Onda 10: `StartDate` vira **mês/ano de referência** do início da cobrança (sempre dia 1; input `type=month`; migração 035); lista do funcionário com 1ª coluna **Data do Empréstimo** ordenada desc; classificação Adiantamento passa a comparar mês corrente |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/loan-advance` (CRUD canônico sob `loanAdvance.*`). `LoanAdvance` tem `EmployeeId`, `Amount`, `Installments`, `DiscountSource`, `StartDate`, `IsApproved`, `Description` e `LoanDate` (migration `008`). A migration `018` adicionou `is_fully_paid` (controle de quitação) e a `019`/`020` ajustaram datas. O desconto das parcelas é aplicado em [[folha-pagamento]]; a `DiscountSource` define de onde o valor é descontado.

**`StartDate` é o mês/ano de referência do início da cobrança** (desde Onda 10): sempre o dia 1 do mês — o mapper normaliza, o form usa `<input type=month>` e a migração `035` normalizou o legado. A folha (mensal, 13º, férias e adiantamento de férias) só desconta empréstimos cujo `StartDate <= PeriodEndDate` da folha. A lista por funcionário exibe `LoanDate` (data do empréstimo) como 1ª coluna, ordenada desc via `OrderDirection=desc`.

> Última atualização: 2026-06-12 12:52 (SPEC-20260612-1215 — Onda 10)

## Decisões arquiteturais ativas

- **Quitação controlada por flag + parcelas** (origem: estado pré-SPEC, 2026-06-08 20:15) — `Installments` + `is_fully_paid` (migration `018`) controlam o desconto progressivo na folha até a quitação.
- **`StartDate` = mês de referência, sempre dia 1** (origem: SPEC-20260612-1215, 2026-06-12 12:52) — associa o início da cobrança diretamente ao período da folha (comparação `<= PeriodEndDate` funciona porque folhas são alinhadas ao mês civil). Trade-off: perde-se a granularidade de dia, que não tinha uso real.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **O desconto efetivo acontece na folha, não aqui** (2026-06-08 20:15) — esta feature cadastra/aprova o empréstimo; o abatimento das parcelas é responsabilidade de [[folha-pagamento]].
- **Migração `035` precisa ser aplicada manualmente no Postgres** (2026-06-12 12:52, SPEC-20260612-1215) — normaliza `loan_advance_start_date` legado para dia 1; sem ela, registros antigos com dia > 1 continuam válidos mas fora da convenção nova.

## Estado congelado (se houver)

_(nenhum)_
