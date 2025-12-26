<div align="right">
  <a href="./Chapter14-Automated-Deep-Research-Agent.md">English</a> | <a href="./第十四章%20自动化深度研究智能体.md">中文</a> | Português
</div>

# Capítulo 14: Agente de Pesquisa Profunda Automatizada

No projeto de assistente de viagem do Capítulo 13, experimentamos como aplicar o HelloAgents a um produto multi-agente. Neste capítulo, continuamos avançando, focando em **aplicações intensivas em conhecimento**: **construindo um assistente agente que pode executar automaticamente tarefas de pesquisa profunda (Deep Research)**.

Comparado ao planejamento de viagens, a dificuldade da pesquisa profunda reside na divergência contínua de informações, rápidas atualizações de fatos e altos requisitos dos usuários para fontes de citação. Para entregar relatórios de pesquisa confiáveis, precisamos equipar os agentes com três capacidades principais:

**(1) Análise de Problemas**: Decompor tópicos abertos dos usuários em declarações de consulta recuperáveis.

**(2) Coleta de Informações Multi-Rodada**: Minerar continuamente materiais combinando diferentes APIs de pesquisa e desduplicar e integrar.

**(3) Reflexão e Resumo**: Identificar lacunas de conhecimento com base em resultados de estágio, decidir se continuar a recuperação e gerar resumos estruturados.

## 14.1 Visão Geral do Projeto e Design de Arquitetura

### 14.1.1 Por Que Precisamos de um Assistente de Pesquisa Profunda

Na era da explosão de informações, precisamos entender rapidamente novas tecnologias, conceitos ou eventos todos os dias. Os métodos tradicionais de pesquisa têm vários pontos de dor. Primeiro é a **sobrecarga de informações**. Os mecanismos de busca retornam milhares de resultados, e você precisa clicar em links um por um e ler muito conteúdo para encontrar informações úteis. Segundo é a **falta de estrutura**. Mesmo que você encontre informações relevantes, essas informações são frequentemente fragmentadas e carecem de organização sistemática. Finalmente está o **trabalho repetitivo**. Toda vez que você pesquisa um novo tópico, precisa repetir o processo de "pesquisar → ler → resumir → organizar".

Este é o problema que o assistente de pesquisa profunda precisa resolver. Não é apenas uma ferramenta de busca, mas um assistente de pesquisa que pode planejar autonomamente, executar e resumir.

**Valor Central do Assistente de Pesquisa Profunda:**

1. **Economizar Tempo**: Comprimir 1-2 horas de trabalho de pesquisa em 5-10 minutos
2. **Melhorar Qualidade**: Processo de pesquisa sistemático para evitar perder informações importantes
3. **Rastreável**: Registrar todos os resultados de pesquisa e fontes para fácil verificação e citação
4. **Extensível**: Adicionar facilmente novos mecanismos de busca, fontes de dados e ferramentas de análise

### 14.1.2 Visão Geral da Arquitetura Técnica

Este sistema ainda adota a clássica **arquitetura de separação entre front-end e back-end**, conforme mostrado na Figura 14.1.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-1.png" alt="" width="85%"/>
  <p>Figura 14.1 Arquitetura Técnica do Assistente de Pesquisa Profunda</p>
</div>

O sistema é projetado com uma arquitetura de quatro camadas:

**Camada Front-End (Vue3+TypeScript)**: Interface de diálogo modal em tela cheia, visualização de resultados em Markdown

**Camada Back-End (FastAPI)**: Roteamento de API (`/research/stream`)

**Camada de Agente (HelloAgents)**: Três Agentes especializados (TODO Planner, Task Summarizer, Report Writer) + Duas ferramentas principais (SearchTool, NoteTool)

**Camada de Serviço Externo**: Mecanismos de busca + provedores de LLM

Vamos ver como uma solicitação completa de pesquisa flui através do sistema, conforme mostrado na Figura 14.2:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-2.png" alt="" width="85%"/>
  <p>Figura 14.2 Processo de Fluxo de Dados do Assistente de Pesquisa Profunda</p>
</div>

1. **Entrada do Usuário**: Usuário insere o tópico de pesquisa no front-end
2. **Front-End Envia**: Front-end conecta a `/research/stream` via SSE
3. **Back-End Recebe**: FastAPI recebe solicitação, cria estado de pesquisa
4. **Fase de Planejamento**: Chama agente de planejamento de pesquisa, decompõe em 3 subtarefas
5. **Fase de Execução**: Executa cada subtarefa uma por uma
   - Usar SearchTool para pesquisar
   - Chamar agente de resumo de tarefa para resumir
   - Usar NoteTool para registrar resultados
6. **Fase de Relatório**: Chamar agente de geração de relatório, integrar todos os resumos
7. **Retorno em Streaming**: Enviar progresso e resultados para o front-end via SSE
8. **Exibição Front-End**: Front-end atualiza status da tarefa, barra de progresso, logs e relatório em tempo real

A estrutura de diretórios do projeto é a seguinte:

```
helloagents-deepresearch/
├── backend/                    # Código back-end
│   ├── src/
│   │   ├── agent.py           # Coordenador central
│   │   ├── main.py            # Entrada FastAPI
│   │   ├── models.py          # Modelos de dados
│   │   ├── prompts.py         # Templates de prompt
│   │   ├── config.py          # Gerenciamento de configuração
│   │   └── services/          # Camada de serviço
│   │       ├── planner.py     # Serviço de planejamento
│   │       ├── summarizer.py  # Serviço de resumo
│   │       ├── reporter.py    # Serviço de relatório
│   │       └── search.py      # Serviço de pesquisa
│   ├── .env                   # Variáveis de ambiente
│   ├── pyproject.toml         # Gerenciamento de dependências
│   └── workspace/             # Notas de pesquisa
│
└── frontend/                   # Código front-end
    ├── src/
    │   ├── App.vue            # Componente principal
    │   ├── components/        # Componentes UI
    │   │   └── ResearchModal.vue
    │   └── composables/       # Funções composable
    │       └── useResearch.ts
    ├── package.json           # Dependências npm
    └── vite.config.ts         # Configuração de build
```

### 14.1.3 Experiência Rápida: Execute o Projeto em 5 Minutos

Antes de mergulhar nos detalhes de implementação, vamos primeiro executar o projeto para ver o resultado final. Desta forma, você terá uma compreensão intuitiva de todo o sistema.

Você pode verificar as versões com os seguintes comandos:

```bash
python --version  # Deve mostrar Python 3.10.x ou superior
node --version    # Deve mostrar v16.x.x ou superior
npm --version     # Deve mostrar 8.x.x ou superior
```

**(1) Iniciar o Back-End**

```bash
# 1. Entrar no diretório back-end
cd helloagents-deepresearch/backend

# 2. Instalar dependências
# Método 1: Usando uv (recomendado, gerenciador de pacotes Python mais rápido)
uv sync

# Método 2: Usando pip
pip install -e .

# 3. Configurar variáveis de ambiente
cp .env.example .env

# 4. Editar arquivo .env, preencher suas chaves API
# Abrir arquivo .env com seu editor favorito
# No mínimo, configurar:
# - LLM_PROVIDER (ex: openai, deepseek, qwen)
# - LLM_API_KEY (sua chave API LLM)
# - SEARCH_API (ex: duckduckgo, tavily)

# 5. Iniciar back-end
python src/main.py
```

Se tudo estiver normal, você verá uma saída semelhante a:

```
INFO:     Started server process [12345]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

**(2) Iniciar o Front-End**

Abra uma nova janela de terminal:

```bash
# 1. Entrar no diretório front-end
cd helloagents-deepresearch/frontend

# 2. Instalar dependências
npm install

# 3. Iniciar front-end
npm run dev
```

Se tudo estiver normal, você verá uma saída semelhante a:

```
  VITE v5.0.0  ready in 500 ms

  ➜  Local:   http://localhost:5174/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

**(3) Iniciar Pesquisa**

Abra seu navegador e visite `http://localhost:5174`. Você verá um cartão de entrada centralizado, conforme mostrado na Figura 14.3. Insira um tópico de pesquisa, por exemplo `Que tipo de organização é o Datawhale?`, selecione um mecanismo de busca (se vários estiverem configurados) e clique no botão "Iniciar Pesquisa".

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-3.png" alt="" width="85%"/>
  <p>Figura 14.3 Página de Pesquisa do Assistente de Pesquisa Profunda</p>
</div>

Conforme mostrado na Figura 14.4, o sistema expandirá automaticamente para tela cheia, com informações de pesquisa exibidas à esquerda e progresso e resultados da pesquisa exibidos em tempo real à direita. Todo o processo de pesquisa leva cerca de 1-3 minutos, dependendo da complexidade do tópico e da velocidade de resposta do mecanismo de busca.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-4.png" alt="" width="85%"/>
  <p>Figura 14.4 Pesquisa Expandida do Assistente de Pesquisa Profunda</p>
</div>

Após a conclusão da pesquisa, você verá:

- **Lista de Tarefas**: Mostra todas as subtarefas e seus status
- **Log de Progresso**: Mostra todas as operações durante o processo de pesquisa
- **Relatório Final**: Relatório Markdown estruturado contendo resumos de todas as subtarefas e citações de fonte

Agora você executou com sucesso o assistente de pesquisa profunda e tem uma compreensão intuitiva do sistema.

## 14.2 Paradigma de Pesquisa Orientado por TODO

### 14.2.1 O Que é Pesquisa Orientada por TODO

Os mecanismos de busca tradicionais só podem responder perguntas únicas, enquanto a pesquisa profunda precisa responder a uma série de perguntas relacionadas. O paradigma de pesquisa orientado por TODO decompõe tópicos de pesquisa complexos em múltiplas subtarefas (TODOs), executa-as uma por uma e integra os resultados.

A ideia central deste paradigma é: **Transformar a tarefa complexa de "pesquisa" em um processo de "planejamento → execução → integração"**.

Vamos entender esta transformação através de um exemplo. Suponha que você queira pesquisar "Que tipo de organização é o Datawhale?". O método de pesquisa tradicional é:

```
Entrada do usuário: Que tipo de organização é o Datawhale?
Mecanismo de busca: Retorna 10-20 links
Usuário: Clica em links um por um, lê conteúdo, faz anotações
Resultado: Informações fragmentadas, falta de sistematização
```

O problema com essa abordagem é que cada link cobre apenas um aspecto do tópico, falta estrutura sistemática e requer organização e resumo manual.

**Abordagem Orientada por TODO: Pesquisa Sistemática**

```
Entrada do usuário: Que tipo de organização é o Datawhale?

Planejamento do sistema:
  ├─ TODO 1: Informações básicas sobre o Datawhale (posicionamento organizacional)
  ├─ TODO 2: Projetos principais do Datawhale (conteúdo central)
  ├─ TODO 3: Cultura comunitária do Datawhale (valores)
  └─ TODO 4: Influência do Datawhale (contribuição social)

Execução do sistema:
  Para cada TODO:
    1. Pesquisar materiais relevantes
    2. Resumir informações-chave
    3. Registrar citações de fonte

Integração do sistema:
  Gerar relatório estruturado:
    ├─ Parte 1: Posicionamento organizacional (do TODO 1)
    ├─ Parte 2: Conteúdo central (do TODO 2)
    ├─ Parte 3: Valores (do TODO 3)
    ├─ Parte 4: Contribuição social (do TODO 4)
    └─ Referências: Todas as citações de fonte
```

As vantagens dessa abordagem são que decompõe tópicos complexos em sub-questões claras, registra resultados de pesquisa e resumos para cada subtarefa para fácil rastreamento, e o processo de pesquisa sistemático evita perder informações importantes. Também é fácil adicionar novas subtarefas ou ajustar a ordem de execução.

Um sistema completo de pesquisa orientado por TODO contém três elementos centrais:

**(1) Planejador Inteligente (TODO Planner)**: Responsável por decompor tópicos de pesquisa em subtarefas. Um bom planejador precisa entender os aspectos-chave e objetivos de pesquisa do tópico, decompor o tópico em 3-5 subtarefas (muito poucas não cobrirão tudo, muitas serão redundantes) e projetar consultas de pesquisa apropriadas para cada subtarefa.

**(2) Executor de Tarefas**: Responsável por executar cada subtarefa. O executor precisa usar mecanismos de busca para obter materiais relevantes, extrair informações-chave e remover conteúdo redundante, enquanto salva todas as citações de fonte para fácil verificação.

**(3) Escritor de Relatórios (Report Writer)**: Responsável por integrar os resultados de todas as subtarefas. O gerador precisa organizar o conteúdo em ordem lógica, mesclar informações duplicadas e adicionar citações de fonte para cada ponto de vista.

No nosso caso, o processo de pesquisa orientado por TODO é mostrado na Figura 14.5:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-5.png" alt="" width="85%"/>
  <p>Figura 14.5 Processo de Pesquisa Orientado por TODO</p>
</div>

Todo o processo é linear, mas cada estágio tem entradas e saídas claras. Este design torna o sistema fácil de entender e depurar.

### 14.2.2 Processo de Pesquisa em Três Estágios

O processo de pesquisa orientado por TODO é dividido em três estágios: Planejamento, Execução e Relatório. Cada estágio tem um Agente dedicado responsável por ele.

**(1) Estágio 1: Planejamento**

O objetivo do estágio de planejamento é decompor o tópico de pesquisa em 3-5 subtarefas. O sistema recebe o tópico de pesquisa e a data atual como entrada e produz uma lista de subtarefas em formato JSON. Cada subtarefa contém três campos: título (título da tarefa), intenção (intenção de pesquisa) e consulta (consulta de pesquisa).

O agente de planejamento de pesquisa adota diferentes estratégias de decomposição com base nas características do tópico, geralmente começando com conceitos básicos, depois entendendo o status técnico, aplicações práticas e tendências de desenvolvimento, e realizando análise comparativa quando necessário. Por exemplo, para "Que tipo de organização é o Datawhale?", o agente de planejamento pode gerar as seguintes subtarefas:

```json
[
  {
    "title": "Informações básicas sobre o Datawhale",
    "intent": "Entender o posicionamento organizacional, tempo de fundação, história de desenvolvimento do Datawhale",
    "query": "introdução organização Datawhale história 2024"
  },
  {
    "title": "Projetos principais do Datawhale",
    "intent": "Entender os principais projetos e tutoriais de código aberto do Datawhale",
    "query": "projetos tutoriais código aberto Datawhale 2024"
  },
  ...
]
```

Um bom plano deve ser abrangente, logicamente claro, ter consultas precisas e um número apropriado de itens.

**(2) Estágio 2: Execução**

O estágio de execução executa cada subtarefa uma por uma, pesquisando e resumindo materiais relevantes. O sistema recebe a lista de subtarefas e configuração do mecanismo de busca como entrada e produz um resumo (formato Markdown) e lista de citação de fonte para cada subtarefa. O processo de execução é o seguinte:

Para cada subtarefa, o executor irá:

1. **Pesquisar materiais**: Usar o mecanismo de busca configurado para executar a pesquisa

   ```python
   search_results = search_tool.run({
       "input": task.query,
       "backend": "tavily",
       "mode": "structured",
       "max_results": 5
   })
   ```

2. **Obter resultados de pesquisa**: Extrair título, URL, snippet

   ```json
   {
     "results": [
       {
         "title": "O que é um Modelo Multimodal?",
         "url": "https://example.com/multimodal-model",
         "snippet": "Um modelo multimodal é um modelo de IA que pode processar múltiplos tipos de dados..."
       },
       ...
     ]
   }
   ```

3. **Chamar agente de resumo**: Resumir resultados de pesquisa

   ```python
   summary = summarizer_agent.run(
       task=task,
       search_results=search_results
   )
   ```

4. **Registrar resumo e fontes**: Salvar no NoteTool

   ```python
   note_tool.run({
       "action": "create",
       "title": task.title,
       "content": f"## {task.title}\n\n{summary}\n\n## Sources\n{sources}",
       "tags": ["research", "summary"]
   })
   ```

O agente de resumo de tarefa extrairá pontos de vista centrais de cada resultado de pesquisa, mesclará informações semelhantes, reterá números importantes, datas, nomes e outros dados-chave, e adicionará citações de fonte para cada ponto de vista. Por exemplo, para os resultados de pesquisa de "Informações básicas sobre o Datawhale", o agente de resumo pode gerar:

```markdown
## Informações Básicas sobre o Datawhale

O Datawhale é uma organização de código aberto focada em ciência de dados e IA, fundada em 2018[1]. A missão central da organização é "para o aprendiz, crescer junto com os aprendizes", comprometida em construir uma comunidade de aprendizado pura[2].

**Posicionamento Central:**

1. **Plataforma de Educação de Código Aberto**: Fornece recursos de aprendizado de IA e ciência de dados de alta qualidade[1]
2. **Comunidade de Aprendizes**: Reúne dezenas de milhares de aprendizes e praticantes de IA[3]
3. **Compartilhamento de Conhecimento**: Defende o espírito de código aberto, todo o conteúdo é completamente gratuito e aberto[2]

**História de Desenvolvimento:**

- **2018**: Datawhale foi fundado, lançou primeiro tutorial de código aberto[1]
- **2020**: Tornou-se uma das principais comunidades de aprendizado de IA na China[3]
- **2024**: Lançou 50+ projetos de código aberto, impactando 100.000+ aprendizes[4]

## Fontes

[1] https://github.com/datawhalechina
[2] https://datawhale.club/about
[3] https://www.zhihu.com/org/datawhale
[4] https://datawhale.cn
```

Durante a execução, o sistema enviará informações de progresso para o front-end em tempo real:

```json
{
  "type": "status",
  "message": "Pesquisando: Informações básicas sobre o Datawhale"
}
```

```json
{
  "type": "status",
  "message": "Resumindo resultados de pesquisa..."
}
```

```json
{
  "type": "task",
  "task": {
    "id": 1,
    "title": "Informações básicas sobre o Datawhale",
    "status": "completed"
  }
}
```

**(3) Estágio 3: Relatório**

O objetivo do estágio de relatório é integrar os resumos de todas as subtarefas e gerar o relatório final. O sistema recebe os resumos de todas as subtarefas e o tópico de pesquisa como entrada e produz o relatório final em formato Markdown. O relatório contém cinco partes: título, visão geral, análise detalhada de cada subtarefa, resumo e referências. Por exemplo, para "Que tipo de organização é o Datawhale?", o relatório final pode ser:

```markdown
# Que Tipo de Organização é o Datawhale?

## Visão Geral

Este relatório pesquisou sistematicamente a organização de código aberto Datawhale, cobrindo quatro aspectos: informações básicas, projetos principais, cultura comunitária e influência.

## 1. Informações Básicas sobre o Datawhale

O Datawhale é uma organização de código aberto focada em ciência de dados e IA, fundada em 2018...

(Inserir resumo da subtarefa 1 aqui)

## 2. Projetos Principais do Datawhale

O Datawhale lançou múltiplos tutoriais de código aberto de alta qualidade, incluindo Hello-Agents, Joyful-Pandas, etc...

(Inserir resumo da subtarefa 2 aqui)
...
## Resumo

Através desta pesquisa, aprendemos sobre o posicionamento organizacional, projetos centrais, cultura comunitária e contribuições sociais do Datawhale. O Datawhale é uma comunidade de aprendizado pura que fez contribuições importantes para a educação em IA.

## Referências

[1] https://github.com/datawhalechina
[2] https://datawhale.club/about
...
```

O agente de geração de relatório organizará o conteúdo na ordem lógica das subtarefas, adicionará uma breve visão geral no início, mesclará informações duplicadas, unificará o formato Markdown e organizará todas as citações de fonte na seção de referências.

## 14.3 Design do Sistema de Agentes

### 14.3.1 Divisão de Responsabilidades dos Agentes

No assistente de pesquisa profunda, projetamos três Agentes especializados, cada um responsável por uma tarefa específica. Isso torna cada Agente simples, fácil de entender e manter.

No Capítulo 7, aprendemos como usar `SimpleAgent` para construir agentes. A filosofia de design do `SimpleAgent` é simples e direta: cada vez que o método `run()` é chamado, o Agente analisa a pergunta do usuário, decide se deve chamar ferramentas e então retorna o resultado. Este design é muito eficaz ao lidar com tarefas simples, mas quando enfrentamos tarefas complexas como pesquisa profunda, precisamos continuar usando uma abordagem de colaboração multi-agente.

Conforme mostrado na Tabela 14.1, os três Agentes são respectivamente responsáveis por planejamento, resumo e geração de relatório.

<div align="center">
  <p>Tabela 14.1 Divisão de Responsabilidades dos Três Agentes</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-table-1.png" alt="" width="85%"/>
</div>

Vamos introduzir o design de cada Agente em detalhes.

**Agente 1: Especialista em Planejamento de Pesquisa (TODO Planner)**

**Responsabilidade**: Decompor tópicos de pesquisa em 3-5 subtarefas

**Filosofia de Design**: A tarefa central do especialista em planejamento de pesquisa é entender o tópico de pesquisa do usuário, analisar os aspectos-chave do tópico e então gerar uma série de subtarefas. Este processo é semelhante ao estágio de "brainstorming" de pesquisadores humanos antes de iniciar a pesquisa.

**Design de Prompt**:

```python
todo_planner_instructions = """
Você é um especialista em planejamento de pesquisa. Sua tarefa é decompor o tópico de pesquisa do usuário em 3-5 subtarefas.

Data atual: {current_date}

Tópico de pesquisa: {research_topic}

Por favor, analise este tópico de pesquisa e decomponha-o em 3-5 subtarefas. Cada subtarefa deve:
1. Cobrir um aspecto importante do tópico
2. Ter um objetivo de pesquisa claro
3. Ser capaz de encontrar materiais relevantes através de mecanismos de busca

Por favor, retorne a lista de subtarefas em formato JSON, cada subtarefa contendo:
- title: Título da tarefa (conciso e claro)
- intent: Intenção da tarefa (por que pesquisar isso)
- query: Consulta de pesquisa (string de consulta para mecanismos de busca, pode usar inglês para melhores resultados de pesquisa)

Exemplo de saída:
[
  {{
    "title": "O que é um modelo multimodal",
    "intent": "Entender os conceitos básicos de modelos multimodais para estabelecer a base para pesquisa subsequente",
    "query": "multimodal model definition concept 2024"
  }},
  ...
]

Por favor, certifique-se:
1. Número de subtarefas está entre 3-5
2. Subtarefas têm relacionamentos lógicos (ex: do básico às aplicações, do status atual às tendências)
3. Consultas de pesquisa podem encontrar materiais relevantes com precisão
4. Retornar apenas JSON, não incluir outro texto
"""
```

**Pontos-Chave de Design**: O prompt inclui a data atual para obter as informações mais recentes, requer explicitamente saída em formato JSON para fácil análise, ajuda o Agente a entender a saída esperada através de exemplos e enfatiza restrições como número de subtarefas e relacionamentos lógicos.

**Código de Implementação**:

O ToolAwareSimpleAgent aqui é uma extensão do SimpleAgent. Você pode aprender sobre isso na Seção 14.3.2, não precisa se aprofundar aqui.

```python
class PlanningService:
    def __init__(self, llm: HelloAgentsLLM):
        self._agent = ToolAwareSimpleAgent(
            name="TODO Planner",
            system_prompt="Você é um especialista em planejamento de pesquisa",
            llm=llm,
            tool_call_listener=self._on_tool_call
        )

    def plan_todo_list(self, state: SummaryState) -> List[TodoItem]:
        prompt = todo_planner_instructions.format(
            current_date=get_current_date(),
            research_topic=state.research_topic,
        )

        response = self._agent.run(prompt)
        tasks_payload = self._extract_tasks(response)

        todo_items = []
        for idx, item in enumerate(tasks_payload, start=1):
            task = TodoItem(
                id=idx,
                title=item["title"],
                intent=item["intent"],
                query=item["query"],
            )
            todo_items.append(task)

        return todo_items

    def _extract_tasks(self, response: str) -> List[dict]:
        """Extrair JSON da resposta do Agente"""
        # Usar regex para extrair parte JSON
        json_match = re.search(r'\[.*\]', response, re.DOTALL)
        if json_match:
            json_str = json_match.group(0)
            return json.loads(json_str)
        else:
            raise ValueError("Não foi possível extrair JSON da resposta")
```

**Agente 2: Especialista em Resumo de Tarefas (Task Summarizer)**

**Responsabilidade**: Resumir resultados de pesquisa, extrair informações-chave

**Filosofia de Design**: A tarefa central do especialista em resumo de tarefas é ler resultados de pesquisa, extrair informações-chave e apresentá-las de forma estruturada. Este processo é semelhante a pesquisadores humanos fazendo anotações após ler literatura.

**Design de Prompt**:

```python
task_summarizer_instructions = """
Você é um especialista em resumo de tarefas. Sua tarefa é resumir resultados de pesquisa e extrair informações-chave.

Título da tarefa: {task_title}
Intenção da tarefa: {task_intent}
Consulta de pesquisa: {task_query}

Resultados de pesquisa:
{search_results}

Por favor, leia cuidadosamente os resultados de pesquisa acima, extraia informações-chave e retorne um resumo em formato Markdown.

O resumo deve incluir:
1. **Pontos de Vista Centrais**: Pontos de vista centrais e conclusões dos resultados de pesquisa
2. **Dados-Chave**: Números importantes, datas, nomes, etc.
3. **Citações de Fonte**: Adicionar citações de fonte para cada ponto de vista (usando [1], [2], etc.)

Por favor, certifique-se:
1. Resumo é conciso e claro, evitando redundância
2. Reter detalhes e dados importantes
3. Adicionar citações de fonte para cada ponto de vista
4. Usar formato Markdown (títulos, listas, negrito, etc.)

Exemplo de saída:
## Pontos de Vista Centrais

Modelos multimodais são modelos de IA que podem processar múltiplos tipos de dados[1]. Ao contrário dos modelos unimodais tradicionais, modelos multimodais podem entender simultaneamente texto, imagens, áudio, etc.[2].

**Características-Chave:**
- Compreensão cross-modal[1]
- Representação unificada[3]
- Treinamento end-to-end[2]

## Fontes

[1] https://example.com/source1
[2] https://example.com/source2
[3] https://example.com/source3
"""
```

**Pontos-Chave de Design**: O prompt inclui título da tarefa, intenção, consulta e outro contexto para ajudar o Agente a entender a tarefa, requer explicitamente que a saída inclua pontos de vista centrais, dados-chave e citações de fonte, enfatiza adicionar citações de fonte para cada ponto de vista e ajuda o Agente a entender o formato de saída esperado através de exemplos.

**Código de Implementação**:

```python
class SummarizationService:
    def __init__(self, llm: HelloAgentsLLM):
        self._agent = ToolAwareSimpleAgent(
            name="Task Summarizer",
            system_prompt="Você é um especialista em resumo de tarefas",
            llm=llm,
            tool_call_listener=self._on_tool_call
        )

    def summarize_task(
        self,
        task: TodoItem,
        search_results: List[dict]
    ) -> str:
        # Formatar resultados de pesquisa
        formatted_sources = self._format_sources(search_results)

        prompt = task_summarizer_instructions.format(
            task_title=task.title,
            task_intent=task.intent,
            task_query=task.query,
            search_results=formatted_sources,
        )

        summary = self._agent.run(prompt)
        return summary

    def _format_sources(self, search_results: List[dict]) -> str:
        """Formatar resultados de pesquisa"""
        formatted = []
        for idx, result in enumerate(search_results, start=1):
            formatted.append(
                f"[{idx}] {result['title']}\n"
                f"URL: {result['url']}\n"
                f"Snippet: {result['snippet']}\n"
            )
        return "\n".join(formatted)
```

**Agente 3: Especialista em Redação de Relatórios (Report Writer)**

**Responsabilidade**: Integrar resumos de todas as subtarefas e gerar relatório final

**Filosofia de Design**: A tarefa central do especialista em redação de relatórios é integrar os resumos de todas as subtarefas em um relatório estruturado. Este processo é semelhante a pesquisadores humanos escrevendo relatórios de pesquisa após completar todas as investigações.

**Design de Prompt**:

```python
report_writer_instructions = """
Você é um especialista em redação de relatórios. Sua tarefa é integrar os resumos de todas as subtarefas e gerar um relatório de pesquisa estruturado.

Tópico de pesquisa: {research_topic}

Resumos de subtarefas:
{task_summaries}

Por favor, integre todos os resumos de subtarefas acima e gere um relatório de pesquisa estruturado.

O relatório deve incluir:
1. **Título**: Tópico de pesquisa
2. **Visão Geral**: Introduzir brevemente o tópico de pesquisa e estrutura do relatório (2-3 parágrafos)
3. **Análise Detalhada de Cada Subtarefa**: Organizar em ordem lógica (usando títulos de nível 2)
4. **Resumo**: Resumir as principais descobertas da pesquisa (1-2 parágrafos)
5. **Referências**: Todas as citações de fonte (agrupadas por subtarefa)

Por favor, certifique-se:
1. Estrutura do relatório é clara e logicamente coerente
2. Eliminar informações duplicadas
3. Reter todas as citações de fonte
4. Usar formato Markdown

Exemplo de saída:
# Avanços Mais Recentes em Grandes Modelos Multimodais

## Visão Geral

Este relatório pesquisou sistematicamente os avanços mais recentes em grandes modelos multimodais...

## 1. O Que é um Modelo Multimodal

(Inserir resumo da subtarefa 1 aqui)

## 2. Quais são os Modelos Multimodais Mais Recentes

(Inserir resumo da subtarefa 2 aqui)

...

## Resumo

Através desta pesquisa, aprendemos sobre...

## Referências

### Tarefa 1: O Que é um Modelo Multimodal
[1] https://example.com/source1
...
"""
```

**Pontos-Chave de Design**: O prompt requer explicitamente que o relatório inclua título, visão geral, análise detalhada, resumo, referências e outras estruturas, enfatiza organizar o conteúdo em ordem lógica, requer mesclar informações duplicadas para eliminar redundância e retém todas as citações de fonte.

**Código de Implementação**:

```python
class ReportingService:
    def __init__(self, llm: HelloAgentsLLM):
        self._agent = ToolAwareSimpleAgent(
            name="Report Writer",
            system_prompt="Você é um especialista em redação de relatórios",
            llm=llm,
            tool_call_listener=self._on_tool_call
        )

    def generate_report(
        self,
        research_topic: str,
        task_summaries: List[Tuple[TodoItem, str]]
    ) -> str:
        # Formatar resumos de subtarefas
        formatted_summaries = self._format_summaries(task_summaries)

        prompt = report_writer_instructions.format(
            research_topic=research_topic,
            task_summaries=formatted_summaries,
        )

        report = self._agent.run(prompt)
        return report

    def _format_summaries(
        self,
        task_summaries: List[Tuple[TodoItem, str]]
    ) -> str:
        """Formatar resumos de subtarefas"""
        formatted = []
        for idx, (task, summary) in enumerate(task_summaries, start=1):
            formatted.append(
                f"## Tarefa {idx}: {task.title}\n"
                f"Intenção: {task.intent}\n\n"
                f"{summary}\n"
            )
        return "\n".join(formatted)
```

### 14.3.2 Design do ToolAwareSimpleAgent

No Capítulo 7, implementamos o `SimpleAgent`, que é o Agente básico do framework HelloAgents. Mas no assistente de pesquisa profunda, precisamos de um Agente que possa **registrar chamadas de ferramentas**. É aí que o `ToolAwareSimpleAgent` entra.

No assistente de pesquisa profunda, precisamos registrar o status de chamada de ferramenta de cada Agente para:

1. **Depuração**: Visualizar quais ferramentas o Agente chamou e quais parâmetros foram passados
2. **Logging**: Registrar todas as operações durante o processo de pesquisa
3. **Análise**: Analisar os padrões de comportamento do Agente
4. **Exibição de Progresso**: Mostrar em tempo real o que o Agente está fazendo

O `SimpleAgent` em si não suporta escuta de chamadas de ferramentas, então precisamos estendê-lo.

O `ToolAwareSimpleAgent` adiciona um parâmetro `tool_call_listener` em cima do `SimpleAgent`. Esta é uma função de callback que é chamada toda vez que uma ferramenta é chamada.

**Exemplo de Uso:**

```python
from hello_agents import ToolAwareSimpleAgent

def tool_listener(call_info):
    print(f"Agente: {call_info['agent_name']}")
    print(f"Ferramenta: {call_info['tool_name']}")
    print(f"Parâmetros: {call_info['parsed_parameters']}")
    print(f"Resultado: {call_info['result']}")

agent = ToolAwareSimpleAgent(
    name="Assistente de Pesquisa",
    system_prompt="Você é um assistente de pesquisa",
    llm=llm,
    tool_call_listener=tool_listener
)
```

O `ToolAwareSimpleAgent` herda do `SimpleAgent` e sobrescreve o método `_execute_tool_call`:

```python
class ToolAwareSimpleAgent(SimpleAgent):
    def __init__(
        self,
        name: str,
        system_prompt: str,
        llm: HelloAgentsLLM,
        tool_registry: Optional[ToolRegistry] = None,
        tool_call_listener: Optional[Callable] = None,
    ):
        super().__init__(
            name=name,
            system_prompt=system_prompt,
            llm=llm,
            tool_registry=tool_registry,
        )
        self._tool_call_listener = tool_call_listener

    def _execute_tool_call(self, tool_name: str, parameters: str) -> str:
        """Executar chamada de ferramenta e notificar ouvinte"""
        # Analisar parâmetros
        parsed_parameters = self._parse_parameters(parameters)

        # Chamar ferramenta
        result = super()._execute_tool_call(tool_name, parameters)

        # Notificar ouvinte
        if self._tool_call_listener:
            self._tool_call_listener({
                "agent_name": self.name,
                "tool_name": tool_name,
                "parsed_parameters": parsed_parameters,
                "result": result,
            })

        return result
```

No assistente de pesquisa profunda, usamos `ToolAwareSimpleAgent` para registrar todas as chamadas de ferramentas do Agente:

```python
class DeepResearchAgent:
    def __init__(self, config: Configuration):
        self.config = config
        self.llm = HelloAgentsLLM(...)

        # Criar ouvinte de chamada de ferramenta
        def tool_listener(call_info):
            self._emit_event({
                "type": "tool_call",
                "agent": call_info["agent_name"],
                "tool": call_info["tool_name"],
                "parameters": call_info["parsed_parameters"],
            })

        # Criar três Agentes, todos usando o mesmo ouvinte
        self.planner = PlanningService(self.llm, tool_listener)
        self.summarizer = SummarizationService(self.llm, tool_listener)
        self.reporter = ReportingService(self.llm, tool_listener)
```

Desta forma, todas as chamadas de ferramentas do Agente são registradas e enviadas para o front-end via SSE, exibidas ao usuário em tempo real.

### 14.3.3 Modo de Colaboração de Agentes

Os três Agentes têm uma relação de **colaboração sequencial**, conforme mostrado na Figura 14.6.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-6.png" alt="" width="85%"/>
  <p>Figura 14.6 Processo de Colaboração de Agentes</p>
</div>

As características do modo de colaboração sequencial são:

1. **Processo Linear**: Agentes executam em ordem fixa
2. **Entrada e Saída Claras**: A entrada de cada Agente vem da saída do Agente anterior
3. **Sem Concorrência**: Apenas um Agente está trabalhando ao mesmo tempo

O `DeepResearchAgent` é o coordenador central de todo o sistema, responsável por agendar os três Agentes:

```python
class DeepResearchAgent:
    def run(self, research_topic: str) -> str:
        # 1. Estágio de planejamento
        self._emit_event({"type": "status", "message": "Planejando tarefas de pesquisa..."})
        todo_list = self.planner.plan_todo_list(research_topic)
        self._emit_event({"type": "tasks", "tasks": todo_list})

        # 2. Estágio de execução
        task_summaries = []
        for task in todo_list:
            self._emit_event({
                "type": "status",
                "message": f"Pesquisando: {task.title}"
            })

            # Pesquisar
            search_results = self.search_service.search(task.query)

            # Resumir
            summary = self.summarizer.summarize_task(task, search_results)
            task_summaries.append((task, summary))

            self._emit_event({
                "type": "task_completed",
                "task_id": task.id
            })

        # 3. Estágio de relatório
        self._emit_event({"type": "status", "message": "Gerando relatório..."})
        report = self.reporter.generate_report(research_topic, task_summaries)
        self._emit_event({"type": "report", "content": report})

        return report
```

## 14.4 Integração do Sistema de Ferramentas

### 14.4.1 Extensão do SearchTool

No Capítulo 7, implementamos a versão básica do `SearchTool`, integrando mecanismos de busca Tavily e SerpApi, demonstrando a ideia de design de pesquisa multi-fonte. No assistente de pesquisa profunda deste capítulo, estendemos ainda mais as capacidades do `SearchTool`, adicionando DuckDuckGo, Perplexity, SearXNG e outros mecanismos de busca, e implementando o modo Avançado (Advanced mode - combinando múltiplos mecanismos de busca). A pesquisa é a função mais central do assistente de pesquisa profunda, e essas extensões permitem que o sistema se adapte a diferentes cenários de uso e necessidades.

Conforme mostrado na Tabela 14.2, os mecanismos de busca adicionados desta vez têm características diferentes e cenários aplicáveis.

<div align="center">
  <p>Tabela 14.2 Comparação de Mecanismos de Busca Múltiplos</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-table-2.png" alt="" width="85%"/>
</div>

Não vamos mais discutir como estender separadamente. Você pode consultar o código-fonte e os casos de extensão no Capítulo 7 para implementação. O `SearchTool` fornece uma interface de pesquisa unificada. Não importa qual mecanismo de busca seja usado, o método de chamada é o mesmo.

No assistente de pesquisa profunda, selecionamos o mecanismo de busca através do arquivo de configuração:

```python
# config.py
class SearchAPI(str, Enum):
    TAVILY = "tavily"
    DUCKDUCKGO = "duckduckgo"
    PERPLEXITY = "perplexity"
    SEARXNG = "searxng"
    ADVANCED = "advanced"

class Configuration(BaseModel):
    search_api: SearchAPI = SearchAPI.DUCKDUCKGO
    # ...
```

```python
# .env
SEARCH_API=tavily
```

Desta forma, os usuários podem selecionar o mecanismo de busca modificando o arquivo `.env` sem modificar o código.

O resultado retornado pelo `SearchTool` é um dicionário contendo:

- `results`: Lista de resultados de pesquisa, cada resultado contém título, URL, snippet
- `backend`: Mecanismo de busca usado
- `answer`: Resposta gerada por IA (apenas Perplexity)
- `notices`: Informações de notificação (como limites de API, erros, etc.)

Aqui estão alguns tratamentos de casos especiais.

Os resultados de pesquisa podem conter URLs duplicadas, precisamos desduplicar:

```python
def deduplicate_sources(sources: List[dict]) -> List[dict]:
    """Remover URLs duplicadas"""
    seen_urls = set()
    unique_sources = []

    for source in sources:
        if source["url"] not in seen_urls:
            seen_urls.add(source["url"])
            unique_sources.append(source)

    return unique_sources
```

Os resultados de pesquisa podem conter uma grande quantidade de texto, precisamos limitar o número de tokens para cada fonte:

```python
def limit_source_tokens(source: dict, max_tokens: int = 2000) -> dict:
    """Limitar o número de tokens de uma fonte"""
    snippet = source["snippet"]

    # Estimativa simples de token: 1 token é aproximadamente 4 caracteres
    max_chars = max_tokens * 4

    if len(snippet) > max_chars:
        snippet = snippet[:max_chars] + "..."

    return {
        **source,
        "snippet": snippet
    }
```

### 14.4.2 Uso do NoteTool

No assistente de pesquisa profunda, usamos `NoteTool` para persistir o progresso da pesquisa. O `NoteTool` é uma ferramenta integrada no Capítulo 9, usada para criar, ler, atualizar e deletar notas.

Durante o processo de pesquisa, precisamos registrar os resultados de pesquisa, resumos e relatório de pesquisa final para cada subtarefa. Essas informações precisam ser persistidas no disco para que a pesquisa possa continuar do último progresso quando interrompida, e também é conveniente visualizar todas as operações durante o processo de pesquisa e analisar a qualidade e eficiência da pesquisa.

O `NoteTool` armazena notas no diretório de workspace especificado, com cada nota sendo um arquivo Markdown. O nome do arquivo da nota é o ID da tarefa, e o conteúdo inclui título da tarefa, intenção da tarefa, consulta de pesquisa, resultados de pesquisa e resumo.

O estilo de arquivo gerado final estará na seguinte estrutura de árvore:

```
workspace/
├── notes/
│   ├── 1.md  # Notas para tarefa 1
│   ├── 2.md  # Notas para tarefa 2
│   ├── 3.md  # Notas para tarefa 3
│   └── ...
└── reports/
    └── final_report.md  # Relatório final
```

No assistente de pesquisa profunda, usamos `NoteTool` para registrar o progresso de pesquisa de cada subtarefa:

```python
class NotesService:
    def __init__(self, workspace: str):
        self.note_tool = NoteTool(workspace=workspace)

    def save_task_summary(
        self,
        task: TodoItem,
        search_results: List[dict],
        summary: str
    ):
        """Salvar resumo da tarefa"""
        # Formatar conteúdo da nota
        content = self._format_note_content(
            task=task,
            search_results=search_results,
            summary=summary
        )

        # Criar nota
        self.note_tool.run({
            "action": "create",
            "title": f"Tarefa {task.id}: {task.title}",
            "content": content,
            "tags": ["research", "summary"]
        })

    def _format_note_content(
        self,
        task: TodoItem,
        search_results: List[dict],
        summary: str
    ) -> str:
        """Formatar conteúdo da nota"""
        content = f"# Tarefa {task.id}: {task.title}\n\n"
        content += f"## Informações da Tarefa\n\n"
        content += f"- **Intenção**: {task.intent}\n"
        content += f"- **Consulta**: {task.query}\n\n"

        content += f"## Resultados de Pesquisa\n\n"
        for idx, result in enumerate(search_results, start=1):
            content += f"[{idx}] {result['title']}\n"
            content += f"URL: {result['url']}\n"
            content += f"Snippet: {result['snippet']}\n\n"

        content += f"## Resumo\n\n{summary}\n"

        return content
```

### 14.4.3 Gerenciamento de Ferramentas com ToolRegistry

O `ToolRegistry` é o registro de ferramentas do framework HelloAgents, também suportado no nosso Capítulo 7, usado para gerenciar o registro e invocação de todas as ferramentas. No assistente de pesquisa profunda, usamos `ToolRegistry` para gerenciar `SearchTool` e `NoteTool`.

Antes de criar um Agente, precisamos registrar as ferramentas primeiro:

```python
from hello_agents import ToolAwareSimpleAgent
from hello_agents.tools import ToolRegistry
from hello_agents.tools import SearchTool
from hello_agents.tools import NoteTool

# Criar ferramentas
search_tool = SearchTool(backend="hybrid")
note_tool = NoteTool(workspace="./workspace/notes")

# Criar registro
registry = ToolRegistry()

# Registrar ferramentas
registry.register_tool(search_tool)
registry.register_tool(note_tool)

# Criar Agente
agent = ToolAwareSimpleAgent(
    name="Assistente de Pesquisa",
    system_prompt="Você é um assistente de pesquisa",
    llm=llm,
    tool_registry=registry
)
```

Quando um Agente precisa chamar uma ferramenta, ele gera uma instrução de chamada de ferramenta, conforme mostrado na Figura 14.7.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-7.png" alt="" width="85%"/>
  <p>Figura 14.7 Processo de Chamada de Ferramenta</p>
</div>

**Processo de Chamada de Ferramenta**:

1. **Agente gera instrução**: Agente gera instrução de chamada de ferramenta, como `[TOOL_CALL:search_tool:{"input": "organização Datawhale", "backend": "tavily"}]`
2. **Analisar instrução**: `ToolRegistry` analisa a instrução, extrai nome da ferramenta e parâmetros
3. **Encontrar ferramenta**: `ToolRegistry` encontra a ferramenta correspondente com base no nome da ferramenta
4. **Chamar ferramenta**: Chamar o método `run` da ferramenta, passando parâmetros
5. **Retornar resultado**: Ferramenta retorna resultado de execução
6. **Formatar resultado**: Formatar o resultado como uma string e retorná-lo ao Agente

## 14.5 Implementação da Camada de Serviço

Esta seção introduzirá em detalhes a implementação dos serviços centrais, incluindo PlanningService, SummarizationService, ReportingService e SearchService. Esses serviços são a ponte que conecta Agentes e ferramentas, responsáveis pela lógica de negócio específica.

### 14.5.1 Serviço de Planejamento de Tarefas

O `PlanningService` é responsável por chamar o agente de planejamento de pesquisa para decompor o tópico de pesquisa em subtarefas. Este é o primeiro e mais crítico passo de todo o processo de pesquisa.

**(1) Abordagem de Implementação**

Suas responsabilidades centrais são:

1. **Construir Prompt de planejamento**: Construir Prompt com base no tópico de pesquisa e data atual
2. **Chamar agente de planejamento**: Chamar TODO Planner Agent para gerar lista de subtarefas
3. **Analisar resposta JSON**: Extrair lista de subtarefas em formato JSON da resposta do Agente
4. **Validar formato de subtarefa**: Garantir que cada subtarefa contenha os campos obrigatórios (title, intent, query)

```python
import re
import json
from typing import List, Callable, Optional
from datetime import datetime

from hello_agents import HelloAgentsLLM
from hello_agents import ToolAwareSimpleAgent
from models import TodoItem, SummaryState
from prompts import todo_planner_instructions

class PlanningService:
    """Serviço de planejamento de tarefas"""

    def __init__(
        self,
        llm: HelloAgentsLLM,
        tool_call_listener: Optional[Callable] = None
    ):
        self._llm = llm
        self._tool_call_listener = tool_call_listener

        # Criar agente de planejamento
        self._agent = ToolAwareSimpleAgent(
            name="TODO Planner",
            system_prompt="Você é um especialista em planejamento de pesquisa, hábil em decompor tópicos de pesquisa complexos em subtarefas claras.",
            llm=llm,
            tool_call_listener=tool_call_listener
        )

    def plan_todo_list(self, state: SummaryState) -> List[TodoItem]:
        """Planejar lista TODO

        Args:
            state: Estado de pesquisa, contendo tópico de pesquisa

        Returns:
            Lista de subtarefas
        """
        # Construir Prompt
        prompt = todo_planner_instructions.format(
            current_date=self._get_current_date(),
            research_topic=state.research_topic,
        )

        # Chamar Agente
        response = self._agent.run(prompt)

        # Analisar JSON
        tasks_payload = self._extract_tasks(response)

        # Validar e criar TodoItem
        todo_items = []
        for idx, item in enumerate(tasks_payload, start=1):
            # Validar campos obrigatórios
            if not all(key in item for key in ["title", "intent", "query"]):
                raise ValueError(f"Tarefa {idx} está faltando campos obrigatórios")

            task = TodoItem(
                id=idx,
                title=item["title"],
                intent=item["intent"],
                query=item["query"],
            )
            todo_items.append(task)

        return todo_items

    def _get_current_date(self) -> str:
        """Obter data atual"""
        return datetime.now().strftime("%Y-%m-%d")

    def _extract_tasks(self, response: str) -> List[dict]:
        """Extrair JSON da resposta do Agente

        A resposta do Agente pode conter texto extra, como:
        "Ok, vou planejar as seguintes tarefas para você:\n[{...}, {...}]\nEssas tarefas cobrem..."

        Precisamos extrair a parte JSON.
        """
        # Método 1: Usar regex para extrair array JSON
        json_match = re.search(r'\[.*\]', response, re.DOTALL)
        if json_match:
            json_str = json_match.group(0)
            try:
                return json.loads(json_str)
            except json.JSONDecodeError as e:
                raise ValueError(f"Falha na análise JSON: {e}")

        # Método 2: Se nenhum array JSON for encontrado, tentar analisar a resposta inteira diretamente
        try:
            return json.loads(response)
        except json.JSONDecodeError:
            raise ValueError("Não foi possível extrair JSON da resposta")
```

**(2) Análise e Validação JSON**

O JSON retornado pelo Agente pode conter texto extra ou erros de formato, então precisamos de lógica de análise robusta:

**Problemas Comuns**:

1. **Contém texto extra**: Agente pode adicionar texto explicativo antes e depois do JSON
2. **Erros de formato**: JSON pode estar faltando aspas, vírgulas, etc.
3. **Campos faltando**: Algumas subtarefas podem estar faltando campos obrigatórios

**Soluções**:

1. **Usar regex**: Extrair parte JSON
2. **Múltiplas estratégias de análise**: Primeiro tentar extrair array JSON, depois tentar analisar diretamente
3. **Validação de campo**: Garantir que cada subtarefa contenha campos obrigatórios

**Exemplo**:

```python
# Exemplo de resposta do Agente 1: Contém texto extra
response1 = """
Ok, vou planejar as seguintes tarefas para você:

[
  {
    "title": "O que é um modelo multimodal",
    "intent": "Entender conceitos básicos",
    "query": "multimodal model definition"
  },
  {
    "title": "Modelos multimodais mais recentes",
    "intent": "Entender status técnico",
    "query": "latest multimodal models 2024"
  }
]

Essas tarefas cobrem as informações básicas e projetos centrais da organização Datawhale.
"""

# Extrair JSON
tasks1 = service._extract_tasks(response1)
# Resultado: [{"title": "Informações básicas sobre o Datawhale", ...}, ...]

# Exemplo de resposta do Agente 2: JSON puro
response2 = """
[
  {"title": "Informações básicas sobre o Datawhale", "intent": "Entender posicionamento organizacional", "query": "introdução organização Datawhale"},
  {"title": "Projetos principais do Datawhale", "intent": "Entender conteúdo central", "query": "projetos tutoriais Datawhale 2024"}
]
"""

# Extrair JSON
tasks2 = service._extract_tasks(response2)
# Resultado: [{"title": "O que é um modelo multimodal", ...}, ...]
```

**(3) Avaliação de Qualidade do Planejamento**

Um bom plano deve atender aos seguintes critérios:

1. **Cobertura abrangente**: Cobrir todos os aspectos importantes do tópico
2. **Lógica clara**: Relacionamentos lógicos claros entre subtarefas
3. **Consultas precisas**: Consultas de pesquisa podem encontrar materiais relevantes com precisão
4. **Quantidade apropriada**: 3-5 subtarefas

Podemos adicionar um método de avaliação:

```python
def evaluate_plan(self, todo_items: List[TodoItem]) -> dict:
    """Avaliar qualidade do planejamento

    Returns:
        Resultados de avaliação, incluindo pontuação e sugestões
    """
    score = 100
    suggestions = []

    # Verificar quantidade
    if len(todo_items) < 3:
        score -= 20
        suggestions.append("Poucas subtarefas, pode perder informações importantes")
    elif len(todo_items) > 5:
        score -= 10
        suggestions.append("Muitas subtarefas, pode haver redundância")

    # Verificar qualidade da consulta
    for task in todo_items:
        if len(task.query.split()) < 2:
            score -= 10
            suggestions.append(f"Consulta para tarefa '{task.title}' é muito simples")

    # Verificar relacionamentos lógicos
    # (Verificações de lógica mais complexas podem ser adicionadas aqui)

    return {
        "score": score,
        "suggestions": suggestions
    }
```

### 14.5.2 Serviço de Resumo

O `SummarizationService` é responsável por chamar o agente de resumo de tarefa para resumir resultados de pesquisa. Este é o link central do processo de pesquisa e determina a qualidade da pesquisa.

Suas responsabilidades são:

1. **Formatar resultados de pesquisa**: Formatar resultados de pesquisa em texto legível
2. **Construir Prompt de resumo**: Construir Prompt com base em informações da tarefa e resultados de pesquisa
3. **Chamar agente de resumo**: Chamar Task Summarizer Agent para gerar resumo
4. **Extrair citações de fonte**: Extrair citações de fonte do resumo

Código central:

```python
from typing import List, Callable, Optional, Tuple

from hello_agents import HelloAgentsLLM
from hello_agents import ToolAwareSimpleAgent
from models import TodoItem
from prompts import task_summarizer_instructions

class SummarizationService:
    """Serviço de resumo"""

    def __init__(
        self,
        llm: HelloAgentsLLM,
        tool_call_listener: Optional[Callable] = None
    ):
        self._llm = llm
        self._tool_call_listener = tool_call_listener

        # Criar agente de resumo
        self._agent = ToolAwareSimpleAgent(
            name="Task Summarizer",
            system_prompt="Você é um especialista em resumo de tarefas, hábil em extrair informações-chave de resultados de pesquisa.",
            llm=llm,
            tool_call_listener=tool_call_listener
        )

    def summarize_task(
        self,
        task: TodoItem,
        search_results: List[dict]
    ) -> Tuple[str, List[str]]:
        """Resumir tarefa

        Args:
            task: Informações da tarefa
            search_results: Lista de resultados de pesquisa

        Returns:
            (Texto do resumo, lista de URLs de fonte)
        """
        # Formatar resultados de pesquisa
        formatted_sources = self._format_sources(search_results)

        # Construir Prompt
        prompt = task_summarizer_instructions.format(
            task_title=task.title,
            task_intent=task.intent,
            task_query=task.query,
            search_results=formatted_sources,
        )

        # Chamar Agente
        summary = self._agent.run(prompt)

        # Extrair URLs de fonte
        source_urls = [result["url"] for result in search_results]

        return summary, source_urls

    def _format_sources(self, search_results: List[dict]) -> str:
        """Formatar resultados de pesquisa

        Formatar resultados de pesquisa em texto legível, incluindo:
        - Número de série
        - Título
        - URL
        - Snippet
        """
        formatted = []
        for idx, result in enumerate(search_results, start=1):
            formatted.append(
                f"[{idx}] {result['title']}\n"
                f"URL: {result['url']}\n"
                f"Snippet: {result['snippet']}\n"
            )
        return "\n".join(formatted)
```

### 14.5.3 Serviço de Geração de Relatório

O `ReportingService` é responsável por chamar o agente de geração de relatório para integrar os resumos de todas as subtarefas. Este é o último passo do processo de pesquisa, gerando o relatório de pesquisa final.

Suas responsabilidades são:

1. **Formatar resumos de subtarefas**: Formatar todos os resumos de subtarefas em um formato unificado
2. **Construir Prompt de relatório**: Construir Prompt com base no tópico de pesquisa e resumos de subtarefas
3. **Chamar agente de relatório**: Chamar Report Writer Agent para gerar relatório final
4. **Organizar citações**: Organizar todas as citações de fonte na seção de referências

**Implementação de Código Central**:

```python
from typing import List, Callable, Optional, Tuple

from hello_agents import HelloAgentsLLM
from hello_agents import ToolAwareSimpleAgent
from models import TodoItem
from prompts import report_writer_instructions

class ReportingService:
    """Serviço de geração de relatório"""

    def __init__(
        self,
        llm: HelloAgentsLLM,
        tool_call_listener: Optional[Callable] = None
    ):
        self._llm = llm
        self._tool_call_listener = tool_call_listener

        # Criar agente de relatório
        self._agent = ToolAwareSimpleAgent(
            name="Report Writer",
            system_prompt="Você é um especialista em redação de relatórios, hábil em integrar informações e gerar relatórios estruturados.",
            llm=llm,
            tool_call_listener=tool_call_listener
        )

    def generate_report(
        self,
        research_topic: str,
        task_summaries: List[Tuple[TodoItem, str, List[str]]]
    ) -> str:
        """Gerar relatório final

        Args:
            research_topic: Tópico de pesquisa
            task_summaries: Lista de resumos de subtarefas, cada elemento é (task, summary, source URL list)

        Returns:
            Relatório final (formato Markdown)
        """
        # Formatar resumos de subtarefas
        formatted_summaries = self._format_summaries(task_summaries)

        # Construir Prompt
        prompt = report_writer_instructions.format(
            research_topic=research_topic,
            task_summaries=formatted_summaries,
        )

        # Chamar Agente
        report = self._agent.run(prompt)

        return report

    def _format_summaries(
        self,
        task_summaries: List[Tuple[TodoItem, str, List[str]]]
    ) -> str:
        """Formatar resumos de subtarefas

        Formatar todos os resumos de subtarefas em um formato unificado, incluindo:
        - Número de série da tarefa
        - Título da tarefa
        - Intenção da tarefa
        - Conteúdo do resumo
        - URLs de fonte
        """
        formatted = []
        for idx, (task, summary, source_urls) in enumerate(task_summaries, start=1):
            formatted.append(
                f"## Tarefa {idx}: {task.title}\n\n"
                f"**Intenção**: {task.intent}\n\n"
                f"{summary}\n\n"
                f"**Fontes**:\n"
            )
            for url in source_urls:
                formatted.append(f"- {url}\n")
            formatted.append("\n")

        return "".join(formatted)
```

### 14.5.4 Serviço de Agendamento de Pesquisa

O `SearchService` é responsável por agendar mecanismos de busca, executar pesquisas e retornar resultados. Esta é a ponte que conecta Agentes e SearchTool. Aqui não adotamos a forma usual de ter SimpleAgent chamando ferramentas diretamente, mas em vez disso retornamos os resultados de execução do SearchTool para o Agente através de uma camada intermediária, o que torna o Agente mais focado em processar as informações obtidas.

Suas responsabilidades são:

1. **Agendar mecanismo de busca**: Selecionar mecanismo de busca com base na configuração
2. **Executar pesquisa**: Chamar SearchTool para executar pesquisa
3. **Processar resultados**: Desduplicar, limitar tokens, formatar
4. **Tratamento de erros**: Lidar com situações de falha de pesquisa

Código central:

```python
from typing import List, Optional
import logging

from hello_agents.tools import SearchTool
from config import Configuration

logger = logging.getLogger(__name__)

class SearchService:
    """Serviço de agendamento de pesquisa"""

    def __init__(self, config: Configuration):
        self.config = config

        # Criar SearchTool
        self.search_tool = SearchTool(backend="hybrid")

    def search(
        self,
        query: str,
        max_results: int = 5
    ) -> List[dict]:
        """Executar pesquisa

        Args:
            query: Consulta de pesquisa
            max_results: Número máximo de resultados

        Returns:
            Lista de resultados de pesquisa
        """
        try:
            # Chamar SearchTool
            raw_response = self.search_tool.run({
                "input": query,
                "backend": self.config.search_api.value,
                "mode": "structured",
                "max_results": max_results
            })

            # Extrair resultados
            results = raw_response.get("results", [])

            # Processar resultados
            results = self._deduplicate_sources(results)
            results = self._limit_source_tokens(results)

            logger.info(f"Pesquisa bem-sucedida: {query}, retornou {len(results)} resultados")

            return results

        except Exception as e:
            logger.error(f"Pesquisa falhou: {query}, erro: {e}")
            return []

    def _deduplicate_sources(self, sources: List[dict]) -> List[dict]:
        """Remover URLs duplicadas"""
        seen_urls = set()
        unique_sources = []

        for source in sources:
            url = source.get("url", "")
            if url and url not in seen_urls:
                seen_urls.add(url)
                unique_sources.append(source)

        return unique_sources

    def _limit_source_tokens(
        self,
        sources: List[dict],
        max_tokens_per_source: int = 2000
    ) -> List[dict]:
        """Limitar o número de tokens por fonte"""
        limited_sources = []

        for source in sources:
            snippet = source.get("snippet", "")

            # Estimativa simples de token: 1 token é aproximadamente 4 caracteres
            max_chars = max_tokens_per_source * 4

            if len(snippet) > max_chars:
                snippet = snippet[:max_chars] + "..."

            limited_sources.append({
                **source,
                "snippet": snippet
            })

        return limited_sources
```

Selecionar mecanismo de busca com base na configuração, conforme mostrado na Figura 14.8:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-8.png" alt="" width="85%"/>
  <p>Figura 14.8 Processo de Agendamento de Mecanismo de Busca</p>
</div>

**Lógica de Agendamento**:

1. **Ler configuração**: Ler configuração `SEARCH_API` do arquivo `.env`
2. **Selecionar engine**: Selecionar mecanismo de busca com base na configuração (tavily, duckduckgo, perplexity, etc.)
3. **Executar pesquisa**: Chamar SearchTool para executar pesquisa
4. **Processar resultados**: Desduplicar, limitar tokens, formatar
5. **Retornar resultados**: Retornar resultados de pesquisa processados

Para melhorar a eficiência e reduzir custos, podemos adicionar cache de resultados de pesquisa:

```python
import hashlib
import json
from pathlib import Path

class SearchService:
    def __init__(self, config: Configuration):
        self.config = config
        self.search_tool = SearchTool(backend="hybrid")

        # Diretório de cache
        self.cache_dir = Path("./cache/search")
        self.cache_dir.mkdir(parents=True, exist_ok=True)

    def search(
        self,
        query: str,
        max_results: int = 5,
        use_cache: bool = True
    ) -> List[dict]:
        """Executar pesquisa (com cache)"""
        # Gerar chave de cache
        cache_key = self._generate_cache_key(query, max_results)
        cache_file = self.cache_dir / f"{cache_key}.json"

        # Tentar ler do cache
        if use_cache and cache_file.exists():
            logger.info(f"Lendo resultados de pesquisa do cache: {query}")
            with open(cache_file, "r", encoding="utf-8") as f:
                return json.load(f)

        # Executar pesquisa
        results = self._execute_search(query, max_results)

        # Salvar no cache
        if use_cache and results:
            with open(cache_file, "w", encoding="utf-8") as f:
                json.dump(results, f, ensure_ascii=False, indent=2)

        return results

    def _generate_cache_key(self, query: str, max_results: int) -> str:
        """Gerar chave de cache"""
        # Gerar hash MD5 usando consulta e max results
        content = f"{query}_{max_results}_{self.config.search_api.value}"
        return hashlib.md5(content.encode()).hexdigest()
```

Através de quatro serviços centrais (PlanningService, SummarizationService, ReportingService, SearchService), construímos um processo de pesquisa completo. Esses serviços desempenham suas funções e colaboram através de interfaces claras, alcançando um processo automatizado do tópico de pesquisa ao relatório final.

## 14.6 Design de Interação Front-End

Nas seções anteriores, implementamos o sistema back-end completo. Esta seção introduzirá em detalhes o design de interação front-end, incluindo interface de diálogo modal em tela cheia, exibição de progresso em tempo real e visualização de resultados de pesquisa.

### 14.6.1 Design de Interface de Diálogo Modal em Tela Cheia

O assistente de pesquisa profunda adota um design de interface de diálogo modal em tela cheia, que tem as seguintes vantagens:

1. **Experiência imersiva**: Exibição em tela cheia, evitando distrações, focando na pesquisa
2. **Hierarquia clara**: Página principal e página de pesquisa são separadas, com hierarquia clara
3. **Fácil de fechar**: Clicar no botão fechar ou pressionar tecla ESC para retornar à página principal
4. **Design responsivo**: Adapta-se a diferentes tamanhos de tela

Conforme mostrado na Figura 14.9, o diálogo modal em tela cheia contém as seguintes partes:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-9.png" alt="" width="85%"/>
  <p>Figura 14.9 Interface de Diálogo Modal em Tela Cheia</p>
</div>

**Componentes da Interface**:

1. **Barra superior**: Contém tópico de pesquisa e botão fechar
2. **Área de progresso**: Mostra progresso atual da pesquisa (planejamento, execução, relatório)
3. **Área de conteúdo**: Mostra resultados de pesquisa (formato Markdown)
4. **Barra inferior**: Mostra informações de status (como "Pesquisando...", "Concluído")

A implementação Vue correspondente é a seguinte (ResearchModal.vue):

```vue
<template>
  <div v-if="isOpen" class="modal-overlay" @click.self="close">
    <div class="modal-container">
      <!-- Barra superior -->
      <div class="modal-header">
        <h2>{{ researchTopic }}</h2>
        <button @click="close" class="close-button">
          <svg><!-- Ícone fechar --></svg>
        </button>
      </div>

      <!-- Área de progresso -->
      <div class="progress-section">
        <div class="progress-bar">
          <div
            class="progress-fill"
            :style="{ width: progressPercentage + '%' }"
          ></div>
        </div>
        <div class="progress-text">{{ progressText }}</div>
      </div>

      <!-- Área de conteúdo -->
      <div class="content-section">
        <div v-if="isLoading" class="loading-spinner">
          <div class="spinner"></div>
          <p>Pesquisando, aguarde...</p>
        </div>

        <div v-else class="markdown-content" v-html="renderedMarkdown"></div>
      </div>

      <!-- Barra inferior -->
      <div class="modal-footer">
        <span class="status-text">{{ statusText }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { marked } from 'marked'

interface Props {
  isOpen: boolean
  researchTopic: string
}

const props = defineProps<Props>()
const emit = defineEmits<{
  close: []
}>()

// Estado
const isLoading = ref(true)
const progressPercentage = ref(0)
const progressText = ref('Preparando...')
const statusText = ref('Pesquisando...')
const markdownContent = ref('')

// Renderizar Markdown
const renderedMarkdown = computed(() => {
  return marked(markdownContent.value)
})

// Fechar modal
const close = () => {
  emit('close')
}

// Ouvir tecla ESC
const handleKeydown = (e: KeyboardEvent) => {
  if (e.key === 'Escape') {
    close()
  }
}

// Adicionar ouvinte de teclado ao montar
watch(() => props.isOpen, (isOpen) => {
  if (isOpen) {
    document.addEventListener('keydown', handleKeydown)
  } else {
    document.removeEventListener('keydown', handleKeydown)
  }
})
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
...
</style>
```

Para adaptar a diferentes tamanhos de tela, adicionamos media queries:

```css
/* Dispositivos tablet */
@media (max-width: 768px) {
  .modal-container {
    width: 95vw;
    height: 95vh;
  }

  .modal-header,
  .progress-section,
  .content-section,
  .modal-footer {
    padding: 15px 20px;
  }
}

/* Dispositivos móveis */
@media (max-width: 480px) {
  .modal-container {
    width: 100vw;
    height: 100vh;
    border-radius: 0;
  }

  .modal-header h2 {
    font-size: 18px;
  }
}
```

### 14.6.2 Exibição de Progresso em Tempo Real

O assistente de pesquisa profunda usa SSE (Server-Sent Events) para implementar exibição de progresso em tempo real. SSE é uma tecnologia de push do servidor que permite ao servidor enviar dados ativamente para o cliente, o que também é explicado no capítulo de protocolo.

Conforme mostrado na Figura 14.10, o processo SSE inclui os seguintes passos:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/14-figures/14-10.png" alt="" width="85%"/>
  <p>Figura 14.10 Processo SSE</p>
</div>

**Descrição do Processo**:

1. **Cliente inicia solicitação**: Enviar solicitação POST para `/api/research`, contendo tópico de pesquisa
2. **Servidor estabelece conexão SSE**: Retornar resposta `text/event-stream`
3. **Servidor envia progresso**: Periodicamente enviar progresso da pesquisa (planejamento, execução, relatório)
4. **Cliente recebe progresso**: Ouvir eventos SSE, atualizar interface
5. **Pesquisa concluída**: Servidor envia relatório final, fecha conexão

Se você quiser usar SSE em projetos front-end e back-end, também precisa fazer as seguintes configurações.

**Endpoint SSE do Back-End FastAPI**:

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from typing import AsyncGenerator
import asyncio
import json

app = FastAPI()

async def research_stream(topic: str) -> AsyncGenerator[str, None]:
    """Gerador de streaming de pesquisa

    Gerar dados em formato SSE:
    data: {"type": "progress", "data": {...}}

    """
    try:
        # 1. Estágio de planejamento
        yield f"data: {json.dumps({'type': 'progress', 'stage': 'planning', 'percentage': 10, 'text': 'Planejando tarefas de pesquisa...'})}\n\n"

        # Chamar PlanningService
        todo_items = await planning_service.plan_todo_list(topic)

        yield f"data: {json.dumps({'type': 'plan', 'data': [item.dict() for item in todo_items]})}\n\n"

        # 2. Estágio de execução
        task_summaries = []
        for idx, task in enumerate(todo_items, start=1):
            # Atualizar progresso
            percentage = 10 + (idx / len(todo_items)) * 70
            yield f"data: {json.dumps({'type': 'progress', 'stage': 'executing', 'percentage': percentage, 'text': f'Pesquisando tarefa {idx}/{len(todo_items)}: {task.title}'})}\n\n"

            # Pesquisar
            search_results = await search_service.search(task.query)

            # Resumir
            summary, source_urls = await summarization_service.summarize_task(task, search_results)

            task_summaries.append((task, summary, source_urls))

            # Enviar resumo da tarefa
            yield f"data: {json.dumps({'type': 'task_summary', 'task_id': task.id, 'summary': summary})}\n\n"

        # 3. Estágio de relatório
        yield f"data: {json.dumps({'type': 'progress', 'stage': 'reporting', 'percentage': 90, 'text': 'Gerando relatório final...'})}\n\n"

        # Gerar relatório
        report = await reporting_service.generate_report(topic, task_summaries)

        # Enviar relatório final
        yield f"data: {json.dumps({'type': 'report', 'data': report})}\n\n"

        # Concluído
        yield f"data: {json.dumps({'type': 'progress', 'stage': 'completed', 'percentage': 100, 'text': 'Pesquisa concluída!'})}\n\n"

    except Exception as e:
        # Tratamento de erros
        yield f"data: {json.dumps({'type': 'error', 'message': str(e)})}\n\n"

@app.post("/api/research")
async def research(request: ResearchRequest):
    """Endpoint de pesquisa (SSE)"""
    return StreamingResponse(
        research_stream(request.topic),
        media_type="text/event-stream",
        headers={
            "Cache-Control": "no-cache",
            "Connection": "keep-alive",
        }
    )
```

**Front-End Usando EventSource para Receber SSE**:

```typescript
// composables/useResearch.ts
import { ref } from 'vue'

export function useResearch() {
  const isLoading = ref(false)
  const progressPercentage = ref(0)
  const progressText = ref('')
  const markdownContent = ref('')
  const error = ref<string | null>(null)

  const startResearch = (topic: string) => {
    isLoading.value = true
    error.value = null

    // Criar EventSource
    const eventSource = new EventSource(`/api/research?topic=${encodeURIComponent(topic)}`)

    // Ouvir mensagens
    eventSource.onmessage = (event) => {
      const data = JSON.parse(event.data)

      switch (data.type) {
        case 'progress':
          progressPercentage.value = data.percentage
          progressText.value = data.text
          break

        case 'plan':
          // Exibir resultados de planejamento
          console.log('Resultados de planejamento:', data.data)
          break

        case 'task_summary':
          // Anexar resumo da tarefa ao Markdown
          markdownContent.value += `\n\n## Tarefa ${data.task_id}\n\n${data.summary}`
          break

        case 'report':
          // Exibir relatório final
          markdownContent.value = data.data
          break

        case 'error':
          error.value = data.message
          eventSource.close()
          isLoading.value = false
          break

        case 'completed':
          eventSource.close()
          isLoading.value = false
          break
      }
    }

    // Tratamento de erros
    eventSource.onerror = (err) => {
      console.error('Erro SSE:', err)
      error.value = 'Falha na conexão, tente novamente'
      eventSource.close()
      isLoading.value = false
    }
  }

  return {
    isLoading,
    progressPercentage,
    progressText,
    markdownContent,
    error,
    startResearch,
  }
}
```

**Usando no Componente**:

```vue
<script setup lang="ts">
import { useResearch } from '@/composables/useResearch'

const {
  isLoading,
  progressPercentage,
  progressText,
  markdownContent,
  error,
  startResearch
} = useResearch()

const handleStartResearch = (topic: string) => {
  startResearch(topic)
}
</script>
```

### 14.6.3 Visualização de Resultados de Pesquisa

Os resultados de pesquisa são exibidos em formato Markdown, incluindo títulos, parágrafos, listas, citações e outros elementos. Usamos a biblioteca `marked` para converter Markdown em HTML e adicionar estilos personalizados.

**Renderizando Markdown**:

```typescript
import { marked } from 'marked'

// Configurar marked
marked.setOptions({
  breaks: true,  // Suportar quebras de linha
  gfm: true,     // Suportar GitHub Flavored Markdown
})

// Renderizar
const renderedHtml = marked(markdownContent.value)
```

Relatórios de pesquisa contêm um grande número de citações de fonte, que precisamos tratar especialmente:

```markdown
## Referências

### Tarefa 1: Informações Básicas sobre o Datawhale
- [Datawhale GitHub](https://github.com/datawhalechina)
- [Site Oficial Datawhale](https://datawhale.club)

### Tarefa 2: Projetos Principais do Datawhale
- [Tutorial Hello-Agents](https://github.com/datawhalechina/Hello-Agents)
...
```

Através de interface de diálogo modal em tela cheia, exibição de progresso em tempo real SSE e visualização de resultados em Markdown, construímos uma interface front-end amigável ao usuário. Os usuários podem ver claramente o progresso da pesquisa e visualizar resultados de pesquisa em um formato bonito.

## 14.7 Resumo do Capítulo

Neste capítulo, construímos um sistema completo de agente de pesquisa profunda automatizado do zero. Vamos revisar os pontos centrais:

**(1) Paradigma de Pesquisa Orientado por TODO**

Propusemos um novo paradigma de pesquisa - pesquisa orientada por TODO. Este paradigma decompõe tópicos de pesquisa complexos em subtarefas executáveis e completa a pesquisa através de três estágios:

- **Estágio de planejamento**: Decompor tópico de pesquisa em 3-5 subtarefas, cada subtarefa contém título, intenção e consulta de pesquisa
- **Estágio de execução**: Executar pesquisa e resumo para cada subtarefa, gerando conhecimento estruturado
- **Estágio de relatório**: Integrar resumos de todas as subtarefas, gerar relatório de pesquisa final

As vantagens deste paradigma são:

1. **Forte controlabilidade**: Cada subtarefa tem objetivos e escopo claros
2. **Qualidade confiável**: Agentes dedicados garantem qualidade em cada estágio
3. **Fácil de depurar**: Pode depurar cada subtarefa individualmente
4. **Boa escalabilidade**: Pode facilmente adicionar novas subtarefas ou modificar subtarefas existentes

**(2) Sistema de Colaboração de Três Agentes**

Projetamos três Agentes especializados, cada um desempenhando suas funções:

- **TODO Planner (Especialista em Planejamento de Pesquisa)**: Responsável por decompor tópicos de pesquisa em subtarefas
- **Task Summarizer (Especialista em Resumo de Tarefas)**: Responsável por resumir resultados de pesquisa para cada subtarefa
- **Report Writer (Especialista em Redação de Relatórios)**: Responsável por integrar resumos de todas as subtarefas e gerar relatório final

As vantagens deste design são:

1. **Responsabilidades claras**: Cada Agente foca em uma tarefa específica
2. **Otimização de prompt**: Pode customizar Prompts especializados para cada Agente
3. **Fácil de manter**: Modificar um Agente não afeta outros Agentes
4. **Garantia de qualidade**: Cada Agente é um "especialista" em seu campo

**(3) Design do ToolAwareSimpleAgent**

Estendemos o `SimpleAgent` do framework HelloAgents e implementamos `ToolAwareSimpleAgent`. Este Agente tem capacidade de escuta de chamada de ferramenta e pode:

- **Ouvir chamadas de ferramentas**: Ouvir cada chamada de ferramenta através de funções de callback
- **Feedback em tempo real**: Enviar informações de chamada de ferramenta para o front-end em tempo real
- **Suporte de depuração**: Registrar todas as chamadas de ferramentas para fácil depuração

Este Agente foi integrado ao framework HelloAgents e pode ser reutilizado em outros projetos.

**(4) Integração do Sistema de Ferramentas**

Utilizamos totalmente o sistema de ferramentas do framework HelloAgents:

- **SearchTool**: Estendido para suportar mais mecanismos de busca (Tavily, DuckDuckGo, Perplexity, etc.)
- **NoteTool**: Persistir progresso de pesquisa, suportar recuperação e auditoria
- **ToolRegistry**: Gerenciamento unificado de todas as ferramentas, suportar extensões customizadas

Através de design baseado em configuração, os usuários podem facilmente trocar mecanismos de busca sem modificar código.

**(5) Implementação de Serviços Centrais**

Implementamos quatro serviços centrais conectando Agentes e ferramentas:

- **PlanningService**: Chamar agente de planejamento, analisar JSON, validar formato
- **SummarizationService**: Chamar agente de resumo, processar resultados de pesquisa, extrair fontes
- **ReportingService**: Chamar agente de relatório, integrar resumos, gerar relatório
- **SearchService**: Agendar mecanismos de busca, processar resultados, degradação de erros, cache de resultados

Esses serviços desempenham suas funções e colaboram através de interfaces claras, alcançando um processo automatizado do tópico de pesquisa ao relatório final.

**(6) Design de Interação Front-End**

Projetamos uma interface front-end amigável ao usuário:

- **Diálogo modal em tela cheia**: Experiência imersiva, hierarquia clara
- **Progresso em tempo real SSE**: Exibição em tempo real do progresso da pesquisa, boa experiência do usuário
- **Visualização Markdown**: Formato bonito, estrutura clara

Através da stack tecnológica Vue 3 + TypeScript + SSE, implementamos uma aplicação web moderna.

Este conhecimento não é apenas aplicável a assistentes de pesquisa profunda, mas também pode ser aplicado a outras aplicações de IA. Esperamos que os leitores possam explorar mais possibilidades com base neste capítulo e construir sistemas de IA mais poderosos.

No próximo capítulo, construiremos um sistema multi-agente combinado com um motor de jogo - Cyber Town, explorando padrões complexos de interação e colaboração entre Agentes. Fique ligado!
