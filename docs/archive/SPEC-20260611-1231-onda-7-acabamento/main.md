# SPEC-20260611-1231: Onda 7 — Acabamento (aplicar rateio, categoria em transações/relatório, gestão completa de tarefas)

**Status:** done
**Criada:** 2026-06-11 12:31
**Ativada:** 2026-06-11 12:31
**Concluída:** 2026-06-11 13:05
**Pausada em:** —
**Commit final:** erp-back `9dea2e0` / erp-front `f719ff3`
**Keywords:** template de rateio, aplicar, categoria, transação, relatório por categoria, tarefa, comentário, foto, atribuir, editar hora
**Features:** centros-de-custo, plano-de-contas, transacoes-financeiras, relatorios, tarefas
**Branch:** feature/onda-7-acabamento (erp-back + erp-front)
**Depende de:** SPEC-20260609-1538 (templates de rateio), SPEC-20260609-1212 (plano de contas), SPEC-20260609-1334 (tarefas operacional)
**Bloqueia:** —
**Ordem no programa:** pós-programa (acabamento solicitado pelo usuário em 2026-06-11)
**Origem:** usuário em 2026-06-11: gaps apontados ("onde os templates/categorias são usados?") + "a edição de tarefa está muito pobre, quero gerenciar TUDO da tarefa"
**Resumo:** Fecha os gaps de consumo das telas criadas na Onda 6/1 (aplicar template de rateio com 1 clique; categoria nas transações financeiras + relatório por categoria) e transforma a edição de tarefa numa central de gestão (funcionários com ações, fotos e comentários).

## Objetivo

(1) Tornar o **template de rateio** útil: seletor "Aplicar template" no componente compartilhado de rateio (todos os formulários ganham de uma vez). (2) Estender o **plano de contas**: categoria nas transações financeiras (FK já existia no banco desde a 025) + **relatório por categoria**. (3) **Gestão completa da tarefa** na edição: funcionários (atribuir/remover/iniciar/pausar/parar/finalizar/reabrir/editar hora/pausa geral), fotos (galeria + upload Base64 + excluir) e comentários (listar/criar/excluir — endpoints novos).

## Escopo

**DENTRO:**
- **A** — Aplicar template de rateio: seletor no `CostCenterDistribution` (consome `api/cost-center-allocation-template`); preenche linhas centro+% e recalcula valores. Ativo em Contrato, Conta a Pagar/Receber, Empréstimo, Transação e Processar OC.
- **B** — Categoria em Transações Financeiras: `FinancialTransaction.FinancialCategoryId` (entity/DTOs/mapper — coluna já existia) + dropdown no form.
- **C** — Relatório por Categoria: `GET api/report/category` (títulos do período agrupados por categoria, com pago/aberto/total) + tela `/reports/category` + sidebar.
- **D** — Gestão completa da tarefa: endpoints de comentário (`GET/POST {taskId}/comments`, `DELETE comment/{id}` — DTOs/mapper existiam sem fiação) + `TaskManagePanel` no `TaskForm` (funcionários com todas as ações, fotos com compressão Base64 + lightbox, comentários). Migração 032 (widen `task_image_url`/`task_comment_attachment_url` p/ text — Base64 não cabia em 2048).

**FORA (deferido — transparente, R.6.2):**
- Anexo (foto) **no comentário**: o campo existe (`attachment_url`, já text); a UI ficou texto-puro nesta onda.
- Relatório por categoria sobre **transações** (caixa): o relatório usa títulos (APR), onde as categorias vivem desde a Onda 1; a visão por transação fica para quando houver massa de transações categorizadas.

## Implementação

Migração 032 (type widen, mesma natureza da 026) aplicada na Railway + `erp.sql`. Back: fiação do TaskComment (repo novo + registro + 3 endpoints), categoria no FT (entity/DTOs/mapper), `GetCategoryReportAsync` + endpoint. Front: seletor de template no componente compartilhado de rateio, dropdown de categoria no form de transação, página `CategoryReport`, `TaskManagePanel` (funcionários/fotos/comentários) + métodos no `taskService`. `dotnet build` + `npm run build` limpos.

## Critério de aceite

- [x] **A** — aplicar template de rateio nos formulários (componente compartilhado) (2026-06-11 13:05)
- [x] **B** — categoria nas transações financeiras (back + dropdown) (2026-06-11 13:05)
- [x] **C** — relatório por categoria (endpoint + tela + sidebar) (2026-06-11 13:05)
- [x] **D** — gestão completa da tarefa (comentários ligados + painel funcionários/fotos/comentários) (2026-06-11 13:05)
- [x] migração 032 aplicada na Railway + `erp.sql` atualizado (2026-06-11 12:40)
- [x] erp-back compila e erp-front builda (2026-06-11 13:05)
- [x] **Features tocadas (centros-de-custo, plano-de-contas, transacoes-financeiras, relatorios, tarefas) atualizadas** (2026-06-11 13:05)
- [x] `state.md` com entrada `[conclusão]` (2026-06-11 13:05)
- [x] `memory.md` com TL;DR final atualizado (2026-06-11 13:05)
