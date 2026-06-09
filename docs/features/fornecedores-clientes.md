# Feature: fornecedores-clientes

**Keywords:** fornecedor, cliente, supplier, customer, parceiro, cadastro
**Arquivos principais:**
  - erp-back/-4-WebApi/Controllers/SupplierCustomerController.cs
  - erp-back/-2-Application/Services/SupplierCustomerService.cs
  - erp-back/-3-Infrastructure/Repositories/SupplierCustomerRepository.cs
  - erp-back/-1-Domain/Entities/supplierCustomer.cs
  - erp-front/src/services/supplierCustomerService.ts
  - erp-front/src/pages/supplier-customers/
**Resumo:** Cadastro unificado de fornecedores e clientes (`SupplierCustomer`) da empresa, referenciado por compras, títulos a pagar/receber e relatórios.

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

API em `api/supplier-customer` (CRUD canônico sob `supplierCustomer.*`). Cadastro multi-tenant (`CompanyId`) que serve de contraparte para [[ordens-de-compra]], [[contas-pagar-receber]] e o "Relatório de Fornecedores/Clientes" em [[relatorios]].

> Última atualização: 2026-06-08 20:15 (engenharia reversa inicial — sem SPEC associada)

## Decisões arquiteturais ativas

- **Fornecedor e cliente na mesma entidade** (origem: estado pré-SPEC, 2026-06-08 20:15) — um único cadastro (`SupplierCustomer`) distingue o papel por campo, evitando duplicar parceiros que são ambos.

## Alternativas consideradas e rejeitadas

- _(não documentado — nenhuma SPEC registrou alternativas para esta área ainda)_

## Gotchas

- _(nenhum registrado ainda)_

## Estado congelado (se houver)

_(nenhum)_
