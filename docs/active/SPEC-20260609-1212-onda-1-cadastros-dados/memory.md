# Memory — SPEC-20260609-1212

> Main: [main.md](./main.md)
> State: [state.md](./state.md)
> Criado: 2026-06-09 12:12

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-09 12:14 (sessão #1)
**Onde tô:** Onda 1 — item F (bancário/PIX no contrato) concluído; falta G/H/I.
**Próximo passo:** G feriados → H plano de contas → I anexo de justificativa → fechar onda.
**Última decisão:** migrações aplicadas via `node .migrate/apply.js`; só aditivas; reusar attachment_url p/ Base64 no item I.
**Bloqueio atual:** nenhum.
**Se retomar, ler:** state.md (fatos confirmados + log).

---

## Contexto ativo

### O que está sendo feito AGORA

Onda 1 do programa de migração. Cadastros & dados. Item F (dados bancários/PIX no contrato) entregue. Próximos: feriados, plano de contas, anexo de justificativa.

### Decisões recentes que importam pra continuar

- [2026-06-09 12:14] Padrão de migração: SQL numerado idempotente (IF NOT EXISTS) → `node .migrate/apply.js` → conferir com `--query` → atualizar `erp.sql` (via node p/ não quebrar alinhamento). Novas props de entidade sem Fluent explícito no ErpContext funcionam (convenção mapeia string→text).
- [2026-06-09 12:14] Colunas bancárias como `text?` direto no Contract.

### Respostas-chave do usuário

- [2026-06-09 11:43] "pode executar sem medo" (migrações na Railway) + manter erp.sql; SEM equipamentos/face; merge na main local por onda, push só no fim.

### Arquivos ativamente sendo tocados

- Onda 1 / item F: Contract (entidade/DTOs/mapper), contractService, ContractForm — concluído.
- Próximos: novos Holiday* e FinancialCategory* (back) + páginas no front + feature docs feriados/plano-de-contas.

### Onde parei exatamente

Item F concluído e buildando; prestes a commitar e seguir para G (feriados).

---

## Histórico de sessões

| # | Início | Duração | Tipo | Sumário 1 linha |
|---|--------|---------|------|-----------------|
| 1 | 2026-06-09 12:12 | em curso | ativação | Onda 1 ativada; item F (bancário/PIX) concluído |
