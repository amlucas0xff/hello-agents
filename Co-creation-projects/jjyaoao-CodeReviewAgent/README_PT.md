<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# CodeReviewAgent - Assistente Inteligente de Revisão de Código

> Ferramenta inteligente de revisão de código baseada no framework HelloAgents

## 📝 Introdução ao Projeto

CodeReviewAgent é um assistente inteligente de revisão de código, capaz de analisar automaticamente a qualidade do código Python, descobrir problemas potenciais e fornecer sugestões de otimização.

### Recursos Principais

- ✅ Análise de estrutura de código: Estatísticas de funções, classes, linhas de código, etc.
- ✅ Verificação de estilo: Verifica conformidade com padrões PEP 8
- ✅ Sugestões inteligentes: Fornece análise profunda e sugestões de otimização baseadas em LLM
- ✅ Geração de relatórios: Gera relatórios de revisão em formato Markdown

## 🛠️ Stack Tecnológica

- Framework HelloAgents (SimpleAgent)
- Módulo AST do Python (análise de código)
- OpenAI API (análise inteligente)

## 🚀 Início Rápido

### Instalação de Dependências

```bash
pip install -r requirements.txt
```

### Configuração de Parâmetros LLM

**Método 1: Usando arquivo .env (recomendado)**

```bash
# Copie o arquivo de exemplo
cp .env.example .env

# Edite o arquivo .env, preenchendo sua configuração
# LLM_MODEL_ID=Qwen/Qwen2.5-72B-Instruct
# LLM_API_KEY=your_api_key_here
# LLM_BASE_URL=https://api-inference.modelscope.cn/v1/
```

**Método 2: Configuração direta no Notebook (já configurado)**

O projeto já está pré-configurado com a API do ModelScope em `main.ipynb`, pode ser usado diretamente. Se precisar modificar, edite o código de configuração na Parte 1:

```python
os.environ["LLM_MODEL_ID"] = "your_model"
os.environ["LLM_API_KEY"] = "your_key"
os.environ["LLM_BASE_URL"] = "your_api_url"
```

### Executar o Projeto

```bash
jupyter lab
# Abra main.ipynb e execute todas as células
```

## 📖 Exemplo de Uso

### Experiência Rápida

Abra `main.ipynb`, execute "Parte 0: Demonstração Rápida" para entender rapidamente as funcionalidades do projeto.

### Funcionalidade Completa

1. Coloque o código a ser revisado em `data/sample_code.py`
2. Execute sequencialmente as Partes 1-7 de `main.ipynb`
3. Veja o relatório de revisão gerado `outputs/review_report.md`

## 🎯 Destaques do Projeto

- **Automação**: Não requer verificação linha por linha manual, descobre problemas automaticamente
- **Inteligência**: Utiliza LLM para entender a semântica do código, fornecendo sugestões profundas
- **Extensibilidade**: Fácil adicionar novas regras de verificação e ferramentas

## 📂 Estrutura do Projeto

```
jjyaoao-CodeReviewAgent/
├── README.md              # Documentação do projeto
├── requirements.txt       # Lista de dependências
├── .gitignore            # Arquivo de ignorar do Git
├── .env.example          # Exemplo de variáveis de ambiente
├── main.ipynb            # Programa principal (inclui demonstração rápida e funcionalidade completa)
├── data/
│   └── sample_code.py    # Código de exemplo
└── outputs/
    └── review_report.md  # Relatório de revisão
```

## 🔧 Implementação Técnica

### Sistema de Ferramentas

1. **CodeAnalysisTool**: Usa módulo AST do Python para analisar estrutura do código
2. **StyleCheckTool**: Verifica padrões de estilo de código PEP 8

### Design do Agente

Usa SimpleAgent do HelloAgents, combinado com ferramentas personalizadas para implementar revisão inteligente de código.

## 📊 Exemplo de Saída

```markdown
# Relatório de Revisão de Código

## Análise de Estrutura de Código
- Quantidade de funções: 3
- Quantidade de classes: 1
- Linhas de código: 45

## Problemas de Estilo
- Linha 12: Excede 79 caracteres
- Linha 25: Indentação irregular

## Sugestões de Otimização
1. Recomenda-se dividir funções longas em múltiplas funções pequenas
2. Adicionar anotações de tipo para melhorar legibilidade do código
3. Complementar docstrings
```

## 🚧 Melhorias Futuras

- [ ] Suportar mais linguagens de programação (JavaScript, Java, etc.)
- [ ] Adicionar detecção de vulnerabilidades de segurança
- [ ] Integrar mais ferramentas de análise estática
- [ ] Suportar revisão em lote de arquivos
- [ ] Gerar relatórios em formato HTML

## 👤 Autor

- GitHub: [@jjyaoao](https://github.com/jjyaoao)
- Link do projeto: [CodeReviewAgent](https://github.com/datawhalechina/Hello-Agents/tree/main/Co-creation-projects/jjyaoao-CodeReviewAgent)

## 🙏 Agradecimentos

Agradecimentos à comunidade Datawhale e ao projeto Hello-Agents!

## 📄 Licença

Este projeto adota licença MIT.
