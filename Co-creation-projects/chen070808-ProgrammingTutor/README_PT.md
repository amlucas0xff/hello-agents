<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# Tutor de Programação Inteligente (Intelligent Programming Tutor)

Um sistema assistente de aprendizado de programação inteligente baseado no framework HelloAgents, fornecendo experiência de aprendizado de programação personalizada.

## Características do Projeto

### 🎯 Funcionalidades Principais

- **Planejamento de Caminho de Aprendizado**: cria plano de estudos personalizado baseado em objetivos de aprendizado e nível atual
- **Geração Inteligente de Exercícios**: gera automaticamente exercícios de programação baseados no conteúdo de aprendizado
- **Revisão de Código**: realiza revisão profissional do código submetido, fornece sugestões de melhoria e orientação de melhores práticas

### 🤖 Arquitetura Multi-Agente

Este projeto adota modo de trabalho colaborativo multi-agente, incluindo os seguintes agentes:

- **TutorAgent (Tutor)**: agente coordenador principal, responsável por entender necessidades do usuário e chamar sub-agentes apropriados
- **PlannerAgent (Planejador)**: desenvolve planos e caminhos de aprendizado personalizados
- **ExerciseAgent (Gerador de Exercícios)**: gera exercícios de programação baseados no conteúdo de aprendizado
- **ReviewerAgent (Revisor)**: revisa código e fornece feedback profissional, suporta teste de execução de código

## Estrutura do Projeto

```
chen070808-ProgrammingTutor/
├── src/
│   ├── agents/          # Definição de agentes
│   │   ├── tutor.py     # Agente tutor principal
│   │   ├── planner.py   # Agente de planejamento de aprendizado
│   │   ├── exercise.py  # Agente de geração de exercícios
│   │   └── reviewer.py  # Agente de revisão de código
│   └── tools/           # Definição de ferramentas
│       ├── agent_tool.py    # Encapsulamento de ferramenta agente A2A
│       └── code_runner.py   # Ferramenta de execução de código
├── main.ipynb           # Notebook de demonstração de exemplo
├── requirements.txt     # Dependências do projeto
└── .env                 # Configuração de ambiente (criar você mesmo)
```

## Instalação e Configuração

### 1. Requisitos de Ambiente

- Python 3.8+
- Framework HelloAgents

### 2. Instalação de Dependências

```bash
pip install -r requirements.txt
```

### 3. Configuração de Variáveis de Ambiente

Criar arquivo `.env` e configurar os seguintes parâmetros:

```bash
# Configuração do modelo LLM
LLM_MODEL_ID=Qwen/Qwen2.5-72B-Instruct
LLM_API_KEY=your_api_key_here
LLM_BASE_URL=https://api-inference.modelscope.cn/v1
LLM_TIMEOUT=60
```

Consulte o arquivo `.env.example` para opções de configuração completas.

## Métodos de Uso

### Método 1: Jupyter Notebook

Abra `main.ipynb` e execute as células em ordem para experimentar o fluxo de aprendizado completo:

1. Planejamento de caminho de aprendizado
2. Obter exercícios de programação
3. Revisão e feedback de código

### Método 2: Código Python

```python
from hello_agents import HelloAgentsLLM
from src.agents.tutor import TutorAgent

# Inicializar LLM
llm = HelloAgentsLLM.from_env()

# Criar agente tutor
tutor = TutorAgent(llm)

# Exemplo 1: Solicitar plano de aprendizado
response = tutor.run("Quero aprender compreensão de listas em Python")
print(response)

# Exemplo 2: Solicitar exercício
response = tutor.run("Por favor, me dê um exercício sobre compreensão de listas")
print(response)

# Exemplo 3: Revisão de código
code = """
numbers = [1, 2, 3, 4, 5]
squares = []
for n in numbers:
    squares.append(n * n)
"""
response = tutor.run(f"Por favor, revise o seguinte código: {code}")
print(response)
```

## Arquitetura Técnica

### Mecanismo de Colaboração entre Agentes

- Adota modo de chamada de ferramenta **Agent-to-Agent (A2A)**
- `TutorAgent` chama sub-agentes através de interface de ferramentas:
  - `call_planner(query)` - Chama planejador de aprendizado
  - `call_exercise(query)` - Chama gerador de exercícios
  - `call_reviewer(query)` - Chama revisor de código

### Capacidade de Execução de Código

`ReviewerAgent` integra ferramenta `CodeRunner`, pode:
- Executar código Python submetido por usuário com segurança
- Capturar erros e exceções em tempo de execução
- Fornecer feedback mais preciso baseado em resultados de execução

## Cenários de Exemplo

### Cenário 1: Planejamento de Caminho de Aprendizado

**Entrada do Usuário**:
```
Quero aprender decoradores em Python, mas só entendo definição básica de funções
```

**Resposta do Sistema**:
O tutor chamará PlannerAgent, gerando plano de aprendizado incluindo:
- Verificação de conhecimento prévio
- Objetivos de aprendizado em etapas
- Recursos de aprendizado recomendados
- Sugestões de projetos práticos

### Cenário 2: Obter Exercícios

**Entrada do Usuário**:
```
Por favor, me dê um exercício sobre decoradores
```

**Resposta do Sistema**:
ExerciseAgent gerará um exercício incluindo:
- Descrição e requisitos do problema
- Exemplos de entrada/saída
- Nível de dificuldade
- Pontos de conhecimento avaliados

### Cenário 3: Revisão de Código

**Entrada do Usuário**:
```python
@decorator
def greet(name):
    print(f"Hello, {name}")
```

**Resposta do Sistema**:
ReviewerAgent irá:
1. Executar código verificando sintaxe e erros de tempo de execução
2. Analisar qualidade do código e melhores práticas
3. Fornecer sugestões de melhoria
4. Apontar problemas potenciais

## Desenvolvimento e Expansão

### Adicionar Novos Agentes

1. Criar nova classe de agente no diretório `src/agents/`
2. Herdar classe base `SimpleAgent`
3. Registrar ferramenta do novo agente em `TutorAgent`

### Ferramentas Personalizadas

1. Criar nova classe de ferramenta no diretório `src/tools/`
2. Herdar classe base `Tool` e implementar método `run()`
3. Injetar ferramenta no agente apropriado

## Notas de Atenção

- Certifique-se de que o arquivo `.env` está configurado corretamente, especialmente a chave API
- Funcionalidade de execução de código tem modo sandbox ativado por padrão, recomenda-se não executar código não confiável
- Chamadas LLM requerem conexão de rede, certifique-se de que a rede está funcionando

## Contribuidores

- chen070808

## Licença

Este projeto segue a licença MIT.

## Agradecimentos

Este projeto é desenvolvido baseado no framework [HelloAgents](https://github.com/datawhalechina/hello-agents).
