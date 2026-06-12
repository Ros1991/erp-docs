# Memory — SPEC-20260612-1215

> Main: [main.md](./main.md)
> State: [state.md](./state.md)
> Criado: 2026-06-12 12:15

---

## TL;DR (sobrescrever ao fim de cada sessão)

**Última atualização:** 2026-06-12 12:50 (sessão #1)
**Onde tô:** SPEC CONCLUÍDA em 2026-06-12 12:50, commits `9dd7de7` (erp-back) + `715020e` (erp-front), merges na main pushed.
**Próximo passo:** nenhum (arquivada). Operacional: aplicar migração `035` no Postgres Railway.
**Última decisão:** mês de referência do empréstimo vale para TODOS os caminhos da folha (mensal, 13º, férias, adiantamento de férias).
**Bloqueio atual:** nenhum
**Se retomar, ler:** state.md, log de 2026-06-12 12:25 em diante.

---

## Contexto ativo

### O que está sendo feito AGORA

Nada — entrega concluída em sessão única: exploração fan-out (6 agentes) → implementação dos 11 itens → builds → revisão adversarial (4 bugs corrigidos) → commit/push nos 2 repos → docs/archive.

### Hipóteses em jogo

- **Comparação ano/mês basta para o mês de referência do empréstimo** (status: confirmada — filtro `StartDate <= PeriodEndDate` com StartDate dia 1 + folhas alinhadas ao mês civil). 2026-06-12 12:48
- **Sidebar: bug era o auto-expand por rota** (status: confirmada — Sidebar.tsx:75-94 removido). 2026-06-12 12:40

### Decisões recentes que importam pra continuar

- [2026-06-12 12:48] 13º/férias/adiantamento agora passam `payroll.PeriodEndDate` ao buscar empréstimos pendentes (era pré-existente ignorar; viola o contrato novo do mês de referência).
- [2026-06-12 12:40] Ordenação de empregados no BACKEND (coalesce Nickname/FullName) — telas não precisaram mudar.

### Respostas-chave do usuário

- [2026-06-12 12:10] Usuário: "pode fazer todas essas mudanças e fazer commit e push"
  Contexto: lote de 12 itens enviado de uma vez; autorização para executar e publicar.
- [2026-06-12 12:14] Usuário: escolheu "2 SPECs: ajustes + equipamentos" e "Sim, criar 'equipamentos'".
  Contexto: AskUserQuestion de classificação R.9/R.13.

### Tentativas que falharam (para NÃO repetir)

- [2026-06-12 12:48] Enviar `IsAscending` como query param para o backend NÃO funciona — `PagedRequest.IsAscending` é computado (sem setter); usar `OrderDirection=asc|desc`. Ver state 12:48.

### Arquivos ativamente sendo tocados

- _(nenhum — concluída)_

### Onde parei exatamente

SPEC concluída e arquivada. Migração `035` pendente de aplicação manual no banco.

---

## Histórico de sessões

| # | Início | Duração | Tipo | Sumário 1 linha |
|---|--------|---------|------|-----------------|
| 1 | 2026-06-12 12:15 | ~35min | ativação+conclusão | 11 itens implementados, revisados (4 fixes), publicados e arquivados |
