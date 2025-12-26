<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# Agente Escritor de Colunas (Column Writer Agent)

Um sistema inteligente de escrita de colunas construído com base no framework [HelloAgents](https://github.com/helloagents/hello-agents), adotando múltiplos agentes/padrões de design, completando automaticamente o planejamento, redação, revisão e otimização de colunas.
![Captura de tela de execução](./assets/agent_run.jpg)

## ▸ Introdução ao Projeto

Este agente simula uma equipe profissional de criadores, incluindo:
- **Especialista em Planejamento**: Responsável pelo design de alto nível e planejamento de conteúdo
- **Especialista em Redação**: Responsável pela redação de conteúdo específico e chamadas de ferramentas
- **Especialista em Revisão**: Responsável pelo controle de qualidade de conteúdo e feedback

Suporta geração recursiva em árvore de índice de colunas, podendo criar colunas técnicas longas com estrutura rigorosa e conteúdo detalhado.

## ▸ Recursos Principais

1.  **Planejamento e Decomposição Inteligente**:
    *   Utiliza o padrão Plan-and-Solve, decompõe automaticamente um tema amplo (como "Programação Assíncrona Python") em um esboço completo contendo múltiplos subtópicos e capítulos.
    *   Suporta expansão recursiva em múltiplos níveis, gerando conteúdo em profundidade.

2.  **Escrita Inteligente Multi-Padrão**:
    *   **Padrão ReAct**: Combina raciocínio e ação, chama ativamente ferramentas de busca durante o processo de escrita para obter informações mais recentes.
    *   **Padrão Reflection**: Através do mecanismo de auto-reflexão (Self-Reflection), revisa e otimiza automaticamente após gerar o rascunho inicial.

3.  **Aprimoramento com Busca Online**:
    *   Integra Tavily/SerpApi, garantindo atualidade e precisão do conteúdo.
    *   Integra GitHub MCP, pode ler diretamente código de projetos open source como casos de exemplo.

4.  **Controle de Qualidade em Loop Fechado**:
    *   Sistema de pontuação integrado, realiza revisão multidimensional do conteúdo gerado (precisão, lógica, legibilidade).
    *   Pontuação insuficiente aciona automaticamente processo de modificação até atingir padrão de qualidade.

5.  **Cache Inteligente e Tolerância a Falhas**:
    *   Suporta cache de resultados de planejamento, evitando geração repetida.
    *   Possui mecanismo robusto de recuperação de erros, degrada automaticamente quando chamada de Agent falha, garantindo conclusão da tarefa.

## ▸ Stack Tecnológica

*   **Framework Principal**: [HelloAgents](https://github.com/helloagents/hello-agents)
*   **Agent Patterns**: Plan-and-Solve, ReAct, Reflection
*   **Tools**:
    *   MCP (Model Context Protocol)
    *   Tavily / SerpApi (Search)
    *   GitHub API
*   **Runtime**: Python 3.10+

## ▸️ Arquitetura de Módulos

O sistema é composto pelos seguintes módulos principais:

| Módulo | Arquivo | Descrição |
|------|------|------|
| **Orchestrator** | `orchestrator.py` | **Centro de Controle Principal**. Responsável por coordenar fluxo de trabalho de vários Agents, gerenciar transições de estado, combinar resultados finais. |
| **Agents** | `agents.py` | **Implementação de Agentes**. Contém classes principais como `PlannerAgent` (planejamento), `WriterAgent` (redação), `ReviewerAgent` (revisão), `RevisionAgent` (modificação), `ReflectionWriterAgent` (redação reflexiva), etc. |
| **Models** | `models.py` | **Modelagem de Dados**. Define estruturas de dados como `ContentNode` (árvore de conteúdo), `ColumnPlan` (planejamento), `ReviewResult` (resultado de revisão), etc. |
| **Tools** | `agents.py` | **Ferramentas**. Integra `SearchTool` (Tavily/SerpApi) e `MCPTool` (GitHub), conferindo aos Agents capacidade de acesso online e a repositórios de código. |
| **Prompts** | `prompts.py` | **Templates de Prompt**. Contém Prompt Templates para planejamento, redação, revisão, modificação e outros estágios. |
| **Config** | `config.py` | **Gerenciamento de Configuração**. Processa variáveis de ambiente, parâmetros de modelo, limites de revisão, etc. |
| **Utils** | `utils.py` | **Funções Utilitárias**. Contém ferramentas comuns como `JSONExtractor` (extração JSON), `parse_react_output` (análise de saída ReAct), etc. |

## ▸ Fluxo Básico (Workflow)

O fluxo de trabalho do sistema é um processo multi-estágio e recursivo:

1.  **Fase de Planejamento (Planning)**
    *   Usuário insere tema da coluna.
    *   **Planner Agent** (`PlanAndSolveAgent`) analisa tema, decompõe tarefas, gera `ColumnPlan` estruturado (contém título, introdução, público-alvo, lista de subtópicos).

2.  **Fase de Redação (Writing - Recursive)**
    *   Orchestrator percorre cada subtópico no planejamento, iniciando tarefa de redação.
    *   **Writer Agent** (`ReActAgent` ou `ReflectionAgent`) responsável por gerar conteúdo.
    *   **Expansão Recursiva**:
        *   **Level 1 (Topic)**: Gera introdução e visão geral do subtópico.
        *   **Level 2 (Section)**: Detalha em seções, fazendo elaboração aprofundada.
        *   **Level 3 (Detail)**: Complementa com casos específicos, código ou explicações detalhadas.
    *   Agent julga automaticamente se deve continuar expandindo com base no `MAX_DEPTH` configurado.

3.  **Chamada de Ferramentas (Tool Use)**
    *   Durante o processo de redação, Writer Agent pode chamar ferramentas ativamente:
        *   `web_search`: Busca dinâmicas tecnológicas mais recentes, dados estatísticos.
        *   `search_code_examples`: Busca exemplos de código.
        *   `verify_facts`: Verifica precisão de fatos.
        *   `github`: (opcional) Busca repositórios GitHub, lê código de projetos reais.

4.  **Revisão e Otimização (Review & Refine)**
    *   **Padrão ReAct + Revisão Independente**: Após geração de conteúdo, `ReviewerAgent` realiza pontuação multidimensional (qualidade de conteúdo, estrutura lógica, expressão linguística, formatação). Se pontuação abaixo do limite (padrão 75 pontos), `RevisionAgent` realiza modificações baseadas em opiniões de revisão, em loop até passar ou atingir número máximo de modificações.
    *   **Padrão Reflection**: Usa `ReflectionAgent`, Agent gera rascunho inicial e imediatamente realiza auto-reflexão (Self-Reflection) e otimiza automaticamente, em uma etapa.

5.  **Montagem e Exportação (Assembly & Export)**
    *   Expande árvore de conteúdo recursiva (Content Tree) gerada.
    *   Gera artigo completo em formato Markdown.
    *   Gera relatório estatístico (`REPORT.md`), incluindo contagem de palavras, tempo gasto, pontuação de qualidade, etc.

## ▸ Padrões de Agente (Agent Patterns)

Este projeto aplica vários padrões de design de Agent:

### 1. Plan-and-Solve (Planejamento e Resolução)
*   **Aplicação**: `PlannerAgent`
*   **Princípio**: Decompõe tarefa complexa em lista de passos (Plan), depois executa um a um (Solve).
*   **Vantagens**: Adequado para lidar com tarefas macroscópicas que requerem raciocínio em cadeia longa, evitando confusão lógica causada por geração em uma única etapa.

### 2. ReAct (Raciocínio + Ação)
*   **Aplicação**: `WriterAgent`
*   **Princípio**: Loop executando **Reasoning (pensar)** -> **Acting (agir/chamar ferramentas)** -> **Observation (observar resultados)**.
*   **Vantagens**: Permite que Agent interaja com mundo externo (buscar, consultar banco), não dependendo apenas de dados de treinamento para escrever, garantindo atualidade e precisão do conteúdo.

### 3. Reflection (Reflexão)
*   **Aplicação**: `ReflectionWriterAgent`
*   **Princípio**: Gera conteúdo -> Auto-avaliação (Critic) -> Otimiza conteúdo (Refine).
*   **Vantagens**: Melhora significativamente qualidade do conteúdo, simula hábito humano de criação "escrever, ler uma vez e depois modificar".

### 4. Independent Review (Revisão Independente)
*   **Aplicação**: `ReviewerAgent` + `RevisionAgent`
*   **Princípio**:
    - `ReviewerAgent`: Realiza revisão multidimensional do conteúdo gerado (qualidade de conteúdo 40 pontos, estrutura lógica 30 pontos, expressão linguística 20 pontos, formatação 10 pontos), gera pontuação detalhada e sugestões de modificação.
    - `RevisionAgent`: Realiza modificações direcionadas baseadas em opiniões de revisão, mantém pontos fortes, corrige problemas.
*   **Vantagens**: Divisão profissional, padrão de revisão unificado, histórico de revisão rastreável, suporta múltiplas rodadas de modificação até atingir padrão.

## ▸️ Modelos e Ferramentas (Models & Tools)

### Suporte de Modelos
Através de configuração em `config.py`, suporta vários backends LLM:
- **Outros modelos compatíveis**: Qualquer modelo que suporta formato de interface OpenAI

### Ferramentas de Modelo
1.  **SearchTool (Busca Online)**
    *   Backends suportados: Tavily (recomendado), SerpApi, DuckDuckGo, etc.
    *   Funcionalidade: Fornece recuperação de informações em tempo real, resolve problemas de alucinação de modelos grandes e defasagem de conhecimento.
2.  **MCPTool (Model Context Protocol)**
    *   Suporta GitHub MCP Server.
    *   Funcionalidade: Permite que Agent busque diretamente repositórios GitHub, visualize conteúdo de arquivos, analise estrutura de código, adequado para escrever colunas técnicas.

## ▸ Características de Otimização (Features)

### 1. Mecanismo de Cache Inteligente (Smart Caching)
*   **Cache de Planner**: `CachedExecutor` cacheia resultado de cada passo da fase de planejamento. Se tema for o mesmo, em nova execução carrega diretamente do cache, economizando Token e tempo.
*   **Cache de Arquivo**: Resultado de planejamento (`ColumnPlan`) é persistido localmente no diretório `.cache`.

![Mecanismo de cache](./assets/feature_cache.jpg)

### 2. Análise de Saída de Modelo (Robust Parser)
*   Implementa analisador JSON aprimorado, capaz de processar vários formatos JSON não-padrão gerados por LLM (como contendo blocos de código Markdown, comentários, colchetes incompletos, etc.).
*   Suporta extração retroativa de informações válidas do histórico de conversação (`history`), prevenindo falha de toda tarefa devido a erro de formato em determinada saída.

### 3. Recuperação de Erros (Error Recovery)
*   Quando `ReActAgent` atinge número máximo de passos ou falha na execução, retorna automaticamente para `SimpleAgent`, utilizando informações históricas existentes (`history_summary`) tentando gerar resultado diretamente, garantindo que fluxo não termine diretamente.

![Análise e recuperação](./assets/feature_robust.jpg)

## ▸ Início Rápido

### 1. Instalação de Dependências
```bash
pip install -r requirements.txt
```

### 2. Configuração de Variáveis de Ambiente
Copie `env.example` para `.env` e preencha a configuração:
```env
# Configuração LLM
OPENAI_API_KEY=your_key
OPENAI_BASE_URL=...

# Configuração de busca (opcional, mas recomendado)
TAVILY_API_KEY=tvly-...
# ou
SERPAPI_API_KEY=...

# GitHub MCP (opcional)
GITHUB_PERSONAL_ACCESS_TOKEN=...
```

### 3. Execução
```bash
# Modo interativo
python main.py

# Modo linha de comando
python main.py "Programação Assíncrona Python"
```

### 4. Ver Resultados
Após conclusão da execução, resultados serão salvos no diretório `output_YYYYMMDD_HHMMSS`.

![Ver resultados de saída](./assets/feature_output.jpg)

## ▸ Informações do Autor
```
- Nome: Xinyu Liu
- Trabalho: Trip.com
- Função: Desenvolvedor Web
- Github: melxy1997
- Email: melxy#foxmail.com
```
