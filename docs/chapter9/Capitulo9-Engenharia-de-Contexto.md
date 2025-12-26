# Capítulo 9 Engenharia de Contexto

<div align="right">
  <a href="./Chapter9-Context-Engineering.md">English</a> | <a href="./第九章%20上下文工程.md">中文</a> | Português
</div>

Nos capítulos anteriores, introduzimos sistemas de memória e RAG para agentes. No entanto, para permitir que os agentes "pensem" e "ajam" de forma estável em cenários complexos reais, apenas memória e recuperação não são suficientes—precisamos de uma metodologia de engenharia para construir continuamente e sistematicamente "contexto" apropriado para o modelo. Este é o tema deste capítulo: **Engenharia de Contexto (Context Engineering)**. Ela se concentra em "como montar e otimizar o contexto de entrada de forma reutilizável, mensurável e evolutiva antes de cada chamada do modelo", melhorando assim correção, robustez e eficiência.

Para permitir que os leitores experimentem rapidamente a funcionalidade completa deste capítulo, fornecemos um pacote Python diretamente instalável. Você pode instalar a versão correspondente a este capítulo com o seguinte comando:

```bash
pip install "hello-agents[all]==0.2.8"
```

Este capítulo apresenta principalmente os conceitos centrais e práticas de engenharia de contexto, e adiciona um construtor de contexto e duas ferramentas de suporte ao framework HelloAgents:

- **ContextBuilder** (`hello_agents/context/builder.py`): Construtor de contexto que implementa o pipeline GSSC (Gather-Select-Structure-Compress), fornecendo uma interface unificada de gerenciamento de contexto
- **NoteTool** (`hello_agents/tools/builtin/note_tool.py`): Ferramenta de anotações estruturadas que suporta gerenciamento de memória persistente para agentes
- **TerminalTool** (`hello_agents/tools/builtin/terminal_tool.py`): Ferramenta de terminal que suporta operações de sistema de arquivos e recuperação de contexto just-in-time para agentes

Esses componentes juntos constituem uma solução completa de engenharia de contexto, que é fundamental para implementar gerenciamento de tarefas de longo horizonte e busca agêntica, e será introduzida detalhadamente nas seções subsequentes.

Além de instalar o framework, você também precisa configurar a API LLM no arquivo `.env`. Os exemplos deste capítulo utilizam principalmente grandes modelos de linguagem para gerenciamento de contexto e tomada de decisão inteligente.

Após concluir a configuração, você pode começar a jornada de aprendizagem deste capítulo!

## 9.1 O Que é Engenharia de Contexto

Após anos de Prompt Engineering se tornar o foco da IA aplicada, um novo termo veio à tona: **Engenharia de Contexto (Context Engineering)**. Hoje, construir sistemas com modelos de linguagem não é mais apenas sobre encontrar a redação e formulação corretas em prompts, mas sobre responder uma questão mais macro: **Que tipo de configuração de contexto é mais provável de fazer o modelo produzir o comportamento que esperamos?**

O chamado "contexto" refere-se ao conjunto de tokens incluídos ao fazer amostragem de um grande modelo de linguagem (LLM). O problema de engenharia em questão é **otimizar a utilidade desses tokens** sob as restrições inerentes do LLM, a fim de obter resultados esperados de forma estável. Para aproveitar efetivamente os LLMs, frequentemente é necessário "pensar em contexto"—isto é: em qualquer chamada, examinar o estado geral visível ao LLM e prever o comportamento que este estado pode induzir.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/9-figures/9-1.webp" alt="" width="85%"/>
  <p>Figura 9.1 Engenharia de prompt vs Engenharia de contexto</p>
</div>

Esta seção explorará a emergente engenharia de contexto e fornecerá um modelo mental refinado para construir agentes **controláveis e eficazes**.

### Engenharia de Contexto vs. Engenharia de Prompt

Como mostrado na Figura 9.1, da perspectiva dos principais fornecedores de modelo, a engenharia de contexto é a evolução natural da engenharia de prompt. A engenharia de prompt se concentra em como escrever e organizar instruções LLM para obter melhores resultados (como escrita de prompt de sistema e estratégias estruturadas); enquanto a engenharia de contexto é **como planejar e manter o "conjunto ótimo de informações (tokens)" durante o estágio de inferência**, que inclui não apenas o próprio prompt, mas também todas as outras informações que entrarão na janela de contexto.

Nos estágios iniciais da engenharia LLM, os prompts eram frequentemente o trabalho principal, porque a maioria dos casos de uso (exceto chat diário) exigia otimização de prompt ajustada para classificação de turno único ou geração de texto. Como o nome sugere, o núcleo da engenharia de prompt é "como escrever prompts eficazes", especialmente prompts de sistema. No entanto, à medida que começamos a engenheirar agentes mais fortes que trabalham em períodos de tempo mais longos e em várias rodadas de inferência, precisamos de estratégias que possam gerenciar o **estado completo do contexto**—incluindo instruções do sistema, ferramentas, MCP (Model Context Protocol), dados externos, histórico de mensagens, etc.

## 9.2 Por Que a Engenharia de Contexto é Importante

Embora os modelos estejam ficando mais rápidos e possam lidar com escalas de dados maiores, observamos que: como humanos, os LLMs vão "divagar" ou "ficar confusos" em certo ponto. Benchmarks needle-in-a-haystack revelam um fenômeno: **context rot (apodrecimento de contexto)**—à medida que o número de tokens na janela de contexto aumenta, a capacidade do modelo de recordar com precisão informações do contexto na verdade diminui.

Modelos diferentes podem ter curvas de degradação mais suaves, mas essa característica aparece em quase todos os modelos. Portanto, **o contexto deve ser visto como um recurso limitado com retornos marginais decrescentes**. Assim como humanos têm capacidade limitada de memória de trabalho, os LLMs também têm um "orçamento de atenção". Cada novo token consome parte deste orçamento, então precisamos ser mais cuidadosos sobre quais tokens devem ser fornecidos ao LLM.

### 9.2.1 A "Anatomia" do Contexto Eficaz

Sob a restrição de "orçamento de atenção limitado", o objetivo da excelente engenharia de contexto é: **maximizar a probabilidade de obter resultados esperados com o menor número possível de tokens mas com alta densidade de sinal**. Na prática, recomendamos engenharia em torno dos seguintes componentes:

- **System Prompt (Prompt de Sistema)**: Linguagem clara e direta, com hierarquia de informação em altura "adequada". Armadilhas comuns em dois extremos:
  - Codificação excessiva: Escrever lógica if-else complexa e frágil em prompts, com altos custos de manutenção a longo prazo e fragilidade.
  - Muito vago: Fornecer apenas objetivos macro e orientação generalizada, faltando **sinais específicos** para saída esperada ou assumindo "contexto compartilhado" incorreto.

- **Tools (Ferramentas)**: As ferramentas definem o contrato entre o agente e o espaço de informação/ação, e devem promover eficiência: devem retornar informações **amigáveis a tokens** enquanto incentivam comportamento eficiente do agente.

- **Few-shot Examples (Exemplos de Poucos Tiros)**: Sempre recomendamos fornecer exemplos, mas não recomendamos encher "todas as condições de fronteira" em prompts. Por favor, selecione cuidadosamente um conjunto de exemplos **diversos e típicos** que retratem diretamente "comportamento esperado". Para LLMs, **bons exemplos valem mil palavras**.

### 9.2.2 Recuperação de Contexto e Busca Agêntica

Uma definição concisa: **Agente = LLM chamando ferramentas autonomamente em um loop**. À medida que as capacidades dos modelos subjacentes aumentam, o nível de autonomia dos agentes pode ser melhorado: eles podem explorar de forma mais independente espaços de problema complexos e se recuperar de erros.

A prática de engenharia está gradualmente transitando de "recuperação única antes da inferência (recuperação de embedding)" para "**Contexto Just-in-time (JIT)**". Este último não pré-carrega mais todos os dados relevantes, mas mantém **referências leves** (caminhos de arquivo, consultas de armazenamento, URLs, etc.), carregando dinamicamente dados necessários através de ferramentas em tempo de execução. Isso permite que o modelo escreva consultas direcionadas, armazene em cache resultados necessários e analise grandes volumes de dados com comandos como `head`/`tail`—sem enfiar blocos inteiros de dados no contexto de uma vez. Seu padrão cognitivo está mais próximo dos humanos: não memorizamos todas as informações, mas usamos índices externos como sistemas de arquivos, caixas de entrada, favoritos para extrair sob demanda.

## 9.3 Prática em Hello-Agents: ContextBuilder

Esta seção detalhará a prática de engenharia de contexto no framework HelloAgents. Demonstraremos gradualmente como construir um sistema de gerenciamento de contexto de nível de produção desde a motivação de design, estruturas de dados centrais, detalhes de implementação até casos completos. A filosofia de design do ContextBuilder é "simples e eficiente", removendo complexidade desnecessária, selecionando uniformemente com base em scores de "relevância + recenticidade", conformando-se à orientação de engenharia de modularidade e manutenibilidade do Agente.

### 9.3.1 Motivação e Objetivos do Design

Antes de construir o ContextBuilder, primeiro precisamos esclarecer seus objetivos de design e valor central. Um excelente sistema de gerenciamento de contexto deve resolver os seguintes problemas chave:

1. **Entrada Unificada**: Abstrair "Gather-Select-Structure-Compress" como um pipeline reutilizável, reduzindo código de template repetitivo em implementações de Agent.

2. **Forma Estável**: Gerar um template de contexto com esqueleto fixo, facilitando depuração, testes A/B e avaliação. Adotamos uma estrutura de template secionada:
   - `[Role & Policies]`: Esclarecer o posicionamento do papel do Agente e diretrizes comportamentais
   - `[Task]`: A tarefa específica atualmente a ser concluída
   - `[State]`: O estado atual do Agente e informações de contexto
   - `[Evidence]`: Informações de evidência recuperadas de bases de conhecimento externas
   - `[Context]`: Diálogo histórico e memórias relacionadas
   - `[Output]`: Formato de saída esperado e requisitos

3. **Guardião de Orçamento**: Reter informação de alto valor tanto quanto possível dentro do orçamento de tokens, fornecendo estratégias de compressão de fallback para contextos que excedem o limite.

4. **Regras Mínimas**: Não introduzir dimensões de classificação como fonte/prioridade para evitar crescimento de complexidade.

### 9.3.2 Estruturas de Dados Centrais

A implementação do ContextBuilder depende de duas estruturas de dados centrais que definem a configuração do sistema e unidades de informação.

```python
from dataclasses import dataclass
from typing import Optional, Dict, Any
from datetime import datetime

@dataclass
class ContextPacket:
    """Pacote de informação candidata

    Attributes:
        content: Conteúdo da informação
        timestamp: Timestamp
        token_count: Contagem de tokens
        relevance_score: Score de relevância (0.0-1.0)
        metadata: Metadados opcionais
    """
    content: str
    timestamp: datetime
    token_count: int
    relevance_score: float = 0.5
    metadata: Optional[Dict[str, Any]] = None

    def __post_init__(self):
        """Processamento pós-inicialização"""
        if self.metadata is None:
            self.metadata = {}
        # Garantir que o score de relevância está dentro do intervalo válido
        self.relevance_score = max(0.0, min(1.0, self.relevance_score))

@dataclass
class ContextConfig:
    """Configuração de construção de contexto

    Attributes:
        max_tokens: Contagem máxima de tokens
        reserve_ratio: Proporção reservada para instruções do sistema (0.0-1.0)
        min_relevance: Limiar mínimo de relevância
        enable_compression: Se habilitar compressão
        recency_weight: Peso de recenticidade (0.0-1.0)
        relevance_weight: Peso de relevância (0.0-1.0)
    """
    max_tokens: int = 3000
    reserve_ratio: float = 0.2
    min_relevance: float = 0.1
    enable_compression: bool = True
    recency_weight: float = 0.3
    relevance_weight: float = 0.7

    def __post_init__(self):
        """Validar parâmetros de configuração"""
        assert 0.0 <= self.reserve_ratio <= 1.0
        assert 0.0 <= self.min_relevance <= 1.0
        assert abs(self.recency_weight + self.relevance_weight - 1.0) < 1e-6
```

### 9.3.3 Pipeline GSSC Detalhado

O núcleo do ContextBuilder é o pipeline GSSC (Gather-Select-Structure-Compress), que decompõe o processo de construção de contexto em quatro estágios claros.

**(1) Gather: Coleta de Informação Multi-fonte**

O primeiro estágio é coletar informação candidata de múltiplas fontes. A chave para este estágio é tolerância a falhas e flexibilidade.

**(2) Select: Seleção Inteligente de Informação**

O segundo estágio é pontuar e selecionar informação candidata com base em relevância e recenticidade. Este é o núcleo de todo o pipeline e determina diretamente a qualidade do contexto final.

```python
def _select(
    self,
    packets: List[ContextPacket],
    user_query: str,
    available_tokens: int
) -> List[ContextPacket]:
    """Selecionar os pacotes de informação mais relevantes"""
    # Separar instruções do sistema e outras informações
    system_packets = [p for p in packets if p.metadata.get("type") == "system_instruction"]
    other_packets = [p for p in packets if p.metadata.get("type") != "system_instruction"]

    # Calcular tokens ocupados por instruções do sistema
    system_tokens = sum(p.token_count for p in system_packets)
    remaining_tokens = available_tokens - system_tokens

    # Calcular scores abrangentes
    scored_packets = []
    for packet in other_packets:
        relevance = self._calculate_relevance(packet.content, user_query)
        recency = self._calculate_recency(packet.timestamp)

        combined_score = (
            self.config.relevance_weight * relevance +
            self.config.recency_weight * recency
        )

        if relevance >= self.config.min_relevance:
            scored_packets.append((combined_score, packet))

    # Ordenar por score em ordem decrescente
    scored_packets.sort(key=lambda x: x[0], reverse=True)

    # Seleção gananciosa: preencher de score alto para baixo até atingir limite de tokens
    selected = system_packets.copy()
    current_tokens = system_tokens

    for score, packet in scored_packets:
        if current_tokens + packet.token_count <= available_tokens:
            selected.append(packet)
            current_tokens += packet.token_count

    return selected
```

**(3) Structure: Saída Estruturada**

O terceiro estágio é organizar informação selecionada em um template de contexto estruturado.

**(4) Compress: Compressão de Fallback**

O quarto estágio é comprimir contextos que excedem o limite.

### 9.3.4 Exemplo de Uso Completo

```python
from hello_agents.context import ContextBuilder, ContextConfig
from hello_agents.tools import MemoryTool, RAGTool

# Criar ContextBuilder
config = ContextConfig(
    max_tokens=3000,
    reserve_ratio=0.2,
    min_relevance=0.2,
    enable_compression=True
)

builder = ContextBuilder(
    memory_tool=MemoryTool(user_id="user123"),
    rag_tool=RAGTool(knowledge_base_path="./knowledge_base"),
    config=config
)

# Construir contexto
context = builder.build(
    user_query="Como otimizar o uso de memória do Pandas?",
    conversation_history=conversation_history,
    system_instructions="Você é um consultor sênior de engenharia de dados Python."
)
```

## 9.4 NoteTool: Anotações Estruturadas

O NoteTool é um componente de memória externa estruturada fornecido para "tarefas de longo horizonte". Ele usa arquivos Markdown como portadores, com YAML front matter no cabeçalho para registrar informações chave, e o corpo para registrar status, conclusões, bloqueadores e itens de ação.

### 9.4.1 Filosofia de Design e Cenários de Aplicação

O NoteTool preenche a lacuna entre memória conversacional e gerenciamento de projeto, fornecendo:

- **Gravação Estruturada**: Usa formato Markdown + YAML, adequado tanto para análise por máquina quanto leitura e edição humana
- **Versão Amigável**: Formato de texto simples, suporta naturalmente sistemas de controle de versão como Git
- **Baixa Sobrecarga**: Nenhuma operação complexa de banco de dados necessária, adequado para rastreamento de estado leve
- **Categorização Flexível**: Organiza anotações flexivelmente através de `type` e `tags`, suportando recuperação multidimensional

### 9.4.2 Formato de Armazenamento Detalhado

Cada anotação é um arquivo `.md` independente com o seguinte formato:

```markdown
---
id: note_20250119_153000_0
title: Progresso do Projeto - Fase 1
type: task_state
tags: [refatoração, fase1, backend]
created_at: 2025-01-19T15:30:00
updated_at: 2025-01-19T15:30:00
---

# Progresso do Projeto - Fase 1

## Status de Conclusão

Refatoração completa da camada de modelo de dados, principais mudanças incluem:

1. Convenções unificadas de nomenclatura de classe de entidade
2. Introduzidas type hints para melhorar manutenibilidade do código
3. Desempenho otimizado de consulta ao banco de dados

## Cobertura de Teste

- Cobertura de teste unitário: 85%
- Cobertura de teste de integração: 70%

## Próximos Passos

1. Refatorar camada de lógica de negócio
2. Resolver problemas de conflito de dependência
3. Aumentar cobertura de teste de integração para 85%
```

## 9.5 TerminalTool: Acesso Instantâneo ao Sistema de Arquivos

O TerminalTool fornece aos agentes **capacidade de execução segura de linha de comando**, suportando comandos comuns de sistema de arquivos e processamento de texto, enquanto garante segurança do sistema através de mecanismos de segurança de múltiplas camadas.

### 9.5.1 Mecanismos de Segurança

**Primeira Camada: Whitelist de Comandos**

Apenas permite comandos seguros de somente leitura, proíbe completamente quaisquer operações que possam modificar o sistema:

```python
ALLOWED_COMMANDS = {
    'ls', 'dir', 'tree',
    'cat', 'head', 'tail', 'less', 'more',
    'find', 'grep', 'egrep', 'fgrep',
    'wc', 'sort', 'uniq', 'cut', 'awk', 'sed',
    'pwd', 'cd',
    'file', 'stat', 'du', 'df',
    'echo', 'which', 'whereis',
}
```

**Segunda Camada: Restrição de Diretório de Trabalho (Sandbox)**

O TerminalTool pode apenas acessar o diretório de trabalho especificado e seus subdiretórios.

**Terceira Camada: Controle de Timeout**

Cada comando tem um limite de tempo de execução para prevenir loops infinitos ou esgotamento de recursos.

**Quarta Camada: Limite de Tamanho de Saída**

Limita o tamanho da saída do comando para prevenir overflow de memória.

### 9.5.2 Padrões de Uso Típicos

```python
from hello_agents.tools import TerminalTool

terminal = TerminalTool(workspace="./my_project")

# Exploração navegacional
print(terminal.run({"command": "ls -la"}))
print(terminal.run({"command": "cd src"}))
print(terminal.run({"command": "find . -name '*.py'"}))

# Análise de arquivo de dados
print(terminal.run({"command": "head -n 5 data/sales.csv"}))
print(terminal.run({"command": "wc -l data/*.csv"}))

# Análise de arquivo de log
print(terminal.run({"command": "tail -n 50 app.log | grep ERROR"}))
```

## 9.6 Agente de Longo Horizonte na Prática: Assistente de Manutenção de Codebase

Agora, vamos integrar ContextBuilder, NoteTool e TerminalTool para construir um agente completo de longo horizonte—**Assistente de Manutenção de Codebase**.

```python
from hello_agents import SimpleAgent, HelloAgentsLLM
from hello_agents.context import ContextBuilder, ContextConfig
from hello_agents.tools import MemoryTool, NoteTool, TerminalTool

class CodebaseMaintainer:
    """Assistente de Manutenção de Codebase - exemplo de agente de longo horizonte"""

    def __init__(self, project_name: str, codebase_path: str):
        self.project_name = project_name

        # Inicializar ferramentas
        self.memory_tool = MemoryTool(user_id=project_name)
        self.note_tool = NoteTool(workspace=f"./{project_name}_notes")
        self.terminal_tool = TerminalTool(workspace=codebase_path)

        # Inicializar construtor de contexto
        self.context_builder = ContextBuilder(
            memory_tool=self.memory_tool,
            config=ContextConfig(max_tokens=4000)
        )

    def run(self, user_input: str, mode: str = "auto") -> str:
        """Executar assistente"""
        # Recuperar anotações relevantes
        relevant_notes = self._retrieve_relevant_notes(user_input)

        # Construir contexto otimizado
        context = self.context_builder.build(
            user_query=user_input,
            custom_packets=relevant_notes
        )

        # Chamar LLM
        response = self.llm.invoke(context)

        # Pós-processamento e gerenciamento de anotações
        self._postprocess_response(user_input, response)

        return response
```

## 9.7 Resumo do Capítulo

Neste capítulo, exploramos profundamente as bases teóricas e práticas de engenharia da engenharia de contexto:

### Nível Teórico

1. **Essência da Engenharia de Contexto**: Evolução de "engenharia de prompt" para "engenharia de contexto", o núcleo é gerenciar orçamento de atenção limitado
2. **Context Rot**: Entender degradação de desempenho trazida por contextos longos, reconhecer contexto como um recurso escasso
3. **Três Grandes Estratégias**: Compactação, anotações estruturadas, arquiteturas de sub-agente

### Prática de Engenharia

1. **ContextBuilder**: Implementa pipeline GSSC, fornece interface de gerenciamento de contexto unificada
2. **NoteTool**: Formato híbrido de Markdown+YAML, suporta memória estruturada de longo prazo
3. **TerminalTool**: Ferramenta de linha de comando segura, suporta acesso instantâneo ao sistema de arquivos
4. **Agente de Longo Horizonte**: Integra três grandes ferramentas, constrói assistente de manutenção de codebase entre sessões

### Conclusões Centrais

- **Design em Camadas**: Acesso instantâneo (TerminalTool) + memória de sessão (MemoryTool) + anotações persistentes (NoteTool)
- **Filtragem Inteligente**: Mecanismo de pontuação baseado em relevância e recenticidade
- **Segurança em Primeiro Lugar**: Mecanismos de segurança de múltiplas camadas garantem estabilidade do sistema
- **Colaboração Humano-Máquina**: Equilíbrio entre automação e controlabilidade

Através do aprendizado deste capítulo, você não apenas dominou as tecnologias centrais de engenharia de contexto, mas mais importante, entendeu como construir sistemas de agente que podem manter coerência e eficácia em longos períodos de tempo. Essas habilidades se tornarão uma base importante para você construir aplicações de agente de nível de produção.

## Exercícios

1. Analise a diferença entre engenharia de contexto e engenharia de prompt. Por que ainda precisamos gerenciar cuidadosamente o contexto mesmo quando os modelos suportam janelas de contexto de 100K ou mesmo 200K?

2. Baseado no código na Seção 9.3.4, adicione uma função de "avaliação de qualidade de contexto" ao ContextBuilder: Após cada construção de contexto, avalie automaticamente a densidade de informação, relevância e completude do contexto, e forneça sugestões de otimização.

3. Projete um mecanismo de "organização automática de anotações": Quando anotações temporárias se acumularem até certo número, o agente pode analisar automaticamente essas anotações, promover informação importante para anotações de tarefa ou projeto, e limpar conteúdo redundante.

4. Combine NoteTool e TerminalTool, projete um "assistente inteligente de refatoração de código": Pode analisar estrutura de codebase, registrar planos de refatoração, executar operações de refatoração passo a passo, e rastrear progresso e problemas encontrados em anotações.

## Referências

[1] Anthropic. Effective Context Engineering for AI Agents. `https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents`

[2] David Kim. Context-Engineering (GitHub). `https://github.com/davidkimai/Context-Engineering`
