# State — SPEC-20260612-1256

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-12 12:56

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-12 13:15
**Onde tô:** SPEC CONCLUÍDA — módulo completo nos 2 repos, revisão adversarial sem achados, builds verdes, código na main (erp-back `0a78108`/merge `f201a28`; erp-front `ce56767`/merge `d33d68b`).
**Próximo passo:** nenhum — arquivada. Migração `036` precisa ser aplicada manualmente no Postgres (Railway). Vincular equipamento à tarefa ficou FORA (SPEC futura).
**Última decisão:** criação navega para a edição para liberar a grade de manutenções (espelha o sistema antigo).
**Bloqueio atual:** nenhum
**Se retomar, ler:** log de 2026-06-12.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| 1 | Levantamento no sistema antigo (telas Equipamentos) | concluído | 2026-06-12 12:56 | — |
| 2 | Backend: entidades + migração + fatia vertical + permissões | concluído | 2026-06-12 13:05 | `0a78108` |
| 3 | Frontend: service + lista + form + rotas + sidebar | concluído | 2026-06-12 13:10 | `ce56767` |
| 4 | Builds + revisão + docs + archive + push | concluído | 2026-06-12 13:15 | — |

### Próximos passos

- [x] Backend completo (2026-06-12 13:05, commit `0a78108`)
- [x] Frontend completo (2026-06-12 13:10, commit `ce56767`)
- [x] Builds verdes + revisão (2026-06-12 13:12)
- [ ] (operacional, fora do repo) aplicar migração `036_create_equipment.sql` no Postgres Railway

### Bloqueios ativos

_(nenhum)_

---

## Fatos confirmados

- [2026-06-12 12:56] Campos do sistema antigo (doc `03 - Tabelas (Cadastros)/Equipamentos - *.md`): Descrição (obrig., 60), Data Fabricação, Modelo (30), Valor (monetário), Data Aquisição, Placa (8), Chassi (17), Renavam (10), Detalhes (longo), Foto (binário), Depreciável (bool), Vida Útil (num 5). Grade filha "Manutenções Previstas": Manutenção (25), Tempo (combo KM/Horas/Dias/Meses/Anos, obrig.), Valor (num). Exclusão do equipamento exclui manutenções juntas. Fonte: D:\Projetos\Documentacao Sistema Antigo.
- [2026-06-12 12:56] `tb_task.task_equipment_id` (bigint NULL) já existe no erp.sql:658 sem FK nem tabela de equipamentos. Fonte: erp.sql.

## Inferências prováveis

- [2026-06-12 12:56] "Vida útil" do antigo é em anos (uso típico p/ depreciação). Validar com: usuário, se um dia o cálculo de depreciação for implementado.

## Dúvidas em aberto

_(nenhuma bloqueante)_

---

## Log cronológico (APPEND-ONLY — NUNCA editar entradas antigas)

## 2026-06-12 12:56 — [ativação]

SPEC criada direto em active/ (segunda do lote do usuário de 2026-06-12 12:10; recorte em 2 SPECs e feature nova `equipamentos` aprovados pelo usuário em 12:14). Doc do sistema antigo lida antes da ativação (item explícito do pedido). Plano: fatia vertical padrão no erp-back (referência Holiday/CostCenter) + telas no erp-front, branch feature/equipamentos nos 2 repos, merge na main + push ao concluir.

## 2026-06-12 13:05 — [MARCO] [decisão] Desenho do módulo

- **Tabelas**: `tb_equipment` (FK p/ tb_company) + `tb_equipment_maintenance` (FK ON DELETE CASCADE) — migração `036` + bloco no erp.sql.
- **Valor em centavos (bigint)** seguindo o padrão do projeto; **foto Base64 em coluna text** (padrão TaskImage pós-032); datas como `date`.
- **Multi-tenancy reforçada**: diferente do padrão Holiday (GetOne sem checagem), o `EquipmentService` verifica `CompanyId` em GetOne/Update/Delete e nas operações de manutenção (helper `GetOwnedEquipmentAsync`/`GetOwnedMaintenanceAsync` → 404 p/ registro de outra empresa).
- **Unidades de tempo validadas no service** (KM/Horas/Dias/Meses/Anos — combo do sistema antigo).
- **Módulo de permissão próprio `equipment.*`** (canView/canCreate/canEdit/canDelete; manutenções sob canEdit).
- **Sem FK em `tb_task.task_equipment_id`** — dados legados podem ter ids órfãos; vínculo tarefa↔equipamento fica para SPEC futura.

## 2026-06-12 13:10 — [nota] Frontend

`equipmentService.ts` (usa `OrderDirection` — lição da Onda 10), lista paginada com busca (ILIKE em descrição/modelo/placa no backend), form com foto comprimida (helper compressImage), grade de manutenções só na edição (criação salva e navega p/ edição, espelhando o fluxo do antigo "Salvo com sucesso → continuar"). Rotas + item na sidebar (Cadastros, ícone Wrench).

## 2026-06-12 13:12 — [tentativa] Revisão adversarial: zero achados

Workflow equipamentos-review (2 revisores — backend: colunas SQL×[Column], tenancy, DateTime Kind, Include por convenção, JSON de permissões; frontend: round-trips, ConfirmDialog/Protected, rotas, interceptor 403): **nenhum finding major/critical**. Builds verdes nos 2 repos.

## 2026-06-12 13:15 — [conclusão] Cadastro de equipamentos entregue

Módulo completo publicado. erp-back: `0a78108` (merge `f201a28`, pushed). erp-front: `ce56767` (merge `d33d68b`, pushed). Critérios todos marcados; feature `equipamentos` preenchida (R.7). Pendência operacional: aplicar migração `036` no Postgres (Railway).
