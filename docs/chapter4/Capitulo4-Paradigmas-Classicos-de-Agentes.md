<div align="right">
  <a href="./Chapter4-Building-Classic-Agent-Paradigms.md">English</a> | <a href="./第四章%20智能体经典范式构建.md">中文</a> | Português
</div>

# Capítulo 4: Construindo Paradigmas Clássicos de Agentes

No capítulo anterior, exploramos profundamente os modelos de linguagem grandes como o "cérebro" dos agentes modernos. Aprendemos sobre sua arquitetura interna Transformer, métodos para interagir com eles e suas capacidades e limitações. Agora, é hora de transformar esse conhecimento teórico em prática e construir agentes com nossas próprias mãos.

A capacidade central de um agente moderno reside em sua habilidade de conectar o poder de raciocínio dos modelos de linguagem grandes com o mundo externo. Ele pode compreender autonomamente a intenção do usuário, decompor tarefas complexas e alcançar objetivos chamando uma série de "ferramentas" como interpretadores de código, mecanismos de busca e APIs para obter informações e executar operações. No entanto, os agentes não são onipotentes; eles também enfrentam desafios do problema de "alucinação" inerente aos modelos grandes, loops de raciocínio potenciais em tarefas complexas e uso incorreto de ferramentas, que constituem os limites de capacidade dos agentes.

Para organizar melhor os processos de "pensamento" e "ação" dos agentes, a indústria emergiu com múltiplos paradigmas arquitetônicos clássicos. Neste capítulo, vamos focar nos três mais representativos e implementá-los passo a passo do zero:

- **ReAct (Reasoning and Acting):** Um paradigma que combina firmemente "pensamento" e "ação", permitindo que os agentes pensem enquanto agem e se ajustem dinamicamente.
- **Plan-and-Solve:** Um paradigma de "pense antes de agir" onde os agentes primeiro geram um plano de ação completo e depois o executam estritamente.
- **Reflection (Reflexão):** Um paradigma que dota os agentes com capacidades de "reflexão", otimizando resultados através de autocrítica e correção.

Depois de entender esses conceitos, você pode perguntar: com muitos frameworks excelentes como LangChain e LlamaIndex já disponíveis, por que "reinventar a roda"? A resposta está no fato de que, embora os frameworks maduros tenham vantagens significativas em eficiência de engenharia, usar ferramentas altamente abstratas não nos ajuda a entender como os mecanismos de design subjacentes funcionam ou quais benefícios eles oferecem. Em segundo lugar, esse processo expõe desafios de engenharia em projetos. Os frameworks lidam com muitas questões para nós, como analisar formatos de saída do modelo, retentar chamadas de ferramentas que falharam e impedir que os agentes caiam em loops infinitos. Lidar com essas questões em primeira mão é a maneira mais direta de cultivar capacidades de design de sistemas. Finalmente, e mais importante, dominar princípios de design permite que você realmente se transforme de um "usuário" de framework para um "criador" de aplicações inteligentes. Quando componentes padrão não podem atender suas necessidades complexas, você terá a capacidade de personalizar profundamente ou até mesmo construir um agente completamente novo do zero.

## 4.1 Preparação do Ambiente e Definição Básica de Ferramentas

Antes de começarmos a construir, precisamos configurar o ambiente de desenvolvimento e definir alguns componentes básicos. Isso nos ajudará a evitar trabalho repetitivo e focar mais na lógica central ao implementar paradigmas diferentes posteriormente.

### 4.1.1 Instalando Dependências

A parte prática deste livro usará principalmente a linguagem Python, e recomenda-se Python 3.10 ou superior. Primeiro, certifique-se de ter instalado a biblioteca `openai` para interagir com modelos de linguagem grandes, e a biblioteca `python-dotenv` para gerenciar com segurança nossas chaves de API.

Execute o seguinte comando no seu terminal:

```bash
pip install openai python-dotenv
```

### 4.1.2 Configurando Chaves de API

Para tornar nosso código mais universal, vamos configurar uniformemente informações relacionadas ao serviço do modelo (ID do modelo, chave de API, endereço do serviço) em variáveis de ambiente.

1. No diretório raiz do seu projeto, crie um arquivo chamado `.env`.
2. Neste arquivo, adicione o seguinte conteúdo. Você pode apontá-lo para o serviço oficial da OpenAI ou qualquer serviço local/de terceiros compatível com a interface OpenAI de acordo com suas necessidades.
3. Se você realmente não sabe como obtê-lo, pode consultar a Seção [1.2 API Setup](https://datawhalechina.github.io/handy-multi-agent/#/chapter1/1.2.api-setup) em outro tutorial Datawhale.

```bash
# Arquivo .env
LLM_API_KEY="SUA-CHAVE-API"
LLM_MODEL_ID="SEU-MODELO"
LLM_BASE_URL="SUA-URL"
```

Nosso código carregará automaticamente essas configurações deste arquivo.

### 4.1.3 Encapsulando Funções Básicas de Chamada LLM

Para tornar a estrutura do código mais clara e reutilizável, vamos definir uma classe cliente LLM dedicada. Esta classe encapsulará todos os detalhes de interação com serviços de modelo, permitindo que nossa lógica principal foque mais na construção do agente.

```python
import os
from openai import OpenAI
from dotenv import load_dotenv
from typing import List, Dict

# Carregar variáveis de ambiente do arquivo .env
load_dotenv()

class HelloAgentsLLM:
    """
    Um cliente LLM personalizado para o livro "Hello Agents".
    É usado para chamar qualquer serviço compatível com a interface OpenAI e usa respostas em streaming por padrão.
    """
    def __init__(self, model: str = None, apiKey: str = None, baseUrl: str = None, timeout: int = None):
        """
        Inicializa o cliente. Prioriza parâmetros passados; se não fornecidos, carrega de variáveis de ambiente.
        """
        self.model = model or os.getenv("LLM_MODEL_ID")
        apiKey = apiKey or os.getenv("LLM_API_KEY")
        baseUrl = baseUrl or os.getenv("LLM_BASE_URL")
        timeout = timeout or int(os.getenv("LLM_TIMEOUT", 60))

        if not all([self.model, apiKey, baseUrl]):
            raise ValueError("ID do modelo, chave de API e endereço do serviço devem ser fornecidos ou definidos no arquivo .env.")

        self.client = OpenAI(api_key=apiKey, base_url=baseUrl, timeout=timeout)

    def think(self, messages: List[Dict[str, str]], temperature: float = 0) -> str:
        """
        Chama o modelo de linguagem grande para pensar e retorna sua resposta.
        """
        print(f"🧠 Chamando o modelo {self.model}...")
        try:
            response = self.client.chat.completions.create(
                model=self.model,
                messages=messages,
                temperature=temperature,
                stream=True,
            )

            # Lidar com resposta em streaming
            print("✅ Resposta do modelo de linguagem grande bem-sucedida:")
            collected_content = []
            for chunk in response:
                content = chunk.choices[0].delta.content or ""
                print(content, end="", flush=True)
                collected_content.append(content)
            print()  # Nova linha após o fim da saída em streaming
            return "".join(collected_content)

        except Exception as e:
            print(f"❌ Erro ao chamar a API LLM: {e}")
            return None

# --- Exemplo de Uso do Cliente ---
if __name__ == '__main__':
    try:
        llmClient = HelloAgentsLLM()

        exampleMessages = [
            {"role": "system", "content": "Você é um assistente útil que escreve código Python."},
            {"role": "user", "content": "Escreva um algoritmo quicksort"}
        ]

        print("--- Chamando LLM ---")
        responseText = llmClient.think(exampleMessages)
        if responseText:
            print("\n\n--- Resposta Completa do Modelo ---")
            print(responseText)

    except ValueError as e:
        print(e)


>>>
--- Chamando LLM ---
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
Quicksort é um algoritmo de ordenação muito eficiente...
```



## 4.2 ReAct

Após preparar o cliente LLM, vamos construir o primeiro e mais clássico paradigma de agente: **ReAct (Reason + Act)**. O ReAct foi proposto por Shunyu Yao em 2022<sup>[1]</sup>. Sua ideia central é imitar como os humanos resolvem problemas, combinando explicitamente **Raciocínio** e **Ação** para formar um loop "pensar-agir-observar".

### 4.2.1 Fluxo de Trabalho do ReAct

Antes do ReAct surgir, os métodos principais podiam ser divididos em duas categorias: uma é o tipo "pensamento puro", como **Chain-of-Thought**, que pode guiar modelos a realizar raciocínio lógico complexo mas não pode interagir com o mundo externo e é propenso a alucinações factuais; a outra é o tipo "ação pura", onde modelos produzem diretamente ações para executar mas carecem de capacidades de planejamento e correção de erros.

A engenhosidade do ReAct está em reconhecer que **pensamento e ação são complementares**. O pensamento guia a ação, enquanto os resultados da ação por sua vez corrigem o pensamento. Para isso, o paradigma ReAct usa uma engenharia de prompt especial para guiar o modelo de modo que cada passo de sua saída siga uma trajetória fixa:

- **Thought (Pensamento):** Este é o "monólogo interior" do agente. Ele analisa a situação atual, decompõe tarefas, formula o próximo plano, ou reflete sobre os resultados do passo anterior.
- **Action (Ação):** Esta é a ação específica que o agente decide tomar, geralmente chamando uma ferramenta externa, como `Search['Último telefone da Huawei']`.
- **Observation (Observação):** Este é o resultado retornado da ferramenta externa após executar a `Action`, como um resumo dos resultados de busca ou um valor de retorno de API.

O agente continuará repetindo este loop **Thought -> Action -> Observation**, anexando novos resultados de observação ao histórico para formar um contexto em crescimento contínuo até que ele determine em `Thought` que encontrou a resposta final e então produz o resultado. Este processo forma uma sinergia poderosa: **o raciocínio torna as ações mais propositais, enquanto as ações fornecem base factual para o raciocínio.**

Podemos expressar formalmente este processo, como mostrado na Figura 4.1. Especificamente, em cada passo de tempo $t$, a política do agente (ou seja, o modelo de linguagem grande $\pi$) gera o pensamento atual $th_t$ e ação $a_t$ com base na questão inicial $q$ e na trajetória histórica de todos os passos anteriores "ação-observação" $((a_1,o_1),\dots,(a_{t-1},o_{t-1}))$:

$$\left(th_t,a_t\right)=\pi\left(q,(a_1,o_1),\ldots,(a_{t-1},o_{t-1})\right)$$

Subsequentemente, a ferramenta $T$ no ambiente executa a ação $a_t$ e retorna um novo resultado de observação $o_t$:

$$o_t = T(a_t)$$

Este loop continua, anexando novos pares $(a_t,o_t)$ ao histórico até que o modelo determine no pensamento $th_t$ que a tarefa está completa.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/4-figures/4-1.png" alt="Loop sinérgico Pensar-Agir-Observar no paradigma ReAct" width="90%"/>
  <p>Figura 4.1 Loop Sinérgico Pensar-Agir-Observar no Paradigma ReAct</p>
</div>

Este mecanismo é particularmente adequado para os seguintes cenários:

- **Tarefas que requerem conhecimento externo**: Como consultar informações em tempo real (clima, notícias, preços de ações), buscar conhecimento em domínios profissionais, etc.
- **Tarefas que requerem cálculos precisos**: Delegar problemas matemáticos a ferramentas de calculadora para evitar erros de cálculo do LLM.
- **Tarefas que requerem interação com API**: Como operar bancos de dados, chamar a API de um serviço para completar funções específicas.

Portanto, vamos construir um agente ReAct com a capacidade de **usar ferramentas externas** para responder perguntas que os modelos de linguagem grandes não podem responder diretamente apenas com sua própria base de conhecimento. Por exemplo: "Qual é o último telefone da Huawei? Quais são seus principais pontos de venda?" Esta questão requer que o agente entenda que precisa buscar online, chamar ferramentas para buscar resultados e resumir a resposta.

### 4.2.2 Definição e Implementação de Ferramentas

Se os modelos de linguagem grandes são o cérebro de um agente, então **Ferramentas** são suas "mãos e pés" para interagir com o mundo externo. Para permitir que o paradigma ReAct realmente resolva os problemas que definimos, o agente precisa da capacidade de chamar ferramentas externas.

Para o objetivo definido nesta seção—responder perguntas sobre "o último telefone da Huawei"—precisamos fornecer ao agente uma ferramenta de busca na web. Aqui escolhemos **SerpApi**, que fornece resultados de busca estruturados do Google através de uma API e pode retornar diretamente "caixas de resumo de resposta" ou informações precisas de gráficos de conhecimento.

Primeiro, você precisa instalar a biblioteca:

```bash
pip install google-search-results
```

Ao mesmo tempo, você precisa ir ao [site oficial da SerpApi](https://serpapi.com/) para registrar uma conta gratuita, obter sua chave de API e adicioná-la ao arquivo `.env` no diretório raiz do nosso projeto:

```bash
# Arquivo .env
# ... (Manter configuração LLM anterior)
SERPAPI_API_KEY="SUA_CHAVE_API_SERPAPI"
```

Em seguida, vamos definir e gerenciar esta ferramenta através de código. Vamos proceder passo a passo: primeiro implementar a funcionalidade central da ferramenta, depois construir um gerenciador de ferramentas geral.

(1) Implementando a Lógica Central da Ferramenta de Busca

Uma ferramenta bem definida deve conter os seguintes três elementos centrais:

1. **Nome**: Um identificador conciso e único para o agente chamar em `Action`, como `Search`.
2. **Descrição**: Uma descrição clara em linguagem natural explicando o propósito desta ferramenta. **Esta é a parte mais crítica de todo o mecanismo** porque o modelo de linguagem grande dependerá desta descrição para determinar quando usar qual ferramenta.
3. **Lógica de Execução**: A função ou método que realmente executa a tarefa.

Nossa primeira ferramenta é a função `search`, que recebe uma string de consulta e então retorna os resultados da busca.

```python
from serpapi import SerpApiClient

def search(query: str) -> str:
    """
    Uma ferramenta prática de busca na web baseada na SerpApi.
    Ela analisa inteligentemente os resultados da busca, priorizando respostas diretas ou informações de gráficos de conhecimento.
    """
    print(f"🔍 Executando busca na web [SerpApi]: {query}")
    try:
        api_key = os.getenv("SERPAPI_API_KEY")
        if not api_key:
            return "Erro: SERPAPI_API_KEY não configurada no arquivo .env."

        params = {
            "engine": "google",
            "q": query,
            "api_key": api_key,
            "gl": "cn",  # Código do país
            "hl": "zh-cn", # Código do idioma
        }

        client = SerpApiClient(params)
        results = client.get_dict()

        # Análise inteligente: priorizar encontrar a resposta mais direta
        if "answer_box_list" in results:
            return "\n".join(results["answer_box_list"])
        if "answer_box" in results and "answer" in results["answer_box"]:
            return results["answer_box"]["answer"]
        if "knowledge_graph" in results and "description" in results["knowledge_graph"]:
            return results["knowledge_graph"]["description"]
        if "organic_results" in results and results["organic_results"]:
            # Se não houver resposta direta, retorna resumos dos três primeiros resultados orgânicos
            snippets = [
                f"[{i+1}] {res.get('title', '')}\n{res.get('snippet', '')}"
                for i, res in enumerate(results["organic_results"][:3])
            ]
            return "\n\n".join(snippets)

        return f"Desculpe, nenhuma informação encontrada sobre '{query}'."

    except Exception as e:
        return f"Erro durante a busca: {e}"
```

No código acima, ele primeiro verifica se existe `answer_box` (caixa de resumo de resposta do Google) ou informações de `knowledge_graph` (gráfico de conhecimento). Se existir, retorna diretamente essas respostas mais precisas. Caso contrário, retorna resumos dos três primeiros resultados de busca regulares. Esta "análise inteligente" pode fornecer entrada de informações de maior qualidade para o LLM.

(2) Construindo um Executor de Ferramentas Geral

Quando um agente precisa usar múltiplas ferramentas (por exemplo, além de busca, também pode precisar de cálculo, consultas a banco de dados, etc.), precisamos de um gerenciador unificado para registrar e despachar essas ferramentas. Para isso, criamos uma classe `ToolExecutor`.

```python
from typing import Dict, Any

class ToolExecutor:
    """
    Um executor de ferramentas responsável por gerenciar e executar ferramentas.
    """
    def __init__(self):
        self.tools: Dict[str, Dict[str, Any]] = {}

    def registerTool(self, name: str, description: str, func: callable):
        """
        Registra uma nova ferramenta na caixa de ferramentas.
        """
        if name in self.tools:
            print(f"Aviso: Ferramenta '{name}' já existe e será sobrescrita.")
        self.tools[name] = {"description": description, "func": func}
        print(f"Ferramenta '{name}' registrada.")

    def getTool(self, name: str) -> callable:
        """
        Obtém a função de execução de uma ferramenta pelo nome.
        """
        return self.tools.get(name, {}).get("func")

    def getAvailableTools(self) -> str:
        """
        Obtém uma string de descrição formatada de todas as ferramentas disponíveis.
        """
        return "\n".join([
            f"- {name}: {info['description']}"
            for name, info in self.tools.items()
        ])

```

(3) Testando

Agora, vamos registrar a ferramenta `search` no `ToolExecutor` e simular uma chamada para verificar se todo o processo funciona corretamente.

```python
# --- Exemplo de Inicialização e Uso de Ferramenta ---
if __name__ == '__main__':
    # 1. Inicializar executor de ferramentas
    toolExecutor = ToolExecutor()

    # 2. Registrar nossa ferramenta de busca prática
    search_description = "Um mecanismo de busca na web. Use esta ferramenta quando precisar responder perguntas sobre eventos atuais, fatos e informações não encontradas em sua base de conhecimento."
    toolExecutor.registerTool("Search", search_description, search)

    # 3. Imprimir ferramentas disponíveis
    print("\n--- Ferramentas Disponíveis ---")
    print(toolExecutor.getAvailableTools())

    # 4. Chamada de Action do agente, desta vez perguntamos algo em tempo real
    print("\n--- Executar Action: Search['Qual é o modelo de GPU mais recente da NVIDIA'] ---")
    tool_name = "Search"
    tool_input = "Qual é o modelo de GPU mais recente da NVIDIA"

    tool_function = toolExecutor.getTool(tool_name)
    if tool_function:
        observation = tool_function(tool_input)
        print("--- Observation ---")
        print(observation)
    else:
        print(f"Erro: Ferramenta chamada '{tool_name}' não encontrada.")

>>>
Ferramenta 'Search' registrada.

--- Ferramentas Disponíveis ---
- Search: Um mecanismo de busca na web. Use esta ferramenta quando precisar responder perguntas sobre eventos atuais, fatos e informações não encontradas em sua base de conhecimento.

--- Executar Action: Search['Qual é o modelo de GPU mais recente da NVIDIA'] ---
🔍 Executando busca na web [SerpApi]: Qual é o modelo de GPU mais recente da NVIDIA
--- Observation ---
[1] Placas Gráficas Série GeForce RTX 50
As GPUs Série GeForce RTX™ 50 são alimentadas pela arquitetura NVIDIA Blackwell, trazendo nova jogabilidade para jogadores e criadores. A Série RTX 50 tem poder de computação AI poderoso, trazendo experiência aprimorada e gráficos mais realistas.

[2] Compare Placas Gráficas Série GeForce Última Geração e Geração Anterior
Compare as placas gráficas série RTX 30 mais recentes com série RTX 20 anterior, série GTX 10 e 900. Veja especificações, recursos, suporte técnico, etc.

[3] Placas Gráficas GeForce | NVIDIA
DRIVE AGX. Poder de computação poderoso dentro do veículo para sistemas de veículos inteligentes impulsionados por AI · Clara AGX. Computação AI para dispositivos médicos inovadores e imagem. Jogos e Criação. GeForce. Explore placas gráficas, soluções de jogos, AI...
```

Até agora, equipamos o agente com uma ferramenta `Search` que se conecta à internet do mundo real, fornecendo uma base sólida para o loop ReAct subsequente.



### 4.2.3 Implementação em Código do Agente ReAct

Agora, vamos montar todos os componentes independentes—o cliente LLM e o executor de ferramentas—para construir um agente ReAct completo. Vamos encapsular sua lógica central através de uma classe `ReActAgent`. Para facilitar o entendimento, vamos dividir o processo de implementação desta classe nas seguintes partes-chave para explicação.

(1) Design do Prompt do Sistema

O prompt é a pedra angular de todo o mecanismo ReAct, fornecendo instruções operacionais para o modelo de linguagem grande. Precisamos projetar cuidadosamente um modelo que inserirá dinamicamente ferramentas disponíveis, perguntas dos usuários e o histórico de interação de passos intermediários.

```bash
# Modelo de Prompt ReAct
REACT_PROMPT_TEMPLATE = """
Observe que você é um assistente inteligente capaz de chamar ferramentas externas.

As ferramentas disponíveis são as seguintes:
{tools}

Por favor, responda estritamente no seguinte formato:

Thought: Seu processo de pensamento, usado para analisar problemas, decompor tarefas e planejar a próxima ação.
Action: A ação que você decide tomar, deve estar em um dos seguintes formatos:
- {{tool_name}}[{{tool_input}}]`: Chamar uma ferramenta disponível.
- `Finish[resposta final]`: Quando você acredita que obteve a resposta final.
- Quando você tiver coletado informações suficientes para responder à pergunta final do usuário, você deve usar `finish(answer="...")` após o campo Action: para produzir a resposta final.

Agora, por favor comece a resolver o seguinte problema:
Question: {question}
History: {history}
"""
```

Este modelo define a especificação para interação entre o agente e o LLM:

- **Definição de Papel**: "Você é um assistente inteligente capaz de chamar ferramentas externas" define o papel do LLM.
- **Lista de Ferramentas (`{tools}`)**: Informa ao LLM quais "mãos e pés" ele tem disponíveis.
- **Convenção de Formato (`Thought`/`Action`)**: Esta é a parte mais importante, forçando a saída do LLM a ser estruturada para que possamos analisar precisamente sua intenção através de código.
- **Contexto Dinâmico (`{question}`/`{history}`)**: Injeta a pergunta original do usuário e o histórico de interação continuamente acumulado, permitindo que o LLM tome decisões baseadas no contexto completo.

(2) Implementação do Loop Central

O núcleo do `ReActAgent` é um loop que continuamente "formata prompt -> chama LLM -> executa ação -> integra resultados" até que a tarefa seja completa ou o limite máximo de passos seja atingido.

```python
class ReActAgent:
    def __init__(self, llm_client: HelloAgentsLLM, tool_executor: ToolExecutor, max_steps: int = 5):
        self.llm_client = llm_client
        self.tool_executor = tool_executor
        self.max_steps = max_steps
        self.history = []

    def run(self, question: str):
        """
        Executa o agente ReAct para responder uma pergunta.
        """
        self.history = [] # Resetar histórico para cada execução
        current_step = 0

        while current_step < self.max_steps:
            current_step += 1
            print(f"--- Passo {current_step} ---")

            # 1. Formatar prompt
            tools_desc = self.tool_executor.getAvailableTools()
            history_str = "\n".join(self.history)
            prompt = REACT_PROMPT_TEMPLATE.format(
                tools=tools_desc,
                question=question,
                history=history_str
            )

            # 2. Chamar LLM para pensar
            messages = [{"role": "user", "content": prompt}]
            response_text = self.llm_client.think(messages=messages)

            if not response_text:
                print("Erro: LLM falhou ao retornar uma resposta válida.")
                break

            # ... (Passos subsequentes de análise, execução e integração)

```

O método `run` é o ponto de entrada do agente. Seu loop `while` constitui o corpo principal do paradigma ReAct, e o parâmetro `max_steps` é uma válvula de segurança importante para evitar que o agente caia em um loop infinito e esgote recursos.

(3) Implementação do Analisador de Saída

O LLM retorna texto simples, e precisamos extrair precisamente `Thought` e `Action` dele. Isso é realizado através de várias funções auxiliares de análise, que normalmente usam expressões regulares.

```python
# (Estes métodos fazem parte da classe ReActAgent)
    def _parse_output(self, text: str):
        """Analisa a saída do LLM para extrair Thought e Action."""
        thought_match = re.search(r"Thought: (.*)", text)
        action_match = re.search(r"Action: (.*)", text)
        thought = thought_match.group(1).strip() if thought_match else None
        action = action_match.group(1).strip() if action_match else None
        return thought, action

    def _parse_action(self, action_text: str):
        """Analisa a string Action para extrair nome da ferramenta e entrada."""
        match = re.match(r"(\w+)\[(.*)\]", action_text)
        if match:
            return match.group(1), match.group(2)
        return None, None
```

- `_parse_output`: Responsável por separar as duas partes principais `Thought` e `Action` da resposta completa do LLM.
- `_parse_action`: Responsável por analisar ainda mais a string `Action`, por exemplo, extraindo o nome da ferramenta `Search` e a entrada da ferramenta `Último telefone da Huawei` de `Search[Último telefone da Huawei]`.

(4) Invocação e Execução de Ferramenta

```python
# (Esta lógica está dentro do loop while do método run)
            # 3. Analisar saída do LLM
            thought, action = self._parse_output(response_text)

            if thought:
                print(f"Thought: {thought}")

            if not action:
                print("Aviso: Falha ao analisar Action válida, processo terminado.")
                break

            # 4. Executar Action
            if action.startswith("Finish"):
                # Se for uma instrução Finish, extrair resposta final e terminar
                final_answer = re.match(r"Finish\[(.*)\]", action).group(1)
                print(f"🎉 Resposta Final: {final_answer}")
                return final_answer

            tool_name, tool_input = self._parse_action(action)
            if not tool_name or not tool_input:
                # ... Lidar com formato de Action inválido ...
                continue

            print(f"🎬 Action: {tool_name}[{tool_input}]")

            tool_function = self.tool_executor.getTool(tool_name)
            if not tool_function:
                observation = f"Erro: Ferramenta chamada '{tool_name}' não encontrada."
            else:
                observation = tool_function(tool_input) # Chamar ferramenta real

```

Este código é o centro de execução de `Action`. Ele primeiro verifica se é uma instrução `Finish`; se for, o processo termina. Caso contrário, obtém a função de ferramenta correspondente através de `tool_executor` e a executa para obter a `observation`.

(5) Integração de Resultados de Observação

O último passo, e a chave para formar um loop fechado, é adicionar a própria `Action` e a `Observation` após a execução da ferramenta de volta ao histórico, fornecendo novo contexto para o próximo loop.

```python
# (Esta lógica segue a invocação da ferramenta, no final do loop while)
            print(f"👀 Observation: {observation}")

            # Adicionar Action e Observation desta rodada ao histórico
            self.history.append(f"Action: {action}")
            self.history.append(f"Observation: {observation}")

        # Loop termina
        print("Número máximo de passos atingido, processo terminado.")
        return None
```

Ao anexar `Observation` a `self.history`, o agente pode "ver" os resultados da ação anterior ao gerar o prompt na próxima rodada, e conduzir novo pensamento e planejamento de acordo.

(6) Instância de Execução e Análise

Combinando todas as partes acima, obtemos a classe `ReActAgent` completa. A instância de código de execução completa pode ser encontrada na pasta `code` do repositório de código que acompanha este livro.

Abaixo está um registro de execução real:

```
Ferramenta 'Search' registrada.

--- Passo 1 ---
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
Thought: Para responder esta pergunta, preciso buscar o modelo de telefone mais recente lançado pela Huawei e seus recursos principais. Esta informação pode estar fora da minha base de conhecimento existente, então preciso usar um mecanismo de busca para obter os dados mais recentes.
Action: Search[modelo de telefone mais recente da Huawei e principais pontos de venda]
🤔 Thought: Para responder esta pergunta, preciso buscar o modelo de telefone mais recente lançado pela Huawei e seus recursos principais. Esta informação pode estar fora da minha base de conhecimento existente, então preciso usar um mecanismo de busca para obter os dados mais recentes.
🎬 Action: Search[modelo de telefone mais recente da Huawei e principais pontos de venda]
🔍 Executando busca na web [SerpApi]: modelo de telefone mais recente da Huawei e principais pontos de venda
👀 Observation: [1] Telefones Huawei - Site Oficial da Huawei
Smartphones ; Série Mate. Flagship Extraordinário · HUAWEI Mate XTs. Mestre Extraordinário ; Série Pura. Imagem Pioneira · HUAWEI Pura 80 Pro+ ; Série Pocket. Novo Capítulo Estético. HUAWEI Pocket 2 ; Série nova. Retrato Profissional.

[2] Qual telefone Huawei tem melhor custo-benefício em 2025? Recomendações de telefones Huawei e análise de mercado...
Agora o maior ponto de venda dos telefones Huawei é apenas o sistema HarmonyOS, e a fé controversa na marca. Aqui estão vários telefones da série Huawei que valem a pena comprar, escolha de acordo com diferentes orçamentos:. O mais popular da Huawei, também equipado com...

[3] Qual novo telefone Huawei tem melhor custo-benefício em 2025? 10 recomendações de novos telefones Huawei
Escolher Huawei principalmente recomenda telefones de ponta, Mate 70 e série Pura 70 são os modelos flagship mais recentes. HUAWEI Mate 70. As vantagens são, configuração de fotografia ainda é de nível superior, cobertura de alcance focal completo, adequado para fotografia profissional, excelente acabamento, resistência a quedas ao ar livre...

--- Passo 2 ---
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
Thought: Com base nos resultados da busca, os modelos flagship mais recentes da Huawei incluem Mate 70 e Pura 80 Pro+. Para determinar o modelo "mais recente" e seus principais pontos de venda, vou focar nesta informação. Dos links fornecidos, tanto a série Mate 70 quanto a Pura 80 Pro+ são produtos lançados recentemente, mas qual é "mais recente" precisa de confirmação adicional. Enquanto isso, posso extrair seus principais pontos de venda desta informação.
Action: Finish[De acordo com as informações mais recentes, os telefones mais recentes da Huawei podem ser HUAWEI Pura 80 Pro+ ou HUAWEI Mate 70. Entre eles, os principais pontos de venda do HUAWEI Mate 70 incluem configuração de fotografia de nível superior, cobertura de alcance focal completo, adequado para fotografia profissional, excelente acabamento e boa resistência a quedas ao ar livre. Enquanto o HUAWEI Pura 80 Pro+ enfatiza tecnologia de imagem pioneira.]
🤔 Thought: Com base nos resultados da busca, os modelos flagship mais recentes da Huawei incluem Mate 70 e Pura 80 Pro+. Para determinar o modelo "mais recente" e seus principais pontos de venda, vou focar nesta informação. Dos links fornecidos, tanto a série Mate 70 quanto a Pura 80 Pro+ são produtos lançados recentemente, mas qual é "mais recente" precisa de confirmação adicional. Enquanto isso, posso extrair seus principais pontos de venda desta informação.
🎉 Resposta Final: De acordo com as informações mais recentes, os telefones mais recentes da Huawei podem ser HUAWEI Pura 80 Pro+ ou HUAWEI Mate 70. Entre eles, os principais pontos de venda do HUAWEI Mate 70 incluem configuração de fotografia de nível superior, cobertura de alcance focal completo, adequado para fotografia profissional, excelente acabamento e boa resistência a quedas ao ar livre. Enquanto o HUAWEI Pura 80 Pro+ enfatiza tecnologia de imagem pioneira.
```

Da saída acima, podemos ver que o agente demonstra claramente sua cadeia de pensamento: primeiro percebe que seu conhecimento é insuficiente e precisa usar a ferramenta de busca; então, raciocina e resume com base nos resultados da busca, chegando à resposta final em dois passos.

Vale notar que como o conhecimento do modelo e as informações da internet estão constantemente sendo atualizados, seus resultados de execução podem não ser exatamente os mesmos que este. Em 8 de setembro de 2025, quando esta seção foi escrita, o HUAWEI Mate 70 e o HUAWEI Pura 80 Pro+ mencionados nos resultados de busca eram de fato os telefones da série flagship mais recentes da Huawei naquela época. Isso demonstra plenamente a capacidade poderosa do paradigma ReAct em lidar com questões sensíveis ao tempo.

### 4.2.4 Características, Limitações e Técnicas de Depuração do ReAct

Ao implementar um agente ReAct em primeira mão, não apenas dominamos seu fluxo de trabalho mas também devemos ter uma compreensão mais profunda de seus mecanismos internos. Qualquer paradigma técnico tem seus destaques e áreas para melhoria; esta seção resumirá o ReAct.

(1) Principais Características do ReAct

1. **Alta Interpretabilidade**: Uma das maiores vantagens do ReAct é a transparência. Através da cadeia de `Thought`, podemos ver claramente a "jornada mental" do agente em cada passo—por que escolheu esta ferramenta e o que planeja fazer a seguir. Isso é crucial para entender, confiar e depurar o comportamento do agente.
2. **Capacidade de Planejamento Dinâmico e Correção de Erros**: Diferente de paradigmas que geram planos completos de uma vez, o ReAct é "dar um passo, olhar um passo." Ele ajusta dinamicamente os subsequentes `Thought` e `Action` com base na `Observation` obtida do mundo externo em cada passo. Se os resultados de busca anteriores forem insatisfatórios, ele pode corrigir os termos de busca no próximo passo e tentar novamente.
3. **Capacidade de Sinergia de Ferramentas**: O paradigma ReAct combina naturalmente a capacidade de raciocínio dos modelos de linguagem grandes com a capacidade de execução de ferramentas externas. Os LLMs são responsáveis por estratégias (planejamento e raciocínio), as ferramentas são responsáveis por resolver problemas específicos (buscar, calcular), e os dois trabalham sinergicamente, quebrando as limitações inerentes dos LLMs únicos em atualidade do conhecimento, precisão computacional, etc.

(2) Limitações Inerentes do ReAct

1. **Forte Dependência das Próprias Capacidades do LLM**: O sucesso do processo ReAct depende muito das capacidades abrangentes do LLM subjacente. Se a capacidade de raciocínio lógico, capacidade de seguir instruções ou capacidade de saída formatada do LLM for insuficiente, é fácil produzir planejamento errado na fase de `Thought` ou gerar instruções que não se conformam ao formato na fase de `Action`, causando a interrupção de todo o processo.
2. **Problemas de Eficiência de Execução**: Devido à sua natureza passo a passo, completar uma tarefa geralmente requer múltiplas chamadas ao LLM. Cada chamada é acompanhada por latência de rede e custo computacional. Para tarefas complexas que requerem muitos passos, este loop serial "pensar-agir" pode levar a alto tempo total e custo.
3. **Fragilidade do Prompt**: A operação estável de todo o mecanismo é construída sobre um modelo de prompt cuidadosamente projetado. Qualquer mudança menor no modelo, mesmo diferenças na redação, pode afetar o comportamento do LLM. Adicionalmente, nem todos os modelos podem seguir consistentemente formatos predefinidos, aumentando a incerteza em aplicações práticas.
4. **Pode Cair em Ótimos Locais**: O modo de tomada de decisão passo a passo significa que o agente carece de um plano global de longo prazo. Ele pode escolher um caminho que parece correto a curto prazo mas não é ótimo a longo prazo devido à `Observation` imediata, ou até mesmo cair em um loop "girando no mesmo lugar" em alguns casos.

(3) Técnicas de Depuração

Quando seu agente ReAct construído se comporta inesperadamente, você pode depurar dos seguintes aspectos:

- **Verificar Prompt Completo**: Antes de cada chamada ao LLM, imprima o prompt formatado final completo contendo todo o histórico. Esta é a maneira mais direta de rastrear a fonte das decisões do LLM.
- **Analisar Saída Bruta**: Quando a análise de saída falha (por exemplo, expressões regulares não corresponderam a `Action`), certifique-se de imprimir o texto bruto não processado retornado pelo LLM. Isso pode ajudá-lo a determinar se o LLM não seguiu o formato ou se sua lógica de análise está errada.
- **Verificar Entrada e Saída da Ferramenta**: Verifique se o `tool_input` gerado pelo agente está no formato esperado pela função da ferramenta, e também certifique-se de que a `observation` retornada pela ferramenta está em um formato que o agente pode entender e processar.
- **Ajustar Exemplos no Prompt (Few-shot Prompting)**: Se o modelo frequentemente comete erros, você pode adicionar um ou dois casos completos de sucesso "Thought-Action-Observation" no prompt para guiar o modelo a seguir melhor suas instruções através de exemplos.
- **Tentar Modelos ou Parâmetros Diferentes**: Mudar para um modelo mais capaz ou ajustar o parâmetro `temperature` (geralmente definido para 0 para garantir determinismo de saída) às vezes pode resolver o problema diretamente.

## 4.3 Plan-and-Solve

Após dominar o ReAct, este paradigma de agente reativo e de tomada de decisão passo a passo, vamos explorar a seguir um método com estilo muito diferente mas igualmente poderoso: **Plan-and-Solve**. Como o nome sugere, este paradigma divide explicitamente o processamento de tarefas em duas fases: **Planejar primeiro, depois Resolver**.

Se o ReAct é como um detetive experiente que raciocina passo a passo com base em pistas na cena (Observation) e ajusta a direção da investigação a qualquer momento; então Plan-and-Solve é mais como um arquiteto que deve primeiro desenhar um projeto completo (Plan) antes de começar a construção, depois construir estritamente de acordo com o projeto (Solve). De fato, muitos modos Agent de ferramentas de modelos grandes que usamos agora incorporam este padrão de design.

### 4.3.1 Princípio de Funcionamento do Plan-and-Solve

Plan-and-Solve Prompting foi proposto por Lei Wang em 2023<sup>[2]</sup>. Sua motivação central é resolver o problema de que chain-of-thought facilmente "sai dos trilhos" ao lidar com problemas complexos de múltiplos passos.

Diferente do ReAct, que integra pensamento e ação em cada passo, Plan-and-Solve desacopla todo o processo em duas fases centrais, como mostrado na Figura 4.2:

1. **Fase de Planejamento**: Primeiro, o agente recebe a pergunta completa do usuário. Sua primeira tarefa não é resolver diretamente o problema ou chamar ferramentas, mas **decompor o problema e formular um plano de ação claro, passo a passo**. Este plano em si é o produto de uma chamada ao modelo de linguagem grande.
2. **Fase de Resolução**: Após obter o plano completo, o agente entra na fase de execução. Ele **executará estritamente de acordo com os passos do plano, um por um**. A execução de cada passo pode ser uma chamada LLM independente ou processamento dos resultados do passo anterior, até que todos os passos do plano sejam completados e a resposta final seja obtida.

Esta estratégia "planejar antes de agir" permite que o agente mantenha maior consistência de objetivos ao lidar com tarefas complexas que requerem planejamento de longo prazo, evitando se perder em passos intermediários.

Podemos expressar formalmente este processo de duas fases. Primeiro, o modelo de planejamento $\pi_{\text{plan}}$ gera um plano $P = (p_1, p_2, \dots, p_n)$ contendo $n$ passos com base na pergunta original $q$:

$$
P = \pi_{\text{plan}}(q)
$$

Subsequentemente, na fase de execução, o modelo de execução $\pi_{\text{solve}}$ completará os passos do plano um por um. Para o $i$-ésimo passo, a geração de sua solução $s_i$ dependerá da pergunta original $q$, do plano completo $P$, e dos resultados de execução de todos os passos anteriores $(s_1, \dots, s_{i-1})$:

$$
s_i = \pi_{\text{solve}}(q, P, (s_1, \dots, s_{i-1}))
$$

A resposta final é o resultado de execução do último passo $s_n$.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/4-figures/4-2.png" alt="Fluxo de trabalho de duas fases do paradigma Plan-and-Solve" width="90%"/>
  <p>Figura 4.2 Fluxo de Trabalho de Duas Fases do Paradigma Plan-and-Solve</p>
</div>

Plan-and-Solve é especialmente adequado para tarefas complexas com forte estrutura que podem ser claramente decompostas, como:

- **Problemas de palavras matemáticos de múltiplos passos**: Precisa primeiro listar passos de cálculo, depois resolver um por um.
- **Escrita de relatórios integrando múltiplas fontes de informação**: Precisa primeiro planejar a estrutura do relatório (introdução, fonte de dados A, fonte de dados B, resumo), depois preencher o conteúdo um por um.
- **Tarefas de geração de código**: Precisa primeiro conceber a estrutura de funções, classes e módulos, depois implementar um por um.

### 4.3.2 Fase de Planejamento

Para destacar as vantagens do paradigma Plan-and-Solve em tarefas de raciocínio estruturado, não usaremos ferramentas mas completaremos uma tarefa de raciocínio através de design de prompt.

A característica deste tipo de tarefa é que a resposta não pode ser obtida através de uma única consulta ou cálculo; o problema deve primeiro ser decomposto em uma série de sub-passos logicamente coerentes, depois resolvidos em ordem. Isso aproveita precisamente a capacidade central do Plan-and-Solve de "planejar primeiro, executar depois."

**Nosso problema alvo é:** "Uma loja de frutas vendeu 15 maçãs na segunda-feira. O número de maçãs vendidas na terça-feira foi o dobro da segunda-feira. O número vendido na quarta-feira foi 5 a menos que terça-feira. Quantas maçãs foram vendidas no total nesses três dias?"

Este problema não é particularmente difícil para modelos de linguagem grandes, mas contém uma cadeia lógica clara para referência. Para alguns quebra-cabeças lógicos reais, se o modelo grande não pode raciocinar respostas precisas com alta qualidade, você pode consultar este padrão de design para projetar seu próprio Agente para completar a tarefa. O agente precisa:

1. **Fase de Planejamento**: Primeiro, decompor o problema em três passos de cálculo independentes (calcular vendas de terça-feira, calcular vendas de quarta-feira, calcular vendas totais).
2. **Fase de Execução**: Então, seguir estritamente o plano, executar cálculos passo a passo, e usar o resultado de cada passo como entrada para o próximo passo, finalmente obtendo o total.

O objetivo da fase de planejamento é ter o modelo de linguagem grande receber o problema original e produzir um plano claro, passo a passo. Este plano deve ser estruturado para que nosso código possa facilmente analisar e executar um por um. Portanto, o prompt que projetamos precisa dizer claramente ao modelo seu papel e tarefa e fornecer um exemplo do formato de saída.

````python
PLANNER_PROMPT_TEMPLATE = """
Você é um especialista em planejamento de IA de primeira linha. Sua tarefa é decompor problemas complexos colocados pelos usuários em um plano de ação consistindo de múltiplos passos simples.
Por favor, certifique-se de que cada passo no plano seja uma subtarefa independente e executável e seja estritamente organizado em ordem lógica.
Sua saída deve ser uma lista Python, onde cada elemento é uma string descrevendo uma subtarefa.

Question: {question}

Por favor, produza estritamente seu plano no seguinte formato, com ```python e ``` como prefixo e sufixo sendo necessários:
```python
["Passo 1", "Passo 2", "Passo 3", ...]
```
"""
````

Este prompt garante qualidade e estabilidade de saída através dos seguintes pontos:
- **Configuração de Papel**: "Especialista em planejamento de IA de primeira linha" ativa as capacidades profissionais do modelo.
- **Descrição da Tarefa**: Define claramente o objetivo de "decompor problemas."
- **Restrição de Formato**: Força a saída a ser uma string em formato de lista Python, o que simplifica muito o trabalho de análise de código subsequente, tornando-o mais estável e confiável do que analisar linguagem natural.

Em seguida, encapsulamos esta lógica de prompt em uma classe `Planner`, que também é nosso planejador.

```python
# Assume que a classe HelloAgentsLLM em llm_client.py já está definida
# from llm_client import HelloAgentsLLM

class Planner:
    def __init__(self, llm_client):
        self.llm_client = llm_client

    def plan(self, question: str) -> list[str]:
        """
        Gera um plano de ação baseado na pergunta do usuário.
        """
        prompt = PLANNER_PROMPT_TEMPLATE.format(question=question)

        # Para gerar um plano, construímos uma lista de mensagens simples
        messages = [{"role": "user", "content": prompt}]

        print("--- Gerando Plano ---")
        # Usa saída em streaming para obter o plano completo
        response_text = self.llm_client.think(messages=messages) or ""

        print(f"✅ Plano Gerado:\n{response_text}")

        # Analisa a saída de string da lista pelo LLM
        try:
            # Encontra conteúdo entre ```python e ```
            plan_str = response_text.split("```python")[1].split("```")[0].strip()
            # Usa ast.literal_eval para executar com segurança a string e convertê-la em uma lista Python
            plan = ast.literal_eval(plan_str)
            return plan if isinstance(plan, list) else []
        except (ValueError, SyntaxError, IndexError) as e:
            print(f"❌ Erro ao analisar plano: {e}")
            print(f"Resposta bruta: {response_text}")
            return []
        except Exception as e:
            print(f"❌ Erro desconhecido ocorreu ao analisar plano: {e}")
            return []
```

### 4.3.3 Executor e Gerenciamento de Estado

Após o planejador (`Planner`) gerar um projeto de ação claro, precisamos de um executor (`Executor`) para completar as tarefas no plano uma por uma. O executor não é apenas responsável por chamar o modelo de linguagem grande para resolver cada sub-problema mas também desempenha um papel crucial: **gerenciamento de estado**. Ele deve registrar os resultados de execução de cada passo e fornecê-los como contexto para passos subsequentes, garantindo que a informação flua suavemente por toda a cadeia de tarefas.

O prompt do executor é diferente do planejador. Seu objetivo não é decompor problemas mas **focar em resolver o passo atual baseado no contexto existente**. Portanto, o prompt precisa incluir as seguintes informações-chave:

- **Pergunta Original**: Garante que o modelo sempre entenda o objetivo final.
- **Plano Completo**: Permite que o modelo entenda a posição do passo atual em toda a tarefa.
- **Passos Históricos e Resultados**: Fornece trabalho completado até agora como entrada direta para o passo atual.
- **Passo Atual**: Instrui claramente o modelo sobre qual tarefa específica ele precisa resolver agora.

```python
EXECUTOR_PROMPT_TEMPLATE = """
Você é um especialista em execução de IA de primeira linha. Sua tarefa é seguir estritamente o plano dado e resolver o problema passo a passo.
Você receberá a pergunta original, o plano completo, e os passos e resultados completados até agora.
Por favor, foque em resolver o "passo atual" e produza apenas a resposta final para aquele passo, sem quaisquer explicações ou diálogos adicionais.

# Pergunta Original:
{question}

# Plano Completo:
{plan}

# Passos Históricos e Resultados:
{history}

# Passo Atual:
{current_step}

Por favor, produza apenas a resposta para o "passo atual":
"""
```

Encapsulamos a lógica de execução na classe `Executor`. Esta classe percorrerá o plano, chamará o LLM e manterá um histórico (estado).

```python
class Executor:
    def __init__(self, llm_client):
        self.llm_client = llm_client

    def execute(self, question: str, plan: list[str]) -> str:
        """
        Executa passo a passo de acordo com o plano e resolve o problema.
        """
        history = "" # String para armazenar passos históricos e resultados

        print("\n--- Executando Plano ---")

        for i, step in enumerate(plan):
            print(f"\n-> Executando passo {i+1}/{len(plan)}: {step}")

            prompt = EXECUTOR_PROMPT_TEMPLATE.format(
                question=question,
                plan=plan,
                history=history if history else "Nenhum", # Se for o primeiro passo, histórico está vazio
                current_step=step
            )

            messages = [{"role": "user", "content": prompt}]

            response_text = self.llm_client.think(messages=messages) or ""

            # Atualiza histórico para o próximo passo
            history += f"Passo {i+1}: {step}\nResultado: {response_text}\n\n"

            print(f"✅ Passo {i+1} completado, resultado: {response_text}")

        # Após o loop terminar, a resposta do último passo é a resposta final
        final_answer = response_text
        return final_answer
```

Agora construímos separadamente o `Planner` responsável por "planejar" e o `Executor` responsável por "executar." O último passo é integrar esses dois componentes em um agente unificado `PlanAndSolveAgent` e dar a ele capacidades completas de resolução de problemas. Vamos criar uma classe principal `PlanAndSolveAgent` cuja responsabilidade é muito clara: receber um cliente LLM, inicializar planejador e executor internos, e fornecer um método `run` simples para iniciar todo o processo.

```python
class PlanAndSolveAgent:
    def __init__(self, llm_client):
        """
        Inicializa o agente e cria instâncias de planejador e executor.
        """
        self.llm_client = llm_client
        self.planner = Planner(self.llm_client)
        self.executor = Executor(self.llm_client)

    def run(self, question: str):
        """
        Executa o processo completo do agente: planejar primeiro, depois executar.
        """
        print(f"\n--- Começando a Processar Pergunta ---\nPergunta: {question}")

        # 1. Chamar planejador para gerar plano
        plan = self.planner.plan(question)

        # Verifica se o plano foi gerado com sucesso
        if not plan:
            print("\n--- Tarefa Terminada --- \nIncapaz de gerar plano de ação válido.")
            return

        # 2. Chamar executor para executar plano
        final_answer = self.executor.execute(question, plan)

        print(f"\n--- Tarefa Completada ---\nResposta Final: {final_answer}")
```

O design desta classe `PlanAndSolveAgent` incorpora o princípio de "composição sobre herança." Ela não contém lógica complexa em si mas age como um orquestrador, chamando claramente seus componentes internos para completar tarefas.

### 4.3.4 Instância de Execução e Análise

O código completo também pode ser encontrado na pasta `code` do repositório de código que acompanha este livro; aqui demonstramos apenas os resultados finais.

````bash
--- Começando a Processar Pergunta ---
Pergunta: Uma loja de frutas vendeu 15 maçãs na segunda-feira. O número de maçãs vendidas na terça-feira foi o dobro da segunda-feira. O número vendido na quarta-feira foi 5 a menos que terça-feira. Quantas maçãs foram vendidas no total nesses três dias?
--- Gerando Plano ---
🧠 Chamando o modelo xxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
```python
["Calcular vendas de maçãs na segunda-feira: 15", "Calcular vendas de maçãs na terça-feira: quantidade da segunda-feira × 2 = 15 × 2 = 30", "Calcular vendas de maçãs na quarta-feira: quantidade da terça-feira - 5 = 30 - 5 = 25", "Calcular vendas totais de três dias: segunda-feira + terça-feira + quarta-feira = 15 + 30 + 25 = 70"]
```
✅ Plano Gerado:
```python
["Calcular vendas de maçãs na segunda-feira: 15", "Calcular vendas de maçãs na terça-feira: quantidade da segunda-feira × 2 = 15 × 2 = 30", "Calcular vendas de maçãs na quarta-feira: quantidade da terça-feira - 5 = 30 - 5 = 25", "Calcular vendas totais de três dias: segunda-feira + terça-feira + quarta-feira = 15 + 30 + 25 = 70"]
```

--- Executando Plano ---

-> Executando passo 1/4: Calcular vendas de maçãs na segunda-feira: 15
🧠 Chamando o modelo xxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
15
✅ Passo 1 completado, resultado: 15

-> Executando passo 2/4: Calcular vendas de maçãs na terça-feira: quantidade da segunda-feira × 2 = 15 × 2 = 30
🧠 Chamando o modelo xxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
30
✅ Passo 2 completado, resultado: 30

-> Executando passo 3/4: Calcular vendas de maçãs na quarta-feira: quantidade da terça-feira - 5 = 30 - 5 = 25
🧠 Chamando o modelo xxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
25
✅ Passo 3 completado, resultado: 25

-> Executando passo 4/4: Calcular vendas totais de três dias: segunda-feira + terça-feira + quarta-feira = 15 + 30 + 25 = 70
🧠 Chamando o modelo xxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
70
✅ Passo 4 completado, resultado: 70

--- Tarefa Completada ---
Resposta Final: 70
````

Do log de saída acima, podemos ver claramente o fluxo de trabalho do paradigma Plan-and-Solve:

1. **Fase de Planejamento**: O agente primeiro chama `Planner` e decompõe com sucesso o problema de palavra complexo em uma lista Python contendo quatro passos lógicos. Este plano estruturado estabelece a base para execução subsequente.
2. **Fase de Execução**: `Executor` executa estritamente passo a passo de acordo com o plano gerado. Em cada passo, ele usa resultados históricos como contexto, garantindo transferência correta de informação (por exemplo, passo 2 usa corretamente o resultado do passo 1 "15", e passo 3 também usa corretamente o resultado do passo 2 "30").
3. **Resultado**: Todo o processo é logicamente claro com passos explícitos, e o agente chega com precisão à resposta correta "70".

## 4.4 Reflexão

Nos paradigmas ReAct e Plan-and-Solve que já implementamos, uma vez que o agente completa uma tarefa, seu fluxo de trabalho termina. No entanto, as respostas iniciais que eles geram, sejam trajetórias de ação ou resultados finais, podem conter erros ou ter espaço para melhoria. A ideia central do mecanismo de Reflexão é introduzir um **loop de autocorreção pós-fato** para o agente, permitindo que ele revise seu trabalho, descubra deficiências e otimize iterativamente, assim como os humanos fazem.

### 4.4.1 Ideia Central do Mecanismo de Reflexão

A inspiração para o mecanismo de Reflexão vem do processo de aprendizagem humana: revisamos após completar um primeiro rascunho e verificamos após resolver um problema de matemática. Esta ideia está incorporada em múltiplos estudos, como o framework Reflexion proposto por Shinn, Noah em 2023<sup>[3]</sup>. Seu fluxo de trabalho central pode ser resumido como um loop conciso de três passos: **Executar -> Refletir -> Refinar**.

1. **Execução**: Primeiro, o agente tenta completar a tarefa usando métodos familiares (como ReAct ou Plan-and-Solve), gerando uma solução preliminar ou trajetória de ação. Isso pode ser visto como um "primeiro rascunho."
2. **Reflexão**: Em seguida, o agente entra na fase de reflexão. Ele chama uma instância independente de modelo de linguagem grande, ou uma com prompts especiais, para desempenhar o papel de um "revisor." Este "revisor" examina o "primeiro rascunho" gerado no primeiro passo e o avalia de múltiplas dimensões, como:
   - **Erros Factuais**: Há conteúdo que contradiz senso comum ou fatos conhecidos?
   - **Falhas Lógicas**: Há inconsistências ou contradições no processo de raciocínio?
   - **Problemas de Eficiência**: Há um caminho mais direto, mais conciso para completar a tarefa?
   - **Informação Faltando**: Algumas restrições-chave ou aspectos do problema são negligenciados? Baseado na avaliação, ele gera **Feedback** estruturado, apontando problemas específicos e sugestões de melhoria.
3. **Refinamento**: Finalmente, o agente usa o "primeiro rascunho" e "feedback" como novo contexto, chama o modelo de linguagem grande novamente, e pede para ele revisar o primeiro rascunho baseado no conteúdo do feedback, gerando um "rascunho revisado" mais completo.

Como mostrado na Figura 4.3, este loop pode ser repetido múltiplas vezes até que a fase de reflexão não encontre mais novos problemas ou atinja um limite de iteração predefinido. Podemos expressar formalmente este processo de otimização iterativa. Assumindo que $O_i$ é a saída produzida pela $i$-ésima iteração ($O_0$ é a saída inicial), o modelo de reflexão $\pi_{\text{reflect}}$ gera feedback $F_i$ para $O_i$:
$$
F_i = \pi_{\text{reflect}}(\text{Tarefa}, O_i)
$$
Subsequentemente, o modelo de refinamento $\pi_{\text{refine}}$ combina a tarefa original, a saída da versão anterior e feedback para gerar a saída da nova versão $O_{i+1}$:
$$
O_{i+1} = \pi_{\text{refine}}(\text{Tarefa}, O_i, F_i)
$$



<div align="center">
<img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/4-figures/4-3.png" alt="Loop iterativo Executar-Refletir-Refinar no mecanismo de Reflexão" width="70%"/>
<p>Figura 4.3 Loop Iterativo Executar-Refletir-Refinar no Mecanismo de Reflexão</p>
</div>



Comparado aos dois paradigmas anteriores, o valor da Reflexão está em:

- Ela fornece ao agente um loop interno de correção de erros, tornando-o não mais completamente dependente de feedback de ferramentas externas (Observation do ReAct), podendo assim corrigir erros lógicos e estratégicos de nível superior.
- Ela transforma execução de tarefa única em um processo de otimização contínua, melhorando significativamente a taxa de sucesso final e qualidade da resposta para tarefas complexas.
- Ela constrói uma **"memória de curto prazo"** temporária para o agente. Toda a trajetória "executar-refletir-refinar" forma um registro de experiência valioso; o agente não apenas sabe a resposta final mas também lembra como iterou de um primeiro rascunho com falhas para a versão final. Além disso, este sistema de memória também pode ser **multimodal**, permitindo que o agente reflita e revise saídas além de texto (como código, imagens, etc.), estabelecendo a base para construir agentes multimodais mais poderosos.

### 4.4.2 Configuração de Caso e Design do Módulo de Memória

Para incorporar o mecanismo de Reflexão na prática, vamos introduzir um mecanismo de gerenciamento de memória, porque reflexão geralmente corresponde a armazenamento e recuperação de informação. Se o contexto for longo o suficiente, ter o "revisor" obtendo diretamente toda a informação e depois refletindo muitas vezes introduz muita informação redundante. Neste passo prático, principalmente completamos **geração de código e otimização iterativa**.

O objetivo da tarefa para este passo é: "Escreva uma função Python para encontrar todos os números primos entre 1 e n."

Esta tarefa é um cenário excelente para testar o mecanismo de Reflexão:

1. **Caminho Claro de Otimização Existe**: O código inicialmente gerado pelo modelo de linguagem grande provavelmente será uma implementação recursiva simples mas ineficiente.
2. **Pontos Claros de Reflexão**: Através de reflexão, problemas como "complexidade de tempo excessivamente alta" ou "cálculos redundantes" podem ser descobertos.
3. **Direção Clara de Otimização**: Baseado no feedback, pode ser otimizado para uma versão iterativa mais eficiente ou uma versão usando o padrão de memorização.

O núcleo da Reflexão está na iteração, e o pré-requisito para iteração é a capacidade de lembrar tentativas anteriores e feedback recebido. Portanto, um módulo de "memória de curto prazo" é essencial para implementar este paradigma. Este módulo de memória será responsável por armazenar a trajetória completa de cada loop "executar-refletir".

```python
from typing import List, Dict, Any, Optional

class Memory:
    """
    Um módulo de memória de curto prazo simples para armazenar a trajetória de ação e reflexão do agente.
    """

    def __init__(self):
        """
        Inicializa uma lista vazia para armazenar todos os registros.
        """
        self.records: List[Dict[str, Any]] = []

    def add_record(self, record_type: str, content: str):
        """
        Adiciona um novo registro à memória.

        Parâmetros:
        - record_type (str): Tipo de registro ('execution' ou 'reflection').
        - content (str): Conteúdo específico do registro (por exemplo, código gerado ou feedback de reflexão).
        """
        record = {"type": record_type, "content": content}
        self.records.append(record)
        print(f"📝 Memória atualizada, adicionado um registro de '{record_type}'.")

    def get_trajectory(self) -> str:
        """
        Formata todos os registros de memória em um texto de string coerente para construir prompts.
        """
        trajectory_parts = []
        for record in self.records:
            if record['type'] == 'execution':
                trajectory_parts.append(f"--- Tentativa Anterior (Código) ---\n{record['content']}")
            elif record['type'] == 'reflection':
                trajectory_parts.append(f"--- Feedback do Revisor ---\n{record['content']}")

        return "\n\n".join(trajectory_parts)

    def get_last_execution(self) -> Optional[str]:
        """
        Obtém o resultado de execução mais recente (por exemplo, o código gerado mais recente).
        Retorna None se não existir.
        """
        for record in reversed(self.records):
            if record['type'] == 'execution':
                return record['content']
        return None
```

O design desta classe `Memory` é relativamente conciso, com a estrutura principal sendo:

- Usa uma lista `records` para armazenar cada ação e reflexão em ordem.
- O método `add_record` é responsável por adicionar novas entradas à memória.
- O método `get_trajectory` é o núcleo; ele "serializa" a trajetória de memória em um segmento de texto que pode ser diretamente inserido em prompts subsequentes, fornecendo contexto completo para reflexão e otimização do modelo.
- `get_last_execution` torna conveniente obter o "primeiro rascunho" mais recente para reflexão.



### 4.4.3 Implementação em Código do Agente de Reflexão

Com o módulo `Memory` como base, podemos agora proceder para construir a lógica central do `ReflectionAgent`. Todo o fluxo de trabalho do agente girará em torno do loop "executar-refletir-refinar" que discutimos anteriormente e guiará o modelo de linguagem grande a desempenhar papéis diferentes através de prompts cuidadosamente projetados.

(1) Design de Prompt

Diferente dos paradigmas anteriores, o mecanismo de Reflexão requer múltiplos prompts para papéis diferentes trabalharem juntos.

1. **Prompt de Execução Inicial**: Este é o prompt para a primeira tentativa do agente de resolver o problema, com conteúdo relativamente direto, apenas exigindo que o modelo complete a tarefa especificada.

```bash
INITIAL_PROMPT_TEMPLATE = """
Você é um programador Python sênior. Por favor, escreva uma função Python de acordo com os seguintes requisitos.
Seu código deve incluir assinatura de função completa, docstring, e seguir padrões de codificação PEP 8.

Requisito: {task}

Por favor, produza o código diretamente sem quaisquer explicações adicionais.
"""
```

2. **Prompt de Reflexão**: Este prompt é a alma do mecanismo de Reflexão. Ele instrui o modelo a desempenhar o papel de um "revisor de código," analisar criticamente o código gerado na rodada anterior, e fornecer feedback específico e acionável.

````bash
REFLECT_PROMPT_TEMPLATE = """
Você é um especialista em revisão de código extremamente rigoroso e engenheiro de algoritmos sênior com requisitos finais para desempenho de código.
Sua tarefa é revisar o seguinte código Python e focar em encontrar seus principais gargalos em <strong>eficiência de algoritmo</strong>.

# Tarefa Original:
{task}

# Código para Revisar:
```python
{code}
```

Por favor, analise a complexidade de tempo deste código e considere se há uma solução <strong>algoritmicamente superior</strong> para melhorar significativamente o desempenho.
Se existir, por favor aponte claramente as deficiências do algoritmo atual e proponha sugestões específicas e viáveis de melhoria de algoritmo (por exemplo, usar método de peneira em vez de divisão por tentativa).
Somente se o código tiver atingido otimalidade no nível de algoritmo você pode responder "nenhuma melhoria necessária."

Por favor, produza seu feedback diretamente sem quaisquer explicações adicionais.
"""
````

3. **Prompt de Refinamento**: Após receber feedback, este prompt guiará o modelo a revisar e otimizar o código original baseado no conteúdo do feedback.

````bash

REFINE_PROMPT_TEMPLATE = """
Você é um programador Python sênior. Você está otimizando seu código baseado no feedback de um especialista em revisão de código.

# Tarefa Original:
{task}

# Sua Tentativa de Código Anterior:
{last_code_attempt}
Feedback do Revisor:
{feedback}

Por favor, gere uma nova versão otimizada do código baseada no feedback do revisor.
Seu código deve incluir assinatura de função completa, docstring, e seguir padrões de codificação PEP 8.
Por favor, produza o código otimizado diretamente sem quaisquer explicações adicionais.
"""
````

(2) Encapsulamento e Implementação do Agente

Agora, vamos integrar este conjunto de lógica de prompt e o módulo `Memory` na classe `ReflectionAgent`.

```python
# Assume que llm_client.py e memory.py já estão definidos
# from llm_client import HelloAgentsLLM
# from memory import Memory

class ReflectionAgent:
    def __init__(self, llm_client, max_iterations=3):
        self.llm_client = llm_client
        self.memory = Memory()
        self.max_iterations = max_iterations

    def run(self, task: str):
        print(f"\n--- Começando a Processar Tarefa ---\nTarefa: {task}")

        # --- 1. Execução Inicial ---
        print("\n--- Realizando Tentativa Inicial ---")
        initial_prompt = INITIAL_PROMPT_TEMPLATE.format(task=task)
        initial_code = self._get_llm_response(initial_prompt)
        self.memory.add_record("execution", initial_code)

        # --- 2. Loop Iterativo: Reflexão e Refinamento ---
        for i in range(self.max_iterations):
            print(f"\n--- Iteração {i+1}/{self.max_iterations} ---")

            # a. Reflexão
            print("\n-> Realizando Reflexão...")
            last_code = self.memory.get_last_execution()
            reflect_prompt = REFLECT_PROMPT_TEMPLATE.format(task=task, code=last_code)
            feedback = self._get_llm_response(reflect_prompt)
            self.memory.add_record("reflection", feedback)

            # b. Verificar se é necessário parar
            if "nenhuma melhoria necessária" in feedback.lower():
                print("\n✅ Reflexão considera que código não precisa de melhoria, tarefa completada.")
                break

            # c. Refinamento
            print("\n-> Realizando Refinamento...")
            refine_prompt = REFINE_PROMPT_TEMPLATE.format(
                task=task,
                last_code_attempt=last_code,
                feedback=feedback
            )
            refined_code = self._get_llm_response(refine_prompt)
            self.memory.add_record("execution", refined_code)

        final_code = self.memory.get_last_execution()
        print(f"\n--- Tarefa Completada ---\nCódigo Final Gerado:\n```python\n{final_code}\n```")
        return final_code

    def _get_llm_response(self, prompt: str) -> str:
        """Um método auxiliar para chamar LLM e obter resposta em streaming completa."""
        messages = [{"role": "user", "content": prompt}]
        response_text = self.llm_client.think(messages=messages) or ""
        return response_text

```

### 4.4.4 Instância de Execução e Análise

O código completo também pode ser encontrado na pasta `code` do repositório de código que acompanha este livro; aqui fornecemos uma instância de saída.

````python
--- Começando a Processar Tarefa ---
Tarefa: Escreva uma função Python para encontrar todos os números primos entre 1 e n.

--- Realizando Tentativa Inicial ---
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
```python
def find_primes(n):
    ...
    return primes
```
📝 Memória atualizada, adicionado um registro de 'execution'.

--- Iteração 1/2 ---

-> Realizando Reflexão...
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
O código atual tem complexidade de tempo de O(n * sqrt(n)). Embora esta implementação seja aceitável para valores menores de n, o desempenho degradará significativamente quando n for muito grande. O principal gargalo é que cada número precisa de verificação de divisão por tentativa, levando a alta sobrecarga de tempo.

Recomenda-se usar o algoritmo Peneira de Eratóstenes, que tem complexidade de tempo de O(n log(log n)) e pode melhorar significativamente a eficiência de encontrar números primos.

Código melhorado como segue:
```python
def find_primes(n):
    ...
    return primes
```
📝 Memória atualizada, adicionado um registro de 'reflection'.

-> Realizando Refinamento...
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
```python
def find_primes(n):
    ...
    return primes
```
📝 Memória atualizada, adicionado um registro de 'execution'.

--- Iteração 2/2 ---

-> Realizando Reflexão...
🧠 Chamando o modelo xxxxxx...
✅ Resposta do modelo de linguagem grande bem-sucedida:
O código atual usa a Peneira de Eratóstenes com complexidade de tempo O(n log log n) e complexidade de espaço O(n). Este algoritmo já é muito eficiente para encontrar todos os números primos entre 1 e n, e geralmente não requer otimização adicional. No entanto, em alguns cenários específicos, as seguintes melhorias podem ser consideradas:

1. <strong>Peneira Segmentada</strong>: Adequada para casos onde n é muito grande mas memória é limitada. Divide o intervalo em múltiplos segmentos pequenos, processa cada segmento separadamente com o método de peneira, reduzindo uso de memória.
2. <strong>Peneira de Números Ímpares</strong>: Exceto 2, todos os números primos são ímpares. Ao inicializar o array `is_prime`, apenas marcar números ímpares pode reduzir a complexidade de espaço pela metade enquanto reduz alguns cálculos desnecessários.

No entanto, estas melhorias não são necessárias para a maioria dos cenários de aplicação porque a Peneira de Eratóstenes padrão já é eficiente o suficiente. Portanto, em casos gerais, <strong>nenhuma melhoria necessária</strong>.
📝 Memória atualizada, adicionado um registro de 'reflection'.

✅ Reflexão considera que código não precisa de melhoria, tarefa completada.

--- Tarefa Completada ---
Código Final Gerado:
```python
def find_primes(n):
    """
    Encontra todos os números primos entre 1 e n usando o algoritmo Peneira de Eratóstenes.

    :param n: O limite superior do intervalo para encontrar números primos.
    :return: Uma lista de todos os números primos entre 1 e n.
    """
    if n < 2:
        return []

    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False

    p = 2
    while p * p <= n:
        if is_prime[p]:
            for i in range(p * p, n + 1, p):
                is_prime[i] = False
        p += 1

    primes = [num for num in range(2, n + 1) if is_prime[num]]
    return primes
```
````

Esta instância de execução demonstra como o mecanismo de Reflexão impulsiona o agente a realizar otimização profunda:

1. **"Crítica" Efetiva é Pré-requisito para Otimização**: Na primeira rodada de reflexão, porque usamos um prompt "extremamente rigoroso" e "focado em eficiência de algoritmo," o agente não ficou satisfeito com o código inicial funcionalmente correto mas precisamente apontou seu gargalo de complexidade de tempo `O(n * sqrt(n))` e propôs sugestões de melhoria de nível de algoritmo—a Peneira de Eratóstenes.
2. **Melhoria Iterativa**: Após receber feedback claro, o agente implementou com sucesso um método de peneira mais eficiente na fase de refinamento, reduzindo a complexidade de algoritmo para `O(n log log n)`, completando a primeira auto-iteração significativa.
3. **Convergência e Término**: Na segunda rodada de reflexão, enfrentando o método de peneira já eficiente, o agente demonstrou conhecimento mais profundo. Ele não apenas afirmou a eficiência do algoritmo atual mas até mesmo mencionou direções de otimização mais avançadas como peneira segmentada, mas finalmente fez o julgamento correto de "nenhuma melhoria necessária em casos gerais." Este julgamento acionou nossa condição de término, permitindo que o processo de otimização convergisse.

Este caso prova completamente que o valor de um mecanismo de Reflexão bem projetado não está apenas em corrigir erros mas mais importante em **impulsionar soluções para alcançar melhorias graduais em qualidade e eficiência**, tornando-se uma das tecnologias-chave para construir agentes complexos e de alta qualidade.

### 4.4.5 Análise Custo-Benefício do Mecanismo de Reflexão

Embora o mecanismo de Reflexão tenha desempenho excelente em melhorar a qualidade de solução de tarefas, esta capacidade não é sem custo. Em aplicações práticas, precisamos pesar os benefícios que ele traz contra os custos correspondentes.

(1) Principais Custos

1. **Aumento da Sobrecarga de Chamadas ao Modelo**: Este é o custo mais direto. Cada iteração requer pelo menos duas chamadas adicionais ao modelo de linguagem grande (uma para reflexão, uma para refinamento). Se iterando múltiplas rodadas, custos de chamada de API e consumo de recursos computacionais aumentarão exponencialmente.

2. **Aumento Significativo da Latência de Tarefa**: Reflexão é um processo serial; cada rodada de refinamento deve esperar a reflexão da rodada anterior completar. Isso estende significativamente o tempo total da tarefa, tornando-a inadequada para cenários com altos requisitos de tempo real.

3. **Aumento da Complexidade de Engenharia de Prompt**: Como nosso caso demonstra, o sucesso da Reflexão depende muito de prompts de alta qualidade e direcionados. Projetar e depurar prompts eficazes para diferentes fases como "execução," "reflexão," e "refinamento" requer mais esforço de desenvolvimento.

(2) Benefícios Centrais

1. **Salto na Qualidade de Solução**: O maior benefício é que pode otimizar iterativamente uma solução inicial "qualificada" em uma solução final "excelente." Esta melhoria de funcionalmente correto para eficiente em desempenho, de lógica aproximada para lógica rigorosa, é crucial em muitas tarefas críticas.

2. **Robustez e Confiabilidade Melhoradas**: Através de loops internos de autocorreção, o agente pode descobrir e corrigir falhas lógicas potenciais, erros factuais ou manipulação inadequada de casos de borda na solução inicial, melhorando muito a confiabilidade do resultado final.

Em resumo, o mecanismo de Reflexão é uma estratégia típica de "custo por qualidade." É muito adequado para cenários que **têm requisitos extremamente altos para qualidade, precisão e confiabilidade dos resultados finais, e têm requisitos relativamente relaxados para tempo real de conclusão de tarefa**. Por exemplo:

- Gerar código de negócio crítico ou relatórios técnicos.
- Conduzir raciocínio lógico complexo em pesquisa científica.
- Sistemas de suporte a decisão requerendo análise e planejamento profundos.

Por outro lado, se o cenário de aplicação requer respostas rápidas, ou uma resposta "aproximadamente correta" já é suficiente, usar paradigmas ReAct ou Plan-and-Solve mais leves pode ser uma escolha mais custo-efetiva.

## 4.5 Resumo do Capítulo

Neste capítulo, baseando-nos no conhecimento de modelos de linguagem grandes dominado no Capítulo 3, codificamos e implementamos três paradigmas clássicos de construção de agentes da indústria do zero através de "construir rodas nós mesmos": ReAct, Plan-and-Solve e Reflexão. Não apenas exploramos seus princípios de funcionamento centrais mas também compreendemos profundamente suas respectivas vantagens, limitações e cenários aplicáveis através de casos práticos específicos.

**Revisão de Conhecimento Central:**

1. ReAct: Construímos um agente ReAct que pode interagir com o mundo externo. Através do loop dinâmico de "pensamento-ação-observação," ele usou com sucesso mecanismos de busca para responder perguntas em tempo real que sua própria base de conhecimento não poderia cobrir. Suas vantagens centrais estão em **adaptabilidade ambiental** e **capacidade de correção de erros dinâmica**, tornando-o a primeira escolha para lidar com tarefas exploratórias requerendo entrada de ferramentas externas.
2. Plan-and-Solve: Implementamos um agente Plan-and-Solve que planeja primeiro depois executa, e o usamos para resolver problemas de palavras matemáticos requerendo raciocínio de múltiplos passos. Ele decompõe tarefas complexas em passos claros, depois os executa um por um. Suas vantagens centrais estão em **estrutura** e **estabilidade**, particularmente adequadas para lidar com tarefas com caminhos lógicos determinados e raciocínio interno intensivo.
3. Reflexão (Auto-Reflexão e Iteração): Construímos um agente de Reflexão com capacidades de auto-otimização. Ao introduzir o loop iterativo "executar-refletir-refinar," ele otimizou com sucesso uma solução de código inicialmente ineficiente em uma versão de alto desempenho algoritmicamente superior. Seu valor central está em **melhorar significativamente a qualidade de solução**, adequado para cenários com requisitos extremamente altos para precisão e confiabilidade de resultados.

Os três paradigmas explorados neste capítulo representam três estratégias diferentes para agentes resolverem problemas, como mostrado na Tabela 4.1. Em aplicações práticas, qual escolher depende dos requisitos centrais da tarefa:

<div align="center">
<p>Tabela 4.1 Estratégia de Seleção para Diferentes Loops de Agente</p>
<img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/4-figures/4-4.png" alt="" width="70%"/>
</div>

Neste ponto, dominamos as tecnologias centrais para construir agentes individuais. Para transitar conhecimento e obter insights mais profundos sobre aplicações práticas, na próxima seção vamos explorar como usar diferentes plataformas low-code e soluções leves de código para construir agentes.

## Exercícios

> **Nota**: Alguns exercícios não têm respostas padrão; o foco está em cultivar a compreensão abrangente e capacidade prática dos aprendizes em design de paradigma de agente.

1. Este capítulo introduziu três paradigmas clássicos de agente: `ReAct`, `Plan-and-Solve`, e `Reflection`. Por favor analise:

   - Quais são as diferenças essenciais em como esses três paradigmas organizam "pensamento" e "ação"?
   - Se você fosse projetar um "assistente de controle de casa inteligente" (precisa controlar luzes, ar condicionado, cortinas e outros dispositivos, e ajustar automaticamente baseado em hábitos do usuário), qual paradigma você escolheria como arquitetura básica? Por quê?
   - Esses três paradigmas podem ser combinados? Se sim, por favor tente projetar uma arquitetura de agente de paradigma híbrido e explique seus cenários aplicáveis.

2. Na implementação `ReAct` na Seção 4.2, usamos expressões regulares para analisar a saída do modelo de linguagem grande (como `Thought` e `Action`). Por favor considere:

   - Que fragilidades potenciais existem no método de análise atual? Sob que circunstâncias pode falhar?
   - Além de expressões regulares, quais são algumas soluções de análise de saída mais robustas?
   - Tente modificar o código neste capítulo para usar um formato de saída mais confiável, e compare os prós e contras das duas abordagens.

3. Invocação de ferramentas é uma das capacidades centrais de agentes modernos. Baseado no design `ToolExecutor` na Seção 4.2.2, por favor complete a seguinte prática de extensão:

   > **Nota**: Esta é uma questão de prática hands-on; é recomendado escrever código de fato.

   - Adicione uma ferramenta "calculadora" ao agente `ReAct` para que ele possa lidar com problemas de cálculos matemáticos complexos (como "Calcule o resultado de `(123 + 456) × 789 / 12 = ?`").
   - Projete e implemente um mecanismo de tratamento de "falha de seleção de ferramenta": quando o agente chama repetidamente a ferramenta errada ou fornece parâmetros errados, como o sistema deve guiá-lo a corrigir?
   - Considere: Se o número de ferramentas chamáveis aumentar para 50 ou até 100, o método de descrição de ferramenta atual ainda funcionará efetivamente? De uma perspectiva de engenharia, como podemos otimizar a organização e mecanismo de recuperação de ferramentas quando o número de ferramentas chamáveis aumenta significativamente com necessidades de negócio?

4. O paradigma `Plan-and-Solve` decompõe tarefas em duas fases: "planejamento" e "execução." Por favor analise em profundidade:

   - Na implementação na Seção 4.3, o plano gerado na fase de planejamento é "estático" (gerado uma vez, não modificável). Se durante a execução for descoberto que um certo passo não pode ser completado ou o resultado não atende às expectativas, como um mecanismo de "replanejamento dinâmico" deveria ser projetado?
   - Compare `Plan-and-Solve` com `ReAct`: Ao lidar com uma tarefa como "reservar uma viagem de negócios de Beijing a Shanghai (incluindo voos, hotéis, aluguel de carro)," qual paradigma é mais adequado? Por quê?
   - Tente projetar um sistema de "planejamento hierárquico": primeiro gere um plano abstrato de alto nível, depois gere sub-planos detalhados para cada passo de alto nível. Quais vantagens este design tem?

5. O mecanismo `Reflection` melhora qualidade de saída através do loop "executar-refletir-refinar." Por favor considere:

   - No caso de geração de código na Seção 4.4, o mesmo modelo é usado para diferentes fases. Se dois modelos diferentes fossem usados (por exemplo, usar um modelo mais poderoso para reflexão e um modelo mais rápido para execução), que impacto teria?
   - A condição de término para o mecanismo `Reflection` é "feedback contém **nenhuma melhoria necessária**" ou "contagem máxima de iteração atingida." Este design é razoável? Uma condição de término mais inteligente pode ser projetada?
   - Suponha que você queira construir um "assistente de escrita de artigos acadêmicos" que pode gerar rascunhos e continuamente otimizar conteúdo de artigo. Por favor projete um mecanismo de Reflexão multidimensional que reflete e melhora de múltiplas perspectivas como lógica de parágrafo, inovação de método, expressão de linguagem e padrões de citação.

6. Engenharia de prompt é uma tecnologia-chave afetando o efeito final de agentes. Este capítulo demonstrou múltiplos modelos de prompt cuidadosamente projetados. Por favor analise:

   - Compare o prompt `ReAct` na Seção 4.2.3 e o prompt `Plan-and-Solve` na Seção 4.3.2; eles obviamente têm diferenças significativas em design estrutural. Como essas diferenças servem à lógica central de seus respectivos paradigmas?
   - No prompt `Reflection` na Seção 4.4.3, usamos uma configuração de papel como "você é um especialista em revisão de código extremamente rigoroso." Tente modificar esta configuração de papel (como mudá-la para "você é um mantenedor de projeto open-source que valoriza legibilidade de código"), observe as mudanças nos resultados de saída, e resuma o impacto de configurações de papel no comportamento do agente.
   - Adicionar exemplos `few-shot` aos prompts muitas vezes pode melhorar significativamente a capacidade do modelo de seguir formatos específicos. Por favor tente adicionar exemplos `few-shot` a um dos agentes neste capítulo e compare os efeitos.

7. Uma startup de e-commerce agora espera usar um "agente de atendimento ao cliente" para substituir atendimento ao cliente humano para redução de custos e melhoria de eficiência. Ele precisa ter as seguintes funções:

   a. Entender a razão da solicitação de reembolso do usuário

   b. Consultar informações de pedido do usuário e status de logística

   c. Julgar inteligentemente se o reembolso deve ser aprovado baseado na política da empresa

   d. Gerar um e-mail de resposta apropriado e enviá-lo ao e-mail do usuário

   e. Se a decisão de julgamento é de alguma forma controversa (autoconfiança está abaixo de um limiar), ser capaz de auto-refletir e fornecer sugestões mais prudentes

   Como gerente de produto deste produto:
   - Qual paradigma (ou combinação de paradigmas) deste capítulo você escolheria como arquitetura central do sistema?
   - Que ferramentas este sistema precisa? Por favor liste pelo menos 3 ferramentas e suas descrições funcionais.
   - Como projetar prompts para garantir que as decisões do agente tanto se alinhem com interesses da empresa quanto mantenham uma atitude amigável para com usuários?
   - Que riscos e desafios este produto pode enfrentar após lançamento? Como esses riscos podem ser reduzidos através de meios técnicos?

## Referências

[1] Yao S, Zhao J, Yu D, et al. React: Synergizing reasoning and acting in language models[C]//International Conference on Learning Representations (ICLR). 2023.

[2] Wang L, Xu W, Lan Y, et al. Plan-and-solve prompting: Improving zero-shot chain-of-thought reasoning by large language models[J]. arXiv preprint arXiv:2305.04091, 2023.

[3] Shinn N, Cassano F, Gopinath A, et al. Reflexion: Language agents with verbal reinforcement learning[J]. Advances in Neural Information Processing Systems, 2023, 36: 8634-8652.
