# State — SPEC-20260609-1212

> Main: [main.md](./main.md)
> Memory: [memory.md](./memory.md)
> Criado: 2026-06-09 12:12

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 12:14
**Onde tô:** Item **F (dados bancários/PIX no contrato) CONCLUÍDO** — migração 023 aplicada na Railway, erp.sql atualizado, back+front buildam limpo.
**Próximo passo:** G (feriados), H (plano de contas), I (anexo de justificativa); depois fechar a onda.
**Última decisão:** colunas bancárias como `text` nullable direto no `Contract` (não sub-entidade) — simples e suficiente.
**Bloqueio atual:** nenhum. Execução autônoma.
**Se retomar, ler:** este TL;DR + tabela de fases.

---

## Status snapshot (sobrescrever)

### Fases / etapas

| # | Descrição | Status | Atualizado | Commit |
|---|-----------|--------|-----------|--------|
| F | Dados bancários/PIX no contrato | concluído | 2026-06-09 12:14 | `4c8793b`/`361fc4a` |
| G | Feriados (entidade + CRUD + UI) | concluído | 2026-06-09 12:20 | `a71fb83`/`a4c9df4` |
| H | Plano de contas (FinancialCategory + FK + seletor) | concluído | 2026-06-09 12:33 | `0be4661`/`31e0f24` |
| I | Anexo de justificativa (Base64) | pendente | 2026-06-09 12:14 | — |

### Próximos passos

- [ ] G — Feriados
- [ ] H — Plano de contas
- [ ] I — Anexo de justificativa
- [ ] Fechar onda (critérios + features + archive + merge na main local)

### Bloqueios ativos

_nenhum_

---

## Fatos confirmados

- [2026-06-09 12:12] Runner de migração `node .migrate/apply.js <sql>` aplica na Railway (search_path=erp). `--query` p/ inspecionar. Schema `erp` tinha 33 tabelas.
- [2026-06-09 12:14] Migração `023_add_contract_payment_info.sql` aplicada: `tb_contract` ganhou 6 colunas nullable (contract_pix_key_type, contract_pix_key, contract_bank, contract_bank_agency, contract_bank_account, contract_bank_operation). Confirmado por `--query` em information_schema.

## Inferências prováveis

- [2026-06-09 12:14] Feriados podem usar permissões `companySettings.*` (sem novo módulo de permissão) — validar ao implementar G.

## Dúvidas em aberto

- [2026-06-09 12:14] Anexo de justificativa: reusar `attachment_url` com Data URL Base64 (sem migração) vs nova coluna. Decisão provisória: reusar attachment_url (mais simples).

---

## Log cronológico (APPEND-ONLY)

## 2026-06-09 12:12 — [ativação]

Onda 1 ativada (programa 2/7, depende de SPEC-20260609-1111 já concluída/mergeada). Branches `feature/onda-1-cadastros-dados` em erp-back e erp-front. Runner de migração node-pg pronto (.migrate/).

## 2026-06-09 12:14 — [MARCO] Item F concluído

`023_add_contract_payment_info.sql` aplicado na Railway (6 colunas nullable em tb_contract) + `erp.sql` atualizado (via node, sem mexer no alinhamento). Backend: entidade `Contract` (+6 props), `ContractInputDTO`/`ContractOutputDTO`, `ContractMapper` (ToEntity/UpdateEntity/ToOutputDTO). Frontend: `contractService` (interfaces Contract+ContractInput), `ContractForm` (seção "Dados bancários / PIX": tipo de chave, chave, banco, agência, conta, operação). `dotnet build` 0 erros; `npm run build` ✓ built. A commitar.

## 2026-06-09 12:40 — [conclusão] Onda 1 concluída e arquivada

Todos os critérios F,G,H,I marcados. Migrações 023 (bancário/PIX), 024 (feriados), 025 (plano de contas + FKs) e 026 (widen attachment p/ Base64) aplicadas na Railway + `erp.sql` atualizado. Features tocadas atualizadas (R.7): empregados-contratos (bancário/PIX), justificativas (anexo), feriados (nova), plano-de-contas (nova). Commits — F `4c8793b`/`361fc4a`, G `a71fb83`/`a4c9df4`, H `0be4661`/`31e0f24`, I `4ef7c66`/`70a9e67`. `main.md` Status: done; Commit final erp-back `4ef7c66` / erp-front `70a9e67`. Pasta movida `active/`→`archive/`. Merge `feature/onda-1-cadastros-dados`→main LOCAL em erp-back e erp-front (sem push).

**Entregue na Onda 1:** dados bancários/PIX no contrato; cadastro de feriados; plano de contas (categorias Receita/Despesa) + categorização de títulos; anexo de imagem (atestado) na justificativa com compressão e visualização.
