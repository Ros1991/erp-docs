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

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/loan-advance` (CRUD canônico sob `loanAdvance.*`). `LoanAdvance` tem `EmployeeId`, `Amount`, `Installments`, `DiscountSource`, `StartDate`, `IsApproved` e `Description` (migration `008`). A migration `018` adicionou `is_fully_paid` (controle de quitação) e a `019`/`020` ajustaram datas. O desconto das parcelas é aplicado em [[folha-pagamento]]; a `DiscountSource` define de onde o valor é descontado.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Quitação controlada por flag + parcelas** (origem: estado pré-SPEC, 2026-06-08 20:15) — `Installments` + `is_fully_paid` (migration `018`) controlam o desconto progressivo na folha até a quitação.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **O desconto efetivo acontece na folha, não aqui** (2026-06-08 20:15) — esta feature cadastra/aprova o empréstimo; o abatimento das parcelas é responsabilidade de [[folha-pagamento]].

## Estado congelado (se houver)

_(nenhum)_
