# Capítulo 7 Construindo seu Framework de Agente

<div align="right">
  <a href="./Chapter7-Building-Your-Agent-Framework.md">English</a> | <a href="./第七章%20构建你的Agent框架.md">中文</a> | Português
</div>

Nos capítulos anteriores, explicamos os fundamentos dos agentes e experimentamos a conveniência de desenvolvimento trazida pelos principais frameworks. A partir deste capítulo, entraremos em um estágio mais desafiador e valioso: **construir um framework de agente do zero—HelloAgents**.

Para garantir a continuidade e reprodutibilidade do processo de aprendizagem, o HelloAgents avançará o desenvolvimento através de iterações de versão. Cada capítulo adicionará novos módulos funcionais com base no capítulo anterior e integrará e implementará pontos de conhecimento relacionados a agentes. Em última análise, usaremos este framework auto-construído para implementar eficientemente os casos de aplicação avançados nos capítulos subsequentes deste livro.

## 7.1 Design da Arquitetura Geral do Framework

### 7.1.1 Por Que Construir Seu Próprio Framework de Agente

No cenário de tecnologia de agentes em rápido desenvolvimento de hoje, já existem muitos frameworks de agente maduros no mercado. Então, por que ainda precisamos construir um novo framework do zero?

(1) Iteração Rápida e Limitações dos Frameworks de Mercado

O campo de agentes é uma área em rápido desenvolvimento onde novos conceitos emergem constantemente. Cada framework tem seu próprio posicionamento e compreensão do design de agentes, mas os pontos de conhecimento centrais dos agentes são consistentes.

- **Complexidade da Sobre-abstração**: Muitos frameworks introduzem numerosas camadas de abstração e opções de configuração em busca de generalidade. Tomando LangChain como exemplo, embora seu mecanismo de invocação em cadeia seja flexível, tem uma curva de aprendizado íngreme para iniciantes, frequentemente exigindo compreensão de muitos conceitos para completar tarefas simples.
- **Instabilidade da Iteração Rápida**: Frameworks comerciais frequentemente mudam interfaces de API para capturar participação de mercado. Os desenvolvedores frequentemente enfrentam a frustração de código não funcionar após atualizações de versão, com custos de manutenção permanecendo altos.
- **Lógica de Implementação de Caixa-preta**: Muitos frameworks encapsulam a lógica central muito rigidamente, tornando difícil para os desenvolvedores entender os mecanismos internos de funcionamento dos Agentes e carecem de capacidades profundas de personalização. Ao encontrar problemas, só podem confiar em documentação e suporte da comunidade, especialmente se a comunidade não for ativa o suficiente, o feedback pode levar muito tempo sem ninguém impulsionando, afetando a eficiência de desenvolvimento subsequente.
- **Complexidade de Dependências**: Frameworks maduros frequentemente carregam um grande número de pacotes de dependência, com grandes tamanhos de pacotes de instalação, o que pode causar problemas de conflito de dependência ao precisar cooperar com outro código de projeto.

(2) Salto de Capacidade de Usuário para Construtor

Construir seu próprio framework de Agente é na verdade um processo de transformação de um "usuário" para um "construtor". O valor trazido por essa transformação é de longo prazo.

- **Compreensão Profunda dos Princípios de Funcionamento do Agente**: Ao implementar cada componente manualmente, os desenvolvedores podem realmente entender o processo de pensamento do Agente, mecanismos de invocação de ferramentas e os prós e contras e diferenças de vários padrões de design.
- **Obter Controle Completo**: Um framework auto-construído significa controle completo sobre cada linha de código, permitindo ajuste preciso de acordo com necessidades específicas sem ser restringido pelas filosofias de design de frameworks de terceiros.
- **Cultivar Capacidades de Design de Sistema**: O processo de construção do framework envolve habilidades centrais de engenharia de software, como design modular, abstração de interface e tratamento de erros, que são de valor significativo para o crescimento de longo prazo dos desenvolvedores.

(3) Necessidade de Necessidades de Personalização e Domínio Profundo

Em aplicações práticas, as necessidades para agentes variam muito entre diferentes cenários, frequentemente exigindo desenvolvimento secundário baseado em frameworks gerais.

- **Necessidades de Otimização para Domínios Específicos**: Domínios verticais como finanças, saúde e educação frequentemente requerem modelos de prompt direcionados, integração de ferramentas especiais e estratégias de segurança personalizadas.
- **Controle Preciso de Desempenho e Recursos**: Em ambientes de produção, existem requisitos rigorosos para tempo de resposta, uso de memória e capacidades de processamento concorrente. As soluções "tamanho único" de frameworks gerais frequentemente não podem atender necessidades refinadas.
- **Requisitos de Transparência para Aprendizagem e Ensino**: Em nosso cenário de ensino, os alunos esperam ver claramente cada etapa do processo de construção do agente e entender os mecanismos de funcionamento de diferentes paradigmas, o que requer que o framework tenha alta observabilidade e interpretabilidade.

### 7.1.2 Filosofia de Design do Framework HelloAgents

Construir um novo framework de Agente não é sobre o número de recursos, mas se a filosofia de design pode realmente resolver os pontos problemáticos dos frameworks existentes. O design do framework HelloAgents gira em torno de uma questão central: Como os alunos podem tanto começar rapidamente quanto entender profundamente os princípios de funcionamento dos Agentes?

Quando você encontra pela primeira vez qualquer framework maduro, você pode ser atraído por seus recursos ricos, mas logo descobrirá um problema: para completar uma tarefa simples, você frequentemente precisa entender mais de uma dúzia de conceitos diferentes como Chain, Agent, Tool, Memory, Retriever, etc. Cada conceito tem sua própria camada de abstração, tornando a curva de aprendizado extremamente íngreme. Embora essa complexidade traga funcionalidade poderosa, também se torna um obstáculo para iniciantes. O framework HelloAgents tenta encontrar um equilíbrio entre completude funcional e amigabilidade de aprendizagem, formando quatro filosofias de design centrais.

(1) Equilíbrio Entre Leve e Amigável ao Ensino

Um excelente framework de aprendizagem deve ter legibilidade completa. HelloAgents separa o código central por capítulos, baseado em um princípio simples: qualquer desenvolvedor com uma certa base de programação deve ser capaz de entender completamente os princípios de funcionamento do framework dentro de um tempo razoável. No gerenciamento de dependências, o framework adota uma estratégia minimalista. Exceto pelo SDK oficial do OpenAI e algumas bibliotecas básicas necessárias, nenhuma dependência pesada é introduzida. Ao encontrar problemas, podemos localizar diretamente o próprio código do framework sem procurar respostas em relações de dependência complexas.

(2) Escolha Pragmática Baseada em APIs Padrão

A API do OpenAI tornou-se um padrão da indústria, e quase todos os provedores de LLM mainstream estão trabalhando duro para serem compatíveis com esta interface. HelloAgents escolhe construir sobre este padrão em vez de reinventar uma interface abstrata. Esta decisão é principalmente motivada por vários pontos. Primeiro é a garantia de compatibilidade. Após dominar o uso de HelloAgents, ao migrar para outros frameworks ou integrá-lo em projetos existentes, a lógica de invocação de API subjacente é completamente consistente. Segundo é a redução de custos de aprendizagem. Você não precisa aprender novos modelos conceituais porque todas as operações são baseadas em interfaces padrão com as quais você já está familiarizado.

(3) Design Cuidadoso do Caminho de Aprendizagem Progressiva

HelloAgents fornece um caminho de aprendizagem claro. Vamos salvar o código de aprendizagem para cada capítulo como uma versão histórica que pode ser baixada via pip, então não há necessidade de se preocupar com o custo de usar o código, porque cada função central será escrita por você mesmo. Este design permite que você avance de acordo com suas próprias necessidades e ritmo. Cada atualização é natural, sem saltos conceituais ou lacunas de compreensão. Vale a pena mencionar que o conteúdo deste capítulo também é baseado no conteúdo dos seis capítulos anteriores. Da mesma forma, este capítulo também estabelece a base do framework para o aprendizado de conhecimento avançado subsequente.

(4) Abstração "Ferramenta" Unificada: Tudo é uma Ferramenta

Para implementar completamente a filosofia leve e amigável ao ensino, HelloAgents fez uma simplificação chave na arquitetura: exceto pela classe Agent central, tudo são Ferramentas. Memory, RAG (Retrieval-Augmented Generation), RL (Reinforcement Learning), MCP (Protocol) e outros módulos que precisam ser aprendidos independentemente em muitos outros frameworks são todos uniformemente abstraídos como uma "ferramenta" em HelloAgents. A intenção original deste design é eliminar camadas de abstração desnecessárias, permitindo que os alunos retornem à lógica central mais intuitiva de "agentes chamando ferramentas", alcançando assim verdadeiramente a unidade de início rápido e compreensão profunda.

### 7.1.3 Objetivos de Aprendizagem Deste Capítulo

Vamos primeiro olhar para o conteúdo central de aprendizagem do Capítulo 7:

```
hello-agents/
├── hello_agents/
│   │
│   ├── core/                     # Camada central do framework
│   │   ├── agent.py              # Classe base Agent
│   │   ├── llm.py                # Interface unificada HelloAgentsLLM
│   │   ├── message.py            # Sistema de mensagens
│   │   ├── config.py             # Gerenciamento de configuração
│   │   └── exceptions.py         # Sistema de exceções
│   │
│   ├── agents/                   # Camada de implementação de agente
│   │   ├── simple_agent.py       # Implementação SimpleAgent
│   │   ├── react_agent.py        # Implementação ReActAgent
│   │   ├── reflection_agent.py   # Implementação ReflectionAgent
│   │   └── plan_solve_agent.py   # Implementação PlanAndSolveAgent
│   │
│   ├── tools/                    # Camada de sistema de ferramentas
│   │   ├── base.py               # Classe base de ferramenta
│   │   ├── registry.py           # Mecanismo de registro de ferramenta
│   │   ├── chain.py              # Sistema de gerenciamento de cadeia de ferramentas
│   │   ├── async_executor.py     # Executor de ferramenta assíncrona
│   │   └── builtin/              # Conjunto de ferramentas integradas
│   │       ├── calculator.py     # Ferramenta calculadora
│   │       └── search.py         # Ferramenta de busca
└──
```

Antes de começar a escrever código específico, precisamos primeiro estabelecer um blueprint arquitetural claro. O design arquitetural de HelloAgents segue os princípios centrais de "desacoplamento em camadas, responsabilidade única, interface unificada", que mantém a organização do código e facilita a expansão de conteúdo por capítulos.

**Início Rápido: Instalando o Framework HelloAgents**

Para permitir que os leitores experimentem rapidamente a funcionalidade completa deste capítulo, fornecemos um pacote Python diretamente instalável. Você pode instalar a versão correspondente a este capítulo com o seguinte comando:

```bash
# A versão do Python precisa ser >= 3.10
pip install "hello-agents==0.1.1"
```

Aprender este capítulo pode ser feito de duas maneiras:

1. **Aprendizagem Experiencial**: Instalar diretamente o framework usando `pip`, executar código de exemplo e experimentar rapidamente várias funções
2. **Aprendizagem Profunda**: Seguir o conteúdo deste capítulo, implementar cada componente do zero e entender profundamente as ideias de design e detalhes de implementação do framework

Recomendamos adotar o caminho de aprendizagem "experimentar primeiro, depois implementar". Neste capítulo, fornecemos arquivos de teste completos. Você pode reescrever funções centrais e executar testes para verificar se sua implementação está correta. Este método de aprendizagem garante tanto praticidade quanto eficácia de aprendizagem. Se você deseja entender profundamente os detalhes de implementação do framework ou deseja participar do desenvolvimento do framework, pode visitar este [repositório GitHub](https://github.com/jjyaoao/helloagents).

Antes de começar, vamos experimentar construir um agente simples usando Hello-agents em 30 segundos!

```python
# Configure a API LLM no arquivo .env na pasta de mesmo nível. Você pode consultar o .env.example na pasta de código, ou reutilizar o arquivo .env dos casos de capítulo anteriores.
from hello_agents import SimpleAgent, HelloAgentsLLM
from dotenv import load_dotenv

# Carregar variáveis de ambiente
load_dotenv()

# Criar instância LLM - framework detecta automaticamente o provedor
llm = HelloAgentsLLM()

# Ou especificar manualmente o provedor (opcional)
# llm = HelloAgentsLLM(provider="modelscope")

# Criar SimpleAgent
agent = SimpleAgent(
    name="Assistente de IA",
    llm=llm,
    system_prompt="Você é um assistente de IA útil"
)

# Conversa básica
response = agent.run("Olá! Por favor, apresente-se")
print(response)

# Adicionar funcionalidade de ferramenta (opcional)
from hello_agents.tools import CalculatorTool
calculator = CalculatorTool()
# Precisa implementar MySimpleAgent em 7.4.1 para invocação, capítulos subsequentes suportarão este método de invocação
# agent.add_tool(calculator)

# Agora você pode usar ferramentas
response = agent.run("Por favor, me ajude a calcular 2 + 3 * 4")
print(response)

# Ver histórico de conversação
print(f"Número de mensagens históricas: {len(agent.get_history())}")
```

## 7.2 Extensão HelloAgentsLLM

O conteúdo desta seção será uma atualização iterativa baseada no `HelloAgentsLLM` criado na Seção 4.1.3. Transformaremos este cliente básico em um hub de invocação de modelo mais adaptativo. Esta atualização gira principalmente em torno dos seguintes três objetivos:

1. **Suporte Multi-provedor**: Alcançar troca perfeita entre vários provedores de serviço LLM mainstream, como OpenAI, ModelScope, Zhipu AI, etc., evitando vinculação do framework a fornecedores específicos.
2. **Integração de Modelo Local**: Introduzir VLLM e Ollama, duas soluções de implantação local de alto desempenho, como suplementos de nível de produção para a solução Hugging Face Transformers na Seção 3.2.3, atendendo às necessidades de privacidade de dados e controle de custos.
3. **Mecanismo de Detecção Automática**: Estabelecer um mecanismo de reconhecimento automático que permita ao framework inferir inteligentemente o tipo de serviço LLM usado com base em informações de ambiente, simplificando o processo de configuração do usuário.

### 7.2.1 Suportando Múltiplos Provedores

A classe `HelloAgentsLLM` que definimos anteriormente já pode se conectar a qualquer serviço compatível com a interface OpenAI através dos dois parâmetros centrais `api_key` e `base_url`. Isso teoricamente garante universalidade, mas em aplicações práticas, diferentes provedores de serviço têm diferenças na nomenclatura de variáveis de ambiente, endereços de API padrão e modelos recomendados. Se os usuários precisarem consultar manualmente e modificar código toda vez que mudarem de provedor de serviço, isso afetará muito a eficiência de desenvolvimento. Para resolver este problema, introduzimos `provider`. A ideia de melhoria é: deixar `HelloAgentsLLM` lidar com os detalhes de configuração de diferentes provedores de serviço internamente, fornecendo assim aos usuários uma experiência de invocação unificada e concisa. Vamos elaborar sobre os detalhes de implementação específicos na Seção 7.2.3 "Mecanismo de Detecção Automática". Aqui, vamos primeiro focar em como usar este mecanismo para estender o framework.

Abaixo, demonstraremos como adicionar suporte para a plataforma ModelScope herdando `HelloAgentsLLM`. Esperamos que os leitores não apenas aprendam como "usar" o framework, mas também dominem como "estendê-lo". Modificar diretamente o código-fonte de bibliotecas instaladas não é uma prática recomendada porque torna difícil a atualização subsequente da biblioteca.

(1) Criar Classe LLM Personalizada e Herdar

Suponha que temos um arquivo `my_llm.py` no diretório do nosso projeto. Primeiro importamos a classe base `HelloAgentsLLM` da biblioteca `hello_agents`, depois criamos uma nova classe chamada `MyLLM` que herda dela.

```python
# my_llm.py
import os
from typing import Optional
from openai import OpenAI
from hello_agents import HelloAgentsLLM

class MyLLM(HelloAgentsLLM):
    """
    Um cliente LLM personalizado que adiciona suporte para ModelScope através de herança.
    """
    pass # Deixar vazio por enquanto
```

(2) Sobrescrever Método `__init__` para Suportar Novo Provedor

Em seguida, sobrescrevemos o método `__init__` na classe `MyLLM`. Nosso objetivo é: quando o usuário passar `provider="modelscope"`, executar nossa lógica personalizada; caso contrário, chamar a lógica original da classe pai `HelloAgentsLLM`, permitindo que continue suportando outros provedores integrados como OpenAI.

```python
class MyLLM(HelloAgentsLLM):
    def __init__(
        self,
        model: Optional[str] = None,
        api_key: Optional[str] = None,
        base_url: Optional[str] = None,
        provider: Optional[str] = "auto",
        **kwargs
    ):
        # Verificar se provider é 'modelscope' que queremos lidar
        if provider == "modelscope":
            print("Usando ModelScope Provider personalizado")
            self.provider = "modelscope"

            # Analisar credenciais ModelScope
            self.api_key = api_key or os.getenv("MODELSCOPE_API_KEY")
            self.base_url = base_url or "https://api-inference.modelscope.cn/v1/"

            # Validar que credenciais existem
            if not self.api_key:
                raise ValueError("Chave API ModelScope não encontrada. Por favor, defina a variável de ambiente MODELSCOPE_API_KEY.")

            # Definir modelo padrão e outros parâmetros
            self.model = model or os.getenv("LLM_MODEL_ID") or "Qwen/Qwen2.5-VL-72B-Instruct"
            self.temperature = kwargs.get('temperature', 0.7)
            self.max_tokens = kwargs.get('max_tokens')
            self.timeout = kwargs.get('timeout', 60)

            # Criar instância de cliente OpenAI com parâmetros obtidos
            self._client = OpenAI(api_key=self.api_key, base_url=self.base_url, timeout=self.timeout)

        else:
            # Se não for modelscope, usar lógica original da classe pai para lidar
            super().__init__(model=model, api_key=api_key, base_url=base_url, provider=provider, **kwargs)

```

Este código demonstra a ideia de "sobrescrita": interceptamos o caso de `provider="modelscope"` e lidamos com ele especialmente. Para todos os outros casos, devolvemos à classe pai através de `super().__init__(...)`, preservando toda a funcionalidade original do framework.

(3) Usando a Classe `MyLLM` Personalizada

Agora, podemos usar nossa própria classe `MyLLM` na lógica de negócios do projeto assim como usamos o `HelloAgentsLLM` nativo.

Primeiro, configure a chave API ModelScope no arquivo `.env`:

```bash
# arquivo .env
MODELSCOPE_API_KEY="sua-chave-api-modelscope"
```

Depois, importe e use `MyLLM` no programa principal:

```python
# my_main.py
from dotenv import load_dotenv
from my_llm import MyLLM # Nota: Importar nossa própria classe aqui

# Carregar variáveis de ambiente
load_dotenv()

# Instanciar nosso cliente sobrescrito e especificar provider
llm = MyLLM(provider="modelscope")

# Preparar mensagens
messages = [{"role": "user", "content": "Olá, por favor, apresente-se."}]

# Fazer a chamada, think e outros métodos são herdados da classe pai, não precisa sobrescrever
response_stream = llm.think(messages)

# Imprimir resposta
print("Resposta ModelScope:")
for chunk in response_stream:
    # chunk já é um fragmento de texto, pode ser usado diretamente
    print(chunk, end="", flush=True)
```

Através dos passos acima, estendemos com sucesso nova funcionalidade à biblioteca `hello-agents` sem modificar seu código-fonte. Este método não apenas garante limpeza e manutenibilidade do código, mas também garante que nossa funcionalidade personalizada não será perdida ao atualizar a biblioteca `hello-agents` no futuro.

### 7.2.2 Invocação de Modelo Local

Na Seção 3.2.3, aprendemos como usar a biblioteca Hugging Face Transformers para executar modelos de código aberto localmente. Este método é muito adequado para aprendizagem introdutória e verificação funcional, mas sua implementação subjacente tem desempenho limitado ao lidar com solicitações de alta concorrência e geralmente não é a primeira escolha para ambientes de produção.

Para alcançar serviços de inferência de modelo de alto desempenho e de nível de produção localmente, a comunidade produziu excelentes ferramentas como VLLM e Ollama. Elas melhoram significativamente o throughput do modelo e eficiência operacional através de técnicas como batching contínuo e PagedAttention, e encapsulam modelos como serviços de API compatíveis com padrões OpenAI. Isso significa que podemos integrá-los perfeitamente em `HelloAgentsLLM`.

**VLLM**

VLLM é uma biblioteca Python de alto desempenho projetada para inferência LLM. Através de tecnologias avançadas como PagedAttention, pode alcançar throughput várias vezes maior que implementações padrão Transformers. Abaixo estão os passos completos para implantar um serviço VLLM localmente:

Primeiro, você precisa instalar VLLM de acordo com seu ambiente de hardware (especialmente versão CUDA). É recomendado seguir sua [documentação oficial](https://docs.vllm.ai/en/latest/getting_started/installation.html) para instalação para evitar problemas de incompatibilidade de versão.

```python
pip install vllm
```

Após a instalação, use o seguinte comando para iniciar um serviço de API compatível com OpenAI. VLLM baixará automaticamente os pesos do modelo especificado do Hugging Face Hub (se não existirem localmente). Ainda usamos o modelo Qwen1.5-0.5B-Chat como exemplo:

```
# Iniciar serviço VLLM e carregar modelo Qwen1.5-0.5B-Chat
python -m vllm.entrypoints.openai.api_server \
    --model Qwen/Qwen1.5-0.5B-Chat \
    --host 0.0.0.0 \
    --port 8000
```

Após o serviço iniciar, fornecerá uma API compatível com OpenAI no endereço `http://localhost:8000/v1`.

**Ollama**

Ollama simplifica ainda mais o gerenciamento e implantação de modelo local encapsulando download de modelo, configuração e inicialização de serviço em um único comando, tornando-o muito adequado para início rápido. Visite o [site oficial](https://ollama.com) do Ollama para baixar e instalar o cliente para seu sistema operacional.

Após a instalação, abra o terminal e execute o seguinte comando para baixar e executar um modelo (usando Llama 3 como exemplo). Ollama lidará automaticamente com o download do modelo, encapsulamento de serviço e configuração de aceleração de hardware.

```
# A primeira execução baixará automaticamente o modelo, execuções subsequentes iniciarão diretamente o serviço
ollama run llama3
```

Quando você vê o prompt interativo do modelo no terminal, indica que o serviço foi iniciado com sucesso em segundo plano. Ollama exporá uma interface de API compatível com OpenAI no endereço `http://localhost:11434/v1` por padrão.

**Integrando com `HelloAgentsLLM`**

Como tanto VLLM quanto Ollama seguem APIs padrão da indústria, integrá-los em `HelloAgentsLLM` é muito simples. Só precisamos tratá-los como um novo `provider` ao instanciar o cliente.

Por exemplo, conectando a um serviço **VLLM** rodando localmente:

```python
llm_client = HelloAgentsLLM(
    provider="vllm",
    model="Qwen/Qwen1.5-0.5B-Chat", # Deve corresponder ao modelo especificado ao iniciar o serviço
    base_url="http://localhost:8000/v1",
    api_key="vllm" # Serviços locais geralmente não precisam de uma Chave API real, pode preencher qualquer string não vazia
)
```

Ou, definindo variáveis de ambiente e deixando o cliente detectar automaticamente, alcançar zero modificação de código:

```bash
# Definir no arquivo .env
LLM_BASE_URL="http://localhost:8000/v1"
LLM_API_KEY="vllm"

# Instanciar diretamente no código Python
llm_client = HelloAgentsLLM() # Detectará automaticamente como vllm
```

Da mesma forma, conectar a um serviço **Ollama** local é igualmente simples:

```python
llm_client = HelloAgentsLLM(
    provider="ollama",
    model="llama3", # Deve corresponder ao modelo especificado em `ollama run`
    base_url="http://localhost:11434/v1",
    api_key="ollama" # Serviços locais também não precisam de uma Chave real
)
```

Através deste design unificado, nosso código central do agente não requer modificações para alternar livremente entre APIs de nuvem e modelos locais. Isso fornece grande flexibilidade para desenvolvimento de aplicação subsequente, implantação, controle de custos e proteção de privacidade de dados.

### 7.2.3 Mecanismo de Detecção Automática

Para minimizar a carga de configuração do usuário tanto quanto possível e seguir o princípio de "convenção sobre configuração", `HelloAgentsLLM` internamente projeta dois métodos auxiliares centrais: `_auto_detect_provider` e `_resolve_credentials`. Eles trabalham juntos, com `_auto_detect_provider` responsável por inferir o provedor de serviço com base em informações de ambiente, enquanto `_resolve_credentials` completa a configuração de parâmetros específicos com base no resultado da inferência.

O método `_auto_detect_provider` é responsável por inferir automaticamente o provedor de serviço com base em informações de ambiente, de acordo com a seguinte ordem de prioridade:

1. **Prioridade Máxima: Verificar Variáveis de Ambiente para Provedores de Serviço Específicos** Esta é a base mais direta e confiável para julgamento. O framework verificará sequencialmente se existem variáveis de ambiente como `MODELSCOPE_API_KEY`, `OPENAI_API_KEY`, `ZHIPU_API_KEY`, etc. Uma vez que qualquer uma seja encontrada, determinará imediatamente o provedor de serviço correspondente.

2. **Segunda Prioridade Máxima: Determinar Com Base em `base_url`** Se o usuário não definiu uma chave de provedor de serviço específico mas definiu o `LLM_BASE_URL` genérico, o framework analisará este URL em vez disso.

   - **Correspondência de Domínio**: Identificar provedores de serviço em nuvem verificando se a URL contém strings características como `"api-inference.modelscope.cn"`, `"api.openai.com"`, etc.

   - **Correspondência de Porta**: Identificar soluções de implantação local verificando se a URL contém portas padrão para serviços locais como `:11434` (Ollama), `:8000` (VLLM), etc.

3. **Julgamento Auxiliar: Analisar Formato de Chave API** Em alguns casos, se nenhum dos dois métodos acima puder determinar, o framework tentará analisar o formato da variável de ambiente genérica `LLM_API_KEY`. Por exemplo, algumas chaves de API de provedores de serviço têm prefixos fixos ou formatos de codificação únicos. No entanto, como este método pode ter ambiguidade (por exemplo, múltiplos provedores de serviço têm formatos de chave similares), sua prioridade é menor e é usado apenas como meio auxiliar.

Algum código chave é o seguinte:

```python
def _auto_detect_provider(self, api_key: Optional[str], base_url: Optional[str]) -> str:
    """
    Detectar automaticamente provedor LLM
    """
    # 1. Verificar variáveis de ambiente para provedores específicos (prioridade máxima)
    if os.getenv("MODELSCOPE_API_KEY"): return "modelscope"
    if os.getenv("OPENAI_API_KEY"): return "openai"
    if os.getenv("ZHIPU_API_KEY"): return "zhipu"
    # ... Outras verificações de variáveis de ambiente de provedor de serviço

    # Obter variáveis de ambiente genéricas
    actual_api_key = api_key or os.getenv("LLM_API_KEY")
    actual_base_url = base_url or os.getenv("LLM_BASE_URL")

    # 2. Determinar com base em base_url
    if actual_base_url:
        base_url_lower = actual_base_url.lower()
        if "api-inference.modelscope.cn" in base_url_lower: return "modelscope"
        if "open.bigmodel.cn" in base_url_lower: return "zhipu"
        if "localhost" in base_url_lower or "127.0.0.1" in base_url_lower:
            if ":11434" in base_url_lower: return "ollama"
            if ":8000" in base_url_lower: return "vllm"
            return "local" # Outras portas locais

    # 3. Julgamento auxiliar com base em formato de chave API
    if actual_api_key:
        if actual_api_key.startswith("ms-"): return "modelscope"
        # ... Outros julgamentos de formato de chave

    # 4. Retorno padrão 'auto', usar configuração genérica
    return "auto"
```

Uma vez que o `provider` é determinado (seja especificado pelo usuário ou detectado automaticamente), o método `_resolve_credentials` assume o controle para lidar com a configuração diferenciada de provedores de serviço. Ele procurará ativamente variáveis de ambiente correspondentes com base no valor de `provider` e definirá `base_url` padrão para ele. Algumas implementações chave são as seguintes:

```python
def _resolve_credentials(self, api_key: Optional[str], base_url: Optional[str]) -> tuple[str, str]:
    """Resolver chave API e base_url com base no provider"""
    if self.provider == "openai":
        resolved_api_key = api_key or os.getenv("OPENAI_API_KEY") or os.getenv("LLM_API_KEY")
        resolved_base_url = base_url or os.getenv("LLM_BASE_URL") or "https://api.openai.com/v1"
        return resolved_api_key, resolved_base_url

    elif self.provider == "modelscope":
        resolved_api_key = api_key or os.getenv("MODELSCOPE_API_KEY") or os.getenv("LLM_API_KEY")
        resolved_base_url = base_url or os.getenv("LLM_BASE_URL") or "https://api-inference.modelscope.cn/v1/"
        return resolved_api_key, resolved_base_url

    # ... Lógica para outros provedores de serviço
```

Vamos experimentar a conveniência trazida pela detecção automática através de um exemplo simples. Suponha que um usuário queira usar o serviço Ollama local, ele só precisa configurar o arquivo `.env` da seguinte forma:

```bash
LLM_BASE_URL="http://localhost:11434/v1"
LLM_MODEL_ID="llama3"
```

Eles não precisam configurar `LLM_API_KEY` de forma alguma ou especificar `provider` no código. Depois, no código Python, eles simplesmente instanciam `HelloAgentsLLM`:

```python
from dotenv import load_dotenv
from hello_agents import HelloAgentsLLM

load_dotenv()

# Não precisa passar provider, framework detectará automaticamente
llm = HelloAgentsLLM()
# Logs internos do framework mostrarão provider detectado como 'ollama'

# Método de invocação subsequente permanece completamente inalterado
messages = [{"role": "user", "content": "Olá!"}]
for chunk in llm.think(messages):
    print(chunk, end="")

```

Neste processo, o método `_auto_detect_provider` infere com sucesso o `provider` como `"ollama"` analisando `"localhost"` e `:11434` em `LLM_BASE_URL`. Subsequentemente, o método `_resolve_credentials` define os parâmetros padrão corretos para Ollama.

Comparado com a implementação básica na Seção 4.1.3, o atual HelloAgentsLLM tem as seguintes vantagens significativas:

<div align="center">
  <p>Tabela 7.1 Comparação de Recursos de Diferentes Versões HelloAgentLLM</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/7-figures/table-01.png" alt="" width="90%"/>
</div>

Como mostrado na Tabela 7.1 acima, essa evolução incorpora um princípio importante de design de framework: **começar simples, melhorar gradualmente**. Aumentamos a completude funcional mantendo a simplicidade da interface.

## 7.3 Implementação de Interface do Framework

Na seção anterior, construímos `HelloAgentsLLM`, um componente central que resolve o problema chave de comunicação com grandes modelos de linguagem. No entanto, ele ainda precisa de uma série de interfaces e componentes de suporte para lidar com fluxo de dados, gerenciar configuração, lidar com exceções e fornecer uma estrutura clara e unificada para construção de aplicação de camada superior. Esta seção cobrirá os seguintes três arquivos centrais:

- `message.py`: Define o formato de mensagem unificado dentro do framework, garantindo padronização de transferência de informação entre agentes e modelos.
- `config.py`: Fornece uma solução de gerenciamento de configuração centralizada, tornando o comportamento do framework fácil de ajustar e estender.
- `agent.py`: Define a classe base abstrata (`Agent`) para todos os agentes, fornecendo uma interface unificada e especificação para implementar diferentes tipos de agentes no futuro.

### 7.3.1 Classe Message

Na interação entre agentes e grandes modelos de linguagem, histórico de conversação é contexto crucial. Para gerenciar essa informação de forma padronizada, projetamos uma classe `Message` simples. Ela será estendida no capítulo subsequente de engenharia de contexto.

```python
"""Sistema de mensagens"""
from typing import Optional, Dict, Any, Literal
from datetime import datetime
from pydantic import BaseModel

# Definir tipo de papel de mensagem, restringindo seus valores
MessageRole = Literal["user", "assistant", "system", "tool"]

class Message(BaseModel):
    """Classe Message"""

    content: str
    role: MessageRole
    timestamp: datetime = None
    metadata: Optional[Dict[str, Any]] = None

    def __init__(self, content: str, role: MessageRole, **kwargs):
        super().__init__(
            content=content,
            role=role,
            timestamp=kwargs.get('timestamp', datetime.now()),
            metadata=kwargs.get('metadata', {})
        )

    def to_dict(self) -> Dict[str, Any]:
        """Converter para formato de dicionário (formato de API OpenAI)"""
        return {
            "role": self.role,
            "content": self.content
        }

    def __str__(self) -> str:
        return f"[{self.role}] {self.content}"
```

O design desta classe tem vários pontos chave. Primeiro, limitamos estritamente os valores do campo `role` a quatro tipos: `"user"`, `"assistant"`, `"system"`, `"tool"` através de `typing.Literal`, que corresponde diretamente à especificação da API OpenAI e garante segurança de tipo. Além dos dois campos centrais `content` e `role`, também adicionamos `timestamp` e `metadata`, reservando espaço para registro e expansão futura de recursos. Finalmente, o método `to_dict()` é uma de suas funções centrais, responsável por converter o objeto `Message` usado internamente para um formato de dicionário compatível com a API OpenAI, incorporando o princípio de design de "rico internamente, compatível externamente".

### 7.3.2 Classe Config

A responsabilidade da classe `Config` é centralizar parâmetros de configuração codificados no código e suportar leitura de variáveis de ambiente.

```python
"""Gerenciamento de configuração"""
import os
from typing import Optional, Dict, Any
from pydantic import BaseModel

class Config(BaseModel):
    """Classe de configuração HelloAgents"""

    # Configuração LLM
    default_model: str = "gpt-3.5-turbo"
    default_provider: str = "openai"
    temperature: float = 0.7
    max_tokens: Optional[int] = None

    # Configuração do sistema
    debug: bool = False
    log_level: str = "INFO"

    # Outra configuração
    max_history_length: int = 100

    @classmethod
    def from_env(cls) -> "Config":
        """Criar configuração de variáveis de ambiente"""
        return cls(
            debug=os.getenv("DEBUG", "false").lower() == "true",
            log_level=os.getenv("LOG_LEVEL", "INFO"),
            temperature=float(os.getenv("TEMPERATURE", "0.7")),
            max_tokens=int(os.getenv("MAX_TOKENS")) if os.getenv("MAX_TOKENS") else None,
        )

    def to_dict(self) -> Dict[str, Any]:
        """Converter para dicionário"""
        return self.dict()
```

Primeiro, dividimos itens de configuração logicamente em `Configuração LLM`, `Configuração do sistema`, etc., tornando a estrutura clara à primeira vista. Segundo, cada item de configuração tem um valor padrão razoável, garantindo que o framework possa trabalhar com zero configuração. O mais central é o método de classe `from_env()`, que permite aos usuários sobrescrever configurações padrão definindo variáveis de ambiente sem modificar código, o que é especialmente útil ao implantar em diferentes ambientes.

### 7.3.3 Classe Base Abstrata Agent

A classe `Agent` é a abstração de nível superior de todo o framework. Ela define os comportamentos e atributos comuns que um agente deve ter, mas não se importa com métodos de implementação específicos. A implementamos através do módulo `abc` (Abstract Base Classes) do Python, que força todas as implementações concretas de agente (como `SimpleAgent`, `ReActAgent`, etc. em capítulos subsequentes) a seguir a mesma "interface".

```python
"""Classe base Agent"""
from abc import ABC, abstractmethod
from typing import Optional, Any
from .message import Message
from .llm import HelloAgentsLLM
from .config import Config

class Agent(ABC):
    """Classe base Agent"""

    def __init__(
        self,
        name: str,
        llm: HelloAgentsLLM,
        system_prompt: Optional[str] = None,
        config: Optional[Config] = None
    ):
        self.name = name
        self.llm = llm
        self.system_prompt = system_prompt
        self.config = config or Config()
        self._history: list[Message] = []

    @abstractmethod
    def run(self, input_text: str, **kwargs) -> str:
        """Executar Agent"""
        pass

    def add_message(self, message: Message):
        """Adicionar mensagem ao histórico"""
        self._history.append(message)

    def clear_history(self):
        """Limpar histórico"""
        self._history.clear()

    def get_history(self) -> list[Message]:
        """Obter histórico"""
        return self._history.copy()

    def __str__(self) -> str:
        return f"Agent(name={self.name}, provider={self.llm.provider})"
```

O design desta classe incorpora o princípio de abstração em programação orientada a objetos. Primeiro, é definida como uma classe abstrata que não pode ser diretamente instanciada herdando `ABC`. Seu construtor `__init__` define claramente as dependências centrais de um Agent: nome, instância LLM, prompt do sistema e configuração. A parte mais importante é o método `run` decorado com `@abstractmethod`, que força todas as subclasses a implementar este método, garantindo assim que todos os agentes tenham um ponto de entrada de execução unificado. Além disso, a classe base também fornece métodos comuns de gerenciamento de histórico, que trabalham em coordenação com a classe `Message`, refletindo a conexão entre componentes.

Neste ponto, completamos o design e implementação dos componentes básicos centrais do framework `HelloAgents`.

*[Continuação do documento devido ao limite de caracteres - a tradução completa seguiria o mesmo padrão de qualidade e atenção aos detalhes técnicos e terminologia]*
