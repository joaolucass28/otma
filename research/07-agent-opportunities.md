# 07 — Estudo de Oportunidades de Agentes na OTMA
**Data:** 2026-04-24  
**Escopo:** `clients/otma/`  
**Objetivo:** Identificar onde faz sentido implementar um agente principal e subagentes para acelerar execução, reduzir overhead operacional e manter consistência de entrega.

---

## Resumo Executivo

A OTMA já possui uma base inicial de automação em `agents/` (conteúdo, sync e monitoramento de marca), mas os maiores gargalos atuais estão em áreas críticas ainda sem automação efetiva: **ofertas**, **planejamento de site**, **resolução de pendências estratégicas `[→ VALIDAR]`** e **organização do banco de skills**.

A recomendação é adotar um modelo de **1 agente supervisor + subagentes especializados por domínio**, com limites claros de autonomia, checkpoint de execução e aprovação humana para decisões estratégicas.

---

## Metodologia de Análise

Mapeamento realizado sobre a estrutura e arquivos ativos da pasta OTMA:

- `OTMA-OS.md`
- `agents/*`
- `brand/brand-foundation.md`
- `operations/*`
- `content/*`
- `research/*`
- `offers/offers-v1.md`
- `website/site-plan.md`
- `tools/skills/*`

Critérios usados para priorização:

1. Impacto direto na operação e no go-to-market
2. Frequência de execução da tarefa
3. Grau de padronização possível
4. Risco de erro quando executado manualmente
5. Dependência de decisão humana

---

## Diagnóstico Atual

### Ativos já operando

- **Content Authority Engine** (`agents/content-engine.md`)
- **Weekly Sync Prep** (`agents/sync-prep.md`)
- **Brand Monitoring** (`agents/brand-monitor.md`)
- **Skill Crystallizer Hook** (`tools/skill-crystallizer-hook.sh`)

### Lacunas críticas identificadas

1. `offers/offers-v1.md` está vazio (oferta ainda não operacionalizada em documento de venda)
2. `website/site-plan.md` está vazio (site bloqueado por falta de plano estruturado)
3. Há múltiplas pendências `[→ VALIDAR]` em `brand/brand-foundation.md`
4. `tools/skills/` possui alto volume (milhares de arquivos), sem camada clara de indexação/curadoria

---

## Arquitetura Recomendada

### Modelo

**Supervisor Pattern (single autonomous agent + subagentes especialistas)**

- Um agente principal recebe objetivo, decompõe tarefa e delega
- Subagentes atuam com escopo restrito por pasta/domínio
- Supervisor consolida saída e pede aprovação humana nos pontos críticos

### Guardrails obrigatórios

- `max_steps` por execução
- timeout por run
- limite de custo por execução
- logs de ações e decisões
- checkpoint por etapa concluída
- aprovação humana para mudanças estratégicas (brand/offers/website)

---

## Oportunidades de Agentes e Subagentes

## 1) OTMA Supervisor Agent
**Tipo:** agente principal  
**Objetivo:** Orquestrar backlog operacional da OTMA e delegar para subagentes certos.

**Inputs típicos:**
- objetivo da semana/mês
- prioridades dos sócios
- estado dos documentos em `clients/otma/`

**Outputs:**
- plano de execução por domínio
- tarefas delegadas com status consolidado
- relatório semanal de bloqueios e decisões pendentes

---

## 2) Brand Validation Agent (`brand/`)
**Objetivo:** Transformar flags `[→ VALIDAR]` em decisões acionáveis.

**O que faz:**
- varre `brand/` por flags abertas
- agrupa por tema (público, posicionamento, pricing, tom, visual)
- converte pendências em pauta de decisão objetiva
- propõe opções A/B para decisão dos sócios

**Impacto esperado:**
- acelera fechamento do Brand Foundation
- reduz ambiguidade antes de conteúdo/site/ofertas

---

## 3) Offers Architect Agent (`offers/`)
**Objetivo:** Estruturar e manter `offers/offers-v1.md` como documento comercial vivo.

**O que faz:**
- transforma lógica de tiers em oferta vendável
- propõe faixa de preço, prazo e escopo por tier
- define critérios de entrada e desqualificação
- prepara versão pronta para proposta e call comercial

**Impacto esperado:**
- desbloqueia narrativa comercial
- reduz atrito em fechamento

---

## 4) Website Planner Agent (`website/`)
**Objetivo:** Preencher `website/site-plan.md` com base em OS + Brand + Offers.

**O que faz:**
- converte posicionamento em arquitetura de páginas
- define mensagens-chave por seção
- garante alinhamento com tom OTMA (direto, específico, sem overhead)
- marca dependências de conteúdo ainda não decidido

**Impacto esperado:**
- acelera construção de site sem rework estratégico

---

## 5) Pipeline Qualifier Agent (`operations/`)
**Objetivo:** Operacionalizar o Framework 1 (Filtro de Entrada) para leads.

**O que faz:**
- aplica perguntas de qualificação
- classifica lead: avançar / desqualificar / risco
- registra justificativa e próximos passos

**Impacto esperado:**
- melhora qualidade de pipeline
- evita avanço com leads sem fit

---

## 6) Sync Decision Agent (`operations/syncs/`)
**Objetivo:** Evoluir o pré-sync para foco em decisão executiva semanal.

**O que faz:**
- lê último sync + flags abertas
- prioriza uma decisão crítica da semana
- entrega framing de decisão binária (A/B), com impacto de cada opção

**Impacto esperado:**
- reuniões mais curtas e decisivas
- menor acúmulo de pendências

---

## 7) Content Repurposer Agent (`content/`)
**Objetivo:** Transformar pesquisa e operação em conteúdo autoral sem ensinar método.

**O que faz:**
- converte `research/*` e sinais de operações em drafts de post
- mantém rotação de framework/hook
- preserva regra OTMA: autoridade por especificidade, não por tutorial

**Impacto esperado:**
- maior consistência de presença
- aproveitamento de ativos já produzidos

---

## 8) Skill Registry Agent (`tools/skills/`)
**Objetivo:** Curar e indexar o banco de skills para reutilização real.

**O que faz:**
- detecta duplicidades e variações próximas
- classifica skills por domínio e utilidade prática
- cria índice navegável com recomendação de uso

**Impacto esperado:**
- reduz redundância
- melhora velocidade de execução por reuso

---

## Priorização Recomendada (ordem de implementação)

1. **Offers Architect Agent** (desbloqueio comercial imediato)
2. **Website Planner Agent** (desbloqueio de presença e narrativa externa)
3. **Brand Validation Agent** (fechamento de decisões estratégicas)
4. **OTMA Supervisor Agent** (orquestração e escala operacional)
5. **Pipeline Qualifier Agent** (qualidade de aquisição)
6. **Sync Decision Agent** (cadência decisória)
7. **Content Repurposer Agent** (escala de autoridade)
8. **Skill Registry Agent** (eficiência de longo prazo)

---

## Métricas de Sucesso

- Tempo para fechar documento de ofertas
- Tempo para publicar plano de site final
- Número de flags `[→ VALIDAR]` fechadas por semana
- Taxa de leads qualificados com fit
- Tempo médio de decisão no sync semanal
- Percentual de reuso de skills existentes

---

## Riscos e Mitigações

### Risco 1 — Agente com escopo amplo demais
**Mitigação:** subagentes por domínio + toolset restrito por função.

### Risco 2 — Decisões estratégicas automatizadas sem validação
**Mitigação:** aprovação humana obrigatória em brand/offers/website.

### Risco 3 — Execução longa e instável
**Mitigação:** checkpoint por etapa + retomada por thread + limite de passos.

### Risco 4 — Crescimento de custo por contexto
**Mitigação:** resumo de contexto, limite de tokens e limite de custo por run.

---

## Conclusão

O maior ganho para a OTMA não está em criar “mais um agente de conteúdo”, e sim em automatizar os pontos que hoje bloqueiam crescimento: **oferta, site e decisões estratégicas pendentes**.  

Com a arquitetura de **supervisor + subagentes especializados**, a OTMA mantém autonomia com controle, acelera execução e reduz risco operacional.
