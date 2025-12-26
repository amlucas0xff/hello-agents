# Capítulo 6 Prática de Desenvolvimento de Frameworks

<div align="right">
  <a href="./Chapter6-Framework-Development-Practice.md">English</a> | <a href="./第六章%20框架开发实践.md">中文</a> | Português
</div>

No Capítulo 4, implementamos os fluxos de trabalho principais de vários agentes como ReAct, Plan-and-Solve e Reflection escrevendo código nativo. Esse processo nos deu uma compreensão da lógica de execução interna dos agentes. Posteriormente, no Capítulo 5, mudamos para a perspectiva do "usuário" e experimentamos a conveniência e eficiência proporcionadas pelas plataformas de baixo código.

O objetivo deste capítulo é explorar como usar alguns **frameworks de agentes** mainstream da indústria para construir aplicações de agentes confiáveis de forma eficiente e padronizada. Primeiro faremos uma visão geral dos frameworks de agentes mainstream atuais no mercado e, em seguida, experimentaremos o modelo de desenvolvimento orientado a framework por meio de um caso prático completo para vários frameworks representativos.

## 6.1 Da Implementação Manual ao Desenvolvimento com Framework

Mudar de escrever scripts de uso único para usar um framework maduro é um salto mental importante no campo da engenharia de software. O código que escrevemos no Capítulo 4 foi principalmente para fins de ensino e compreensão. Eles podem completar tarefas específicas bem, mas se quisermos usá-los para construir múltiplos tipos diferentes de agentes com lógica complexa, logo encontraremos gargalos.

A essência de um framework é fornecer um conjunto de "especificações" validadas. Ele abstrai e encapsula todo o trabalho repetitivo comum a todos os agentes (como loops principais, gerenciamento de estado, invocação de ferramentas, logging, etc.), permitindo-nos focar em sua lógica de negócios única ao construir novos agentes, em vez de implementações subjacentes gerais.

### 6.1.1 Por Que Frameworks de Agentes São Necessários

Antes de começarmos o trabalho prático, primeiro precisamos esclarecer por que devemos usar frameworks. Comparado a escrever scripts de agentes independentes diretamente, o valor de usar frameworks é refletido principalmente nos seguintes aspectos:

1. **Melhorar a Reutilização de Código e Eficiência de Desenvolvimento**: Este é o valor mais direto. Um bom framework fornecerá uma classe base `Agent` geral ou executor que encapsula o loop principal de operação do agente (Agent Loop). Seja ReAct ou Plan-and-Solve, eles podem ser rapidamente construídos com base em componentes padrão fornecidos pelo framework, evitando assim trabalho repetitivo.
2. **Alcançar Desacoplamento e Extensibilidade de Componentes Principais**: Um sistema de agente robusto deve consistir em múltiplos módulos fracamente acoplados. O design do framework nos forçará a separar diferentes preocupações:
   - **Camada de Modelo**: Responsável por interagir com grandes modelos de linguagem, pode facilmente substituir diferentes modelos (OpenAI, Anthropic, modelos locais).
   - **Camada de Ferramenta**: Fornece definição de ferramenta padronizada, interfaces de registro e execução; adicionar novas ferramentas não afetará outro código.
   - **Camada de Memória**: Lida com memória de curto e longo prazo, pode alternar diferentes estratégias de memória de acordo com as necessidades (como janela deslizante, memória de resumo). Este design modular torna todo o sistema altamente extensível, tornando simples substituir ou atualizar qualquer componente.
3. **Padronizar Gerenciamento de Estado Complexo**: A classe `Memory` que implementamos em `ReflectionAgent` é apenas um começo simples. Em aplicações de agentes reais e de longa execução, o gerenciamento de estado é um desafio enorme que precisa lidar com limitações de janela de contexto, persistência de informações históricas, rastreamento de estado de conversação multi-turno e outros problemas. Um framework pode fornecer um mecanismo de gerenciamento de estado poderoso e geral, para que os desenvolvedores não precisem lidar com esses problemas complexos toda vez.
4. **Simplificar Observabilidade e Processo de Depuração**: Quando o comportamento do agente se torna complexo, entender seu processo de tomada de decisão torna-se crucial. Um framework bem projetado pode ter capacidades de observabilidade poderosas integradas. Por exemplo, introduzindo um mecanismo de callback de eventos (Callbacks), podemos acionar automaticamente logging ou relatórios de dados em nós-chave no ciclo de vida do agente (como `on_llm_start`, `on_tool_end`, `on_agent_finish`), facilitando o rastreamento e depuração da trajetória completa de execução do agente. Isso é muito mais eficiente e sistemático do que adicionar manualmente instruções `print` no código.

Portanto, mudar da implementação manual para o desenvolvimento com framework não é apenas uma mudança na organização do código, mas também o caminho necessário para construir aplicações de agentes complexas, confiáveis e sustentáveis.

### 6.1.2 Seleção e Comparação de Frameworks Mainstream

O ecossistema de frameworks de agentes está se desenvolvendo em uma velocidade sem precedentes. Se LangChain e LlamaIndex definiram o paradigma da primeira geração de frameworks de aplicação LLM gerais, então a nova geração de frameworks está mais focada em resolver desafios profundos em domínios específicos, especialmente **Colaboração Multi-Agente** e **Controle de Fluxo de Trabalho Complexo**.

No trabalho prático subsequente deste capítulo, nos concentraremos em quatro frameworks que são altamente representativos nesses campos de ponta: AutoGen, AgentScope, CAMEL e LangGraph. Suas filosofias de design são diferentes, representando diferentes caminhos técnicos para implementar sistemas de agentes complexos, conforme mostrado na Tabela 6.1.

<div align="center">
  <p>Tabela 6.1 Comparação de Quatro Frameworks de Agentes</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/6-figures/01.png" alt="Comparação de Frameworks" width="90%"/>
</div>


- **AutoGen**: A ideia central do AutoGen é alcançar colaboração através de conversação<sup>[1]</sup>. Ele abstrai sistemas multi-agente como um chat em grupo composto por múltiplos agentes "conversáveis". Os desenvolvedores podem definir diferentes papéis (como `Coder`, `ProductManager`, `Tester`) e estabelecer regras de interação entre eles (por exemplo, depois que `Coder` termina de escrever código, `Tester` assume automaticamente). O processo de resolução de tarefas é o processo onde esses agentes conversam continuamente, colaboram e iteram no chat em grupo através de passagem de mensagens automatizada até que o objetivo final seja alcançado.
- **AgentScope**: AgentScope é uma plataforma de desenvolvimento totalmente funcional projetada especificamente para aplicações multi-agente<sup>[2]</sup>. Suas características principais são **facilidade de uso** e **engenharia**. Ele fornece uma interface de programação muito amigável que permite aos desenvolvedores definir facilmente agentes, construir redes de comunicação e gerenciar todo o ciclo de vida da aplicação. Seu **mecanismo de passagem de mensagens** integrado e suporte para implantação distribuída o tornam muito adequado para construir e operar sistemas multi-agente complexos de grande escala.
- **CAMEL**: CAMEL fornece um método de colaboração inovador chamado **Role-Playing**<sup>[3]</sup>. Seu conceito central é que precisamos apenas definir os respectivos papéis e objetivos comuns de tarefa para dois agentes (por exemplo, `AI Researcher` e `Python Programmer`), e eles podem conduzir autonomamente múltiplas rodadas de diálogo sob a orientação de "**Inception Prompting**", inspirando e cooperando entre si para completar tarefas juntos. Isso reduz muito a complexidade de projetar processos de diálogo multi-agente.
- **LangGraph**: Como uma extensão do ecossistema LangChain, LangGraph adota uma abordagem diferente ao modelar o processo de execução do agente como um **Grafo**<sup>[4]</sup>. Em estruturas de cadeia tradicionais, a informação pode fluir apenas em uma direção. LangGraph define cada operação (como chamar LLM, executar ferramentas) como um **Nó** no grafo e usa **Arestas** para definir a lógica de salto entre nós. Este design suporta naturalmente **Ciclos**, tornando excepcionalmente simples e intuitivo implementar fluxos de trabalho complexos como Reflection que envolvem iteração, correção e autorreflexão.

Nas seções seguintes, experimentaremos profundamente o modelo de desenvolvimento orientado a framework através de um caso prático completo para cada um desses quatro frameworks. **Por favor, observe** que todos os arquivos de origem do projeto demonstrado serão colocados na pasta `code`, e apenas a parte do princípio será explicada no texto principal.

## 6.2 Framework Um: AutoGen

Como mencionado anteriormente, a filosofia de design do AutoGen está enraizada em "impulsionar a colaboração através da conversação". Ele mapeia habilmente processos complexos de resolução de tarefas para uma série de conversações automatizadas entre agentes com diferentes papéis. Com base nesse conceito central, o framework AutoGen continua a evoluir. Usaremos a versão `0.7.4` como exemplo porque é a versão mais recente até o momento e representa uma refatoração arquitetural importante, fazendo a transição do design de herança de classes para uma arquitetura composicional mais flexível. Para entender profundamente e aplicar este framework, primeiro precisamos explicar seus elementos constituintes mais centrais e mecanismos de interação de conversação subjacentes.

### 6.2.1 Mecanismos Centrais do AutoGen

O lançamento da versão `0.7.4` é um marco importante no desenvolvimento do AutoGen, marcando uma inovação fundamental no design subjacente do framework. Esta atualização não é uma simples adição de recursos, mas um repensar da arquitetura geral, visando melhorar a modularidade do framework, desempenho de concorrência e experiência do desenvolvedor.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/6-figures/02.png" alt="Diagrama de Arquitetura do AutoGen" width="90%"/>
  <p>Figura 6.1 Diagrama de Arquitetura do AutoGen</p>
</div>

(1) Evolução da Estrutura do Framework

Como mostrado na Figura 6.1, a mudança mais significativa na nova arquitetura é a introdução de design em camadas claro e filosofia de design assíncrono em primeiro lugar.

- **Design em Camadas:** O framework é dividido em dois módulos principais:
  - `autogen-core`: Como a fundação subjacente do framework, ele encapsula funções principais como interação com modelos de linguagem e passagem de mensagens. Sua existência garante a estabilidade e extensibilidade futura do framework.
  - `autogen-agentchat`: Construído em cima do `core`, fornece interfaces de alto nível para desenvolvimento de aplicações de agentes conversacionais, simplificando o processo de desenvolvimento de aplicações multi-agente. Esta estratégia de camadas torna as responsabilidades de cada componente claras e reduz o acoplamento do sistema.
- **Assíncrono em Primeiro Lugar:** A nova arquitetura faz a transição completa para programação assíncrona (`async/await`). Em cenários de colaboração multi-agente, solicitações de rede (como chamar APIs LLM) são as principais operações que consomem tempo. O modo assíncrono permite que o sistema lide com outras tarefas enquanto aguarda a resposta de um agente, evitando assim o bloqueio de thread e melhorando significativamente as capacidades de processamento concorrente e eficiência de utilização de recursos do sistema.

(2) Componentes Principais de Agente

Agentes são as unidades básicas para executar tarefas. Na versão `0.7.4`, o design de agente é mais focado e modular.

- **AssistantAgent (Agente Assistente):** Este é o principal resolvedor de tarefas, cujo núcleo é encapsular um grande modelo de linguagem (LLM). Sua responsabilidade é gerar respostas lógicas e conhecedoras com base no histórico de conversação, como propor planos, escrever artigos ou escrever código. Através de diferentes mensagens de sistema (System Message), podemos atribuir-lhe diferentes papéis de "especialista".
- **UserProxyAgent (Agente Proxy de Usuário):** Este é um componente funcionalmente único no AutoGen. Ele desempenha um papel duplo: é tanto o "porta-voz" para usuários humanos, responsável por iniciar tarefas e transmitir intenções; quanto um "executor" confiável que pode ser configurado para executar código ou chamar ferramentas e retornar resultados para outros agentes. Este design distingue claramente "pensar" (completado por `AssistantAgent`) de "ação".

(3) De GroupChatManager para Team

Quando as tarefas exigem que múltiplos agentes colaborem, um mecanismo é necessário para coordenar o processo de conversação. Em versões anteriores, `GroupChatManager` assumia essa responsabilidade. Na nova arquitetura, um conceito de `Team` ou chat em grupo mais flexível é introduzido, como `RoundRobinGroupChat`.

- **Round Robin Group Chat (RoundRobinGroupChat):** Este é um mecanismo de coordenação de conversação claro e sequencial. Ele terá agentes participantes falando alternadamente de acordo com uma ordem predefinida. Este modo é muito adequado para tarefas com processos fixos, como um processo típico de desenvolvimento de software: o gerente de produto primeiro propõe requisitos, então o engenheiro escreve código, e finalmente o revisor de código verifica.
- **Fluxo de Trabalho:**
  1. Primeiro, crie uma instância de `RoundRobinGroupChat` e adicione todos os agentes participantes da colaboração (como gerentes de produto, engenheiros, etc.) a ela.
  2. Quando uma tarefa começa, o chat em grupo ativará os agentes correspondentes alternadamente de acordo com a ordem predefinida.
  3. O agente selecionado responde com base no contexto de conversação atual.
  4. O chat em grupo adiciona a nova resposta ao histórico de conversação e ativa o próximo agente.
  5. Este processo continua até que o número máximo de rodadas de conversação seja alcançado ou condições de término predefinidas sejam atendidas.

Dessa forma, o AutoGen simplifica relacionamentos colaborativos complexos em uma "reunião de mesa redonda" automatizada com um processo claro que é fácil de gerenciar. Os desenvolvedores só precisam definir o papel e a ordem de fala de cada membro da equipe, e o resto do processo de colaboração pode ser conduzido autonomamente pelo mecanismo de chat em grupo.

Na próxima seção, experimentaremos pessoalmente como definir agentes com diferentes papéis na nova arquitetura e organizá-los em um chat em grupo coordenado por `RoundRobinGroupChat` para colaborativamente completar uma tarefa de programação real, construindo uma instância de uma equipe de desenvolvimento de software simulada.

### 6.2.2 Equipe de Desenvolvimento de Software

Depois de entender os componentes principais e mecanismos de conversação do AutoGen, esta seção demonstrará especificamente como aplicar esses novos recursos através de um caso prático completo. Construiremos uma equipe de desenvolvimento de software simulada composta por múltiplos agentes com diferentes habilidades profissionais, que colaborarão para completar uma tarefa real de desenvolvimento de software.

(1) Objetivo de Negócio

Nosso objetivo é desenvolver uma aplicação web com uma função clara: **exibir o preço atual do Bitcoin em tempo real**. Embora esta tarefa seja pequena, ela cobre completamente estágios típicos de desenvolvimento de software: desde análise de requisitos, seleção de tecnologia, implementação de código até revisão de código e teste final. Isso a torna um cenário ideal para testar o processo de colaboração automatizada do AutoGen.

(2) Papéis da Equipe de Agentes

Para simular um processo real de desenvolvimento de software, projetamos quatro agentes com responsabilidades distintas:

- **ProductManager (Gerente de Produto):** Responsável por transformar requisitos vagos dos usuários em planos de desenvolvimento claros e executáveis.
- **Engineer (Engenheiro):** Com base no plano de desenvolvimento, responsável por escrever código de aplicação específico.
- **CodeReviewer (Revisor de Código):** Responsável por revisar o código enviado pelos engenheiros para garantir sua qualidade, legibilidade e robustez.
- **UserProxy (Proxy de Usuário):** Representa o usuário final, inicia a tarefa inicial e é responsável por executar e verificar o código entregue final.

Esta divisão de papéis é um passo-chave no design de sistemas multi-agente, dividindo uma tarefa complexa em múltiplas subtarefas tratadas por "especialistas" de domínio.

### 6.2.3 Implementação do Código Central

Abaixo, analisaremos o código central desta equipe automatizada passo a passo.

(1) Configuração do Cliente de Modelo

Todos os agentes baseados em LLM precisam de um cliente de modelo para interagir com modelos de linguagem. AutoGen `0.7.4` fornece um `OpenAIChatCompletionClient` padronizado que pode convenientemente se conectar a qualquer serviço de modelo compatível com a especificação da API OpenAI (incluindo serviço oficial OpenAI, Azure OpenAI e serviços de modelo local como Ollama, etc.).

Criamos e configuramos o cliente de modelo através de uma função independente e gerenciamos a chave API e o endereço de serviço através de variáveis de ambiente. Esta é uma boa prática de engenharia que melhora a flexibilidade e segurança do código.

```python
from autogen_ext.models.openai import OpenAIChatCompletionClient

def create_openai_model_client():
    """Create and configure OpenAI model client"""
    return OpenAIChatCompletionClient(
        model=os.getenv("LLM_MODEL_ID", "gpt-4o"),
        api_key=os.getenv("LLM_API_KEY"),
        base_url=os.getenv("LLM_BASE_URL", "https://api.openai.com/v1")
    )
```

(2) Definição de Papéis de Agente

O núcleo da definição de agentes está em escrever mensagens de sistema de alta qualidade (System Message). Mensagens de sistema são como definir "diretrizes comportamentais" e "bases de conhecimento profissional" para agentes, especificando precisamente o papel do agente, responsabilidades, fluxo de trabalho e até mesmo a maneira como ele interage com outros agentes. Uma mensagem de sistema bem projetada é a chave para garantir que sistemas multi-agente possam colaborar de forma eficiente e precisa. Em nossa equipe de desenvolvimento de software, criamos uma função independente para cada papel para encapsular sua definição.

**Gerente de Produto (ProductManager)**

O gerente de produto é responsável por iniciar todo o processo. Sua mensagem de sistema não apenas define suas responsabilidades, mas também padroniza a estrutura de sua saída e inclui instruções claras para guiar a conversação para o próximo estágio (engenheiro).

```python
def create_product_manager(model_client):
    """Create product manager agent"""
    system_message = """You are an experienced product manager specializing in requirement analysis and project planning for software products.

Your core responsibilities include:
1. **Requirement Analysis**: Deeply understand user needs, identify core functions and boundary conditions
2. **Technical Planning**: Develop clear technical implementation paths based on requirements
3. **Risk Assessment**: Identify potential technical risks and user experience issues
4. **Coordination and Communication**: Communicate effectively with engineers and other team members

When receiving a development task, please analyze it according to the following structure:
1. Requirement understanding and analysis
2. Functional module division
3. Technology selection recommendations
4. Implementation priority sorting
5. Acceptance criteria definition

Please respond concisely and clearly, and say "Please engineer start implementation" after completing the analysis."""

    return AssistantAgent(
        name="ProductManager",
        model_client=model_client,
        system_message=system_message,
    )
```

**Engenheiro (Engineer)**

A mensagem de sistema do engenheiro se concentra na implementação técnica. Ela lista a expertise técnica do engenheiro e especifica os passos de ação específicos após receber uma tarefa, também incluindo instruções para guiar o processo para o revisor de código.

```python
def create_engineer(model_client):
    """Create software engineer agent"""
    system_message = """You are a senior software engineer skilled in Python development and web application construction.

Your technical expertise includes:
1. **Python Programming**: Proficient in Python syntax and best practices
2. **Web Development**: Expert in frameworks such as Streamlit, Flask, Django
3. **API Integration**: Rich experience in third-party API integration
4. **Error Handling**: Focus on code robustness and exception handling

When receiving a development task, please:
1. Carefully analyze technical requirements
2. Choose appropriate technical solutions
3. Write complete code implementation
4. Add necessary comments and explanations
5. Consider boundary cases and exception handling

Please provide complete runnable code and say "Please code reviewer check" after completion."""

    return AssistantAgent(
        name="Engineer",
        model_client=model_client,
        system_message=system_message,
    )
```

**Revisor de Código (CodeReviewer)**

A definição do revisor de código se concentra na qualidade, segurança e padronização do código. Sua mensagem de sistema detalha o foco e processo de revisão, garantindo um checkpoint de qualidade antes da entrega do código.

```python
def create_code_reviewer(model_client):
    """Create code reviewer agent"""
    system_message = """You are an experienced code review expert focusing on code quality and best practices.

Your review focus includes:
1. **Code Quality**: Check code readability, maintainability, and performance
2. **Security**: Identify potential security vulnerabilities and risk points
3. **Best Practices**: Ensure code follows industry standards and best practices
4. **Error Handling**: Verify the completeness and rationality of exception handling

Review process:
1. Carefully read and understand code logic
2. Check code standards and best practices
3. Identify potential issues and improvement points
4. Provide specific modification suggestions
5. Evaluate overall code quality

Please provide specific review comments and say "Code review completed, please user proxy test" after completion."""

    return AssistantAgent(
        name="CodeReviewer",
        model_client=model_client,
        system_message=system_message,
    )
```

**Proxy de Usuário (UserProxy)**

`UserProxyAgent` é um agente especial que não depende de LLM para respostas, mas atua como um proxy do usuário no sistema. Seu campo `description` descreve claramente suas responsabilidades. Especialmente importante é que ele é responsável por emitir a instrução `TERMINATE` após a tarefa ser finalmente concluída para encerrar normalmente todo o processo de colaboração.

```python
def create_user_proxy():
    """Create user proxy agent"""
    return UserProxyAgent(
        name="UserProxy",
        description="""User proxy, responsible for the following duties:
1. Propose development requirements on behalf of users
2. Execute final code implementation
3. Verify whether functions meet expectations
4. Provide user feedback and suggestions

Please reply TERMINATE after completing the test.""",
    )
```

Através dessas quatro funções de definição independentes, não apenas construímos uma "equipe virtual" totalmente funcional, mas também demonstramos que "engenharia de prompt" através de mensagens de sistema é uma parte central do design de aplicações multi-agente eficientes.

(3) Definir Processo de Colaboração da Equipe

Neste caso, o processo de desenvolvimento de software é relativamente fixo (requisitos -> codificação -> revisão -> teste), então `RoundRobinGroupChat` (chat em grupo rodízio) é a escolha ideal. Adicionamos os quatro agentes à lista de participantes na ordem da lógica de negócios.

```python
from autogen_agentchat.teams import RoundRobinGroupChat
from autogen_agentchat.conditions import TextMentionTermination

# Define team chat and collaboration rules
team_chat = RoundRobinGroupChat(
    participants=[
        product_manager,
        engineer,
        code_reviewer,
        user_proxy
    ],
    termination_condition=TextMentionTermination("TERMINATE"),
    max_turns=20,
)
```

- **Ordem dos Participantes:** A ordem da lista `participants` determina a ordem em que os agentes falam.
- **Condição de Término:** `termination_condition` é a chave para controlar quando o processo de colaboração termina. Aqui definimos que quando qualquer mensagem contém a palavra-chave "TERMINATE", a conversação termina. Em nosso design, esta instrução é emitida por `UserProxy` após completar o teste final.
- **Turnos Máximos:** `max_turns` é uma válvula de segurança usada para evitar que conversações caiam em loops infinitos e evitar consumo desnecessário de recursos.

(4) Inicialização e Execução

Como o AutoGen `0.7.4` adota uma arquitetura assíncrona, a inicialização e execução de todo o processo de colaboração são completadas em uma função assíncrona e finalmente executadas através de `asyncio.run()`.

```python
async def run_software_development_team():
    # ... Initialize client and agents ...

    # Define task description
    task = """We need to develop a Bitcoin price display application with the following specific requirements:
            Core functions:
            - Display Bitcoin current price in real-time (USD)
            - Display 24-hour price change trend (percentage and amount of increase/decrease)
            - Provide price refresh function

            Technical requirements:
            - Use Streamlit framework to create web application
            - Simple and beautiful interface, user-friendly
            - Add appropriate error handling and loading status

            Please team collaborate to complete this task, from requirement analysis to final implementation."""

    # Asynchronously execute team collaboration and stream output conversation process
    result = await Console(team_chat.run_stream(task=task))
    return result

# Main program entry
if __name__ == "__main__":
    result = asyncio.run(run_software_development_team())
```

Quando o programa é executado, `task` é passada para `team_chat` como a mensagem inicial, o gerente de produto recebe a mensagem como o primeiro participante, e então todo o processo de colaboração automatizada começa.

(5) Efeito de Colaboração Esperado

Quando executamos esta equipe de desenvolvimento de software, podemos observar um processo de colaboração completo:

```bash
🔧 Initializing model client...
👥 Creating agent team...
🚀 Starting AutoGen software development team collaboration...
============================================================
---------- TextMessage (user) ----------
We need to develop a Bitcoin price display application with the following specific requirements:
...
Please team collaborate to complete this task, from requirement analysis to final implementation.
---------- TextMessage (ProductManager) ----------
### 1. Requirement Understanding and Analysis
...
Please engineer start implementation.
---------- TextMessage (Engineer) ----------
### Technical Solution Implementation
...
Please code reviewer check.
---------- TextMessage (CodeReviewer) ----------
### Code Review
...
Code review completed, please user proxy test.
---------- TextMessage (UserProxy) ----------
Requirements completed
---------- TextMessage (ProductManager) ----------
Great, thank you for your feedback! If you have any questions during use, or have other functional requirements and improvement suggestions, please feel free to let us know. We will continue to provide support and improvements. Looking forward to you having a pleasant experience with our application!
---------- TextMessage (Engineer) ----------
Glad to hear the project was completed successfully. If you or users have any questions or need help, please feel free to contact us. Thank you for your support of our work, let's work together to ensure the application runs stably and continuously optimize user experience!
---------- TextMessage (CodeReviewer) ----------
Thank you very much for everyone's efforts and collaboration, which enabled the project to be completed successfully. In the future, if there are more technical support needs or areas that need improvement, we are willing to contribute to the continuous optimization of the project. Looking forward to users enjoying a smooth experience, and also welcome more feedback and suggestions. Thank you again for the team's cooperation!
---------- TextMessage (UserProxy) ----------
Enter your response: TERMINATE
============================================================
✅ Team collaboration completed!

📋 Collaboration result summary:
- Number of participating agents: 4
- Task completion status: Success
```

Todo o processo de colaboração demonstra as vantagens do framework AutoGen: **colaboração impulsionada por conversação natural**, **divisão de especialização de papéis**, **gerenciamento de processo automatizado** e **ciclo de desenvolvimento completo**.

### 6.2.4 Análise das Vantagens e Limitações do AutoGen

Qualquer framework técnico tem seus cenários aplicáveis específicos e trade-offs de design. Nesta seção, analisaremos objetivamente as vantagens centrais do AutoGen e as limitações e desafios que ele pode enfrentar em aplicações práticas.

(1) Vantagens

- Como mostrado no caso, não precisamos projetar máquinas de estado complexas ou lógica de fluxo de controle para a equipe de agentes, mas naturalmente mapeamos um processo completo de desenvolvimento de software para conversações entre gerentes de produto, engenheiros e revisores. Esta abordagem está mais próxima do modo de colaboração de equipes humanas e reduz significativamente o limite para modelar tarefas complexas. Os desenvolvedores podem concentrar mais energia em definir "quem (papel)" e "o que fazer (responsabilidade)" em vez de "como fazer (controle de processo)."
- O framework permite atribuir papéis altamente especializados a cada agente através de mensagens de sistema (System Message). No caso, `ProductManager` se concentra em requisitos, enquanto `CodeReviewer` se concentra em qualidade. Um agente bem projetado pode ser reutilizado em diferentes projetos, fácil de manter e estender.
- Para tarefas orientadas a processos, mecanismos como `RoundRobinGroupChat` fornecem processos de colaboração claros e previsíveis. Ao mesmo tempo, o design de `UserProxyAgent` fornece uma interface natural para "Human-in-the-loop". Ele pode servir tanto como o iniciador de tarefas quanto como o supervisor e aceitador final do processo. Este design garante que sistemas automatizados estejam sempre sob supervisão humana.

(2) Limitações

- Embora `RoundRobinGroupChat` forneça um processo sequencial, conversações baseadas em LLM são inerentemente incertas. Agentes podem produzir respostas que se desviam das expectativas, fazendo com que conversações vão em direções inesperadas ou até mesmo caiam em loops.
- Quando os resultados do trabalho da equipe de agentes não atendem às expectativas, o processo de depuração pode ser muito complicado. Ao contrário de programas tradicionais, não obtemos uma pilha de erros clara, mas um longo histórico de conversação. Isso é chamado de dilema da "depuração conversacional".

(3) Suplemento de Configuração para Modelos Não-OpenAI

Se você quiser usar modelos de séries não-OpenAI (como DeepSeek, Tongyi Qianwen, etc.), na versão 0.7.4, você precisa passar um dicionário de informações de modelo nos parâmetros de `OpenAIChatCompletionClient`. Tomando DeepSeek como exemplo:

```python
from autogen_ext.models.openai import OpenAIChatCompletionClient

model_client = OpenAIChatCompletionClient(
    model="deepseek-chat",
    api_key=os.getenv("DEEPSEEK_API_KEY"),
    base_url="https://api.deepseek.com/v1",
    model_info={
        "function_calling": True,
        "max_tokens": 4096,
        "context_length": 32768,
        "vision": False,
        "json_output": True,
        "family": "deepseek",
        "structured_output": True,
    }
)
```

Este dicionário `model_info` ajuda o AutoGen a entender os limites de capacidade do modelo, adaptando-se assim melhor a diferentes serviços de modelo.



## 6.3 Framework Dois: AgentScope

Se a filosofia de design do AutoGen é "impulsionar a colaboração através da conversação", então o AgentScope representa outro caminho técnico: **plataforma multi-agente com engenharia em primeiro lugar**. AgentScope, desenvolvido pela Alibaba DAMO Academy, é projetado especificamente para construir aplicações multi-agente de grande escala e alta confiabilidade. Ele não apenas fornece uma interface de programação intuitiva e fácil de usar, mas, mais importante, tem recursos de nível empresarial integrados, como implantação distribuída, recuperação de falhas e observabilidade, tornando-o particularmente adequado para construir aplicações de ambiente de produção que precisam funcionar estavelmente por longos períodos.

### 6.3.1 Design do AgentScope

Comparado com o AutoGen, a diferença central do AgentScope está em seu **design arquitetural orientado a mensagens** e **práticas de engenharia de nível industrial**. Se o AutoGen é mais como um "estúdio de conversação" flexível, então o AgentScope é um "sistema operacional de agentes" completo, fornecendo aos desenvolvedores suporte de ciclo de vida completo desde desenvolvimento, teste até implantação. Ao contrário do design baseado em herança adotado por muitos frameworks, o AgentScope escolhe **arquitetura composicional** e **modo orientado a mensagens**. Este design não apenas melhora a modularidade do sistema, mas também estabelece a base para suas excelentes capacidades de concorrência e distribuição.

(1) Sistema de Arquitetura em Camadas

Como mostrado na Figura 6.2, o AgentScope adota um design modular em camadas claro, formando um ecossistema completo de desenvolvimento de agentes desde componentes básicos de nível inferior até orquestração de aplicações de nível superior.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/6-figures/03.png" alt="Diagrama de Arquitetura do AgentScope" width="90%"/>
  <p>Figura 6.2 Diagrama de Arquitetura do AgentScope</p>
</div>

Nesta arquitetura, a camada inferior é a camada de **Componentes Fundamentais**, que fornece blocos de construção centrais para todo o framework. O componente `Message` define um formato de mensagem unificado, suportando tudo, desde interação de texto simples até conteúdo multimodal complexo; o componente `Memory` fornece gerenciamento de memória de curto e longo prazo; a camada `Model API` abstrai chamadas para diferentes grandes modelos de linguagem; e o componente `Tool` encapsula a capacidade do agente de interagir com o mundo externo.

Acima dos componentes básicos, a camada de **Infraestrutura de Nível de Agente** fornece abstrações de nível superior. Esta camada não apenas inclui vários agentes pré-construídos (como agentes que usam navegador, agentes de pesquisa profunda), mas também implementa o paradigma ReAct clássico, suportando recursos avançados como hooks de agente, chamada de ferramenta paralela e gerenciamento de estado. Particularmente notável é que esta camada suporta nativamente **execução assíncrona e controle em tempo real**, que é uma vantagem importante do AgentScope em comparação com outros frameworks.

A camada de **Cooperação Multi-Agente** é onde a inovação central do AgentScope está. `MsgHub` serve como o centro de mensagens, responsável por roteamento de mensagens e gerenciamento de estado entre agentes; enquanto o sistema `Pipeline` fornece capacidades flexíveis de orquestração de fluxo de trabalho, suportando vários modos de execução, como sequencial e concorrente. Este design permite que os desenvolvedores construam facilmente cenários complexos de colaboração multi-agente.

A camada superior de **Implantação e Desenvolvimento** reflete a ênfase do AgentScope em engenharia. `AgentScope Runtime` fornece um ambiente de execução de nível de produção, enquanto `AgentScope Studio` fornece aos desenvolvedores uma cadeia de ferramentas de desenvolvimento visual completa.

(2) Orientado a Mensagens

A inovação central do AgentScope está em sua **arquitetura orientada a mensagens**. Nesta arquitetura, todas as interações de agentes são abstraídas como o envio e recebimento de **mensagens**, em vez de chamadas de função tradicionais.

```python
from agentscope.message import Msg

# Standard structure of message
message = Msg(
    name="Alice",           # Sender name
    content="Hello, Bob!",  # Message content
    role="user",           # Role type
    metadata={             # Metadata information
        "timestamp": "2024-01-15T10:30:00Z",
        "message_type": "text",
        "priority": "normal"
    }
)
```

Usar mensagens como a unidade básica de interação traz várias vantagens-chave:

- **Desacoplamento Assíncrono**: O remetente e o receptor de mensagens são desacoplados no tempo, sem precisar esperar um pelo outro, suportando naturalmente cenários de alta concorrência.
- **Transparência de Localização**: Agentes não precisam se preocupar se outro agente está em um processo local ou em um servidor remoto; o sistema de mensagens lida automaticamente com o roteamento.
- **Observabilidade**: Cada mensagem pode ser registrada, rastreada e analisada, simplificando muito a depuração e monitoramento de sistemas complexos.
- **Confiabilidade**: Mensagens podem ser armazenadas persistentemente e refeitas. Mesmo que o sistema falhe, pode garantir a consistência eventual das interações, melhorando a tolerância a falhas do sistema.

(3) Gerenciamento de Ciclo de Vida do Agente

No AgentScope, cada agente tem um ciclo de vida claro (inicialização, execução, pausa, destruição, etc.) e é implementado com base em uma classe base unificada `AgentBase`. Os desenvolvedores geralmente só precisam se concentrar em seu método `reply` central.

```python
from agentscope.agents import AgentBase

class CustomAgent(AgentBase):
    def __init__(self, name: str, **kwargs):
        super().__init__(name=name, **kwargs)
        # Agent initialization logic

    def reply(self, x: Msg) -> Msg:
        # Agent's core response logic
        response = self.model(x.content)
        return Msg(name=self.name, content=response, role="assistant")

    def observe(self, x: Msg) -> None:
        # Agent's observation logic (optional)
        self.memory.add(x)
```

Este padrão de design separa a lógica interna do agente da comunicação externa. Os desenvolvedores só precisam definir como o agente "pensa e responde" no método `reply`.

(4) Mecanismo de Passagem de Mensagens

O AgentScope tem um **Centro de Mensagens (MsgHub)** integrado, que é o hub de toda a arquitetura orientada a mensagens. MsgHub não é apenas responsável por roteamento e distribuição de mensagens, mas também integra funções avançadas como persistência e comunicação distribuída. Ele tem as seguintes características:

- **Roteamento Flexível de Mensagens**: Suporta múltiplos modos de comunicação, como ponto a ponto, broadcast e multicast, e pode construir redes de interação flexíveis e complexas.
- **Persistência de Mensagens**: Pode salvar automaticamente todas as mensagens em bancos de dados (como SQLite, MongoDB), garantindo que o estado de tarefas de longa execução possa ser recuperado.
- **Suporte Distribuído Nativo**: Este é um recurso de assinatura do AgentScope. Agentes podem ser implantados em diferentes processos ou servidores, e `MsgHub` lidará automaticamente com comunicação entre nós através de RPC (Remote Procedure Call), completamente transparente para os desenvolvedores.

Essas capacidades de engenharia fornecidas pela arquitetura subjacente tornam o AgentScope mais vantajoso do que frameworks tradicionais orientados a conversação ao lidar com cenários de aplicação complexos que exigem alta concorrência e alta confiabilidade. É claro que isso também requer que os desenvolvedores entendam e se adaptem ao paradigma de programação assíncrona orientada a mensagens.

Na próxima seção, experimentaremos profundamente as capacidades do framework AgentScope através de um caso prático específico, o jogo de Lobisomem dos Três Reinos, especialmente suas vantagens em lidar com interações concorrentes.

### 6.3.2 Jogo de Lobisomem dos Três Reinos

Para entender profundamente a arquitetura orientada a mensagens do AgentScope e capacidades de colaboração multi-agente, construiremos um jogo "Lobisomem dos Três Reinos" que integra elementos culturais clássicos chineses. Este caso não apenas demonstra as vantagens do AgentScope em lidar com interações multi-agente complexas, mas, mais importante, demonstra como aproveitar totalmente o poder da arquitetura orientada a mensagens em um cenário que requer **colaboração em tempo real**, **interpretação de papéis** e **jogo estratégico**. Ao contrário do Lobisomem tradicional, nosso "Lobisomem dos Três Reinos" introduz personagens clássicos como Liu Bei, Guan Yu e Zhuge Liang no jogo. Cada agente não apenas tem que completar as tarefas básicas do Lobisomem (como lobisomem matando, vidente verificando, aldeão raciocinando), mas também incorpora os traços de personalidade e padrões de comportamento dos personagens correspondentes dos Três Reinos. Este design nos permite observar o desempenho do AgentScope ao lidar com **modelagem de papel multi-nível**.

(1) Design de Arquitetura e Componentes Principais

O design do sistema deste caso segue o princípio de desacoplamento em camadas, dividindo a lógica do jogo em três níveis independentes, cada um dos quais mapeia para um ou mais componentes centrais do AgentScope:

- **Camada de Controle do Jogo**: Uma classe `ThreeKingdomsWerewolfGame` serve como o controlador principal do jogo, responsável por manter o estado global (como lista de sobrevivência de jogadores, estágio atual do jogo), avançar o processo do jogo (chamando fase noturna, fase diurna) e julgar vitória ou derrota.
- **Camada de Interação de Agente**: Completamente impulsionada por `MsgHub`. Toda a comunicação entre agentes, sejam negociações secretas entre lobisomens ou debates públicos durante o dia, é roteada e distribuída através do centro de mensagens.
- **Camada de Modelagem de Papel**: Cada jogador é uma instância baseada em `DialogAgent`. Através de prompts de sistema cuidadosamente projetados, injetamos em cada agente a identidade dupla de "papel do jogo" e "personalidade dos Três Reinos".

(2) Fluxo de Jogo Orientado a Mensagens

O design central deste caso é usar **orientação a mensagens** em vez de **máquina de estado** para gerenciar o fluxo do jogo. Em implementações tradicionais, transições de fase do jogo são geralmente controladas por uma máquina de estado centralizada. No paradigma AgentScope, o fluxo do jogo é naturalmente modelado como uma série de padrões de interação de mensagem bem definidos.

Por exemplo, a implementação da fase lobisomem não é uma simples chamada de função, mas cria dinamicamente um canal de comunicação temporário e privado que inclui apenas jogadores lobisomem através de `MsgHub`:

```python
async def werewolf_phase(self, round_num: int):
    """Werewolf phase - demonstrating message-driven collaboration mode"""
    if not self.werewolves:
        return None

    # Establish werewolf-exclusive communication channel through message center
    async with MsgHub(
        self.werewolves,
        enable_auto_broadcast=True,
        announcement=await self.moderator.announce(
            f"Werewolves, please discuss tonight's kill target. Surviving players: {format_player_list(self.alive_players)}"
        ),
    ) as werewolves_hub:
        # Discussion phase: werewolves exchange strategies through messages
        for _ in range(MAX_DISCUSSION_ROUND):
            for wolf in self.werewolves:
                await wolf(structured_model=DiscussionModelCN)

        # Voting phase: collect and count werewolves' kill decisions
        werewolves_hub.set_auto_broadcast(False)
        kill_votes = await fanout_pipeline(
            self.werewolves,
            msg=await self.moderator.announce("Please choose kill target"),
            structured_model=WerewolfKillModelCN,
            enable_gather=False,
        )
```

A vantagem deste design é que a lógica do jogo é claramente expressa como "em um contexto específico, que modo de troca de mensagens conduzir", em vez de uma série de transições de estado rígidas. Discussão diurna (broadcast completo), verificação de vidente (solicitação ponto a ponto) e outras fases seguem o mesmo paradigma de design.

(3) Restringindo Regras do Jogo com Saída Estruturada

Um desafio-chave em jogos de Lobisomem é como garantir que o comportamento do agente esteja em conformidade com as regras do jogo. O **mecanismo de saída estruturada** do AgentScope fornece uma solução para este problema. Definimos modelos de dados rigorosos para diferentes comportamentos do jogo:

```python
class DiscussionModelCN(BaseModel):
    """Output format for discussion phase"""
    reach_agreement: bool = Field(
        description="Whether consensus has been reached",
        default=False
    )
    confidence_level: int = Field(
        description="Confidence level in current reasoning (1-10)",
        ge=1, le=10,
        default=5
    )
    key_evidence: Optional[str] = Field(
        description="Key evidence supporting your viewpoint",
        default=None
    )

class WitchActionModelCN(BaseModel):
    """Output format for witch action"""
    use_antidote: bool = Field(description="Whether to use antidote")
    use_poison: bool = Field(description="Whether to use poison")
    target_name: Optional[str] = Field(description="Poison target player name")
```

Desta forma, não apenas garantimos **consistência de formato** da saída do agente, mas, mais importante, alcançamos **restrição automatizada de regras do jogo**. Por exemplo, o agente bruxa não pode usar antídoto e veneno no mesmo alvo ao mesmo tempo, e o vidente pode verificar apenas um jogador por noite. Essas restrições são automaticamente executadas através de definições de campo e lógica de validação de modelos de dados.

(4) Duplo Desafio de Modelagem de Papel

Neste caso, o desafio técnico mais interessante é como fazer com que os agentes desempenhem bem dois níveis de papéis ao mesmo tempo: **papel funcional do jogo** (lobisomem, vidente, etc.) e **papel de personalidade cultural** (Liu Bei, Cao Cao, etc.). Resolvemos este problema através de engenharia de prompt:

```python
def get_role_prompt(role: str, character: str) -> str:
    """Get role prompt - integrating game rules and character personality"""
    base_prompt = f"""You are {character}, playing {role} in this Three Kingdoms Werewolf game.

Important rules:
1. You can only participate in the game through dialogue and reasoning
2. Do not attempt to call any external tools or functions
3. Strictly reply in the required JSON format

Role characteristics:
"""

    if role == "Werewolf":
        return base_prompt + f"""
- You are in the werewolf camp, with the goal of eliminating all good people
- At night, you can negotiate with other werewolves on kill targets
- During the day, you must hide your identity and mislead good people
- Speak and act with {character}'s personality
"""
```

Este design nos permite observar um fenômeno interessante: diferentes personagens dos Três Reinos, ao desempenhar o mesmo papel do jogo, exibirão estratégias e estilos de fala completamente diferentes. Por exemplo, "Cao Cao" desempenhando um lobisomem pode aparecer mais astuto e bom em disfarce, enquanto "Zhang Fei" desempenhando um lobisomem pode aparecer mais direto e impulsivo.

(5) Processamento Concorrente e Mecanismo de Tolerância a Falhas

A arquitetura assíncrona do AgentScope desempenha um papel importante neste jogo multi-agente. O jogo frequentemente tem cenários que requerem **coletar simultaneamente decisões de múltiplos agentes**, como a fase de votação:

```python
# Collect voting decisions from all players in parallel
vote_msgs = await fanout_pipeline(
    self.alive_players,
    await self.moderator.announce("Please vote to choose the player to eliminate"),
    structured_model=get_vote_model_cn(self.alive_players),
    enable_gather=False,
)
```

`fanout_pipeline` nos permite enviar a mesma mensagem para todos os agentes em paralelo e coletar assincronamente suas respostas. Isso não apenas melhora a eficiência de execução do jogo, mas, mais importante, simula o cenário de "votação simultânea" em jogos reais de Lobisomem. Ao mesmo tempo, adicionamos tratamento de tolerância a falhas em pontos-chave:

```python
try:
    response = await wolf(
        "Please analyze the current situation and express your viewpoint.",
        structured_model=DiscussionModelCN
    )
except Exception as e:
    print(f"⚠️ {wolf.name} error during discussion: {e}")
    # Create default response to ensure game continues
    default_response = DiscussionModelCN(
        reach_agreement=False,
        confidence_level=5,
        key_evidence="Unable to analyze temporarily"
    )
```

Este design garante que, mesmo que um agente encontre uma exceção, todo o processo do jogo possa continuar.

(6) Saída e Resumo do Caso

Para experimentar mais intuitivamente o mecanismo de operação do AgentScope, o seguinte é um trecho de log de execução real da fase noturna do jogo, mostrando o processo de dois agentes lobisomem desempenhando "Sun Quan" e "Zhou Yu" conduzindo negociações secretas e executando uma morte.

```
🎮 Welcome to Three Kingdoms Werewolf!

=== Game Initialization ===
Game Moderator: 📢 【Sun Quan】You are playing a werewolf in this Three Kingdoms Werewolf game, your character is Sun Quan. You can kill a player at night
Game Moderator: 📢 【Zhou Yu】You are playing a werewolf in this Three Kingdoms Werewolf game, your character is Zhou Yu. You can kill a player at night
...

Game Moderator: 📢 Three Kingdoms Werewolf game begins! Participants: Sun Quan, Zhou Yu, Cao Cao, Zhang Fei, Sima Yi, Zhao Yun
✅ Game setup complete, 6 players in total

=== Round 1 ===
🌙 Night 1 falls, everyone close your eyes...

【Werewolf Phase】
Game Moderator: 📢 🐺 Werewolves please open your eyes, choose tonight's kill target...
Game Moderator: 📢 Werewolves, please discuss tonight's kill target. Surviving players: Sun Quan, Zhou Yu, Cao Cao, Zhang Fei, Sima Yi, Zhao Yun

Sun Quan: Tonight we should eliminate Zhou Yu, this person is extremely intelligent and poses a great threat to us.
Zhou Yu: Sun Quan, you make a good point. But although Zhou Yu is intelligent, he may not be the biggest threat tonight. Cao Cao has great power, if we don't eliminate him early, it may be unfavorable to us.
Sun Quan: Cao Cao is indeed a threat, but if Zhou Yu lives, he can see through our schemes. Better to solve the immediate hidden danger first.
Zhou Yu: Sun Quan, your concern is not without reason. But if Cao Cao becomes our enemy, he can unite other forces to pose a greater threat to us.
Sun Quan: What you say also makes sense, Cao Cao's alliance is indeed troublesome. Then let's deal with Cao Cao first.
Zhou Yu: Very good, Sun Quan. Cao Cao is our primary target tonight.

Game Moderator: 📢 Please choose kill target
Sun Quan: I agree, Cao Cao must be eliminated.
Zhou Yu: I agree, Cao Cao is the target we need to solve tonight.

【Seer Phase】
Game Moderator: 📢 🔮 Seer please open your eyes, choose the player to verify...
Cao Cao: I want to verify Sun Quan.
Game Moderator: 📢 Verification result: Sun Quan is a werewolf

【Witch Phase】
Game Moderator: 📢 🧙‍♀️ Witch please open your eyes...
Game Moderator: 📢 Tonight Cao Cao was killed by werewolves
Zhang Fei: I used the antidote to save Cao Cao last night, now the antidote has been used up.
Game Moderator: 📢 You used the antidote to save Cao Cao

Game Moderator: 📢 Last night was peaceful, no one died.

【Day Discussion Phase】
Game Moderator: 📢 ☀️ Day 1 dawns, everyone please open your eyes...
Game Moderator: 📢 Now begin free discussion. Surviving players: Sun Quan, Zhou Yu, Cao Cao, Zhang Fei, Sima Yi, Zhao Yun

Sun Quan: Everyone, Cao Cao has great power and is a potential threat to us all. Tonight I suggest we concentrate our forces against him.
Zhou Yu: Sun Quan is absolutely right, Cao Cao is not only powerful himself but may also ally with other players, posing a greater threat to us.
Cao Cao: I verified Sun Quan last night, originally thought he was a good person, but the game moderator's result was werewolf. This means there's a werewolf lying.
Zhang Fei: I did save Cao Cao last night, indicating he was attacked by werewolves. But Cao Cao's verification result of Sun Quan is suspicious.
Sima Yi: There seems to be a contradiction between Cao Cao's verification result and Zhang Fei's saving action, we need more information to determine who is the werewolf.
Zhao Yun: The situation is indeed complex, we need to carefully analyze everyone's statements.

【Voting Phase】
Game Moderator: 📢 Please vote to choose the player to eliminate

Sun Quan: Cao Cao's threat is still great, I insist we should vote for him.
Zhou Yu: Based on last night's situation, I think we should vote for Cao Cao first, his threat is the greatest.
Cao Cao: I choose to vote for Sun Quan. According to the game moderator's feedback, Sun Quan is indeed a werewolf.
Zhang Fei: I insist on the fact that I saved Cao Cao last night, but the fact that Sun Quan was found to be a werewolf confuses me.
Sima Yi: We need more information to determine who is the werewolf.

[Game continues...]
```

### 6.3.3 Análise das Vantagens e Limitações do AgentScope

Através deste caso "Lobisomem dos Três Reinos", experimentamos profundamente as vantagens centrais do framework AgentScope. O framework, com sua arquitetura orientada a mensagens como núcleo, mapeia elegantemente processos complexos de jogo em uma série de eventos de passagem de mensagens concorrentes e assíncronos, evitando assim a rigidez e complexidade de máquinas de estado tradicionais. Combinado com sua poderosa capacidade de saída estruturada, transformamos diretamente regras do jogo em restrições de nível de código, melhorando muito a estabilidade e previsibilidade do sistema. Este paradigma de design não apenas demonstra suas vantagens nativas de concorrência em desempenho, mas também garante que, mesmo que um único agente encontre uma exceção, o processo geral possa funcionar robustamente no tratamento de tolerância a falhas.

No entanto, as vantagens de engenharia do AgentScope também trazem um certo custo de complexidade. Embora sua arquitetura orientada a mensagens seja poderosa, ela tem requisitos técnicos altos para os desenvolvedores, exigindo compreensão de programação assíncrona, comunicação distribuída e outros conceitos. Para cenários simples de conversação multi-agente, esta arquitetura pode parecer excessivamente complexa, com o risco de "engenharia excessiva". Além disso, como um framework relativamente novo, seu ecossistema e recursos da comunidade ainda precisam de melhorias adicionais. Portanto, o AgentScope é mais adequado para construir sistemas multi-agente de grande escala e alta confiabilidade de nível de produção, enquanto para desenvolvimento rápido de protótipos ou cenários de aplicação simples, escolher um framework mais leve pode ser mais apropriado.



## 6.4 Framework Três: CAMEL

Ao contrário de frameworks abrangentes como AutoGen e AgentScope, o objetivo central original do CAMEL é explorar como permitir que dois agentes colaborem autonomamente para resolver tarefas complexas através de "interpretação de papéis" com intervenção humana mínima.

### 6.4.1 Colaboração Autônoma no CAMEL

A pedra angular da colaboração autônoma do CAMEL são dois conceitos centrais: **Role-Playing** e **Inception Prompting**.

(1) Role-Playing

No design original do CAMEL, uma tarefa é geralmente completada por dois agentes colaborando. Esses dois agentes são atribuídos papéis complementares e claramente definidos. Um desempenha o **"AI User"**, responsável por propor requisitos, emitir instruções e conceber passos de tarefa; o outro desempenha o **"AI Assistant"**, responsável por executar operações específicas e fornecer soluções com base em instruções.

Por exemplo, em uma tarefa para "desenvolver uma ferramenta de análise de estratégia de negociação de ações":

- O papel **AI User** pode ser um "trader de ações sênior". Ele entende o mercado e estratégias, mas não entende programação.
- O papel **AI Assistant** é um "excelente programador Python". Ele é proficiente em programação, mas não sabe nada sobre negociação de ações.

Através desta configuração, o processo de resolução de tarefas é naturalmente transformado em uma conversação entre dois "especialistas de domínio cruzado". O trader propõe requisitos profissionais, o programador os transforma em implementação de código, e os dois colaboram para completar tarefas complexas que nenhum deles poderia realizar independentemente.

(2) Inception Prompting

Simplesmente definir papéis não é suficiente. Como podemos garantir que duas IAs possam sempre "permanecer em seus papéis" e se mover eficientemente em direção a um objetivo comum sem supervisão humana contínua? É aqui que a tecnologia central do CAMEL, inception prompting, entra em jogo. "Inception prompting" é uma instrução inicial cuidadosamente projetada e estruturada (System Prompt) injetada em ambos os agentes antes do início da conversação. Esta instrução é como um "programa de ação" implantado nos agentes, e geralmente inclui as seguintes partes-chave:

- **Esclarecer próprio papel**: Por exemplo, "Você é um trader de ações sênior..."
- **Informar papel do colaborador**: Por exemplo, "Você está trabalhando com um excelente programador Python..."
- **Definir objetivo comum**: Por exemplo, "Seu objetivo comum é desenvolver uma ferramenta de análise de estratégia de negociação de ações."
- **Definir restrições comportamentais e protocolos de comunicação**: Esta é a parte mais crítica. Por exemplo, a instrução exigirá que o usuário AI "proponha apenas um passo claro e específico de cada vez" e exigirá que o assistente AI "não peça mais detalhes antes de completar o passo anterior", enquanto também especifica que ambas as partes precisam usar marcadores específicos (como `<SOLUTION>`) no final de suas respostas para identificar a conclusão da tarefa.

Essas restrições garantem que a conversação não se desvie do tópico ou caia em loops ineficazes, mas avance de maneira altamente estruturada e orientada a tarefas, como mostrado na Figura 6.3.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/6-figures/04.png" alt="CAMEL Criando Robô de Negociação de Ações" width="90%"/>
  <p>Figura 6.3 CAMEL Criando Robô de Negociação de Ações</p>
</div>

Na próxima seção, experimentaremos este processo através de um exemplo específico.

### 6.4.2 E-book de Divulgação Científica de IA

Para entender as capacidades de interpretação de papéis do framework CAMEL, construiremos um caso de colaboração prático: ter um psicólogo AI trabalhando com um autor AI para co-criar um e-book curto sobre "A Psicologia da Procrastinação". Este caso incorpora a vantagem central do CAMEL de permitir que dois agentes aproveitem seus respectivos domínios profissionais para completar colaborativamente tarefas criativas complexas com as quais um único agente teria dificuldades.

(1) Configuração da Tarefa

**Configuração do Cenário**: Criar um e-book de divulgação científica sobre a psicologia da procrastinação para leitores em geral, exigindo tanto rigor científico quanto boa legibilidade.

**Papéis dos Agentes**:

- **Psicólogo**: Possui base teórica profunda em psicologia, familiarizado com ciência comportamental cognitiva, neurociência e outros campos relacionados, capaz de fornecer insights acadêmicos profissionais e suporte de pesquisa empírica
- **Escritor**: Tem excelentes habilidades de escrita e capacidade narrativa, bom em transformar conceitos acadêmicos complexos em texto vívido e fácil de entender, focando na experiência do leitor e legibilidade do conteúdo

(2) Definir Tarefa de Colaboração

Primeiro, precisamos esclarecer o objetivo comum dos dois especialistas AI. Definimos esta tarefa através de uma string detalhada `task_prompt`.

```python
from colorama import Fore
from camel.societies import RolePlaying
from camel.utils import print_text_animated

# Define collaboration task
task_prompt = """
Create a short e-book on "The Psychology of Procrastination" for general readers interested in psychology.
Requirements:
1. Content should be scientifically rigorous, based on empirical research
2. Language should be easy to understand, avoiding excessive professional terminology
3. Include practical improvement suggestions and case analysis
4. Length controlled at 8000-10000 words
5. Clear structure, including introduction, core chapters, and summary
"""

print(Fore.YELLOW + f"Collaboration task:\n{task_prompt}\n")
```

`task_prompt` é a "especificação de tarefa" para toda a colaboração. Não é apenas o objetivo que queremos alcançar, mas também será usado nos bastidores pelo CAMEL para gerar "inception prompts", garantindo que a conversação entre os dois agentes sempre gire em torno deste objetivo central.

(3) Inicializar "Sociedade" de Interpretação de Papéis

Em seguida, criamos uma instância de sessão `RolePlaying`. Esta é a operação central do CAMEL, que rapidamente constrói uma "sociedade" de colaboração de dois agentes com base nos papéis e tarefas que fornecemos.

```python
# Initialize role-playing session
# AI writer as "user", responsible for proposing writing structure and requirements
# AI psychologist as "assistant", responsible for providing professional knowledge and content
role_play_session = RolePlaying(
    assistant_role_name="Psychologist",
    user_role_name="Writer",
    task_prompt=task_prompt,
    with_task_specify=False, # In this example, we directly use the given task_prompt
)

print(Fore.CYAN + f"Specific task description:\n{role_play_session.task_prompt}\n")
```

`RolePlaying` é uma API de alto nível fornecida pelo CAMEL que encapsula engenharia de prompt complexa. Precisamos apenas passar os nomes dos dois papéis e a tarefa. No design do CAMEL, o papel `user` é o "condutor" e "demandante" da conversação, enquanto o papel `assistant` é o "executor" e "fornecedor de soluções". Portanto, atribuímos o "escritor" responsável pelo planejamento da estrutura a `user_role_name` e o "psicólogo" responsável por fornecer conhecimento profissional a `assistant_role_name`.

(4) Iniciar e Executar Conversação Automatizada

Finalmente, escrevemos um loop para conduzir todo o processo de conversação, permitindo que os dois especialistas AI comecem sua colaboração automatizada.

```python
# Start collaboration conversation
chat_turn_limit, n = 30, 0
# Call init_chat() to get the initial conversation message generated by AI
input_msg = role_play_session.init_chat()

while n < chat_turn_limit:
    n += 1
    # step() method drives a complete round of conversation, AI user and AI assistant each speak once
    assistant_response, user_response = role_play_session.step(input_msg)

    # Check if messages are returned to prevent premature conversation termination
    if assistant_response.msg is None or user_response.msg is None:
        break

    print_text_animated(Fore.BLUE + f"Writer (AI User):\n\n{user_response.msg.content}\n")
    print_text_animated(Fore.GREEN + f"Psychologist (AI Assistant):\n\n{assistant_response.msg.content}\n")

    # Check task completion flag
    if "<CAMEL_TASK_DONE>" in user_response.msg.content or "<CAMEL_TASK_DONE>" in assistant_response.msg.content:
        print(Fore.MAGENTA + "✅ E-book creation completed!")
        break

    # Use assistant's reply as input for next round of conversation
    input_msg = assistant_response.msg

print(Fore.YELLOW + f"Total of {n} rounds of collaborative conversation")
```

Este loop `while` é o núcleo da colaboração automatizada. A conversação é automaticamente iniciada pelo método `init_chat()` com base na tarefa e papéis, sem a necessidade de escrever manualmente uma abertura. Cada passo do loop conduz uma rodada completa de interação chamando `step()` (escritor propõe requisitos, psicólogo fornece conteúdo), e usa a saída do psicólogo da rodada anterior como entrada para a próxima rodada, formando uma cadeia de criação. Todo o processo continuará até que o limite de turnos de conversação predefinido seja alcançado, ou termina automaticamente depois que qualquer agente produz a flag de conclusão de tarefa `<CAMEL_TASK_DONE>`.

(5) Demonstração do Processo de Colaboração

Ao executar o código acima, não obtemos apenas uma longa string de perguntas e respostas monótonas, mas podemos observar um processo de colaboração altamente estruturado, como uma equipe de especialistas humanos, procedendo automaticamente. Todo o processo de criação se divide naturalmente em vários estágios:

**Estágio 1 (aproximadamente rodadas 1-5): Construção de Framework e Alinhamento de Objetivos** Nos primeiros estágios da conversação, o agente "escritor" primeiro desempenha o papel de liderança, propondo ideias iniciais para a estrutura geral e arranjo de capítulos do e-book. Posteriormente, o "psicólogo" revisa e complementa este framework de uma perspectiva profissional, garantindo que módulos acadêmicos centrais (como fundamentos teóricos, conceitos-chave, etc.) não sejam omitidos, alcançando assim consenso sobre a saída final no início da colaboração.

**Estágio 2 (aproximadamente rodadas 6-20): Geração de Conteúdo Central e Tradução de Conhecimento** Este é o estágio de criação de conteúdo mais eficiente. O modo de colaboração torna-se um loop estável "solicitação-resposta":

- **Psicólogo**: Responsável por fornecer conhecimento profissional "hardcore", como explicações científicas de conceitos centrais como "teoria de desconto temporal" e "déficits de função executiva", e citando pesquisas experimentais relevantes para apoiar pontos de vista.
- **Escritor**: Desempenha o papel de "tradutor", transformando esses conceitos acadêmicos rigorosos, mas potencialmente obscuros, em metáforas vívidas e imagéticas e casos relacionados à vida. Por exemplo, pode comparar o conceito de "viés presente no cérebro" a "uma criança teimosa que só se importa com doces imediatos e não com saúde a longo prazo."

**Estágio 3 (aproximadamente rodadas 21-25): Otimização Iterativa e Garantia de Qualidade** Quando o conteúdo principal do livro está completo, o foco da conversação muda para polir e melhorar o texto existente. Neste momento, os papéis dos dois agentes passam por mudanças sutis:

- **Escritor**: Mais focado em examinar a fluência geral, coerência lógica e estilo de linguagem do artigo, propondo sugestões de revisão da perspectiva da "experiência do leitor."
- **Psicólogo**: Novamente desempenha o papel de "verificador de fatos", garantindo que a precisão científica do conhecimento central não seja perdida durante a tradução e polimento, e complementando certos pontos de vista com suporte de pesquisa empírica mais poderoso.

**Estágio 4 (Conclusão): Resumo e Elevação** Nas últimas rodadas de conversação, ambas as partes colaboram para completar o resumo de sugestões práticas e a revisão de todo o livro, garantindo que o e-book tenha um final claro e poderoso que deixe uma impressão profunda nos leitores e forneça valor prático.

```
Collaboration task:
Create a short e-book on "The Psychology of Procrastination" for general readers interested in psychology.
Requirements:
1. Content should be scientifically rigorous, based on empirical research
2. Language should be easy to understand, avoiding excessive professional terminology
3. Include practical improvement suggestions and case analysis
4. Length controlled at 8000-10000 words
5. Clear structure, including introduction, core chapters, and summary

Specific task description:
Write an 8000–10000 word short e-book "The Psychology of Procrastination" for general readers: empirically based, easy to understand. Structure: introduction, causes (cognitive/emotional/reward), motivation and decision-making, habit formation and intervention, practical strategies and exercises, three case analyses, summary and resources. Each chapter contains research citations and actionable steps.

Writer:
Instruction: Please write a 400–600 word Chinese draft for the "Introduction" chapter of the e-book...
Input: None

Psychologist:
Solution:
Draft: Procrastination refers to the behavior and internal tendency of repeatedly postponing or avoiding a task despite knowing it should be completed. It can be an occasional time management problem...

Next request.

Writer:
Instruction: Please revise the following introduction draft into a 450–550 word Chinese text...
Input: Draft: Procrastination refers to the behavior and internal tendency of repeatedly postponing or avoiding a task...
.....
```

### 6.4.3 Análise das Vantagens e Limitações do CAMEL

Através do caso de criação de e-book anterior, experimentamos profundamente o paradigma único de interpretação de papéis do framework CAMEL. Agora vamos analisar objetivamente as vantagens e limitações desta filosofia de design para fazer escolhas técnicas sábias em projetos reais.

(1) Vantagens

A maior vantagem do CAMEL está em sua filosofia de design "arquitetura leve, prompting pesado". Comparado ao gerenciamento de conversação complexo do AutoGen e à arquitetura distribuída do AgentScope, o CAMEL pode alcançar colaboração de agentes de alta qualidade através de prompts iniciais cuidadosamente projetados. Este comportamento colaborativo naturalmente emergente é frequentemente mais flexível e eficiente do que fluxos de trabalho codificados rigidamente.

Vale a pena notar que o framework CAMEL está passando por desenvolvimento e evolução rápidos. Do seu [repositório GitHub](https://github.com/camel-ai/camel), podemos ver que o CAMEL é muito mais do que um framework simples de colaboração de dois agentes e atualmente tem:

- **Capacidades Multimodais**: Suporta colaboração de agentes em múltiplas modalidades, como texto, imagem e áudio
- **Integração de Ferramentas**: Biblioteca de ferramentas rica integrada, incluindo busca, cálculo, execução de código, etc.
- **Adaptação de Modelo**: Suporta múltiplos backends LLM como OpenAI, Anthropic, Google e modelos de código aberto
- **Vinculação de Ecossistema**: Alcançou interoperabilidade com frameworks mainstream como LangChain, CrewAI e AutoGen

(2) Principais Limitações

1. Alta Dependência de Engenharia de Prompt

O sucesso do CAMEL depende em grande parte da qualidade dos prompts iniciais. Isso traz vários desafios:

- **Limiar de Design de Prompt**: Requer compreensão profunda do domínio alvo e características comportamentais do LLM
- **Complexidade de Depuração**: Quando a colaboração é ineficaz, é difícil identificar se o problema está na definição de papel, descrição de tarefa ou regras de interação
- **Desafio de Consistência**: Diferentes LLMs podem ter diferentes compreensões do mesmo prompt

2. Limitações de Escala de Colaboração

Embora o CAMEL tenha um desempenho excelente em colaboração de dois agentes, ele enfrenta desafios ao lidar com cenários multi-agente de grande escala:

- **Gerenciamento de Conversação**: Faltam mecanismos complexos de roteamento de conversação como o AutoGen
- **Sincronização de Estado**: Não tem capacidades de gerenciamento de estado distribuído como o AgentScope
- **Resolução de Conflitos**: Falta mecanismos de arbitragem eficazes quando múltiplos agentes discordam

3. Limites de Aplicabilidade de Tarefas

O CAMEL é particularmente adequado para tarefas que exigem colaboração profunda e pensamento criativo, mas pode não ser a escolha ideal em certos cenários:

- **Controle de Processo Rigoroso**: Para tarefas que exigem controle de passo preciso, a estrutura de grafo do LangGraph é mais adequada
- **Concorrência em Grande Escala**: A arquitetura orientada a mensagens do AgentScope tem mais vantagens em cenários de alta concorrência
- **Árvores de Decisão Complexas**: O modo de chat em grupo do AutoGen é mais flexível em cenários de decisão multipartidária

No geral, o CAMEL representa um paradigma de colaboração multi-agente único e elegante. Através de seu design de interpretação de papéis "centrado no humano", ele transforma problemas complexos de engenharia de sistema em padrões intuitivos de colaboração interpessoal. À medida que seu ecossistema continua a melhorar e funções continuam a se expandir, o CAMEL está se tornando uma das escolhas importantes para construir sistemas de colaboração inteligente.

## 6.5 Framework Quatro: LangGraph

### 6.5.1 Visão Geral da Estrutura do LangGraph

LangGraph, como uma extensão importante do ecossistema LangChain, representa uma direção completamente nova no design de frameworks de agentes. Ao contrário dos frameworks baseados em "conversação" introduzidos anteriormente (como AutoGen e CAMEL), LangGraph modela o fluxo de execução do agente como uma **Máquina de Estado** e o representa como um **Grafo Direcionado**. Neste paradigma, os **Nós** do grafo representam passos computacionais específicos (como chamar LLM, executar ferramentas), enquanto **Arestas** definem a lógica de transição de um nó para outro. O aspecto revolucionário deste design é que ele suporta nativamente loops, tornando sem precedentes intuitivo e simples construir fluxos de trabalho de agentes complexos capazes de iteração, reflexão e autocorreção.

Para entender o LangGraph, precisamos primeiro compreender seus três componentes básicos.

**Primeiro, é o estado global (State)**. Todo o processo de execução do grafo gira em torno de um objeto de estado compartilhado. Este estado é geralmente definido como um `TypedDict` Python, que pode conter qualquer informação que você precise rastrear, como histórico de conversação, resultados intermediários, contagem de iterações, etc. Todos os nós podem ler e atualizar este estado central.

```python
from typing import TypedDict, List

# Define global state data structure
class AgentState(TypedDict):
    messages: List[str]      # Conversation history
    current_task: str        # Current task
    final_answer: str        # Final answer
    # ... any other state to track
```

**Segundo, são os nós (Nodes)**. Cada nó é uma função Python que recebe o estado atual como entrada e retorna um estado atualizado como saída. Nós são unidades que executam trabalho específico.

```python
# Define a "planner" node function
def planner_node(state: AgentState) -> AgentState:
    """Formulate a plan based on current task and update state."""
    current_task = state["current_task"]
    # ... call LLM to generate plan ...
    plan = f"Plan generated for task '{current_task}'..."

    # Append new message to state
    state["messages"].append(plan)
    return state

# Define an "executor" node function
def executor_node(state: AgentState) -> AgentState:
    """Execute latest plan and update state."""
    latest_plan = state["messages"][-1]
    # ... execute plan and get result ...
    result = f"Result of executing plan '{latest_plan}'..."

    state["messages"].append(result)
    return state
```

**Finalmente, são as arestas (Edges)**. Arestas são responsáveis por conectar nós e definir a direção do fluxo de trabalho. A aresta mais simples é uma aresta regular, que especifica que a saída de um nó sempre flui para outro nó fixo. O recurso mais poderoso do LangGraph está em **Arestas Condicionais**. Ele usa uma função para julgar o estado atual e então decidir dinamicamente para qual nó saltar em seguida. Esta é a chave para implementar loops e ramificações lógicas complexas.

```python
def should_continue(state: AgentState) -> str:
    """Condition function: decide next route based on state."""
    # Assume if messages are less than 3, need to continue planning
    if len(state["messages"]) < 3:
        # Returned string needs to match the key defined when adding conditional edge
        return "continue_to_planner"
    else:
        state["final_answer"] = state["messages"][-1]
        return "end_workflow"
```

Depois de definir estado, nós e arestas, podemos montá-los em um fluxo de trabalho executável como blocos de construção.

```python
from langgraph.graph import StateGraph, END

# Initialize a state graph and bind our defined state structure
workflow = StateGraph(AgentState)

# Add node functions to the graph
workflow.add_node("planner", planner_node)
workflow.add_node("executor", executor_node)

# Set graph entry point
workflow.set_entry_point("planner")

# Add regular edge, connecting planner and executor
workflow.add_edge("planner", "executor")

# Add conditional edge, implementing dynamic routing
workflow.add_conditional_edges(
    # Starting node
    "executor",
    # Judgment function
    should_continue,
    # Route mapping: map judgment function's return value to target node
    {
        "continue_to_planner": "planner", # If returns "continue_to_planner", jump back to planner node
        "end_workflow": END               # If returns "end_workflow", end process
    }
)

# Compile graph, generate executable application
app = workflow.compile()

# Run graph
inputs = {"current_task": "Analyze recent AI industry news", "messages": []}
for event in app.stream(inputs):
    print(event)
```

### 6.5.2 Assistente de Perguntas e Respostas de Três Passos
Depois de entender os conceitos centrais do LangGraph, consolidaremos o que aprendemos através de um caso prático. Construiremos um assistente de diálogo de perguntas e respostas simplificado que segue um processo claro e fixo de três passos para responder às perguntas do usuário:

1. **Entender**: Primeiro, analisar a intenção de consulta do usuário.
2. **Buscar**: Então, simular a busca de informações relacionadas à intenção.
3. **Responder**: Finalmente, gerar a resposta final com base na intenção e informações buscadas.

Este caso demonstrará claramente como definir estado, criar nós e conectá-los linearmente em um fluxo de trabalho completo. Dividiremos o código em quatro passos centrais: definir estado, criar nós, construir grafo e executar aplicação.

(1) Definir Estado Global

Primeiro, precisamos definir um estado global que percorra todo o fluxo de trabalho. **Esta é uma estrutura de dados compartilhada que é passada entre cada nó do grafo, servindo como o contexto persistente do fluxo de trabalho.** Cada nó pode ler dados desta estrutura e atualizá-la.

```python
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages

class SearchState(TypedDict):
    messages: Annotated[list, add_messages]
    user_query: str      # User requirement summary after LLM understanding
    search_query: str    # Optimized search query for Tavily API
    search_results: str  # Results returned by Tavily search
    final_answer: str    # Final generated answer
    step: str            # Mark current step
```

Criamos o `SearchState` `TypedDict`, definindo um esquema de dados claro para o objeto de estado. Um design-chave é a inclusão de ambos os campos `user_query` e `search_query`. Isso permite que o agente primeiro otimize a pergunta em linguagem natural do usuário em palavras-chave refinadas mais adequadas para motores de busca, melhorando assim significativamente a qualidade dos resultados de busca.

(2) Definir Nós de Fluxo de Trabalho

Depois de definir a estrutura de estado, o próximo passo é criar os vários nós que compõem nosso fluxo de trabalho. No LangGraph, cada nó é uma função Python que executa uma tarefa específica. Essas funções recebem o objeto de estado atual como entrada e retornam um dicionário contendo campos atualizados.

Antes de definir nós, primeiro completamos a configuração de inicialização do projeto, incluindo carregar variáveis de ambiente e instanciar o grande modelo de linguagem.

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, AIMessage, SystemMessage
from tavily import TavilyClient

# Load environment variables from .env file
load_dotenv()

# Initialize model
# We will use this llm instance to drive the intelligence of all nodes
llm = ChatOpenAI(
    model=os.getenv("LLM_MODEL_ID", "gpt-4o-mini"),
    api_key=os.getenv("LLM_API_KEY"),
    base_url=os.getenv("LLM_BASE_URL", "https://api.openai.com/v1"),
    temperature=0.7
)
# Initialize Tavily client
tavily_client = TavilyClient(api_key=os.getenv("TAVILY_API_KEY"))
```

Agora, vamos criar os três nós centrais um por um.

(1) Nó de Entendimento e Consulta

Este nó é o primeiro passo do fluxo de trabalho. Sua responsabilidade é entender a intenção do usuário e gerar uma consulta de busca otimizada para ela.

```python
def understand_query_node(state: SearchState) -> dict:
    """Step 1: Understand user query and generate search keywords"""
    user_message = state["messages"][-1].content

    understand_prompt = f"""Analyze the user's query: "{user_message}"
Please complete two tasks:
1. Concisely summarize what the user wants to know
2. Generate keywords most suitable for search engines (Chinese or English, must be precise)

Format:
Understanding: [User requirement summary]
Search terms: [Best search keywords]"""

    response = llm.invoke([SystemMessage(content=understand_prompt)])
    response_text = response.content

    # Parse LLM's output, extract search keywords
    search_query = user_message # Default to using original query
    if "Search terms:" in response_text or "搜索词:" in response_text:
        if "Search terms:" in response_text:
            search_query = response_text.split("Search terms:")[1].strip()
        else:
            search_query = response_text.split("搜索词:")[1].strip()

    return {
        "user_query": response_text,
        "search_query": search_query,
        "step": "understood",
        "messages": [AIMessage(content=f"I will search for you: {search_query}")]
    }
```

Este nó usa um prompt estruturado para exigir que o LLM complete simultaneamente duas tarefas: "compreensão de intenção" e "geração de palavras-chave", e atualiza as palavras-chave de busca dedicadas analisadas para o campo `search_query` do estado, preparando-se para o próximo passo de busca precisa.

(2) Nó de Busca

Este nó é responsável por executar a capacidade de "uso de ferramenta" do agente. Ele chamará a API Tavily para busca real na internet e tem funcionalidade básica de tratamento de erros.

```python
def tavily_search_node(state: SearchState) -> dict:
    """Step 2: Use Tavily API for real search"""
    search_query = state["search_query"]
    try:
        print(f"🔍 Searching: {search_query}")
        response = tavily_client.search(
            query=search_query, search_depth="basic", max_results=5, include_answer=True
        )
        # ... (process and format search results) ...
        search_results = ... # Formatted result string

        return {
            "search_results": search_results,
            "step": "searched",
            "messages": [AIMessage(content="✅ Search completed! Organizing answer...")]
        }
    except Exception as e:
        # ... (handle error) ...
        return {
            "search_results": f"Search failed: {e}",
            "step": "search_failed",
            "messages": [AIMessage(content="❌ Search encountered a problem...")]
        }
```

Este nó inicia uma chamada de API real através de `tavily_client.search`. Ele é envolvido em um bloco `try...except` para capturar possíveis exceções. Se a busca falhar, ele atualiza o estado `step` para `"search_failed"`, que será usado pelo próximo nó para acionar um plano de contingência.

(3) Nó de Resposta

O nó de resposta final pode escolher diferentes estratégias de resposta com base em se a busca anterior foi bem-sucedida, possuindo um certo grau de flexibilidade.

```python
def generate_answer_node(state: SearchState) -> dict:
    """Step 3: Generate final answer based on search results"""
    if state["step"] == "search_failed":
        # If search failed, execute fallback strategy, answer based on LLM's own knowledge
        fallback_prompt = f"Search API is temporarily unavailable, please answer the user's question based on your knowledge:\nUser question: {state['user_query']}"
        response = llm.invoke([SystemMessage(content=fallback_prompt)])
    else:
        # Search successful, generate answer based on search results
        answer_prompt = f"""Provide a complete and accurate answer to the user based on the following search results:
User question: {state['user_query']}
Search results:\n{state['search_results']}
Please synthesize the search results and provide an accurate, useful answer..."""
        response = llm.invoke([SystemMessage(content=answer_prompt)])

    return {
        "final_answer": response.content,
        "step": "completed",
        "messages": [AIMessage(content=response.content)]
    }
```

Este nó executa lógica condicional verificando o valor de `state["step"]`. Se a busca falhar, ele usará o conhecimento interno do LLM para responder e informar o usuário da situação. Se a busca for bem-sucedida, ele usará um prompt contendo resultados de busca em tempo real para gerar uma resposta oportuna e baseada em evidências.

(4) Construir Grafo

Conectamos todos os nós juntos.

```python
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import InMemorySaver

def create_search_assistant():
    workflow = StateGraph(SearchState)

    # Add nodes
    workflow.add_node("understand", understand_query_node)
    workflow.add_node("search", tavily_search_node)
    workflow.add_node("answer", generate_answer_node)

    # Set linear process
    workflow.add_edge(START, "understand")
    workflow.add_edge("understand", "search")
    workflow.add_edge("search", "answer")
    workflow.add_edge("answer", END)

    # Compile graph
    memory = InMemorySaver()
    app = workflow.compile(checkpointer=memory)
    return app
```

(5) Demonstração de Execução de Caso

Depois de executar este script, você pode fazer algumas perguntas que exigem informações em tempo real, como o caso em nosso primeiro capítulo: `Vou para Pequim amanhã, como está o clima? Há atrações adequadas?`

Você verá o terminal exibir claramente o processo de "pensamento" do agente:

```
🔍 Intelligent Search Assistant Started!
I will use Tavily API to search for the latest and most accurate information for you
Supports various questions: news, technology, knowledge Q&A, etc.
(Enter 'quit' to exit)

🤔 What would you like to know: I'm going to Beijing tomorrow, what's the weather like? Are there suitable attractions?

============================================================
🧠 Understanding phase: I understand your needs: Understanding: The user wants to know about tomorrow's weather in Beijing and suitable attraction recommendations.
Search terms: Beijing tomorrow weather attraction recommendations Beijing weather tomorrow attractions
🔍 Searching: Beijing tomorrow weather attraction recommendations Beijing weather tomorrow attractions
🔍 Search phase: ✅ Search completed! Found relevant information, organizing answer for you...

💡 Final Answer:
Tomorrow (September 17, 2025) Beijing's weather forecast shows it is expected to be cloudy, with temperatures ranging from 17°C (62°F) to 25°C (77°F). This mild weather is very suitable for outdoor activities.

### Suitable Attraction Recommendations:
1. **Great Wall**: As one of China's most famous historical sites, the Great Wall is a must-visit. You can choose popular sections like Badaling or Mutianyu for your tour.

2. **Forbidden City**: The Forbidden City was the imperial palace of the Ming and Qing dynasties, with rich history and culture, suitable for tourists interested in Chinese history.

3. **Tiananmen Square**: This is one of China's symbols, with many important buildings and monuments on the square, suitable for taking photos.

4. **Summer Palace**: A very beautiful royal garden, suitable for strolling and enjoying natural scenery, especially the lakes and ancient buildings.

5. **798 Art District**: If you're interested in modern art, the 798 Art District is a place that integrates art, culture, and creativity, suitable for exploration and photography.

### Tips:
- Since tomorrow's weather is good, it's recommended to plan your travel route in advance and prepare some water and snacks to maintain sufficient energy during the tour.
- Since weather changes may affect the tour experience, it's recommended to check real-time weather updates.

Hope this information helps you arrange a pleasant Beijing trip! If you need more information about attractions or travel advice, feel free to ask anytime.

============================================================

🤔 What would you like to know:
```

E é um assistente continuamente interativo, você pode continuar a fazer perguntas.

### 6.5.3 Análise das Vantagens e Limitações do LangGraph

Qualquer framework técnico tem seus cenários aplicáveis específicos e trade-offs de design. Nesta seção, analisaremos objetivamente as vantagens centrais do LangGraph e as limitações que ele pode enfrentar em aplicações práticas.

(1) Vantagens

- Como mostrado em nosso caso de assistente de busca inteligente, o LangGraph define explicitamente um processo completo de perguntas e respostas em tempo real como um "fluxograma" composto por estados, nós e arestas. A maior vantagem deste design é **alta controlabilidade e previsibilidade**. Os desenvolvedores podem planejar precisamente cada passo do comportamento do agente, o que é crucial para construir aplicações de nível de produção que exigem alta confiabilidade e auditabilidade. Seu recurso mais poderoso está em **suporte nativo para ciclos**. Através de arestas condicionais, podemos facilmente construir loops de "reflexão-correção". Por exemplo, em nosso caso, se a busca falhar, podemos projetar um caminho para retroceder para um plano de backup. Esta é a chave para construir agentes capazes de auto-otimização e tolerância a falhas.

- Além disso, como cada nó é uma função Python independente, isso traz **alta modularidade**. Ao mesmo tempo, inserir um nó esperando revisão humana no processo torna-se muito direto, fornecendo uma base sólida para implementar colaboração confiável "Human-in-the-loop".

(2) Limitações

- Comparado aos frameworks baseados em conversação, o LangGraph requer que os desenvolvedores escrevam mais **código boilerplate**. Definir estados, nós, arestas e uma série de operações torna o processo de desenvolvimento mais trabalhoso para tarefas simples. Os desenvolvedores precisam pensar mais sobre "como controlar o processo (how)" em vez de apenas "o que fazer (what)". Como o fluxo de trabalho é predefinido, o comportamento do LangGraph é controlável, mas também carece da **interação "emergente"** dinâmica de agentes conversacionais. Sua força está em executar um processo determinado e confiável, em vez de simular colaboração social aberta e imprevisível.

- O processo de depuração também apresenta desafios. Embora o processo seja mais claro do que o histórico de conversação, problemas podem ocorrer em múltiplos pontos: erros lógicos dentro de um nó, mutações em dados de estado passados entre nós, ou erros em julgamentos de condição de transição de aresta. Isso requer que os desenvolvedores tenham uma compreensão global do mecanismo de operação de todo o grafo.

## 6.6 Resumo do Capítulo

Neste capítulo, experimentamos alguns dos frameworks de agentes mais avançados através de prática hands-on na forma de casos.

Vimos que cada framework tem sua própria abordagem para implementar a construção de agentes:

- **AutoGen** abstrai colaboração complexa como um "chat em grupo" automaticamente conduzido com múltiplos papéis, com seu núcleo sendo "impulsionar a colaboração através da conversação."
- **AgentScope** se concentra na robustez e escalabilidade de aplicações de nível industrial, fornecendo uma base de engenharia sólida para construir sistemas multi-agente de alta concorrência e distribuídos.
- **CAMEL** demonstra como estimular colaboração profunda e autônoma entre dois agentes especialistas com código mínimo através de seu paradigma leve de "interpretação de papéis" e "inception prompting".
- **LangGraph** retorna a um modelo de "máquina de estado" mais fundamental, dando aos desenvolvedores controle preciso sobre fluxos de trabalho através de estruturas de grafo explícitas, especialmente sua capacidade de loop, pavimentando o caminho para construir agentes reflexivos e corrigíveis.

Através de análise profunda desses frameworks, podemos destilar um trade-off de design: **a escolha entre "colaboração emergente" e "controle explícito"**. AutoGen e CAMEL dependem mais de definir "papéis" e "objetivos" dos agentes, permitindo que comportamentos colaborativos complexos "emerjam" de regras de conversação simples. Esta abordagem está mais próxima dos padrões de interação humana, mas às vezes é difícil de prever e depurar. LangGraph requer que os desenvolvedores definam explicitamente cada passo e condição de transição, sacrificando algumas surpresas "emergentes" em troca de alta confiabilidade, controlabilidade e observabilidade. Ao mesmo tempo, AgentScope revela uma segunda dimensão igualmente importante: **engenharia**. Independentemente de qual paradigma de colaboração escolhemos, para empurrá-lo de protótipo experimental para aplicação de produção, devemos enfrentar desafios de engenharia como concorrência, tolerância a falhas e implantação distribuída. AgentScope nasceu para resolver esses problemas, representando o salto crítico de "pode funcionar" para "pode servir estavelmente."

Em resumo, não há apenas uma maneira de construir agentes. Compreender profundamente as filosofias de design de framework exploradas neste capítulo pode nos tornar não apenas melhores "usuários de ferramentas", mas também entender os vários prós e contras e trade-offs no design de frameworks.

No próximo capítulo, entraremos no conteúdo central deste tutorial, construindo nosso próprio framework de agentes do zero, integrando toda a teoria e prática.


## Exercícios

1. Este capítulo introduziu quatro frameworks de agentes distintos: `AutoGen`, `AgentScope`, `CAMEL` e `LangGraph`. Por favor, analise:

   - Na Tabela 6.1 da Seção 6.1.2, múltiplas dimensões desses quatro frameworks foram comparadas. Por favor, selecione os dois frameworks com os quais você está mais familiarizado e compare-os em profundidade adicionalmente a partir de três dimensões: "modo de colaboração", "método de controle" e "cenários aplicáveis".
   - Este capítulo mencionou o trade-off entre "colaboração emergente" e "controle explícito". Como você entende o significado dessas duas filosofias de design?

2. No caso `AutoGen` na Seção 6.2, construímos uma "equipe de desenvolvimento de software". Por favor, estenda seu pensamento com base neste caso:

   > **Dica**: Esta é uma questão de prática hands-on, operação real é recomendada

   - A equipe atual usa o modo `RoundRobinGroupChat` (chat em grupo rodízio), onde os agentes falam em ordem fixa. Se os requisitos mudarem e o código do engenheiro precisar ser retornado ao gerente de produto para re-revisão, como o processo de colaboração deve ser modificado? Por favor, projete um mecanismo que suporte "rollback dinâmico".
   - No caso, definimos o papel e responsabilidades de cada agente através de `System Message`. Por favor, tente adicionar um novo papel "Garantia de Qualidade" a esta equipe e projete sua mensagem de sistema para que ele possa executar testes automatizados após a revisão de código.
   - A colaboração conversacional do `AutoGen` tem instabilidade potencial, que pode fazer com que conversações se desviem do tópico ou caiam em loops. Por favor, pense: Como projetar um mecanismo de "monitoramento de qualidade de conversação" para intervir a tempo quando anomalias são detectadas?

3. No caso `AgentScope` na Seção 6.3, implementamos um jogo "Lobisomem dos Três Reinos". Por favor, analise em profundidade:

   - O caso usou `MsgHub` (centro de mensagens) para gerenciar a comunicação entre agentes. Por favor, explique quais vantagens a arquitetura orientada a mensagens tem em comparação com chamadas de função tradicionais? Em que cenários essa arquitetura é particularmente valiosa?
   - O jogo usou saída estruturada (como `DiscussionModelCN`, `WitchActionModelCN`) para restringir o comportamento do agente. Por favor, projete um novo papel de jogo "Caçador" e defina seu modelo de saída estruturada correspondente, incluindo definições de campo e regras de validação.
   - `AgentScope` suporta implantação distribuída, o que significa que diferentes agentes podem funcionar em diferentes servidores. Por favor, pense: Em um cenário de jogo em tempo real como "Lobisomem dos Três Reinos", quais desafios técnicos a implantação distribuída trará? Como garantir a ordenação e consistência de mensagens?

4. No caso `CAMEL` na Seção 6.4, tivemos um psicólogo e escritor colaborando para criar um e-book.

   - No caso, a colaboração é forçadamente terminada quando a flag `<CAMEL_TASK_DONE>` é detectada. Mas e se os dois agentes discordarem (um pensa que pode ser terminado, um pensa que não deveria) e não puderem chegar a um consenso? Por favor, projete um mecanismo de compatibilidade de "resolução de conflitos".
   - `CAMEL` foi originalmente projetado para colaboração de dois agentes, mas agora foi estendido para suportar multi-agente. Por favor, consulte a documentação mais recente do `CAMEL` para entender seu módulo de colaboração multi-agente [`workforce`](https://docs.camel-ai.org/key_modules/workforce), e explique como ele difere do modo de chat em grupo do `AutoGen` em combinação com o diagrama de arquitetura.

5. No caso `LangGraph` na Seção 6.5, construímos um "assistente de perguntas e respostas de três passos". Por favor, analise:

   - `LangGraph` modela o processo do agente como uma máquina de estado e grafo direcionado. Por favor, desenhe a estrutura de grafo do processo "entender-buscar-responder" no caso, marcando nós, arestas e condições de transição de estado.
   - O assistente atual é um processo linear. Por favor, estenda este caso adicionando um nó de "reflexão": se a qualidade da resposta gerada for baixa (por exemplo, muito breve ou faltando detalhes), o sistema deve re-buscar ou regenerar a resposta. Por favor, projete a lógica de aresta condicional para este mecanismo de loop.
   - A vantagem do `LangGraph` está em suporte nativo para loops. Por favor, projete um cenário de aplicação mais complexo que utilize totalmente esse recurso: por exemplo, loop "geração de código-teste-correção", loop "escrita de paper-revisão-revisão", etc. Desenhe a estrutura de grafo completa e explique a função dos nós-chave.

6. A seleção de framework é uma das decisões-chave no desenvolvimento de produtos de agentes. Suponha que você seja um arquiteto técnico em uma empresa de `AI`, e a empresa planeja desenvolver as seguintes três aplicações de produtos de agentes. Por favor, selecione o framework mais adequado para cada aplicação (`AutoGen`, `AgentScope`, `CAMEL`, `LangGraph`, ou desenvolver do zero sem framework) e explique em detalhe:

   **Aplicação A**: Sistema de atendimento ao cliente inteligente, precisa lidar com um grande número de solicitações de usuários concorrentes (1000+ por segundo), requer tempo de resposta menor que 2 segundos, o sistema precisa funcionar estavelmente 7×24 horas e suportar escalonamento horizontal.

   **Aplicação B**: Plataforma de assistência de escrita de papers de pesquisa científica, precisa de um "agente pesquisador" e um "agente escritor" para colaborar profundamente, completando conjuntamente revisão de literatura, design experimental, análise de dados e escrita de paper. Requer que os agentes conduzam múltiplas rodadas de discussão profunda e avancem autonomamente as tarefas.

   **Aplicação C**: Sistema de aprovação de controle de risco financeiro, precisa processar pedidos de empréstimo de acordo com procedimentos rigorosos: revisão de documentos → avaliação de risco → cálculo de quota → verificação de conformidade → revisão manual → decisão final. Cada elo tem critérios de julgamento claros e lógica de ramificação, exigindo processos rastreáveis e auditáveis.


## Referências

[1] Wu Q, Bansal G, Zhang J, et al. Autogen: Enabling next-gen LLM applications via multi-agent conversations[C]//First Conference on Language Modeling. 2024.

[2] Gao D, Li Z, Pan X, et al. Agentscope: A flexible yet robust multi-agent platform[J]. arXiv preprint arXiv:2402.14034, 2024.

[3] Li G, Hammoud H, Itani H, et al. Camel: Communicative agents for" mind" exploration of large language model society[J]. Advances in Neural Information Processing Systems, 2023, 36: 51991-52008.

[4] LangChain. LangGraph [EB/OL]. (2024). https://github.com/langchain-ai/langgraph.

[5] Microsoft. AutoGen - UserProxyAgent [EB/OL]. (2024). https://microsoft.github.io/autogen/stable/reference/python/autogen_agentchat.agents.html#autogen_agentchat.agents.UserProxyAgent.
