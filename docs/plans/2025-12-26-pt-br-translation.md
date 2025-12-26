# PT-BR Translation Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Translate the Hello-Agents tutorial from English/Chinese to Brazilian Portuguese (PT-BR).

**Architecture:** Hybrid translation approach - main chapters from English source, Extra chapters from Chinese source. Parallel subagent execution for independent chapter batches.

**Tech Stack:** Markdown files, Docsify (index.html), no build tools required.

---

## Translation Guidelines (Reference for All Tasks)

### Header Format
```html
<div align="right">
  <a href="./[English-filename].md">English</a> | <a href="./[Chinese-filename].md">中文</a> | Português
</div>
```

### Technical Terms
Keep English terms with PT-BR explanation on first use:
- **Agentes (Agents)** são sistemas que...
- **Aprendizado por Reforço (Reinforcement Learning, RL)** é...

### Images
Keep original URLs, translate captions only:
```markdown
<p>Figura 1.1 Descrição traduzida aqui</p>
```

### Code Blocks
Keep code in English, translate comments only.

---

## Task 1: Create Sidebar Navigation

**Files:**
- Create: `docs/_sidebar_pt.md`
- Reference: `docs/_sidebar_en.md`

**Step 1: Read English sidebar structure**

Read `docs/_sidebar_en.md` to understand the navigation structure.

**Step 2: Create PT-BR sidebar**

Create `docs/_sidebar_pt.md` with translated menu items:

```markdown
- [Hello-Agents](./README_PT.md)
  - [Prefácio](./Prefacio.md)

- <strong>Parte I: Fundamentos de Agentes e Modelos de Linguagem</strong>
  - [Capítulo 1 Introdução aos Agentes](./chapter1/Capitulo1-Introducao-aos-Agentes.md)
  - [Capítulo 2 História dos Agentes](./chapter2/Capitulo2-Historia-dos-Agentes.md)
  - [Capítulo 3 Fundamentos de LLMs](./chapter3/Capitulo3-Fundamentos-de-LLMs.md)

- <strong>Parte II: Construindo seu Agente LLM</strong>
  - [Capítulo 4 Paradigmas Clássicos de Agentes](./chapter4/Capitulo4-Paradigmas-Classicos-de-Agentes.md)
  - [Capítulo 5 Plataformas Low-Code](./chapter5/Capitulo5-Plataformas-Low-Code.md)
  - [Capítulo 6 Prática de Frameworks](./chapter6/Capitulo6-Pratica-de-Frameworks.md)
  - [Capítulo 7 Construindo seu Framework](./chapter7/Capitulo7-Construindo-seu-Framework.md)

- <strong>Parte III: Conhecimento Avançado</strong>
  - [Capítulo 8 Memória e Recuperação](./chapter8/Capitulo8-Memoria-e-Recuperacao.md)
  - [Capítulo 9 Engenharia de Contexto](./chapter9/Capitulo9-Engenharia-de-Contexto.md)
  - [Capítulo 10 Protocolos de Comunicação](./chapter10/Capitulo10-Protocolos-de-Comunicacao.md)
  - [Capítulo 11 Agentic-RL](./chapter11/Capitulo11-Agentic-RL.md)
  - [Capítulo 12 Avaliação de Desempenho](./chapter12/Capitulo12-Avaliacao-de-Desempenho.md)

- <strong>Parte IV: Estudos de Caso Avançados</strong>
  - [Capítulo 13 Assistente de Viagem Inteligente](./chapter13/Capitulo13-Assistente-de-Viagem.md)
  - [Capítulo 14 Agente de Pesquisa Profunda](./chapter14/Capitulo14-Pesquisa-Profunda.md)
  - [Capítulo 15 Construindo Cyber Town](./chapter15/Capitulo15-Cyber-Town.md)

- <strong>Parte V: Projeto Final e Perspectivas Futuras</strong>
  - [Capítulo 16 Projeto de Conclusão](./chapter16/Capitulo16-Projeto-de-Conclusao.md)
```

**Step 3: Commit**

```bash
git add docs/_sidebar_pt.md
git commit -m "feat: add PT-BR sidebar navigation"
```

---

## Task 2: Create PT-BR README

**Files:**
- Create: `docs/README_PT.md`
- Reference: `docs/README_EN.md`

**Step 1: Read English README**

Read `docs/README_EN.md` to understand structure.

**Step 2: Create PT-BR README**

Translate the README maintaining all badges, links, and structure. Key translations:
- "Building Agents from Scratch" → "Construindo Agentes do Zero"
- "Quick Start" → "Início Rápido"
- "Online Reading" → "Leitura Online"
- Update language toggle header to include PT-BR

**Step 3: Commit**

```bash
git add docs/README_PT.md
git commit -m "feat: add PT-BR README"
```

---

## Task 3: Create PT-BR Preface

**Files:**
- Create: `docs/Prefacio.md`
- Reference: `docs/Preface.md`

**Step 1: Translate Preface**

Read `docs/Preface.md` and translate to PT-BR. Add language toggle header.

**Step 2: Commit**

```bash
git add docs/Prefacio.md
git commit -m "feat: add PT-BR preface"
```

---

## Task 4: Translate Chapters 1-3 (Fundamentals)

**Files:**
- Create: `docs/chapter1/Capitulo1-Introducao-aos-Agentes.md`
- Create: `docs/chapter2/Capitulo2-Historia-dos-Agentes.md`
- Create: `docs/chapter3/Capitulo3-Fundamentos-de-LLMs.md`
- Reference: English versions in same directories

**Step 1: Translate Chapter 1**

Source: `docs/chapter1/Chapter1-Introduction-to-Agents.md`
- Add PT-BR language toggle header
- Translate all content
- Keep image URLs, translate captions
- Keep technical terms with PT-BR explanations

**Step 2: Translate Chapter 2**

Source: `docs/chapter2/Chapter2-History-of-Agents.md`

**Step 3: Translate Chapter 3**

Source: `docs/chapter3/Chapter3-Fundamentals-of-Large-Language-Models.md`

**Step 4: Commit**

```bash
git add docs/chapter1/Capitulo1-Introducao-aos-Agentes.md
git add docs/chapter2/Capitulo2-Historia-dos-Agentes.md
git add docs/chapter3/Capitulo3-Fundamentos-de-LLMs.md
git commit -m "feat: add PT-BR chapters 1-3 (fundamentals)"
```

---

## Task 5: Translate Chapters 4-6 (Building Agents)

**Files:**
- Create: `docs/chapter4/Capitulo4-Paradigmas-Classicos-de-Agentes.md`
- Create: `docs/chapter5/Capitulo5-Plataformas-Low-Code.md`
- Create: `docs/chapter6/Capitulo6-Pratica-de-Frameworks.md`

**Step 1: Translate Chapter 4**

Source: `docs/chapter4/Chapter4-Building-Classic-Agent-Paradigms.md`

**Step 2: Translate Chapter 5**

Source: `docs/chapter5/Chapter5-Building-Agents-with-Low-Code-Platforms.md`

**Step 3: Translate Chapter 6**

Source: `docs/chapter6/Chapter6-Framework-Development-Practice.md`

**Step 4: Commit**

```bash
git add docs/chapter4/Capitulo4-Paradigmas-Classicos-de-Agentes.md
git add docs/chapter5/Capitulo5-Plataformas-Low-Code.md
git add docs/chapter6/Capitulo6-Pratica-de-Frameworks.md
git commit -m "feat: add PT-BR chapters 4-6 (building agents)"
```

---

## Task 6: Translate Chapters 7-9 (Advanced I)

**Files:**
- Create: `docs/chapter7/Capitulo7-Construindo-seu-Framework.md`
- Create: `docs/chapter8/Capitulo8-Memoria-e-Recuperacao.md`
- Create: `docs/chapter9/Capitulo9-Engenharia-de-Contexto.md`

**Step 1: Translate Chapter 7**

Source: `docs/chapter7/Chapter7-Building-Your-Agent-Framework.md`

**Step 2: Translate Chapter 8**

Source: `docs/chapter8/Chapter8-Memory-and-Retrieval.md`

**Step 3: Translate Chapter 9**

Source: `docs/chapter9/Chapter9-Context-Engineering.md`

**Step 4: Commit**

```bash
git add docs/chapter7/Capitulo7-Construindo-seu-Framework.md
git add docs/chapter8/Capitulo8-Memoria-e-Recuperacao.md
git add docs/chapter9/Capitulo9-Engenharia-de-Contexto.md
git commit -m "feat: add PT-BR chapters 7-9 (advanced I)"
```

---

## Task 7: Translate Chapters 10-12 (Advanced II)

**Files:**
- Create: `docs/chapter10/Capitulo10-Protocolos-de-Comunicacao.md`
- Create: `docs/chapter11/Capitulo11-Agentic-RL.md`
- Create: `docs/chapter12/Capitulo12-Avaliacao-de-Desempenho.md`

**Step 1: Translate Chapter 10**

Source: `docs/chapter10/Chapter10-Agent-Communication-Protocols.md`

**Step 2: Translate Chapter 11**

Source: `docs/chapter11/Chapter11-Agentic-RL.md`

**Step 3: Translate Chapter 12**

Source: `docs/chapter12/Chapter12-Agent-Performance-Evaluation.md`

**Step 4: Commit**

```bash
git add docs/chapter10/Capitulo10-Protocolos-de-Comunicacao.md
git add docs/chapter11/Capitulo11-Agentic-RL.md
git add docs/chapter12/Capitulo12-Avaliacao-de-Desempenho.md
git commit -m "feat: add PT-BR chapters 10-12 (advanced II)"
```

---

## Task 8: Translate Chapters 13-16 (Case Studies + Graduation)

**Files:**
- Create: `docs/chapter13/Capitulo13-Assistente-de-Viagem.md`
- Create: `docs/chapter14/Capitulo14-Pesquisa-Profunda.md`
- Create: `docs/chapter15/Capitulo15-Cyber-Town.md`
- Create: `docs/chapter16/Capitulo16-Projeto-de-Conclusao.md`

**Step 1: Translate Chapter 13**

Source: `docs/chapter13/Chapter13-Intelligent-Travel-Assistant.md`

**Step 2: Translate Chapter 14**

Source: `docs/chapter14/Chapter14-Automated-Deep-Research-Agent.md`

**Step 3: Translate Chapter 15**

Source: `docs/chapter15/Chapter15-Building-Cyber-Town.md`

**Step 4: Translate Chapter 16**

Source: `docs/chapter16/Chapter16-Graduation-Project.md`

**Step 5: Commit**

```bash
git add docs/chapter13/Capitulo13-Assistente-de-Viagem.md
git add docs/chapter14/Capitulo14-Pesquisa-Profunda.md
git add docs/chapter15/Capitulo15-Cyber-Town.md
git add docs/chapter16/Capitulo16-Projeto-de-Conclusao.md
git commit -m "feat: add PT-BR chapters 13-16 (case studies)"
```

---

## Task 9: Update index.html Language Switcher

**Files:**
- Modify: `docs/index.html`

**Step 1: Add PT-BR to language mapping**

Find the `chapterMapping` object and add PT-BR mappings:

```javascript
const chapterMapping = {
    'zh2en': { /* existing */ },
    'en2zh': { /* existing */ },
    'pt2en': {
        'Prefacio.md': 'Preface.md',
        'Capitulo1-Introducao-aos-Agentes.md': 'Chapter1-Introduction-to-Agents.md',
        'Capitulo2-Historia-dos-Agentes.md': 'Chapter2-History-of-Agents.md',
        // ... all 16 chapters
    },
    'en2pt': {
        'Preface.md': 'Prefacio.md',
        'Chapter1-Introduction-to-Agents.md': 'Capitulo1-Introducao-aos-Agentes.md',
        'Chapter2-History-of-Agents.md': 'Capitulo2-Historia-dos-Agentes.md',
        // ... all 16 chapters
    }
};
```

**Step 2: Add PT-BR language button**

Add Portuguese option to the language toggle UI.

**Step 3: Commit**

```bash
git add docs/index.html
git commit -m "feat: add PT-BR language switcher support"
```

---

## Task 10: Update Root README

**Files:**
- Modify: `README.md`

**Step 1: Add PT-BR to language toggle**

Update the header to include PT-BR option:

```html
<div align="right">
  <a href="./README_EN.md">English</a> | <a href="./README_PT.md">Português</a> | 中文
</div>
```

**Step 2: Commit**

```bash
git add README.md
git commit -m "feat: add PT-BR link to root README"
```

---

## Task 11: Create Root README_PT.md

**Files:**
- Create: `README_PT.md`
- Reference: `README_EN.md`

**Step 1: Translate root README**

Full translation of `README_EN.md` to PT-BR including:
- Project description
- Table of contents with PT-BR chapter links
- Contribution guidelines
- Credits

**Step 2: Commit**

```bash
git add README_PT.md
git commit -m "feat: add PT-BR root README"
```

---

## Parallel Execution Strategy

Tasks can be executed in parallel batches:

| Batch | Tasks | Dependencies |
|-------|-------|--------------|
| **1** | Task 1, 2, 3 | None |
| **2** | Task 4, 5, 6, 7, 8 (parallel) | None |
| **3** | Task 9, 10, 11 | Batch 1-2 (for accurate links) |

---

## Verification Checklist

After all tasks complete:

- [ ] All 16 chapter files exist in `docs/chapter{N}/`
- [ ] `_sidebar_pt.md` links work correctly
- [ ] Language toggle headers present in all files
- [ ] Images display correctly (URLs unchanged)
- [ ] Code blocks preserved with English code
- [ ] Technical terms have PT-BR explanations
- [ ] index.html language switcher works
- [ ] Root README_PT.md accessible
