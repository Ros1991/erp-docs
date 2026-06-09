# Feature: ordens-de-compra

**Keywords:** ordem de compra, purchase order, solicitação, aprovação, requisitante, aprovador, canProcess, status
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/PurchaseOrderController.cs
  - erp-back/-2-Application/Services/PurchaseOrderService.cs
  - erp-back/-3-Infrastructure/Repositories/PurchaseOrderRepository.cs
  - erp-back/-1-Domain/Entities/purchaseOrder.cs
  - erp-front/src/services/purchaseOrderService.ts
  - erp-front/src/pages/purchase-orders/
**Resumo:** Solicitações de compra com fluxo de aprovação (`PurchaseOrder`: requisitante, aprovador, valor, status), com a ação `process` (aprovar/rejeitar) gated por permissão própria `purchaseOrder.canProcess`.

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| — | — | — | _(nenhuma SPEC ainda — feature documentada por engenharia reversa do estado atual)_ |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/purchase-order`: CRUD (`getAll`, `getPaged`, `GET {id}`, `create`, `PUT {id}`, `DELETE {id}`) sob `purchaseOrder.*`, mais **`POST {id}/process`** sob `purchaseOrder.canProcess` (aprovar/rejeitar). `PurchaseOrder` tem `UserIdRequester`, `UserIdApprover`, `Description`, `TotalAmount`, `Status`, `CompanyId`. A migration `2026-01-19_purchase_order_and_company_settings` introduziu/evoluiu a entidade. Há FK em `FinancialTransaction.PurchaseOrderId`, ou seja, o processamento da ordem pode gerar movimentação em [[transacoes-financeiras]].

Permissões refletem o fluxo: `canCreate` = abrir solicitação; `canEdit`/`canDelete` = mexer em ordens pendentes (próprias, ou todas se tiver `canProcess`); `canProcess` = aprovar/rejeitar e editar ordens já processadas.

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Permissão dedicada `canProcess` separada de edição** (origem: estado pré-SPEC, 2026-06-08 20:15) — segrega quem solicita de quem aprova; a edição de ordens já processadas também exige `canProcess`.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- **Escopo de edição depende de `canProcess`** (2026-06-08 20:15) — sem `canProcess`, o usuário só mexe nas próprias ordens pendentes; a regra é aplicada no service, não só pelo atributo de permissão.

## Estado congelado (se houver)

_(nenhum)_
