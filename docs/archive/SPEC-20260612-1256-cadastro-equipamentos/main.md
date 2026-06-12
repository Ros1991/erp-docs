# SPEC-20260612-1256: Cadastro de equipamentos

**Status:** done
**Criada:** 2026-06-12 12:56
**Ativada:** 2026-06-12 12:56
**Concluída:** 2026-06-12 13:15
**Commit final:** `0a78108` (erp-back, merge `f201a28`) + `ce56767` (erp-front, merge `d33d68b`)
**Keywords:** equipamento, equipment, manutenção prevista, depreciação, placa, chassi, renavam, vida útil, trator, veículo
**Features:** equipamentos
**Branch:** feature/equipamentos (erp-front + erp-back)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** — (ad-hoc; separada da Onda 10 por decisão do usuário em 2026-06-12 12:14)
**Origem:** usuário em 2026-06-12 12:10 ("Criar cadastro de equipamentos (olhar a documentação do sistema antigo ...)")
**Resumo:** Módulo novo de cadastro de equipamentos da empresa (tratores, veículos, máquinas, implementos) portado do sistema antigo: identificação, valor, datas, foto, depreciação e grade filha de manutenções previstas — CRUD completo com permissões próprias (`equipment.*`).

## Objetivo

Portar o cadastro de equipamentos do sistema antigo (telas `Equipamentos.aspx`/`Equipamentos_Grid.aspx`, tabela `Equipamentos` do banco CPR) para o ERP novo, criando a base para futuras integrações (tarefas de manutenção já têm `task_equipment_id` órfão no schema).

## Escopo

**DENTRO:**
- Entidades `Equipment` + `EquipmentMaintenance` (manutenções previstas), schema SQL (migração nova + erp.sql).
- Campos do sistema antigo: descrição (obrigatória), modelo, datas de fabricação/aquisição, valor, placa, chassi, Renavam, detalhes, foto (Base64), depreciável, vida útil.
- Manutenções previstas: descrição, unidade de tempo (KM/Horas/Dias/Meses/Anos — obrigatória) e valor/intervalo.
- CRUD backend (`api/equipment` + endpoints filhos de manutenção) sob módulo de permissão novo `equipment.*` (modules-configuration.json).
- Frontend: lista + form (foto, manutenções na edição), rotas guardadas, item na sidebar (grupo Cadastros).

**FORA:**
- Vincular equipamento à tarefa no TaskForm (o campo `task_equipment_id` já existe; seleção fica para SPEC futura).
- Cálculo de depreciação ou agendamento automático de manutenções.

## Implementação

- Fatia vertical idêntica ao padrão do projeto (referência: Holiday/CostCenter): Controller → IEquipmentService → IUnitOfWork.EquipmentRepository → ErpContext; mapper estático; `PagedResult` + FilterDTO.
- Valor em **centavos (bigint)**, padrão do projeto (gotcha de empregados-contratos).
- Foto como texto Base64 (mesmo padrão de TaskImage pós-migração 032 — coluna `text`).
- Exclusão do equipamento cascateia manutenções (FK ON DELETE CASCADE).
- Migração `036_create_equipment.sql` + atualização do `erp.sql`. SEM FK em `tb_task.task_equipment_id` por ora (dados legados podem ter ids órfãos).

## Critério de aceite

- [x] 1. Backend expõe CRUD de equipamentos (`api/equipment`) com permissões `equipment.*` e paginação padrão (2026-06-12 13:15, commit `0a78108`)
- [x] 2. Manutenções previstas (CRUD filho) com unidade de tempo obrigatória (KM/Horas/Dias/Meses/Anos) e cascade na exclusão do equipamento (2026-06-12 13:15, commit `0a78108`)
- [x] 3. Migração SQL nova + `erp.sql` atualizados com `tb_equipment` e `tb_equipment_maintenance` (2026-06-12 13:15, commit `0a78108` — migração 036)
- [x] 4. Tela de lista de equipamentos com busca/paginação e tela de form com todos os campos do sistema antigo (incl. foto e depreciação) (2026-06-12 13:15, commit `ce56767`)
- [x] 5. Grade de manutenções previstas disponível na edição do equipamento (2026-06-12 13:15, commit `ce56767` — criação navega para a edição para liberar a grade, como no sistema antigo)
- [x] 6. Item "Equipamentos" na sidebar (Cadastros) e rotas guardadas por `equipment.canView/canCreate/canEdit` (2026-06-12 13:15, commit `ce56767`)
- [x] 7. Builds verdes nos 2 repos (`dotnet build` + `npm run build`) (2026-06-12 13:10)
- [x] **Features tocadas (equipamentos) atualizadas** com timestamp e referência a esta SPEC (2026-06-12 13:15)
- [x] Decisões das features tocadas revisadas: obsoletas marcadas, ativas confirmadas (2026-06-12 13:15 — feature nova, decisões iniciais registradas)
- [x] `state.md` com entrada `[conclusão]` (2026-06-12 13:15)
- [x] `memory.md` com TL;DR final atualizado (2026-06-12 13:15)
