<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# DataAnalysisAgent - Assistente Inteligente de Análise de Dados

> Ferramenta inteligente de análise de dados baseada no framework HelloAgents

## 📝 Visão Geral do Projeto

DataAnalysisAgent é um assistente inteligente de análise de dados que pode automaticamente analisar dados, gerar gráficos de visualização e produzir relatórios de análise.

### Funcionalidades Principais

- ✅ Análise de dados: analisa tendências de dados estatísticos, seleciona gráficos apropriados, etc.
- ✅ Sugestões inteligentes: fornece código de gráficos de visualização e relatórios de análise baseados em LLM (Modelo de Linguagem Grande)
- ✅ Geração de relatórios: gera análises em formato Markdown

## 🛠️ Stack Tecnológica

- Framework HelloAgents (SimpleAgent)
- Módulo Python AST (análise de código)
- OpenAI API (análise inteligente)

## 🚀 Início Rápido

### Instalação de Dependências

```bash
pip install -r requirements.txt
```

### Configuração de Parâmetros LLM

**Método 1: Usar arquivo .env (recomendado)**

```bash
# Copiar arquivo de exemplo
cp .env.example .env

# Editar arquivo .env, preencher sua configuração
# LLM_MODEL_ID=Qwen/Qwen2.5-72B-Instruct
# LLM_API_KEY=your_api_key_here
# LLM_BASE_URL=https://api-inference.modelscope.cn/v1/
```

**Método 2: Configurar diretamente no Notebook (já configurado)**

O projeto já está pré-configurado com a API do ModelScope no `main.ipynb` e pode ser usado diretamente. Se precisar modificar, edite o código de configuração na Parte 1:

```python
os.environ["LLM_MODEL_ID"] = "your_model"
os.environ["LLM_API_KEY"] = "your_key"
os.environ["LLM_BASE_URL"] = "your_api_url"
```

### Executar o Projeto

```bash
jupyter lab
# Abrir main.ipynb e executar todas as células
```

## 📖 Exemplos de Uso

### Experiência Rápida

Abra `main.ipynb`, execute "Parte 0: Demonstração Rápida" para ter uma visão geral rápida das funcionalidades do projeto.

### Funcionalidade Completa

1. Colocar tabelas de dados a serem analisadas em `data`
2. Executar `main.ipynb` sequencialmente
3. Ver gráficos gerados em `outputs/echarts.html`
4. Ver relatório de análise de dados gerado em `outputs/report.md`

## 📂 Estrutura do Projeto

```
jjyaoao-CodeReviewAgent/
├── README.md              # Documentação do projeto
├── requirements.txt       # Lista de dependências
├── .gitignore            # Arquivo de ignorar do Git
├── .env.example          # Exemplo de variáveis de ambiente
├── main.ipynb            # Programa principal (inclui demonstração rápida e funcionalidade completa)
├── data/
│   └──    # Código de exemplo
└── outputs/
    └── report.md  # Relatório de análise de dados
    └── echarts.html  # HTML de gráficos
```

## 🔧 Implementação Técnica

### Sistema de Ferramentas

1. **DataCleaningTool**: Ferramenta de limpeza de dados - limpa dados de tabelas baseado em regras especificadas pelo usuário
2. **DataStatisticsTool**: Ferramenta de estatísticas de dados - fornece análise estatística descritiva

### Design do Agente

Usa SimpleAgent do HelloAgents, combinado com ferramentas personalizadas para implementar revisão inteligente de código.

```

## 🙏 Agradecimentos

Obrigado à comunidade Datawhale e ao projeto Hello-Agents!
