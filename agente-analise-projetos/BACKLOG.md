# Backlog de Tarefas — CEMIG Agente IA Licenças Externas

> Todas as tarefas do projeto organizadas por etapa e ID.
> Abra uma issue no GitHub para cada tarefa ao iniciar seu desenvolvimento.

---

## ETP-001 — Repositório e Estrutura ✅

| ID | Tarefa | Status |
|----|--------|--------|
| ETP-001-A | Criar repositório no GitHub | ✅ |
| ETP-001-B | Definir estrutura de pastas | ✅ |
| ETP-001-C | Criar README principal com roadmap | ✅ |
| ETP-001-D | Criar ARQ-001 (arquitetura e schema) | ✅ |
| ETP-001-E | Criar templates de issues | ✅ |
| ETP-001-F | Criar BACKLOG.md | ✅ |
| ETP-001-G | Criar CHANGELOG.md | ✅ |
| ETP-001-H | Configurar .gitignore | ⏳ |
| ETP-001-I | Fazer primeiro commit e push | ⏳ |

---

## ETP-002 — Banco de Dados (Supabase)

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| DB-001 | Criar projeto no Supabase | ETP-001 | 🔲 |
| DB-002 | Executar schema SQL (tabelas) | DB-001 | 🔲 |
| DB-003 | Configurar autenticação por e-mail | DB-001 | 🔲 |
| DB-004 | Configurar Row Level Security (RLS) | DB-002 DB-003 | 🔲 |
| DB-005 | Criar bucket de storage para PDFs | DB-001 | 🔲 |
| DB-006 | Inserir prompt base DNIT na tabela prompts_orgao | DB-002 | 🔲 |

---

## ETP-003 — Backend / API (Vercel Functions)

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| API-001 | Inicializar projeto Next.js no VSCode | ETP-002 | 🔲 |
| API-002 | Configurar variáveis de ambiente (.env.local) | DB-001 | 🔲 |
| API-003 | Criar lib de conexão Supabase (`src/lib/supabase.ts`) | API-001 | 🔲 |
| API-004 | Criar lib de conexão Anthropic (`src/lib/anthropic.ts`) | API-001 | 🔲 |
| API-005 | Criar função `/api/analisar` (recebe PDF → chama Claude → salva análise) | API-003 API-004 | 🔲 |
| API-006 | Criar função `/api/reprovar` (registra reprovação → cria regra → atualiza prompt) | API-003 | 🔲 |
| API-007 | Criar função `/api/projetos` (CRUD projetos) | API-003 | 🔲 |
| API-008 | Criar função `/api/historico` (lista análises com filtros) | API-003 | 🔲 |

---

## ETP-004 — Interface React

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| UI-001 | Configurar Tailwind CSS e tema CEMIG | API-001 | 🔲 |
| UI-002 | Criar componente de autenticação (login) | DB-003 | 🔲 |
| UI-003 | Criar layout principal com navegação por abas | UI-001 | 🔲 |
| UI-004 | Criar aba "Analisar projeto" (upload PDF + seleção órgão/tipo) | API-005 | 🔲 |
| UI-005 | Criar componente de exibição do parecer (score + listas) | UI-004 | 🔲 |
| UI-006 | Criar aba "Registrar reprovação" (formulário + listagem) | API-006 | 🔲 |
| UI-007 | Criar aba "Base de aprendizado" (regras ativas + prompt vivo) | API-003 | 🔲 |
| UI-008 | Criar aba "Histórico" (análises + métricas) | API-008 | 🔲 |
| UI-009 | Deploy no Vercel e configurar domínio | UI-008 | 🔲 |

---

## ETP-005 — Base Normativa DNIT

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| NRM-001 | Estruturar IS 6/2008 por tipo de obra (aérea/subterrânea/longitudinal) | — | 🔲 |
| NRM-002 | Estruturar Resolução 9/2020 por artigo relevante | — | 🔲 |
| NRM-003 | Estruturar Portaria 524/2008 (TEP e documentação) | — | 🔲 |
| NRM-004 | Redigir e validar prompt base DNIT completo | NRM-001 NRM-002 NRM-003 | 🔲 |
| NRM-005 | Inserir prompt validado no banco (DB-006) | NRM-004 DB-006 | 🔲 |

---

## ETP-006 — Treinamento com Reprovações Históricas

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| TRN-001 | Levantar e organizar reprovações documentadas da equipe | — | 🔲 |
| TRN-002 | Categorizar por órgão, tipo de obra e item da norma | TRN-001 | 🔲 |
| TRN-003 | Importar reprovações para tabela `reprovacoes` | TRN-002 DB-002 | 🔲 |
| TRN-004 | Gerar regras de agente a partir das reprovações | TRN-003 | 🔲 |
| TRN-005 | Validar prompt atualizado com reprovações reais | TRN-004 | 🔲 |

---

## ETP-007 — Testes e Validação

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| TST-001 | Selecionar 10 projetos reais para teste (incluindo reprovados conhecidos) | ETP-006 | 🔲 |
| TST-002 | Executar análise do agente nos 10 projetos | TST-001 | 🔲 |
| TST-003 | Comparar resultado do agente vs resultado real do órgão | TST-002 | 🔲 |
| TST-004 | Calcular métricas de acurácia (precisão, recall, F1) | TST-003 | 🔲 |
| TST-005 | Ajustar prompt com base nos erros identificados | TST-004 | 🔲 |
| TST-006 | Redigir relatório de validação final | TST-005 | 🔲 |

---

## ETP-008 — Expansão DER-MG (Futuro)

| ID | Tarefa | Depende de | Status |
|----|--------|-----------|--------|
| NRM-006 | Estruturar base normativa DER-MG | ETP-007 | 🔲 |
| NRM-007 | Redigir prompt base DER-MG | NRM-006 | 🔲 |
| TRN-006 | Importar reprovações históricas DER-MG | NRM-007 | 🔲 |
| UI-010 | Adicionar DER-MG como opção no seletor de órgãos | TRN-006 | 🔲 |

---

## Legenda de Status

| Ícone | Significado |
|-------|-------------|
| ✅ | Concluído |
| ⏳ | Em andamento |
| 🔲 | Pendente |
| ❌ | Bloqueado |
| ⏸ | Pausado |
