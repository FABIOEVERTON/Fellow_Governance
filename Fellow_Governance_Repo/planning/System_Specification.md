```markdown
# Fellow Governance 🛡️🤖
> Plataforma SaaS de Governança de IA, Interpretabilidade Geométrica e Defesa Adversarial  
> *Traduzindo requisitos técnicos em evidências jurídicas e políticas executáveis*

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com)
[![Next.js 14](https://img.shields.io/badge/Next.js-14-black.svg)](https://nextjs.org)
[![OPA](https://img.shields.io/badge/Policy-OPA/Rego-00ACD7.svg)](https://openpolicyagent.org)

---

## 🎯 Missão

Capacitar **líderes de governança de IA** e **profissionais do direito** a responderem a autuações por descumprimento de frameworks de IA (NIST AI RMF, EU AI Act, PL 2338/2023, LGPD), transformando requisitos técnicos complexos em:

- ✅ Evidências jurídicas auditáveis
- ✅ Políticas Rego executáveis via OPA
- ✅ Trilhas de auditoria imutáveis (hash chain SHA-256)
- ✅ Geração de defesa adversarial em ≤300 segundos

### 👥 Público-Alvo
| Perfil | Caso de Uso Principal |
|--------|----------------------|
| ⚖️ Advogado | Modo Defesa: upload de auto de infração → tese em 3 linhas + ZIP forense |
| 🛡️ AI Governance Lead | Dashboard de risco, bloqueio de deploy via OPA, tracking MSB-21 |
| 📋 Auditor | Trilhas imutáveis, relatórios forenses, evidências linkadas a controles |
| 💻 Security/DevOps | Deploy de bundles Rego, monitoramento de drift, integração CI/CD |

---

## ⚡ O Problema que Resolvemos

❌ **SEM Fellow Governance**: CEO pressiona por velocidade → Modelo vai ao ar sem AIBOM, provenance ou monitoramento de drift → Autuação ANPD (multa R$2M+) → Equipe de governança demitida → Ciclo se repete.

✅ **COM Fellow Governance**: Gatekeeper OPA bloqueia deploy se MSB-21 falhar → Audit trail imutável registra tentativas de bypass → AI Governance apresenta scores, gaps e políticas → CEO alinha com risco real → Compliance implementado **antes** da autuação.

---

## 🏗️ Arquitetura de Sistema (Visão Completa)

```
┌─────────────────────────────────────────────────────────────┐
│                     FELLOW GOVERNANCE                        │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                    FRONTEND (Next.js 14)                ││
│  │  Chat • PolicyViewer • EvidenceGraph • DefenseResults  ││
│  └───────────────────────────┬─────────────────────────────┘│
│                              │ HTTPS / WebSocket            │
│  ┌───────────────────────────▼─────────────────────────────┐│
│  │                    BACKEND (FastAPI)                    ││
│  │  ┌─────────────────────────────────────────────────┐    ││
│  │  │         SYSTEM EXECUTION KERNEL (SEK)          │    ││
│  │  │  EventBus → Scheduler → StateEngine → PolicyGate│   ││
│  │  │  ↕ MIG Orchestrator ↕ PBSAI Controller ↕ Def   │    ││
│  │  └─────────────────────────────────────────────────┘    ││
│  │  22 AGENTES (A01–A18 + D01–D04)                         ││
│  └───────────────────────────┬─────────────────────────────┘│
│                              │                               │
│  ┌───────────────────────────▼─────────────────────────────┐│
│  │         INFRA: PostgreSQL+pgvector │ Redis │ OPA       ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────┘
```

### 🔑 Componentes Críticos

| Componente | Propósito | Tecnologia |
|------------|-----------|------------|
| **SEK** | Orquestração determinística de agentes | Python + agentscope |
| **MIG** | Interpretabilidade intrínseca (vetores de relevância) | pgvector + transformers |
| **PBSAI** | Segurança por design (21 controles MSB-21) | Rego + evidence graph |
| **PCG** | Plano de controle: admissibilidade → validação → execução | State machine + OPA |
| **AEC** | Arquitetura de execução controlada (runtime governance) | Hash chain + audit trails |
| **Defense Orchestrator** | Resposta adversarial em ≤300s | Timer + fallback pipeline |

---

## 🔒 Canonical Execution Pipeline (Mandatory)

Toda execução na plataforma **DEVE obrigatoriamente seguir este pipeline canônico**, independente do tipo de entrada, agente envolvido ou contexto.

### Fluxo Determinístico Obrigatório


INPUT
→ A13 (Ingestão e normalização)
→ A10 (Validação estrutural)
→ A08 (Interpretabilidade - MIG)
→ A09 (Consolidação de output)
→ A12 (Validação de policy via OPA)
→ AUDIT (Persistência em hash chain)
→ OUTPUT


### Regras de Execução

- ❌ Não configurável
- ❌ Não depende de feature flags
- ❌ Não pode ser bypassado por agentes ou usuários
- ❌ Não possui variações por tipo de input

- ✅ Obrigatório para 100% das execuções
- ✅ Qualquer desvio → execução inválida (`INVALID_RUN`)
- ✅ Garantia de determinismo e auditabilidade jurídica

### Enforcement

O `StateEngine` e o `PolicyGate` garantem que:

- Nenhum estado avance fora dessa sequência
- Nenhum write ocorra sem passar pelo A12 (OPA)
- Toda execução gere entrada na `audit_trails`
---
## 🤖 Os 22 Agentes Especializados (Matriz I/O Completa)

| Agente | Nome | Entrada | Saída | Formato |
|--------|------|---------|-------|---------|
| A01 | Monitor Legislativo | Texto de lei, ementa | Mudanças legislativas + impacto | JSON |
| A02 | Simulador de Impacto | Modelo + cenário regulatório | Score de risco fiscal/legal | JSON |
| A03 | Validador de Dados | Dataset CSV/JSON | Relatório de qualidade + gaps | JSON |
| A04 | Demandas de Inteligência | Query NL | Plano de coleta de evidências | JSON |
| A05 | Motor de Risco | Features + contexto | Matriz de risco (prob × impacto) | JSON |
| A06 | Reconciliador de Frameworks | NIST, EU AI Act, PL 2338 | Mapeamento cruzado de controles | JSON |
| A07 | Classificador de Governança de IA | Metadados do modelo | Nível de maturidade (1-5) | JSON |
| A08 | Auditor MIG | Vetores de camada da IA | Relatório de interpretabilidade | JSON |
| A09 | Gerador de Output | Resultados parciais | Relatório consolidado + NL | JSON + Markdown |
| A10 | Auditor Estrutural | Código/Modelo | Checklist de boas práticas | JSON |
| A11 | Integrador de Conhecimento Externo | Query + fontes | Contexto enriquecido | JSON |
| A12 | Gatekeeper OPA | Ação proposta + contexto | allow/deny + reason | Rego decision |
| A13 | Integrador Universal | PDF, CSV, API, DB | Dados estruturados normalizados | JSON |
| **A14** | **Caçador Ativo** | **Tema jurídico/regulatório** | **Precedentes STJ/ANPD em tempo real** | **JSON** |
| A15 | Curador de Insights | Evidências + contexto | Recomendações acionáveis | JSON |
| A16 | Detector de Shadow AI | Logs de rede/app | Inventário de modelos não governados | JSON |
| A17 | Gerador de Policy-as-Code YAML | Controles selecionados | YAML estruturado para OPA | YAML |
| A18 | Compilador Rego | YAML + templates | Bundle .rego compilado | Rego |
| D01 | Analisador Adversarial | Auto de infração + docs | Entidades extraídas + tese inicial | JSON |
| D02 | Gap Finder MSB-21 | Evidências + baseline | Lista de gaps com confiança | JSON |
| D03 | Gerador de Evidência MIG | Vetores de relevância | Gráfico + explicação NL | JSON + SVG |
| D04 | Gerador de Rego Regressivo | Gap detectado + contexto | Policy limitando autoridade | Rego |

> Todos os agentes seguem contratos Pydantic para input/output, garantindo type safety, validação e rastreabilidade completa.

---

## 🔬 MIG: Motor de Interpretabilidade Geométrica
*(Implementação proprietária baseada em QILIS/QIXAI – Willis, 2026)*

> 📝 **Nota de Padronização**:  
> • **MIG** = Motor de Interpretabilidade Geométrica (nome comercial da FellowGovernance)  
> • **QILIS** = Framework acadêmico de John M. Willis que inspira a implementação  
> • **PCG** = Plano de Controle de Governança (nome comercial)  
> • **AGCP** = AI Governance Control Plane (framework acadêmico de referência)  
> *Utilizamos a matemática pública (espaços vetoriais, hash chains) e citamos a inspiração, sem distribuir software de terceiros.*

### O que faz
Em vez de explicações textuais genéricas ("a IA decidiu X porque Y"), o MIG usa **matemática de Espaços de Hilbert** e **Vetores de Relevância** para provar, geometricamente, quais neurônios/parâmetros influenciaram uma decisão.

### Como funciona na prática
1. **AMSE** (Adaptive Model State Extraction): Extrai vetores de relevância (512-d) por camada da IA.
2. **RBCO** (Relevance-Based Confidence Oracle): Calcula confiança adaptativa baseada na relevância dos vetores.
3. **KB** (Knowledge Base): Armazena vetores em pgvector com TTL 24h para busca por similaridade.
4. **IOG** (Interpretability Output Generator): Gera saídas por perfil (dev, auditor, advogado) com heatmaps, gráficos e explicações em linguagem natural.

### Exemplo de uso jurídico
> **Acusação**: "A IA negou crédito com viés racial."  
> **Resposta do MIG**: "Vetores de relevância mostram que a decisão foi influenciada por 'histórico de pagamentos' (peso 0.92) e 'renda comprovada' (peso 0.88). Variáveis protegidas ('CEP', 'cor') tiveram relevância <0.03. Gráfico de projeção em Espaço de Hilbert anexo."

### Status de Propriedade Intelectual
✅ Você usa a **matemática pública** (espaços vetoriais, similaridade cosseno)  
✅ Cita a inspiração: *"Análise baseada em métricas de relevância geométrica inspirada nos padrões de transparência de ciclo de vida (Willis, 2026)"*  
❌ Não distribui software proprietário de terceiros

---

## ⚖️ PCG: Plano de Controle de Governança
*(Implementação proprietária baseada em AGCP – Willis, 2026)*

> 📝 **Nota de Padronização**: Ver nota na seção MIG acima sobre nomenclatura comercial vs. acadêmica.

### O que faz
É o "cérebro determinístico" que fica entre a IA e o mundo real. Nenhuma ação da IA é executada sem passar por 3 etapas:

```
1️⃣ ADMISSIBILIDADE → A ação proposta é legal? (check contra PL 2338, LGPD, etc.)
2️⃣ VALIDAÇÃO DE BINDING → A autoridade para esta ação existe no momento da execução?
3️⃣ EXECUÇÃO → Apenas após 1 e 2, a ação se torna real
```

### Como funciona na prática
- Advogado sobe o PL 2338 → PCG traduz artigos em regras Rego.
- IA do cliente tenta disparar decisão automatizada de alto risco → PCG consulta OPA.
- Se regra violada → PCG trava a execução e registra no audit trail.

### Workflows PCG Detalhados

#### 1. Canonical State Qualification
```
Entrada: RawState{freshness, completeness, provenance, confidence}
Regras:
• freshness < 24h → PASS else FAIL
• completeness == all_required_fields → PASS else FAIL
• provenance verified via hash chain → PASS else FAIL
• confidence ≥ 0.85 → PASS else FLAG_FOR_REVIEW
Saída: CanonicalState{qualified: bool, reasons: [], timestamp}
```

#### 2. Admissible Set Adjudication
```
Entrada: CandidateTransitions[] (múltiplas possíveis)
Processo:
1. Scheduler gera N transições candidatas
2. Re-deriva autoridade no commit (re-check OPA)
3. Resolve conflitos via voto majoritário + timestamp tie-breaker
4. Aplica binding validation: policy + context → allow/deny
Saída: AdmissibleTransition{selected_id, binding_proof, audit_entry}
```

#### 3. Binding Validation (OPA + Context)
```
Entrada: Policy(Rego) + Context{user, role, resource, action}
Avaliação OPA:
• opa eval --input context.json policy.rego
• Timeout: 2s (invariante global)
• Result: {allow: bool, trace: [], token: jwt}
Saída: BindingDecision{allow, token, trace_id, expires_at}
```

---

## 🔄 AEC: Arquitetura de Execução Controlada
*(Baseado em RGA – Willis, 2026)*

### O que faz
Garante que a governança aconteça **enquanto o sistema roda**, não depois que o erro já foi feito.

### Como funciona na prática
- Cada "proposta" da IA é registrada como `proposal_hash`.
- Cada "validação" do PCG gera `validation_hash`.
- Ambos são encadeados em `audit_trails` via SHA-256 (append-only).
- Resultado: **Forensic ZIP** com prova técnica de que a governança estava ativa no milissegundo da decisão.

### Estrutura do Forensic ZIP
```
forensic_YYYY-MM-DD-XXX.zip
├── manifest.json          (run_id, tenant_id, timestamp)
├── diagnosis.json         (resultados da análise)
├── policies/              (policy_v1.rego, policy_v1.yaml)
├── qilis_relevance_summary.json
├── pbsai_control_sheet.json
├── audit_chain.json       (SHA-256 encadeado, append-only)
└── signature.sha256       (assinado com GPG)
```

---

## 🛡️ PBSAI: Segurança por Design
*(21 Controles MSB-21 + 12 Domínios de Governança)*

### MSB-21: Minimum Security Baseline (21 controles)
| # | Controle | Descrição |
|---|----------|-----------|
| 1 | Inventory de modelos | Catálogo centralizado de todos os modelos em produção |
| 2 | AIBOM assinado | AI Bill of Materials com hash e assinatura digital |
| 3 | Hermetic builds + pinned deps | Builds reproduzíveis com dependências travadas |
| 4 | Provenance de datasets | Rastreabilidade completa da origem dos dados de treino |
| 5 | Drift monitoring (MIG) | Monitoramento contínuo de desvio via vetores de relevância |
| 6 | Human-in-the-loop para alto risco | Revisão humana obrigatória para decisões de alto impacto |
| 7 | Logging estruturado de decisões | Logs machine-readable com contexto completo |
| 8 | Política de retenção de evidências | TTL configurável para audit trails |
| 9 | Testes de viés pré-deploy | Suite automatizada de detecção de bias |
| 10 | Plano de resposta a incidentes | Playbook para falhas de governança |
| 11-21 | *(demais controles de segurança, privacidade, transparência, accountability)* |

### 12 Domínios de Governança
```
A-GRC    : Governance, Risk, Compliance
B-Ethics : Ética e Fairness
C-Privacy: Privacidade e Proteção de Dados
D-Security: Segurança Cibernética
E-Data   : Qualidade, Linhagem e Gestão de Dados
F-Model  : Desenvolvimento, Validação e Monitoramento de Modelos
G-Ops    : Operações, Deploy e Escalabilidade
H-Human  : Interação Humano-IA e Capacitação
I-Legal  : Conformidade Regulatória e Jurídica
J-Supply : Riscos de Terceiros e Cadeia de Suprimentos
K-Transp : Transparência e Explicabilidade
L-Resil  : Resiliência, Recuperação e Continuidade
```

### Evidence Graph
- Árvore interativa (react-flow/d3) mostrando controles atendidos/violados.
- Cada nó linka para `audit_trails` e `kb_state` para auditoria profunda.

### 🖼️ Mockup: Evidence Graph (react-flow)

```
┌─────────────────────────────────────────────────────────────┐
│  EVIDENCE GRAPH - Fellow Governance                         │
│  Run: def-2026-04-18-001 | Tenant: org_publica_01          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  [MSB-21 #2: AIBOM] ──❌── [Gap: Ausente]                   │
│         │                                                   │
│         ▼                                                   │
│  [audit_trails] ──── SHA256: a3f2...9c1b                 │
│         │                                                   │
│         ▼                                                   │
│  [kb_state] ──📊── Relevance Vector (512-d)                │
│         │                                                   │
│         ▼                                                   │
│  [IOG Report] ──📄── "Drift < 0.03 em variáveis protegidas"│
│                                                              │
│  Legenda:                                                  │
│  ✅ Controle atendido  ❌ Gap detectado  🔗 Hash chain      │
│  📊 Vetor pgvector   📄 Explicação NL   ⚠️ Alerta          │
│                                                              │
│  [🔍 Zoom] [📥 Export PNG] [🔗 Copiar URL]                 │
└─────────────────────────────────────────────────────────────┘
```

> 💡 **No HTML interativo**: Este mockup será substituído por um componente `react-flow` real, com nós clicáveis que expandem para `audit_trails`, `kb_state` e explicações em linguagem natural.

---

## ⚡ Pipeline de Defesa Adversarial (≤300 segundos)

```
1️⃣ UPLOAD (0-10s)
   • Advogado sobe: auto de infração, relatório do modelo, CSV de treino
   • Sistema valida formato, extrai metadados, gera run_id

2️⃣ ANÁLISE PARALELA (10-180s)
   • D01: Extrai entidades, mapeia framework aplicável
   • D02: Compara evidências vs MSB-21 → lista gaps com confiança
   • D03: MIG gera vetores de relevância + explicação geométrica
   • D04: Compila policy regressiva limitando autoridade do agente

3️⃣ COMPILAÇÃO (180-270s)
   • Junta resultados em JSON estruturado
   • Gera Forensic ZIP com hash chain + assinatura GPG
   • Compila bundle Rego para deploy imediato

4️⃣ ENTREGA (270-300s)
   • Retorna: furos_detectados[], defesa_proposta{linha1,2,3}, forensic_zip_url, policy_regressiva.rego
   • WebSocket notifica frontend: DefenseResults
```

### Exemplo de Output de Defesa
```json
{
  "run_id": "def-2026-04-18-001",
  "execution_time_ms": 287000,
  "furos_detectados": [
    {"control_id": "MSB-21 #2", "description": "Ausência de AIBOM assinado", "confidence": 0.94},
    {"control_id": "MSB-21 #7", "description": "Logs sem contexto completo", "confidence": 0.88}
  ],
  "defesa_proposta": {
    "linha1": "Ausência de artefatos mínimos (AIBOM, provenance) impede imputação de dolo.",
    "linha2": "Falta de hermetic builds e drift monitoring configura falha de governança do acusador.",
    "linha3": "MIG prova ausência de causalidade entre variáveis protegidas e decisão (drift < 0.03)."
  },
  "forensic_zip": "https://api.fellowgovernance.com/forensic/def-2026-04-18-001.zip",
  "policy_regressiva": "package ceis.regressive\ndefault allow = false\nallow { input.review_by_human == true }"
}
```

---

## 📋 Operações & Runbooks

### 1. Gestão de Segredos
- **Ferramenta**: AWS Secrets Manager ou HashiCorp Vault
- **Rotação**: Automática a cada 90 dias
- **Escopo**: DATABASE_URL, OPA_URL, JWT_SECRET, GPG_KEYS

### 2. Backup & Recovery
| Métrica | Valor | Implementação |
|---------|-------|---------------|
| RPO | 1h | Backup incremental via WAL archiving |
| RTO | 4h | Restore automatizado + health check |
| Retenção | 30 dias diários + 12 mensais | S3 lifecycle policy |

### 3. Alertas Críticos (SLOs)
| Condição | Severidade | Ação Automática |
|----------|------------|-----------------|
| OPA timeout >2s | P1 | Fallback para policy cache + alerta Slack |
| Hash chain break | P0 | LOCK_SESSION + notificação segurança |
| Defense pipeline >300s | P1 | Timeout + resposta degradada + log auditoria |
| CPU/Mem >90% por tenant | P2 | Throttling + alerta capacity |

### 4. Matriz de Conformidade (MSB-21 → Feature → Evidência)
| Controle | Feature | Evidência Auditável |
|----------|---------|---------------------|
| #1 Inventory | A07 + Dashboard | `agent_runs` + `documents` |
| #2 AIBOM assinado | A10 + A18 | `audit_trails` + `signature.sha256` |
| #3 Hermetic builds | CI/CD + A18 | GitHub Actions logs + `policy.sha256` |
| #4 Provenance datasets | A13 + A03 | `documents.hash_sha256` + `kb_state` |
| #5 Drift monitoring | QILIS/AMSE | `kb_state.relevance_vector` + IOG report |
| #6 Human-in-the-loop | PCG + A12 | `opa_decisions.binding_token` + `users.role` |
| #7 Logging estruturado | SEK + A09 | `audit_trails` append-only |
| ... | ... | ... |

> ✅ Todas as evidências são extraíveis via API `/api/v1/audit/{run_id}` ou Forensic ZIP.

### 5. Multi-Tenancy & Versionamento Global
- **Isolamento**: Shared database com `tenant_id` em todas as tabelas + Row-Level Security (RLS) no PostgreSQL. Índices compostos `(tenant_id, created_at)`.
- **Quota**: `RESOURCE_CAP` enforcement via Redis rate-limiter + OPA deny se `tenant_usage > quota`.
- **Versionamento**: 
  - API: `/api/v1/`, `/api/v2/` (header `Accept-Version`)
  - Agentes: `agent_version` em `agent_runs` (semântico `MAJOR.MINOR.PATCH`)
  - Policies: `bundle_version` em `manifest.json`
  - DB: Alembic migrations com hash check pré-execução
  - MIG: `vector_schema_version` no payload para compatibilidade de replay

---

## 🛠️ Stack Tecnológico Completo

### Backend
```
Python 3.11 + FastAPI 0.104+
agentscope 0.5+ (orquestração de agentes)
PostgreSQL 14 + pgvector (busca semântica)
Redis 7 (cache/queue)
OPA 0.58 (policy engine)
Celery 5.3 (tasks assíncronas)
Pydantic 2.0 (validação de dados)
spaCy 3.7 + sentence-transformers 2.2 (NLP)
python-gnupg 0.5 (assinatura digital)
Alembic 1.12 (migrations)
structlog + Prometheus + OpenTelemetry (observabilidade)
```

### Frontend
```
Next.js 14 (App Router) + React 18 + TypeScript 5
Tailwind CSS 3.3 + Zustand 4.4 (estado)
React Query 5.0 (data fetching) + Chart.js 4.4 (visualização)
react-flow 11.8 (Evidence Graph) + socket.io-client 4.5 (realtime)
```

### Infraestrutura
```
Docker 24+ + Kubernetes 1.27 + Helm 3.12
GitHub Actions (CI/CD) + Terraform 1.5 (IaC)
Prometheus 2.45 + Grafana 10.0 + Loki 2.9 (monitoring)
AWS/GCP ready (multi-cloud)
```
## 📁 Estrutura do Repositório

fellow-governance/
├── backend/
│   ├── app/
│   │   ├── agents/              # A01–A18, D01–D04
│   │   ├── core/                # SEK (EventBus, Scheduler, StateEngine)
│   │   ├── policies/            # Rego + YAML
│   │   ├── api/                 # FastAPI routes
│   │   ├── models/              # Pydantic schemas
│   │   ├── services/            # MIG, PBSAI, PCG
│   │   └── db/                  # conexão + migrations
│   ├── tests/
│   └── scripts/
│
├── frontend/
│   ├── app/                     # Next.js App Router
│   ├── components/              # UI (PolicyViewer, EvidenceGraph)
│   ├── hooks/
│   └── services/                # API client
│
├── infrastructure/
│   ├── docker/
│   ├── k8s/
│   └── terraform/
│
├── policies/                    # bundles OPA versionados
├── examples/                    # dados de teste
├── docs/
│   ├── rfc/                     # decisões arquiteturais
│   └── architecture.md
│
├── .env.example
├── docker-compose.yml
└── README.md

---

## 🗄️ Schema do Banco de Dados (13 Tabelas)

```
tenants (1)──N users, documents, agent_runs
agent_runs (1)──N policies, opa_decisions
audit_trails: prev_hash encadeado (SHA-256, append-only)
kb_state: pgvector relevance vectors (AMSE, 512-d)
defense_runs: independente, linkado a tenants & audit_trails

Tabelas principais:
• tenants: id, name, plan, status
• users: id, tenant_id, role, email, password_hash
• documents: id, tenant_id, hash_sha256, mime_type, uploaded_at
• agent_runs: id, run_id, agent_name, status, input_hash, output_hash
• policies: id, run_id, rego_text, sha256, deployed_at
• opa_decisions: id, policy_id, result, binding_token, trace
• audit_trails: id, run_id, prev_hash, payload_hash, timestamp
• kb_state: id, layer_id, relevance_vector (pgvector), ttl_expires
• msb21_attest: id, artifact_id, signature, baseline_status
• pbsai_evid_graph: id, node_type, edge_type, trace_id
• defense_runs: id, run_id, case_text, gaps_json, rego_policy, elapsed_ms
• websocket_sessions: id, tenant_id, run_id, connected_at
• feature_flags: id, tenant_id, flag_key, enabled, updated_at
```

> Hash chain em `audit_trails`: `prev_hash = SHA256(anterior.payload_hash + anterior.timestamp)`  
> Quebra na cadeia → sistema entra em LOCK e alerta segurança.

---

## 🔐 6 Invariantes Globais (Regras Obrigatórias)

| Invariante | Descrição | Implementação |
|------------|-----------|---------------|
| 🔒 RESOURCE_CAP | Orçamento de CPU/memória por tenant para evitar DoS | `if tenant_usage > quota: DENY + alert` |
| 🔗 CODE_IDENTITY | Hash de policy/agente inalterado pós-deploy para garantir integridade | `if sha256(policy) != deployed_hash: LOCK` |
| 🧵 DATA_LINEAGE | Cadeia SHA-256 contínua; quebra → DENY + LOCK_SESSION | `if prev_hash != compute_hash(prev): DENY` |
| ✅ OPA_FINALITY | Nenhuma transição DB sem `allow==true` + commit_token do OPA | `if !opa_allow || !commit_token: ROLLBACK` |
| 🤖 AI_NO_MUTATION | LLM propõe, apenas o Kernel executa writes (separação de concerns) | `if agent_type == "LLM" && action == "write": DENY` |
| ⏱️ DEFENSE_TIMER | Pipeline de defesa ≤ 300 segundos (SLA crítico para advogados) | `if elapsed > 300s: TIMEOUT + fallback` |

---

## 🔗 Event Contract (EventBus Formal)

Todos os componentes se comunicam via **eventos imutáveis versionados**, garantindo rastreabilidade, idempotência e reprocessamento seguro.

### Estrutura Padrão do Evento
```json
{
  "event_id": "uuid-v7",
  "event_type": "analysis_request",
  "version": "v1",
  "timestamp": "2026-04-20T14:32:10Z",
  "producer": "frontend|agent|system",
  "payload": {},
  "metadata": {
    "tenant_id": "string",
    "trace_id": "uuid",
    "correlation_id": "uuid",
    "retry_count": 0,
    "idempotency_key": "sha256(payload)"
  }
}
```

### Tipos de Eventos (Core)

| Evento               | Origem   | Destino       | Descrição            |
| -------------------- | -------- | ------------- | -------------------- |
| `analysis_request`   | Frontend | SEK           | Início de pipeline   |
| `data_ingested`      | A13      | Scheduler     | Dados normalizados   |
| `analysis_completed` | A09      | Frontend      | Resultado final      |
| `opa_evaluated`      | A12      | PCG           | Decisão allow/deny   |
| `defense_started`    | Frontend | D01–D04       | Pipeline adversarial |
| `error_occurred`     | Qualquer | Observability | Falha rastreável     |

### Garantias do EventBus

* **Imutabilidade**: eventos não são alterados após emissão
* **Idempotência**: `idempotency_key` evita duplicação
* **Ordenação por tenant**: garantida por `timestamp + sequence`
* **Retry seguro**: baseado em `retry_count`
* **Replay**: eventos podem ser reprocessados para auditoria

---

## 🔄 State Machine Global (StateEngine)

Define todos os estados possíveis de uma execução (`run_id`) e suas transições válidas.

### Estados

```
INIT
INGESTED
NORMALIZED
ENRICHED
ANALYZED
VALIDATED
BLOCKED
COMPLETED
FAILED
TIMEOUT
```

### Transições Permitidas

```
INIT → INGESTED
INGESTED → NORMALIZED
NORMALIZED → ENRICHED
ENRICHED → ANALYZED
ANALYZED → VALIDATED
VALIDATED → COMPLETED
VALIDATED → BLOCKED
ANY → FAILED
ANY → TIMEOUT
```

### Regras de Transição

* Apenas o **StateEngine** pode alterar estado
* Toda transição gera um `state_transition_event`
* Transições inválidas → rejeitadas + log de auditoria
* Estados `FAILED` e `TIMEOUT` são terminais

### Rollback

* Não há rollback destrutivo
* Correções geram **nova execução (`run_id`)**
* Histórico preservado via `audit_trails`

---

## ⚠️ Estratégia de Erro e Resiliência por Agente

Cada agente opera com política explícita de execução.

### Contrato de Execução

```yaml
timeout: 5s
retry: 2
backoff: exponential (1s, 2s)
fallback: fail_hard | use_cached | degrade
circuit_breaker: enabled (threshold: 5 falhas consecutivas)
```

### Tipos de Falha

| Tipo                             | Ação                  |
| -------------------------------- | --------------------- |
| Timeout                          | Retry até limite      |
| Erro de validação                | Fail_hard             |
| Dependência externa indisponível | Fallback `use_cached` |
| Erro crítico                     | Circuit breaker abre  |

### Comportamento Global

* Falha em agente crítico → pipeline `FAILED`
* Falha em agente não crítico → `degraded_mode=true`
* Todos erros geram `error_occurred` com trace completo

---

## 🤖 Contrato de Execução de Agentes (BaseAgent)

```python
from pydantic import BaseModel, Field
from abc import ABC, abstractmethod
from typing import Any, Dict

class AgentInput(BaseModel):
    tenant_id: str
    run_id: str
    payload: Dict[str, Any]
    context: Dict[str, Any] = Field(default_factory=dict)

class AgentOutput(BaseModel):
    status: str = "success" | "degraded" | "failed"
    data: Dict[str, Any]
    meta: Dict[str, Any] = Field(default_factory=dict)

class BaseAgent(ABC):
    @abstractmethod
    def validate(self, input: AgentInput) -> bool: pass

    @abstractmethod
    def run(self, input: AgentInput) -> AgentOutput: pass

    @abstractmethod
    def fallback(self, error: Exception) -> AgentOutput: pass

    def execute(self, input: AgentInput) -> AgentOutput:
        if not self.validate(input):
            return self.fallback(ValueError("Invalid schema"))
        try:
            return self.run(input)
        except Exception as e:
            self.circuit_break.record(e)
            return self.fallback(e)
```
> Garante: validação pré-execução, fallback determinístico, circuito de quebra e saída tipada para todos os 22 agentes.

---

## ⚖️ Modelo de Consistência do Sistema

Define como os dados se comportam entre componentes distribuídos.

### Tipos de Consistência

| Camada                      | Modelo                              |
| --------------------------- | ----------------------------------- |
| PostgreSQL (dados críticos) | **ACID (forte)**                    |
| OPA (decisão de policy)     | **Consistência forte (bloqueante)** |
| EventBus                    | **Eventual consistency**            |
| Redis cache                 | **Eventual consistency com TTL**    |
| MIG / vetores               | **Eventual consistency controlada** |

### Regras Globais

#### 1. Writes Críticos
* Sempre via PostgreSQL (transação ACID)
* Commit somente após:
  * `OPA allow == true`
  * `commit_token válido`

#### 2. Event Processing
* Assíncrono
* Pode haver atraso, mas nunca perda
* Garantia de replay

#### 3. OPA Finality
```text
Nenhuma mutação persistente ocorre sem validação OPA
```

#### 4. Concorrência
* Controle por tenant (semáforo lógico)
* DB usa:
  * **Row-level locking**
  * ou **optimistic locking (`version`)**

#### 5. Idempotência Global
* Toda operação crítica possui `idempotency_key`
* Reexecuções não duplicam efeitos

---

## ⚖️ Modelo de Autorização (RBAC/ABAC + OPA)

### Roles Definidas
| Role | Permissões Principais |
|------|----------------------|
| `ADMIN` | Gerenciar tenants, deploy policies, view all audit trails, override LOCK |
| `AI_LEAD` | Executar diagnóstico, compilar policies, view drift, block deploy |
| `LAWYER` | Modo defesa, upload docs, view forensic ZIP, export tese |
| `AUDITOR` | Read-only audit trails, export control sheets, verify hash chains |
| `GUEST` | Demo sandbox, sem write, dados mockados |

### Integração OPA por Usuário
```rego
input.user.role == "LAWYER"
input.user.tenant_id == "org_publica_01"
input.request.path == "/api/v1/defense/start"
input.request.method == "POST"

# Regra OPA
allow {
  input.user.role == "LAWYER"
  input.user.tenant_id == "tenant_id_from_jwt"
}
```
> ABAC aplicado via JWT claims (`role`, `tenant_id`, `scope`). OPA valida antes de qualquer write. Bypass lógico impossível.

---

## 📜 API Contracts & Schema Formal

### Versionamento
`/api/v1/` (current) → Header `Accept-Version: v1.2` para compatibilidade. Breaking changes → `/api/v2/`.

### Erros Padronizados
```json
{
  "error": {
    "code": "EPA-042",
    "message": "OPA timeout exceeded",
    "details": {"policy": "msb21_baseline", "duration_ms": 2100},
    "trace_id": "req_7f3a9c"
  }
}
```

### Contracts Principais

**POST /api/v1/diagnostic/start**
```json
// Request
{
  "tenant_id": "string",
  "input_type": "pdf|csv|json",
  "document_hash": "sha256",
  "framework": ["NIST", "PL2338"]
}
// Response
{
  "run_id": "uuid-v7",
  "status": "INIT",
  "estimated_completion": "2026-04-20T14:35:00Z"
}
```

**POST /api/v1/defense/start**
```json
// Request
{
  "tenant_id": "string",
  "case_text": "string",
  "evidence_files": ["sha256", "sha256"]
}
// Response
{
  "run_id": "uuid-v7",
  "timer_started": true,
  "sla_ms": 300000
}
```
> Contratos validados via Pydantic `request_validator` antes de entrar no EventBus. Versionamento de payload garantido.

---

## 📦 Especificação de Policy Bundle (OPA)

```
bundle/
├── manifest.json
│   ├── bundle_version: "1.4.0"
│   ├── signed_by: "gpg_key_id"
│   ├── compatibility: ["opa>=0.58"]
│   └── hash: "sha256_of_bundle"
├── data.json (contexto estático: thresholds, domains)
└── policies/
    ├── msb21_baseline.rego
    ├── nist_ai_rmf.rego
    ├── pl2338_compliance.rego
    └── ceis_internal.rego
```
- **Versionamento**: Semântico `MAJOR.MINOR.PATCH` em `manifest.json`
- **Assinatura**: `gpg --detach-sign bundle.tar.gz` → `signature.asc` no repositório
- **Deploy**: `opa bundle push` + webhook para reload. Rollback automático se `opa eval` falhar em teste de smoke.

---

## 🔬 Execução Concreta do MIG (Implementação)

### Extração de Vetores
```python
# Hook real no modelo (PyTorch/Transformers)
with torch.no_grad():
    outputs = model(input_ids, attention_mask, output_attentions=True, output_hidden_states=True)
    layer_vectors = outputs.hidden_states[-1][:, 0, :]  # CLS token ou pooled output
    attention_weights = outputs.attentions[-1]
```
- **Formato**: `float32`, L2-normalizado, 512-dimensões (`vector / np.linalg.norm(vector)`)
- **Persistência**: `kb_state.relevance_vector` (pgvector `halfvec(512)`)

### Cálculo de Relevância
1. **Baseline**: Vetor médio de decisão não-tendenciosa (treinado em dataset balanceado)
2. **Similaridade**: `cosine_similarity(current_vector, baseline)`
3. **Atribuição**: Integrated Gradients simplificado → `relevance_score = Σ (∂output/∂input_i * input_i)`
4. **Drift**: `AMSE = mean((v_current - v_baseline)^2)`. Threshold `> 0.15` → alerta.

> Executável. Não conceitual. Usa APIs públicas de PyTorch/Transformers + pgvector `cosine_distance`.

---

## 🧩 Garantias Finais do Sistema

Após definição deste bloco, a plataforma garante:

* 🔍 **Rastreabilidade total** (eventos + audit_trails)
* 🔒 **Execução determinística** (state machine explícita)
* ⚖️ **Governança obrigatória** (OPA como gatekeeper)
* 🔁 **Reprocessamento seguro** (event replay)
* 🛡️ **Resiliência controlada** (fallback + circuit breaker)
* 📊 **Consistência definida** (ACID vs eventual explícito)

> ✅ Este bloco elimina ambiguidade de execução e fecha a arquitetura como especificação técnica completa.

---

## 🔌 API Endpoints + WebSocket

### REST API (FastAPI)
```
POST /api/v1/diagnostic/start     → Inicia análise de risco (retorna run_id)
GET  /api/v1/diagnostic/{run_id}  → Status da análise + resultados parciais
POST /api/v1/defense/start        → Inicia modo defesa (≤300s)
GET  /api/v1/defense/{run_id}     → Resultados da defesa + links para ZIP
POST /api/v1/policies/compile     → Compila YAML → Rego bundle
GET  /api/v1/policies/{bundle_id} → Download do bundle .rego
POST /api/v1/opa/eval             → Avalia contexto contra policy (OPA proxy)
GET  /api/v1/audit/{run_id}       → Trilha de auditoria em formato JSON
```

### WebSocket
```
/ws/pipeline/{run_id}
→ Mensagens: PipelineStatus, QILIS_Dashboard, DefenseResults, ErrorAlert
→ Autenticação via JWT + binding_token do OPA
```

### Componentes Frontend Principais
```
• PipelineStatus: Barra de progresso em tempo real
• PolicyViewer: Editor Rego com syntax highlighting + diff
• EvidenceGraph: Árvore interativa de evidências (react-flow)
• KB_Inspector: Busca semântica em vetores de relevância
• QILIS_Dashboard: Heatmaps de drift + explicações NL
• DefenseResults: Tese em 3 linhas + download do ZIP forense
```

---

## 📦 Outputs Gerados (Formatos Reais)

### Diagnóstico (JSON)
```json
{
  "agent": "A01",
  "output": {
    "legislative_changes": [
      {"article": "PL 2338/2023 Art. 27-A", "impact": 0.87, "summary": "Exige revisão humana para decisões de alto risco"}
    ]
  },
  "qilis_relevance_vector": [0.92, 0.78, 0.88, 0.65, "... (512 dims)"]
}
```

### Política Rego (Exemplo)
```rego
package ceis.policy
default allow = false

allow {
  input.risk_score.fiscal < 0.7
  input.risk_score.legal < 0.7
  input.document_has_aibom == true
  input.qilis_drift < 0.15
}

deny_reason["MSB-21 #2 violation"] { not input.document_has_aibom }
deny_reason["Drift excessivo"] { input.qilis_drift >= 0.15 }
```

### Forensic ZIP (Estrutura)
```
forensic_2026-04-18-001.zip
├── manifest.json              {run_id, tenant_id, timestamp}
├── diagnosis.json             (resultados completos da análise)
├── policies/
│   ├── policy_v1.rego         (bundle compilado)
│   └── policy_v1.yaml         (fonte legível)
├── qilis_relevance_summary.json
├── pbsai_control_sheet.json   (status dos 21 controles)
├── audit_chain.json           (hash chain SHA-256 append-only)
└── signature.sha256           (assinatura GPG para verificação)
```

---

## 📅 Cronograma de Construção (90 Dias | ~250h)

### Visão Semanal (12 Semanas)
| Semana | Dias | Tema | Entregáveis Principais |
|--------|------|------|----------------------|
| **1** | 1-7 | Fundação | Docker, Git, FastAPI setup, Next.js scaffold |
| **2** | 8-14 | Banco + Auth | PostgreSQL+pgvector, JWT+RBAC, migrations |
| **3** | 15-21 | agentscope + SEK | BaseAgent, EventBus, Scheduler, StateEngine, PolicyGate |
| **4** | 22-28 | Agentes A01-A03 | Monitor Legislativo, Simulador Impacto, Validação Dados |
| **5** | 29-35 | Agentes A04-A08 | Demandas, Motor de Risco, Reconciliador, Gov Classifier, Auditor MIG |
| **6** | 36-42 | Agentes A09-A14 | Output Generator, Auditoria Estrutural, Conhecimento Externo, Gatekeeper OPA, Integrador Universal, **Caçador Ativo (A14)** |
| **7** | 43-49 | A15-A18 + MIG | Curador Insights, Shadow AI Detector, Policy-as-Code YAML, Compiler Rego + AMSE/RBCO/KB/IOG |
| **8** | 50-56 | PBSAI | MSB-21 validator (21 controles), 12 domínios, evidence graph |
| **9** | 57-63 | PCG + SEK | Vocabulary enforcement, canonical_state, admissible_set, FVL |
| **10** | 64-71 | Defesa D01-D04 | Analisador Adversarial, Gap Finder, MIG Evidence, Rego Regressivo + timer 300s |
| **11** | 72-82 | Frontend | ChatInterface, PolicyViewer, EvidenceGraph, KB_Inspector, QILIS_Dashboard, DefenseInterface |
| **12** | 83-90 | Testes + CI/CD + Docs | pytest, Playwright, GitHub Actions, Helm charts, documentação completa |

> ⚠️ **Nota de Execução Real**: Para produção enterprise completa (incluindo otimização avançada do MIG, hardening de OPA em escala e testes de carga multi-tenant), o ciclo estende-se para ~120 dias. O cronograma de 90 dias refere-se ao MVP funcional com escopo controlado.

### Detalhamento Dia a Dia (Resumo Operacional)
```
Dia 1-7: Setup infra + Git + FastAPI + Next.js
Dia 8-14: Banco de dados + autenticação JWT + RBAC
Dia 15-21: SEK core (EventBus, Scheduler, StateEngine, PolicyGate)
Dia 22-42: Implementação dos 18 agentes de análise (A01-A18)
Dia 43-49: MIG completo (AMSE, RBCO, KB, IOG) + agentes A15-A18
Dia 50-56: PBSAI (MSB-21 + 12 domínios + evidence graph)
Dia 57-63: PCG workflows (canonical_state, admissible_set, binding_validation)
Dia 64-71: Defesa adversarial (D01-D04) + timer 300s
Dia 72-82: Frontend completo (11 componentes principais)
Dia 83-90: Testes unitários/integração, CI/CD, documentação, smoke tests
```

> Cada semana produz um PR com descrição, testes e deploy em staging.

---

## 🚀 Getting Started (Desenvolvimento)

```bash
# 1. Clone e setup inicial
git clone https://github.com/FABIOEVERTON/Fellow_Reg.git
cd Fellow_Reg

# 2. Backend
cd backend
python -m venv venv && source venv/bin/activate
pip install poetry && poetry install
cp .env.example .env  # configure DATABASE_URL, REDIS_URL, OPA_URL

# 3. Frontend
cd ../frontend
pnpm install
cp .env.example .env.local

# 4. Infraestrutura (Docker)
cd ../infrastructure
docker-compose up -d postgres redis opa

# 5. Rodar serviços
# Terminal 1: Backend
cd ../backend && poetry run uvicorn app.main:app --reload

# Terminal 2: Frontend  
cd ../frontend && pnpm dev
# Acesse: http://localhost:3000
```

---

## ▶️ Execução End-to-End (Primeiro Uso)

```bash
# 1. Acesse o frontend
http://localhost:3000

# 2. Login com usuário padrão (seed automático)
Email: admin@demo.com
Senha: admin123

# 3. Execute um diagnóstico:
- Upload: /examples/sample_data/model_report.pdf
- Clique: "Iniciar Análise"

# 4. Acompanhe em tempo real:
- PipelineStatus (barra de progresso)
- EvidenceGraph (árvore de evidências)

# 5. Resultado esperado:
- Relatório JSON em /api/v1/diagnostic/{run_id}
- Policy Rego gerada em /api/v1/policies/{bundle_id}
- Audit trail disponível em /api/v1/audit/{run_id}

# 6. Teste modo defesa (curl):
curl -X POST http://localhost:8000/api/v1/defense/start \
  -H "Authorization: Bearer $(cat .jwt_token)" \
  -H "Content-Type: application/json" \
  -d '{
    "tenant_id": "demo_tenant",
    "case_text": "Autuação por viés algorítmico em licitação",
    "evidence_files": ["sha256_abc123", "sha256_def456"]
  }'
```

---

## 📁 Dados de Exemplo (Fixtures Reais)

```
/examples
├── auto_infracao_exemplo.pdf      # Auto de infração ANPD mockado
├── dataset_exemplo.csv            # Dataset de treino anonimizado
├── model_metadata.json            # Metadados do modelo (AIBOM-style)
├── expected_output.json           # Output esperado para validação
└── sample_data/
    ├── model_report.pdf           # Relatório técnico do modelo
    └── legislative_changes.json   # Mudanças legislativas de teste
```

> ✅ Use os arquivos em `/examples` para reproduzir um cenário completo sem criar dados do zero.

---

## ⚙️ Configuração .env Funcional (Valores Mínimos)

```env
# .env (desenvolvimento)
ENV=development
DATABASE_URL=postgresql://fellow:fellow_pass@localhost:5432/fellow_db
REDIS_URL=redis://localhost:6379/0
OPA_URL=http://localhost:8181
JWT_SECRET=dev_secret_key_change_in_prod
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# MIG/QILIS
VECTOR_DIM=512
RELEVANCE_THRESHOLD=0.15
KB_TTL_HOURS=24

# Defesa adversarial
DEFENSE_SLA_MS=300000
FALLBACK_ENABLED=true

# Multi-tenancy
DEFAULT_QUOTA_CPU=2.0
DEFAULT_QUOTA_MEM_GB=4.0
RLS_ENABLED=true

# Logging
LOG_LEVEL=INFO
STRUCTLOG_ENABLED=true
```

> ✅ Copie `.env.example` e preencha com os valores acima para execução imediata.

---

## 🌱 Bootstrap de Tenant/User (Seed Automático)

```bash
# Executar seed inicial (cria tenant demo + admin)
cd backend
python scripts/seed.py
```

**O que o seed cria:**
```python
{
  "tenant": {
    "id": "demo_tenant",
    "name": "Demo Organization",
    "plan": "trial",
    "quota": {"cpu": 2.0, "mem_gb": 4.0}
  },
  "user": {
    "email": "admin@demo.com",
    "role": "ADMIN",
    "tenant_id": "demo_tenant"
  },
  "policy": {
    "bundle_id": "msb21_baseline_v1",
    "deployed": true
  }
}
```

> ✅ Sistema sobe já utilizável, sem necessidade de cadastro manual.

---

## ✅ Definição de Plataforma Pronta ("Done")

A plataforma é considerada **pronta para uso** quando todos os critérios abaixo forem atendidos:

- [ ] ✅ Executa pipeline completo (diagnóstico → policy → audit) sem intervenção manual
- [ ] ✅ Gera Forensic ZIP válido (assinatura GPG verificável)
- [ ] ✅ OPA bloqueia ação inválida (teste: `allow=false` → deploy negado)
- [ ] ✅ Evidence Graph renderiza dados reais (react-flow com nós interativos)
- [ ] ✅ Defesa adversarial executa em ≤300s (timer enforcement)
- [ ] ✅ Multi-tenant funcional com RBAC (usuário A não acessa dados de B)
- [ ] ✅ Hash chain íntegra (quebra proposital → LOCK_SESSION)

> ✅ Execute `./scripts/ready_check.sh` para validar todos os critérios automaticamente.

---

## 🎯 Demo Rápida (5 minutos)

```bash
# Modo demo pré-carregado (sem input manual)
make demo

# O que acontece:
# 1. Pipeline "demo_run_001" é carregado com dados pré-processados
# 2. Evidências já geradas (MIG vectors, OPA decisions, audit trails)
# 3. UI navega automaticamente pelos componentes principais
# 4. Ao final, exibe Forensic ZIP pronto para download

# Para parar:
Ctrl+C
```

> ✅ Ideal para apresentação a stakeholders sem necessidade de configuração prévia.

---

## 🔗 Validação de Integração Real (e2e_test.sh)

```bash
# Executar teste end-to-end crítico
./scripts/e2e_test.sh

# O teste valida:
# 1. Input entra via API (/api/v1/diagnostic/start)
# 2. Agentes rodam (A13 → A10 → A08 → A09)
# 3. OPA avalia policy (allow/deny com trace)
# 4. Audit trail grava hash chain (append-only)
# 5. Output sai (JSON + ZIP + Rego bundle)

# Saída esperada:
✅ Pipeline completo: PASS
✅ Forensic ZIP válido: PASS
✅ OPA gatekeeping: PASS
✅ Hash chain íntegra: PASS
✅ Tempo total: 287s (<300s SLA): PASS
```

> ✅ Prova que todos os componentes funcionam juntos em produção real.

---

## 🧪 Testes

```bash
# Backend
cd backend
pytest tests/unit/ -v
pytest tests/integration/ -v --cov=app

# Frontend
cd frontend
pnpm test          # Jest (unitários)
pnpm test:e2e      # Playwright (end-to-end)

# Políticas OPA
opa test policies/ -v

# Smoke test do pipeline completo
./scripts/smoke_test.sh  # Valida fluxo upload→análise→defesa→ZIP

# Teste de prontidão (ready check)
./scripts/ready_check.sh  # Valida critérios "Done"
```

---

## 🤝 Contribuindo

```
1. Fork o projeto
2. Crie uma branch de feature: git checkout -b feature/minha-feature
3. Commit semântico: git commit -m 'feat: add MSB-21 #15 validation'
4. Push: git push origin feature/minha-feature
5. Abra um Pull Request com: descrição do problema, solução, testes adicionados, impacto nos 6 invariantes

✅ PRs devem incluir: problema, solução, testes, impacto em invariants, screenshots (se frontend)
```

---

## 📄 Licença

Distribuído sob a Licença MIT. Consulte `LICENSE` para mais informações.

---

## 🔗 Links Úteis

🔗 [agentscope](https://github.com/modelscope/agentscope) – Framework de orquestração de agentes  
🔗 [Open Policy Agent](https://github.com/open-policy-agent/opa) – Motor universal de políticas  
🔗 [pgvector](https://github.com/pgvector/pgvector) – Similaridade vetorial no PostgreSQL  
🔗 [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) – Framework de gestão de risco de IA  
🔗 [PL 2338/2023](https://www.camara.leg.br/proposicoesWeb/fichadetramitacao?idProposicao=23382023) – Marco Legal Brasileiro de IA  

> 💡 **Nota sobre Propriedade Intelectual**:  
> A FellowGovernance é uma implementação proprietária que segue os princípios científicos de vanguarda estabelecidos na literatura técnica de John M. Willis (QILIS, AGCP, RGA). Utilizamos a matemática pública (espaços vetoriais, hash chains, policy-as-code) e citamos a inspiração acadêmica, sem distribuir software de terceiros. Nosso valor agregado está na aplicação prática para o contexto jurídico brasileiro (PL 2338, ANPD, STJ) e na interface acessível para advogados e gestores.

> 📌 **Nota sobre Nomenclatura**:  
> A FellowGovernance utiliza nomes comerciais (MIG, PCG) para componentes inspirados em frameworks acadêmicos (QILIS, AGCP – Willis, 2026). Esta padronização protege a propriedade intelectual da plataforma enquanto mantém transparência sobre as bases científicas.

---

## 🎓 Cursos e Certificações Recomendadas

| Nome / Tema | Plataforma | Tipo | Por que vale a pena |
|-------------|------------|------|-------------------|
| OPA Policy Authoring (Rego) | Styra Academy | Gratuito + certificado | Domínio de policy-as-code em nível de código |
| Responsible AI Principles | Microsoft Learn | Gratuito | Base ética e de governança para IA |
| Governança de Dados | ENAP - Escola Virtual Gov | Gratuito | Base sólida em governança no setor público |
| LGPD Fundamentos | ENAP - Escola Virtual Gov | Gratuito | Compliance e portas para órgãos públicos |
| AI Governance for Responsible Innovation | edX (Univ. Edinburgh) | Audit free | Visão internacional de governança de IA |

---

> **Desenvolvido por Fabio Everton** 🇧🇷  
> [LinkedIn](https://www.linkedin.com/in/fabio-everton-3b62b1129/) • [GitHub](https://github.com/FABIOEVERTON/Fellow_Reg)  
> *Projeto hands-on de aprendizado técnico, construído para demonstrar profundidade em: sistemas distribuídos, governança de IA, policy-as-code e engenharia de software enterprise.*
```