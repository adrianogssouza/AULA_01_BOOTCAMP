# CEMIG — Agente IA para Licenças Externas

> Ferramenta interna de análise de projetos em faixas de domínio com inteligência artificial.

[![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)]()
[![Versão](https://img.shields.io/badge/versão-0.1.0-blue)]()
[![Órgão](https://img.shields.io/badge/fase%201-DNIT-green)]()

---

## Visão Geral

Agente de IA que analisa projetos de rede elétrica submetidos ao DNIT, DER, VALE/VLI e outros órgãos, identificando não conformidades antes do envio — reduzindo reprovações, retrabalho e atrasos em obra.

**Stack:**
- Frontend: React + Tailwind CSS (Vercel — free tier)
- Backend: Vercel Functions (serverless — free tier)
- Banco de dados: Supabase PostgreSQL (free tier)
- Storage: Supabase Storage (PDFs — free tier)
- IA: Anthropic Claude API (claude-sonnet-4-20250514)

---

## Estrutura do Projeto

```
cemig-agente-licencas/
├── README.md                    ← este arquivo (hub de controle)
├── docs/
│   ├── architecture/
│   │   └── ARQ-001_stack.md     ← decisões de arquitetura
│   ├── decisions/
│   │   └── DEC-001_stack.md     ← ADRs (Architecture Decision Records)
│   └── normas/
│       └── DNIT/                ← base normativa estruturada
├── src/
│   ├── agent/                   ← prompts e lógica dos agentes por órgão
│   ├── api/                     ← funções serverless (Vercel Functions)
│   ├── components/              ← componentes React
│   ├── lib/                     ← integrações (Supabase, Anthropic)
│   └── types/                   ← TypeScript types
├── .github/
│   ├── ISSUE_TEMPLATE/          ← templates de tarefas
│   └── workflows/               ← CI/CD automático
└── CHANGELOG.md                 ← histórico de versões
```

---

## Roadmap por Etapas

| ID | Etapa | Descrição | Status |
|----|-------|-----------|--------|
| [ETP-001](#etp-001) | Repositório e estrutura | Setup GitHub, VSCode, nomenclatura | ✅ Concluído |
| [ETP-002](#etp-002) | Banco de dados | Supabase — tabelas e autenticação | ⏳ Próxima |
| [ETP-003](#etp-003) | Backend / API | Vercel Functions + integração Claude | 🔲 Pendente |
| [ETP-004](#etp-004) | Interface React | Painel completo multi-órgão | 🔲 Pendente |
| [ETP-005](#etp-005) | Base normativa DNIT | Estruturação e carga das normas | 🔲 Pendente |
| [ETP-006](#etp-006) | Treinamento inicial | Importação das reprovações históricas | 🔲 Pendente |
| [ETP-007](#etp-007) | Testes e validação | Piloto com projetos reais | 🔲 Pendente |
| [ETP-008](#etp-008) | Expansão DER-MG | Replicar para rodovias estaduais | 🔲 Futuro |

---

## Detalhamento das Etapas

### ETP-001
**Repositório e estrutura do projeto**
- Criação do repositório GitHub
- Definição de nomenclatura e IDs
- Estrutura de pastas
- Templates de issues e PRs
- **Entregável:** repositório documentado e configurado

### ETP-002
**Banco de dados — Supabase**
- Tarefas: [DB-001] a [DB-006]
- Criação das tabelas (ver `docs/architecture/ARQ-001_stack.md`)
- Configuração de autenticação por e-mail
- Políticas de acesso por perfil (analista / admin)
- **Entregável:** banco funcional com schema validado

### ETP-003
**Backend — API e integração IA**
- Tarefas: [API-001] a [API-005]
- Vercel Functions para receber PDF e chamar Claude
- Roteador de órgãos (DNIT / DER / VALE)
- Salvar análises e aprendizados no Supabase
- **Entregável:** endpoint `/api/analisar` funcional

### ETP-004
**Interface — Painel React**
- Tarefas: [UI-001] a [UI-008]
- Upload de PDF, seleção de órgão e tipo de obra
- Exibição de parecer estruturado
- Registro de reprovações e visualização de aprendizados
- Histórico de análises com métricas
- **Entregável:** aplicação web completa no Vercel

### ETP-005
**Base normativa DNIT**
- Tarefas: [NRM-001] a [NRM-004]
- IS 6/2008 estruturada por tipo de obra
- Resolução 9/2020 por artigo e inciso
- Portaria 524/2008 (TEP)
- **Entregável:** prompt base DNIT validado

### ETP-006
**Treinamento com reprovações históricas**
- Tarefas: [TRN-001] a [TRN-003]
- Importação das reprovações documentadas da equipe
- Categorização por tipo de obra e órgão
- Validação do prompt aprendido
- **Entregável:** agente treinado com histórico real CEMIG

### ETP-007
**Testes e validação**
- Tarefas: [TST-001] a [TST-004]
- Análise de 10 projetos reais (incluindo reprovados conhecidos)
- Medição de acurácia vs resultado real
- Ajuste fino do prompt
- **Entregável:** relatório de validação com métricas

### ETP-008
**Expansão — DER-MG**
- Aguardando conclusão de ETP-007
- Replicar arquitetura para normas estaduais

---

## Convenção de IDs

| Prefixo | Domínio | Exemplo |
|---------|---------|---------|
| `ETP-` | Etapas do projeto | ETP-001 |
| `DB-` | Banco de dados | DB-001 |
| `API-` | Backend / funções | API-001 |
| `UI-` | Interface / componentes | UI-001 |
| `NRM-` | Base normativa | NRM-001 |
| `TRN-` | Treinamento do agente | TRN-001 |
| `TST-` | Testes e validação | TST-001 |
| `DEC-` | Decisões de arquitetura | DEC-001 |
| `BUG-` | Correções | BUG-001 |

---

## Convenção de Branches

```
main          → produção estável
develop       → integração contínua
feat/ETP-002  → desenvolvimento por etapa
fix/BUG-001   → correções
```

---

## Convenção de Commits

```
feat(ETP-002): adiciona tabela de projetos no Supabase
fix(BUG-001): corrige upload de PDF acima de 5MB
docs(NRM-001): estrutura IS 6/2008 travessia aérea
chore(ETP-001): configura ESLint e Prettier
```

---

## Equipe e Contatos

| Papel | Responsável |
|-------|-------------|
| Arquiteto / Product Owner | Equipe Licenças Externas CEMIG |
| Desenvolvimento | A definir |
| Base normativa DNIT | Equipe Licenças Externas CEMIG |

---

## Links Úteis

- [Supabase Dashboard](https://supabase.com) — banco de dados
- [Vercel Dashboard](https://vercel.com) — hospedagem
- [Anthropic Console](https://console.anthropic.com) — API Claude
- [Documentação Claude API](https://docs.anthropic.com)

---

*Projeto iniciado em 2025 — CEMIG Distribuição S.A. — Gerência de Engenharia e Projetos*
