# SPEC-20260612-1215: Onda 10 — ajustes gerais de UX, RH e tarefas

**Status:** done
**Criada:** 2026-06-12 12:15
**Ativada:** 2026-06-12 12:15
**Concluída:** 2026-06-12 12:50
**Commit final:** `9dd7de7` (erp-back, merge `5c65e31`) + `715020e` (erp-front, merge `302ce45`)
**Keywords:** combobox alfabético, empréstimo, mês de referência, tarefa anual, expander folha, sidebar, usuário duplicado, feriados automáticos, nova tarefa, kanban
**Features:** empregados-contratos, emprestimos-adiantamentos, tarefas, folha-pagamento, plataforma-core, usuarios, feriados
**Branch:** feature/onda-10-ajustes (erp-front + erp-back)
**Depende de:** —
**Bloqueia:** —
**Ordem no programa:** — (lote ad-hoc; item "cadastro de equipamentos" foi separado em SPEC própria por decisão do usuário em 2026-06-12 12:14)
**Origem:** usuário em 2026-06-12 12:10 (lista de 12 itens; usuário escolheu recorte em 2 SPECs — esta cobre os 11 ajustes)
**Resumo:** Lote de 11 ajustes de usabilidade e regras: ordenação alfabética de comboboxes de funcionários, mês/ano de referência em empréstimos, coluna/ordem na lista de empréstimos, frequência anual de tarefa, ícone do expander da folha, estado da sidebar, preservação de dados ao criar usuário duplicado via funcionário, card de funcionários na criação de tarefa, botão nova tarefa em Minhas Tarefas, geração automática de feriados do ano e reorganização dos grupos da sidebar.

## Objetivo

Aplicar um lote de melhorias pontuais de UX e pequenas regras de negócio reportadas pelo usuário após uso do sistema, melhorando fluidez de cadastros (funcionários, empréstimos, tarefas, feriados) e a navegação (sidebar, folha).

## Escopo

**DENTRO:**
1. Comboboxes que listam empregados em ordem alfabética (todas as telas que escolhem funcionário).
2. Empréstimo/adiantamento: trocar "data do início da cobrança" por **mês/ano de referência** de início da cobrança, associado ao período da folha de pagamento.
3. Lista de empréstimos (após escolher o funcionário): primeira coluna vira **data do empréstimo** (em vez do nome), ordenada decrescente.
4. Frequência de tarefa: adicionar opção **Anual**.
5. Folha: corrigir ícone do expander dos funcionários (setinha invertida).
6. Sidebar: manter estado dos grupos ao clicar em um item (não expandir grupos fechados).
7. Criar usuário pela tela de funcionários: em caso de dado duplicado (email/telefone/CPF), manter o formulário preenchido + oferecer associação ao usuário existente.
8. Inclusão de tarefa: card de funcionários já na criação (igual edição), insert de uma vez.
9. Minhas Tarefas: botão "Nova tarefa" que cria tarefa já atribuída ao empregado logado.
10. Feriados: botão para inserir automaticamente os feriados padrão (nacionais) de um ano escolhido.
11. Reorganização geral dos grupos da sidebar (RH, Financeiro, Cadastros, Ponto, Tarefas, Relatórios, …) mais intuitiva.

**FORA:**
- Cadastro de equipamentos (SPEC própria, decidida pelo usuário em 2026-06-12 12:14).
- Qualquer mudança no motor de cálculo da folha além da leitura do novo mês/ano de referência do empréstimo.

## Implementação

_(visão estática — detalhes de descoberta vão para `state.md`)_

- Ordenação alfabética: preferir ordenar no backend (`EmployeeService`/repository `GetAll`) + garantir `sort` no front onde a lista é montada localmente.
- Mês/ano de referência do empréstimo: manter coluna de data no banco normalizada para dia 1 do mês (sem migração destrutiva se possível); UI com seletor de mês; folha compara ano/mês.
- Feriados automáticos: gerar fixos nacionais + móveis (Carnaval, Sexta-feira Santa, Corpus Christi via cálculo da Páscoa) no backend.

## Critério de aceite

- [x] 1. Todos os comboboxes de escolha de empregado listam em ordem alfabética (2026-06-12 12:50, commit `9dd7de7` — ordenação Nickname??FullName no GetAll e default do GetPaged)
- [x] 2. Empréstimo usa mês/ano de referência para início da cobrança e a folha respeita esse período (2026-06-12 12:50, commits `9dd7de7`/`715020e` — inclusive 13º/férias/adiantamento)
- [x] 3. Lista de empréstimos do funcionário mostra a data do empréstimo como primeira coluna, ordem decrescente (2026-06-12 12:50, commit `715020e`)
- [x] 4. Tarefa aceita frequência Anual (criação/edição e geração de recorrência) (2026-06-12 12:50, commit `715020e` — backend já calculava "anual"; adicionadas também Trimestral/Semestral)
- [x] 5. Ícone do expander da folha aponta na direção correta (fechado ▸ / aberto ▾) (2026-06-12 12:50, commit `715020e`)
- [x] 6. Sidebar não altera grupos abertos/fechados ao navegar (2026-06-12 12:50, commit `715020e` — useEffect de auto-expand removido)
- [x] 7. Criar usuário duplicado pela tela de funcionário mantém dados preenchidos e oferece associar o usuário existente (2026-06-12 12:50, commits `9dd7de7`/`715020e`)
- [x] 8. Criação de tarefa permite escolher funcionários no próprio form (insert único) (2026-06-12 12:50, commit `715020e` — backend já aceitava AssignedEmployeeIds)
- [x] 9. Minhas Tarefas tem botão de nova tarefa que auto-atribui ao empregado logado (2026-06-12 12:50, commits `9dd7de7`/`715020e` — POST api/task/my)
- [x] 10. Cadastro de feriados gera feriados padrão de um ano escolhido (2026-06-12 12:50, commits `9dd7de7`/`715020e` — POST api/holiday/generate/{year})
- [x] 11. Sidebar reorganizada em grupos intuitivos (2026-06-12 12:50, commit `715020e` — RH/Financeiro/Cadastros/Ponto/Tarefas/Relatórios)
- [x] **Features tocadas (empregados-contratos, emprestimos-adiantamentos, tarefas, folha-pagamento, plataforma-core, usuarios, feriados) atualizadas** com timestamp e referência a esta SPEC (2026-06-12 12:52)
- [x] Decisões das features tocadas revisadas: obsoletas marcadas, ativas confirmadas (2026-06-12 12:52 — nenhuma obsoleta; nova decisão "mês de referência" adicionada em emprestimos-adiantamentos)
- [x] `state.md` com entrada `[conclusão]` (2026-06-12 12:50)
- [x] `memory.md` com TL;DR final atualizado (2026-06-12 12:50)
