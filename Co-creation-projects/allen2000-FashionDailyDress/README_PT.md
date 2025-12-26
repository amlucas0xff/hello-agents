<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# 🌤️ Sistema Multi-Agente de Sugestões de Vestuário por Clima

Um sistema de consulta de clima e sugestões de vestuário baseado em colaboração multi-agente, construído com Python e Gradio.

## 🎯 Visão Geral do Projeto

Este projeto implementa um sistema inteligente de sugestões de vestuário por clima através da colaboração de múltiplos agentes:
- **Agente de Consulta de Clima**: responsável por obter dados meteorológicos em tempo real
- **Agente de Sugestões de Vestuário**: gera sugestões profissionais de vestuário baseadas em informações meteorológicas
- **Agente Coordenador**: gerencia a colaboração entre agentes e alocação de tarefas

## ✨ Funcionalidades Principais

- 🌤️ **Consulta de Clima em Tempo Real**: suporta consulta de clima para as principais cidades do mundo
- 🤖 **Sugestões Inteligentes por IA**: gera sugestões profissionais de vestuário baseadas em dados meteorológicos
- 🔄 **Colaboração Multi-Agente**: agentes colaboram eficientemente para processar tarefas complexas
- 🌐 **Interface Web**: interface gráfica amigável com Gradio

## 📁 Estrutura do Projeto

```
bill-FashionDailyDress/
├── fashion_agent.py          # Agente de sugestões de vestuário
├── gradio_app.py            # Interface Web Gradio
├── multi_agent_coordinator.py # Coordenador multi-agente
├── simple_multi_agent.py    # Sistema multi-agente simplificado
├── weather.py               # Funcionalidade de consulta de clima
├── weather_mcp.py           # Servidor MCP de clima
├── requirements.txt         # Dependências do projeto
└── README.md               # Documentação do projeto
```

## 🚀 Início Rápido

### 1. Preparação do Ambiente

```bash
# Clonar o projeto
git clone <repository-url>
cd bill-FashionDailyDress

# Instalar dependências
pip install -r requirements.txt
```

### 2. Configuração de Variáveis de Ambiente

Criar arquivo `.env` e configurar chaves API necessárias:

```env
# Configuração LLM (obrigatório)
LLM_API_KEY=your_llm_api_key
LLM_BASE_URL=your_llm_base_url
LLM_MODEL_ID=your_llm_model_id

# Configuração API de Clima (opcional, para dados meteorológicos reais)
OPENWEATHER_API_KEY=your_openweather_api_key
```

### 3. Executar o Sistema

#### Método 1: Usar Interface Web Gradio (recomendado)
Versão Python 3.12.10
```bash
python gradio_app.py
```
Acessar http://localhost:8899 para usar a interface gráfica

#### Método 2: Interação por Linha de Comando
```bash
python simple_multi_agent.py
```

## 🔧 Descrição dos Módulos Principais

### fashion_agent.py
- **Funcionalidade**: Agente profissional de sugestões de vestuário
- **Características**: fornece sugestões detalhadas baseadas em temperatura, umidade, velocidade do vento e outros fatores meteorológicos
- **Saída**: inclui combinações de roupas, sugestões de acessórios, precauções, etc.

### multi_agent_coordinator.py
- **Funcionalidade**: Coordenador multi-agente
- **Características**: gerencia a colaboração entre agentes de consulta de clima e sugestões de vestuário
- **Fluxo**: recebe consulta → obtém clima → gera sugestões → integra resultados

### gradio_app.py
- **Funcionalidade**: Interface gráfica Web
- **Características**: interface de interação amigável ao usuário, suporta exemplos para experiência rápida
- **Porta**: 8899

### weather.py
- **Funcionalidade**: Encapsulamento de consulta de clima
- **Características**: suporta API real e modo demonstração
- **API**: integração com OpenWeatherMap API

## 📋 Exemplos de Uso

### Exemplos de Entrada
- Beijing
- Shanghai
- Tokyo
- London

### Exemplo de Saída
```
🏙️ Cidade consultada: Beijing
🌡️ Temperatura: 25°C
📝 Clima: ensolarado
💧 Umidade: 60%
🌬️ Velocidade do vento: 3 m/s

👗 Sugestões de vestuário:
Com base nas condições climáticas atuais, sugere-se usar roupas leves e respiráveis...
```

## ⚙️ Instruções de Configuração

### Configuração Obrigatória
- Chave API LLM e endpoint (para raciocínio do agente)

### Configuração Opcional
- Chave API OpenWeatherMap (para dados meteorológicos reais)
- Se não configurada, o sistema usará modo demonstração fornecendo dados simulados

## 🛠️ Stack Tecnológica

- **Framework**: hello-agents, fastmcp
- **Interface Web**: Gradio
- **Requisições HTTP**: requests
- **Gerenciamento de Configuração**: python-dotenv
- **API de Clima**: OpenWeatherMap

## 🔍 Guia de Desenvolvimento

### Adicionar Novos Agentes
1. Criar nova classe de agente (referência fashion_agent.py)
2. Registrar agente no coordenador
3. Atualizar prompts do sistema e lógica de colaboração

### Expandir Funcionalidades
- Suportar mais fontes de dados meteorológicos
- Adicionar análise de clima histórico
- Integrar mais estilos de sugestões de vestuário

## 🤝 Guia de Contribuição

Bem-vindo a submeter Issues e Pull Requests para melhorar o projeto!

## 📄 Licença

Este projeto adota a licença MIT.

## 📞 Informações de Contato

Se tiver perguntas ou sugestões, entre em contato através de:
- GitHub Issues
- E-mail do mantenedor do projeto

---

**Aproveite as sugestões inteligentes de vestuário!** 👗✨
