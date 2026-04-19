# ARQ-001 — Arquitetura do Sistema

**ID:** ARQ-001  
**Etapa:** ETP-001  
**Status:** Aprovado  
**Data:** 2025

---

## Stack de Tecnologias

### Frontend
- **Framework:** React 18 + TypeScript
- **Estilo:** Tailwind CSS
- **Hospedagem:** Vercel (free tier — 100 GB bandwidth/mês)
- **Build:** Vite

### Backend
- **Runtime:** Vercel Functions (serverless, Node.js 20)
- **Linguagem:** TypeScript
- **Limite free:** 100.000 invocações/mês — suficiente para fase de testes

### Banco de Dados
- **Plataforma:** Supabase
- **Engine:** PostgreSQL 15
- **Free tier:** 500 MB dados, 1 GB storage, 50.000 MAU
- **Auth:** Supabase Auth (e-mail + senha)

### Inteligência Artificial
- **Provedor:** Anthropic Claude API
- **Modelo:** claude-sonnet-4-20250514
- **Custo estimado por análise:** US$ 0,003–0,015 (dependendo do tamanho do PDF)
- **Crédito inicial:** US$ 5 (cobre 300–1.600 análises completas)

---

## Schema do Banco de Dados

### DB-001 — Tabela `usuarios`
```sql
CREATE TABLE usuarios (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  nome TEXT NOT NULL,
  perfil TEXT CHECK (perfil IN ('analista', 'admin', 'gestor')) DEFAULT 'analista',
  orgaos_habilitados TEXT[] DEFAULT ARRAY['DNIT'],
  criado_em TIMESTAMPTZ DEFAULT NOW(),
  atualizado_em TIMESTAMPTZ DEFAULT NOW()
);
```

### DB-002 — Tabela `projetos`
```sql
CREATE TABLE projetos (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  ns TEXT,                              -- número do serviço CEMIG
  titulo TEXT NOT NULL,
  orgao TEXT NOT NULL,                  -- DNIT, DER, VALE, etc.
  tipo_obra TEXT NOT NULL,              -- aerea, subterranea, longitudinal, misto
  tensao TEXT,                          -- 13,8 kV / 34,5 kV / etc.
  rodovia TEXT,
  km TEXT,
  municipio TEXT,
  estado TEXT DEFAULT 'MG',
  pdf_url TEXT,                         -- URL no Supabase Storage
  texto_memorial TEXT,                  -- texto extraído do PDF
  status TEXT DEFAULT 'pendente',       -- pendente, analisado, enviado, aprovado, reprovado
  usuario_id UUID REFERENCES usuarios(id),
  criado_em TIMESTAMPTZ DEFAULT NOW(),
  atualizado_em TIMESTAMPTZ DEFAULT NOW()
);
```

### DB-003 — Tabela `analises`
```sql
CREATE TABLE analises (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  projeto_id UUID REFERENCES projetos(id) ON DELETE CASCADE,
  usuario_id UUID REFERENCES usuarios(id),
  orgao TEXT NOT NULL,
  veredicto TEXT CHECK (veredicto IN ('APROVADO', 'ATENCAO', 'REPROVADO')),
  score INTEGER CHECK (score BETWEEN 0 AND 100),
  resumo TEXT,
  nao_conformidades JSONB DEFAULT '[]',
  atencoes JSONB DEFAULT '[]',
  conformidades JSONB DEFAULT '[]',
  parecer_final TEXT,
  regras_aplicadas INTEGER DEFAULT 0,
  regras_aprendidas_aplicadas INTEGER DEFAULT 0,
  tokens_usados INTEGER,
  modelo_ia TEXT DEFAULT 'claude-sonnet-4-20250514',
  criado_em TIMESTAMPTZ DEFAULT NOW()
);
```

### DB-004 — Tabela `reprovacoes`
```sql
CREATE TABLE reprovacoes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  projeto_id UUID REFERENCES projetos(id),
  orgao TEXT NOT NULL,
  sr TEXT,                              -- superintendência regional: MG, SP, ES, GO
  tipo_obra TEXT NOT NULL,
  motivo TEXT NOT NULL,                 -- texto exato do ofício do órgão
  item_norma TEXT,                      -- ex: IS 6/2008 item 2.1-b
  causa_raiz TEXT,                      -- análise interna da equipe
  data_oficio DATE,
  incorporada_ao_agente BOOLEAN DEFAULT FALSE,
  usuario_id UUID REFERENCES usuarios(id),
  criado_em TIMESTAMPTZ DEFAULT NOW()
);
```

### DB-005 — Tabela `regras_agente`
```sql
CREATE TABLE regras_agente (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  orgao TEXT NOT NULL,
  tipo_obra TEXT NOT NULL,              -- aerea, subterranea, longitudinal, todos
  texto TEXT NOT NULL,                  -- descrição da regra
  norma_ref TEXT,                       -- referência normativa
  prioridade TEXT CHECK (prioridade IN ('critica', 'alta', 'media', 'baixa')) DEFAULT 'alta',
  origem TEXT CHECK (origem IN ('normativa', 'reprovacao', 'manual')) DEFAULT 'normativa',
  reprovacao_id UUID REFERENCES reprovacoes(id),
  ativa BOOLEAN DEFAULT TRUE,
  vezes_acionada INTEGER DEFAULT 0,
  criado_em TIMESTAMPTZ DEFAULT NOW(),
  atualizado_em TIMESTAMPTZ DEFAULT NOW()
);
```

### DB-006 — Tabela `prompts_orgao`
```sql
CREATE TABLE prompts_orgao (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  orgao TEXT UNIQUE NOT NULL,
  versao INTEGER DEFAULT 1,
  prompt_sistema TEXT NOT NULL,         -- prompt base com normas
  prompt_aprendizado TEXT,              -- regras aprendidas concatenadas
  prompt_completo TEXT,                 -- gerado automaticamente (base + aprendizado)
  ultima_atualizacao TIMESTAMPTZ DEFAULT NOW(),
  atualizado_por UUID REFERENCES usuarios(id)
);
```

---

## Fluxo de Dados

```
Analista faz upload do PDF
        ↓
Vercel Function /api/analisar
        ↓
Busca prompt do órgão no DB (prompts_orgao)
        ↓
Busca regras aprendidas ativas (regras_agente)
        ↓
Chama Claude API com PDF + prompt completo
        ↓
Recebe JSON estruturado (veredicto, score, itens)
        ↓
Salva análise no Supabase (analises)
        ↓
Retorna parecer para a interface
        ↓
Analista registra reprovação (se houver)
        ↓
Sistema cria nova regra em regras_agente
        ↓
Regenera prompt_completo em prompts_orgao
```

---

## Políticas de Acesso (Row Level Security)

- `analista` — lê e cria projetos/análises; não altera regras nem prompts
- `gestor` — lê tudo, não altera regras base
- `admin` — acesso total, pode editar regras e prompts base

---

## Variáveis de Ambiente Necessárias

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Anthropic
ANTHROPIC_API_KEY=

# App
NEXT_PUBLIC_APP_URL=
```
