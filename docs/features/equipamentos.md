# Feature: equipamentos

**Keywords:** equipamento, equipment, manutenção prevista, depreciação, placa, chassi, renavam, vida útil, trator, veículo, máquina
**Arquivos principais:**
  - erp-back/-1-Domain/Entities/equipment.cs
  - erp-back/-1-Domain/Entities/equipmentMaintenance.cs
  - erp-back/-4-WebApi/Controllers/EquipmentController.cs
  - erp-back/-2-Application/Services/EquipmentService.cs
  - erp-back/-3-Infrastructure/Repositories/EquipmentRepository.cs
  - erp-back/-1-Domain/database/migrations/036_create_equipment.sql
  - erp-front/src/services/equipmentService.ts
  - erp-front/src/pages/equipment/
**Resumo:** Cadastro de equipamentos da empresa (tratores, veículos, máquinas, implementos) com dados de identificação, valor, foto, depreciação e manutenções previstas — portado do sistema antigo (telas Equipamentos.aspx).

## Specs desta feature

### Concluídas
| ID | Data | Commit | Título |
|---|---|---|---|
| SPEC-20260612-1256 | 2026-06-12 | `0a78108`/`ce56767` | Cadastro de equipamentos: entidades + migração 036, CRUD `api/equipment` (+ manutenções previstas) sob módulo `equipment.*`, lista paginada com busca + form com foto/depreciação, item na sidebar (Cadastros) |

### Planejadas (future/)
| ID | Título | Motivo |
|---|---|---|
| — | — | — |

### Em execução (só em branches — não aparece em main)
| ID | Título | Branch |
|---|---|---|
| — | — | — |

## Estado atual

API em `api/equipment` (CRUD canônico: getAll/getPaged/{id}/create/PUT/DELETE) sob permissões `equipment.canView/canCreate/canEdit/canDelete` (módulo próprio em modules-configuration.json, ícone wrench). Manutenções previstas como recurso filho: `POST {id}/maintenance`, `PUT maintenance/{maintenanceId}`, `DELETE maintenance/{maintenanceId}` (todas sob `equipment.canEdit`); o `GET {id}` retorna o equipamento com a lista de manutenções.

`Equipment` (tabela `tb_equipment`, por `CompanyId`, FK p/ `tb_company`): `Description` (obrigatória, 100), `Model`, `ManufactureDate`/`AcquisitionDate` (date), `Value` (**centavos**, bigint), `Plate`, `Chassis`, `Renavam`, `Details` (text), `PhotoBase64` (text — foto comprimida no front), `IsDepreciable` + `UsefulLife`. `EquipmentMaintenance` (tabela `tb_equipment_maintenance`, FK **ON DELETE CASCADE**): `Description`, `TimeUnit` (KM/Horas/Dias/Meses/Anos — validado no service), `IntervalValue`. Migração `036` + bloco no `erp.sql`.

Front: `/equipments` (lista paginada com busca ILIKE em descrição/modelo/placa) + form (`/equipments/new` e `/equipments/:id/edit`); a grade de manutenções só aparece na **edição** — a criação salva e navega para a edição (fluxo do sistema antigo). Item "Equipamentos" na sidebar, grupo Cadastros.

> Última atualização: 2026-06-12 13:15 (SPEC-20260612-1256)

## Decisões arquiteturais ativas

- **Checagem de `CompanyId` em todo acesso unitário** (origem: SPEC-20260612-1256, 2026-06-12 13:15) — `GetOwnedEquipmentAsync`/`GetOwnedMaintenanceAsync` retornam 404 para registro de outra empresa, mais rígido que o padrão dos módulos antigos (GetOne sem checagem). Modelo recomendado para módulos novos.
- **Valor em centavos (bigint)** (origem: SPEC-20260612-1256, 2026-06-12 13:15) — consistente com `Contract.Value`; o front converte reais↔centavos.
- **Sem FK em `tb_task.task_equipment_id`** (origem: SPEC-20260612-1256, 2026-06-12 13:15) — a coluna já existia órfã e dados legados podem ter ids inválidos; o vínculo tarefa↔equipamento (seleção no TaskForm) fica para SPEC futura.

## Alternativas consideradas e rejeitadas

- **Manutenções embutidas no payload do equipamento (update em lote)** — rejeitado em SPEC-20260612-1256 (2026-06-12 13:15). Endpoints filhos dedicados espelham a grade inline do sistema antigo e evitam ressincronizar a coleção inteira a cada save.

## Gotchas

- **Migração `036` precisa ser aplicada manualmente no Postgres (Railway)** (2026-06-12 13:15, SPEC-20260612-1256) — sem ela, qualquer endpoint de equipamentos falha (tabelas inexistentes).
- **A grade de manutenções exige equipamento salvo** (2026-06-12 13:15, SPEC-20260612-1256) — o form de criação não mostra manutenções; ele salva e redireciona para a edição.

## Estado congelado (se houver)

_(nenhum)_
