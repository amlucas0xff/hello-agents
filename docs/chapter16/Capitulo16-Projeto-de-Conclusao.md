# Capítulo 16: Projeto de Conclusão - Construindo Sua Própria Aplicação Multi-Agente

<div align="right">
  <a href="./Chapter16-Graduation-Project.md">English</a> | <a href="./第十六章%20毕业设计.md">中文</a> | Português
</div>

Parabéns por chegar ao capítulo final do tutorial Hello-Agents! Nos 15 capítulos anteriores, construímos o framework HelloAgents do zero e aprendemos sobre conceitos centrais de agentes, múltiplos paradigmas, sistemas de ferramentas, mecanismos de memória, protocolos de comunicação, treinamento por aprendizado por reforço e avaliação de desempenho. Nos Capítulos 13-15, também demonstramos como integrar todo o conhecimento aprendido através de três projetos práticos completos (Assistente Inteligente de Viagem, Agente de Pesquisa Profunda Automatizada e Cyber Town).

Agora é hora de você se tornar um verdadeiro construtor de sistemas de agentes! Este capítulo irá guiá-lo na **construção de sua própria aplicação multi-agente** e compartilhar suas conquistas com a comunidade através de colaboração open-source.

## 16.1 O Significado do Projeto de Conclusão

### 16.1.1 Por Que Fazer um Projeto de Conclusão

A melhor maneira de aprender tecnologia não é lendo tutoriais, mas através da **prática hands-on**. Através dos capítulos anteriores, você dominou o conhecimento teórico e as ferramentas técnicas para construir sistemas de agentes. No entanto, o verdadeiro desafio está em: **Como aplicar este conhecimento a problemas reais? Como projetar um sistema completo? Como lidar com vários casos extremos e exceções?**

O valor central do projeto de conclusão é cultivar sua capacidade de aplicação abrangente, integrando seletivamente todo o conhecimento aprendido anteriormente (paradigmas de agentes, sistemas de ferramentas, mecanismos de memória, protocolos de comunicação, etc.) em um projeto completo.

Através do aprendizado e prática neste capítulo, esperamos que você possa projetar e implementar independentemente uma aplicação completa de agente, usar habilmente várias funções do framework HelloAgents, dominar operações básicas de Git e GitHub, aprender a escrever documentação clara de projeto, participar do desenvolvimento colaborativo da comunidade open-source e, finalmente, obter um trabalho técnico que você pode mostrar.

### 16.1.2 Formato do Projeto de Conclusão

Seu projeto de conclusão será submetido ao repositório de projetos de co-criação Hello-Agents (diretório `Co-creation-projects`) na forma de um **projeto open-source**. Os requisitos específicos são os seguintes:

1. **Nomenclatura do Projeto**: Use o formato `{seu-nome-de-usuario-GitHub}-{nome-do-projeto}`, por exemplo `jjyaoao-CodeReviewAgent`

2. **Conteúdo do Projeto**:
   - Um Jupyter Notebook executável (arquivo `.ipynb`) ou script Python
   - Lista completa de dependências (`requirements.txt`)
   - Documentação README clara (`README.md`)
   - Opcional: vídeos de demonstração, capturas de tela, conjuntos de dados, etc.

3. **Método de Submissão**: Submeter via GitHub Pull Request (PR)

4. **Processo de Revisão**: Membros da comunidade irão revisar seu código, fornecer sugestões de melhoria e mesclar no repositório principal após aprovação

## 16.2 Guia de Seleção de Tópico do Projeto

### 16.2.1 Princípios de Seleção de Tópico

Um bom projeto de conclusão deve ser prático, resolvendo problemas reais ao invés de tecnologia pela tecnologia. Precisamos buscar conclusão dentro de tempo e recursos limitados, enquanto demonstramos claramente suas capacidades técnicas.

### 16.2.2 Direções de Tópico Recomendadas

Aqui estão algumas direções de projeto recomendadas - você pode escolher uma ou propor suas próprias ideias:

**(1) Ferramentas de Produtividade**

- **Assistente Inteligente de Revisão de Código**: Analisar automaticamente a qualidade do código, descobrir bugs potenciais, fornecer sugestões de otimização
- **Gerador Inteligente de Documentação**: Gerar automaticamente documentação de API e manuais de usuário baseados em código
- **Assistente Inteligente de Reuniões**: Gravar conteúdo de reuniões, gerar atas de reunião, extrair itens de ação
- **Assistente Inteligente de Email**: Classificar automaticamente emails, gerar rascunhos de resposta, lembrar de assuntos importantes

**(2) Assistência ao Aprendizado**

- **Parceiro Inteligente de Aprendizado**: Recomendar recursos de aprendizado baseados no progresso de aprendizado, gerar questões práticas, responder perguntas
- **Assistente Inteligente de Artigos**: Ajudar a encontrar literatura, resumir artigos, gerar citações
- **Tutor Inteligente de Programação**: Fornecer exercícios de programação, revisão de código, planejamento de caminho de aprendizado
- **Assistente Inteligente de Aprendizado de Idiomas**: Fornecer prática de conversação, correção gramatical, expansão de vocabulário

**(3) Entretenimento Criativo**

- **Gerador Inteligente de Histórias**: Gerar romances, roteiros, poesia baseados em entrada do usuário
- **NPC Inteligente de Jogos**: Criar personagens de jogos com personalidade que podem conversar naturalmente com jogadores
- **Recomendação Inteligente de Música**: Recomendar música baseada em humor e cena, gerar playlists
- **Assistente Inteligente de Receitas**: Recomendar receitas baseadas em ingredientes e gosto, gerar listas de compras

**(4) Análise de Dados**

- **Analista Inteligente de Dados**: Analisar automaticamente dados, gerar gráficos de visualização, escrever relatórios de análise
- **Análise Inteligente de Ações**: Analisar dados de ações e sentimento de notícias, fornecer conselhos de investimento
- **Monitoramento Inteligente de Opinião Pública**: Monitorar mídias sociais e sites de notícias, analisar tendências de opinião pública
- **Análise Inteligente Competitiva**: Coletar informações de concorrentes, análise comparativa, gerar relatórios

**(5) Serviços de Vida**

- **Assistente Inteligente de Saúde**: Registrar dados de saúde, fornecer conselhos de saúde, criar planos de exercício
- **Assistente Inteligente Financeiro**: Registrar receitas e despesas, analisar hábitos de gastos, fornecer conselhos financeiros
- **Assistente Inteligente de Compras**: Comparar preços, recomendar produtos, gerar listas de compras
- **Controle Inteligente de Casa**: Controlar dispositivos de casa inteligente através de linguagem natural

### 16.2.3 Exemplo de Seleção de Tópico

Vamos ilustrar como selecionar um tópico e projetar um projeto através de um exemplo específico.

**Nome do Projeto**: Assistente Inteligente de Revisão de Código (CodeReviewAgent)

**Análise do Problema**: A revisão de código é uma parte importante do desenvolvimento de software, mas a revisão manual consome tempo e é propensa a perder problemas. Ferramentas de análise estática existentes só podem encontrar erros de sintaxe e não conseguem entender a lógica do código, portanto é necessário um assistente inteligente que possa entender a semântica do código e fornecer análise profunda.

**Funções Centrais**: Este projeto irá implementar análise de qualidade de código (verificar estilo de código, convenções de nomenclatura, completude de comentários), detecção de bugs potenciais (descobrir erros lógicos, problemas de condições de contorno, vazamentos de recursos), sugestões de otimização de desempenho (identificar gargalos de desempenho, propor soluções de otimização), varredura de vulnerabilidades de segurança (detectar injeção SQL, XSS e outros problemas de segurança), e recomendações de melhores práticas (propor melhorias baseadas em características de linguagem e padrões de design).

**Resultados Esperados**: O entregável final será um Jupyter Notebook executável demonstrando o processo completo de revisão, suportando linguagens mainstream como Python e JavaScript, capaz de gerar relatórios de revisão estruturados em formato Markdown, e fornecendo exemplos de código específicos e sugestões de melhoria.

## 16.3 Preparação do Ambiente de Desenvolvimento

### 16.3.1 Instalando Ferramentas Necessárias

Antes de iniciar o desenvolvimento, certifique-se de que seu ambiente de desenvolvimento tem as seguintes ferramentas instaladas:

**(1) Ambiente Python**

```bash
# Instalar HelloAgents
pip install "hello-agents[all]"
```

**(2) Git e GitHub**

```bash
# Verificar versão do Git
git --version

# Configurar informações de usuário Git
git config --global user.name "Seu Nome"
git config --global user.email "seu.email@exemplo.com"

# Configurar chave SSH do GitHub (recomendado)
# 1. Gerar chave SSH
ssh-keygen -t ed25519 -C "seu.email@exemplo.com"

# 2. Adicionar chave pública ao GitHub
# Copiar o conteúdo de ~/.ssh/id_ed25519.pub
# Adicionar em GitHub Settings > SSH and GPG keys

# 3. Testar conexão
ssh -T git@github.com
```

**(3) Jupyter Notebook**

```bash
# Instalar Jupyter
pip install jupyter notebook

# Ou usar JupyterLab (recomendado)
pip install jupyterlab

# Iniciar Jupyter
jupyter lab
```

### 16.3.2 Fork do Repositório do Projeto

**Passo 1: Fork do Repositório**

1. Visite o repositório Hello-Agents: https://github.com/datawhalechina/Hello-Agents
2. Clique no botão "Fork" no canto superior direito, conforme mostrado na caixa vermelha na Figura 16.1
3. Selecione sua conta GitHub e crie o Fork

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/16-figures/16-1.png" alt="" width="85%"/>
  <p>Figura 16.1 Passos de Fork do Repositório</p>
</div>

**Passo 2: Clonar Localmente**

```bash
# Como mostrado na Figura 16.2, clonar seu repositório forkado
git clone git@github.com:seu-nome-de-usuario/Hello-Agents.git

# Entrar no diretório do projeto
cd Hello-Agents

# Adicionar repositório upstream (para sincronizar atualizações)
git remote add upstream https://github.com/datawhalechina/Hello-Agents.git

# Ver repositórios remotos
git remote -v
```

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/16-figures/16-2.png" alt="" width="85%"/>
  <p>Figura 16.2 Clonar Repositório Localmente</p>
</div>

**Passo 3: Criar Branch de Desenvolvimento**

```bash
# Criar e mudar para novo branch
git checkout -b feature/seu-nome-de-projeto

# Por exemplo:
git checkout -b feature/code-review-agent
```

### 16.3.3 Estrutura de Diretório do Projeto

Crie sua pasta de projeto no diretório `Co-creation-projects`:

```bash
# Entrar no diretório de projetos de co-criação
cd Co-creation-projects

# Criar pasta de projeto (formato: nome-de-usuario-GitHub-nome-do-projeto)
mkdir seu-nome-de-usuario-nome-do-projeto

# Por exemplo:
mkdir jjyaoao-CodeReviewAgent

# Entrar no diretório do projeto
cd jjyaoao-CodeReviewAgent
```

Estrutura de projeto recomendada:

```
jjyaoao-CodeReviewAgent/
├── README.md              # Documentação do projeto
├── requirements.txt       # Lista de dependências Python
├── main.ipynb            # Jupyter Notebook principal
├── data/                 # Arquivos de dados (opcional)
│   ├── sample_code.py
│   └── test_cases.json
├── outputs/              # Resultados de saída (opcional)
│   ├── review_report.md
│   └── screenshots/
├── src/                  # Código fonte (opcional, se o código for extenso)
│   ├── agents/
│   ├── tools/
│   └── utils/
└── .env.example          # Template de variáveis de ambiente
```

## 16.4 Guia de Desenvolvimento do Projeto

### 16.4.1 Escrevendo Documentação README

README é a cara do seu projeto. Um bom README deve conter o seguinte:

```markdown
# Nome do Projeto

> Descrição de uma sentença do seu projeto

## 📝 Introdução do Projeto

Introdução detalhada do seu projeto:
- Que problema ele resolve?
- Quais são suas características especiais?
- Para quais cenários é adequado?

## ✨ Características Principais

- [ ] Característica 1: Descrição
- [ ] Característica 2: Descrição
- [ ] Característica 3: Descrição

## 🛠️ Pilha Tecnológica

- Framework HelloAgents
- Paradigmas de agente usados (ex.: ReAct, Plan-and-Solve, etc.)
- Ferramentas e APIs usadas
- Outras bibliotecas de dependência

## 🚀 Início Rápido

### Requisitos de Ambiente

- Python 3.10+
- Outros requisitos

### Instalar Dependências

\`\`\`bash
pip install -r requirements.txt
\`\`\`

### Configurar Chaves de API

\`\`\`bash
# Criar arquivo .env
cp .env.example .env

# Editar arquivo .env e preencher suas chaves de API
\`\`\`

### Executar Projeto

\`\`\`bash
# Iniciar Jupyter Notebook
jupyter lab

# Abrir main.ipynb e executar
\`\`\`

## 📖 Exemplos de Uso

Mostrar como usar seu projeto, de preferência com exemplos de código e resultados.

## 🎯 Destaques do Projeto

- Destaque 1: Explicação
- Destaque 2: Explicação
- Destaque 3: Explicação

## 📊 Avaliação de Desempenho

Se você tem resultados de avaliação, exiba-os aqui:
- Precisão: XX%
- Tempo de resposta: XX segundos
- Outras métricas

## 🔮 Planos Futuros

- [ ] Característica 1 a ser implementada
- [ ] Característica 2 a ser implementada
- [ ] Partes a serem otimizadas

## 🤝 Diretrizes de Contribuição

Issues e Pull Requests são bem-vindos!

## 📄 Licença

Licença MIT

## 👤 Autor

- GitHub: [@seu-nome-de-usuario](https://github.com/seu-nome-de-usuario)
- Email: seu.email@exemplo.com (opcional)

## 🙏 Agradecimentos

Obrigado à comunidade Datawhale e ao projeto Hello-Agents!
```

### 16.4.2 Escrevendo requirements.txt

Liste todas as dependências Python necessárias para o projeto:

```txt
# Dependências principais
hello-agents[all]>=0.2.7

# Visualização (se necessário)
matplotlib>=3.7.0
plotly>=5.14.0

# Framework web (se necessário)
fastapi>=0.109.0
uvicorn>=0.27.0
```

### 16.4.3 Desenvolvendo Jupyter Notebook

**(1) Recomendações de Estrutura do Notebook**

Um bom Jupyter Notebook deve conter as seguintes partes:

```python
# ========================================
# Parte 1: Introdução do Projeto
# ========================================

"""
# Nome do Projeto

## Introdução do Projeto
Breve introdução aos objetivos e características do projeto

## Informações do Autor
- Nome: XXX
- GitHub: @XXX
- Data: 2025-XX-XX
"""

# ========================================
# Parte 2: Configuração de Ambiente
# ========================================

# Instalar dependências
!pip install -q hello-agents[all]

# Importar bibliotecas necessárias
from hello_agents import SimpleAgent, HelloAgentsLLM
from hello_agents.tools import BaseTool
import os
from dotenv import load_dotenv

# Carregar variáveis de ambiente
load_dotenv()

# ========================================
# Parte 3: Definição de Ferramentas
# ========================================

class CustomTool(BaseTool):
    """Classe de ferramenta personalizada"""

    name = "nome_da_ferramenta"
    description = "Descrição da ferramenta"

    def run(self, query: str) -> str:
        """Lógica de execução da ferramenta"""
        # Implementar sua lógica de ferramenta
        return "Resultado"

# ========================================
# Parte 4: Construção do Agente
# ========================================

# Criar LLM
llm = HelloAgentsLLM()

# Criar agente
agent = SimpleAgent(
    name="Nome do Agente",
    llm=llm,
    system_prompt="Prompt do sistema"
)

# Adicionar ferramentas
agent.add_tool(CustomTool())

# ========================================
# Parte 5: Demonstração de Características
# ========================================

# Exemplo 1: Funcionalidade básica
print("=== Exemplo 1: Funcionalidade Básica ===")
result = agent.run("Entrada do usuário")
print(result)

# Exemplo 2: Cenário complexo
print("\n=== Exemplo 2: Cenário Complexo ===")
result = agent.run("Entrada complexa do usuário")
print(result)

# ========================================
# Parte 6: Avaliação de Desempenho (Opcional)
# ========================================

# Código de avaliação
# ...

# ========================================
# Parte 7: Resumo e Perspectivas
# ========================================

"""
## Resumo do Projeto

### Características Implementadas
- Característica 1
- Característica 2

### Desafios Encontrados
- Desafio 1 e solução
- Desafio 2 e solução

### Direções Futuras de Melhoria
- Melhoria 1
- Melhoria 2
"""
```

### 16.4.4 Testando Seu Projeto

Antes da submissão, use esta lista de verificação para determinar se seu projeto atende aos requisitos de submissão:

```markdown
- [ ] Código executa normalmente sem erros
- [ ] Documentação README está completa com instruções claras
- [ ] requirements.txt contém todas as dependências
- [ ] Exemplos de uso claros fornecidos
- [ ] Código tem comentários apropriados
- [ ] Resultados de saída atendem às expectativas
- [ ] Casos de exceção comuns tratados
- [ ] Estrutura do projeto está clara com nomenclatura de arquivos padronizada
- [ ] Arquivos grandes propriamente tratados (veja próxima seção)
```

### 16.4.5 Guia de Tratamento de Arquivos Grandes

**⚠️ Importante: Evite Repositório Principal Sobredimensionado**

Para manter o repositório principal Hello-Agents leve, por favor siga estas diretrizes de tratamento de arquivos grandes:

**(1) Limites de Tamanho de Arquivo**

- **Tamanho total do projeto**: Não excedendo 5MB
- **Proibido de submissão direta**: Arquivos de vídeo, grandes conjuntos de dados, arquivos de modelo

**(2) Soluções de Tratamento de Arquivos Grandes**

Se seu projeto contém arquivos grandes (conjuntos de dados, vídeos, modelos, etc.), por favor use as seguintes soluções:

**Solução 1: Usar Links Externos (Recomendado)**

Carregar arquivos grandes em plataformas externas e fornecer links de download no README:

```markdown
## Conjuntos de Dados

Os conjuntos de dados usados neste projeto são grandes. Por favor baixe dos seguintes links:

- Conjunto de Dados 1: [Baidu Netdisk](link) Código de extração: xxxx
- Conjunto de Dados 2: [Google Drive](link)
- Vídeo de demonstração: [Bilibili](link) / [YouTube](link)
```

Plataformas externas recomendadas:
- **Conjuntos de Dados**: Baidu Netdisk, Google Drive, Kaggle, HuggingFace Datasets
- **Vídeos**: Bilibili, YouTube, Tencent Video
- **Modelos**: HuggingFace Models, ModelScope
- **Imagens**: GitHub Issues, serviços de hospedagem de imagens

**Solução 2: Criar Repositório Independente**

Se o projeto tem muitos recursos, considere criar um repositório de dados independente:

```markdown
## Recursos do Projeto

Devido à grande quantidade de dados e recursos de demonstração, um repositório de recursos separado foi criado:

- Repositório de recursos: https://github.com/seu-nome-de-usuario/nome-do-projeto-recursos
- Contém: Conjuntos de dados, vídeos de demonstração, arquivos de modelo, dados de teste, etc.

### Uso

\`\`\`bash
# Clonar repositório de recursos
git clone https://github.com/seu-nome-de-usuario/nome-do-projeto-recursos.git

# Copiar dados para diretório do projeto
cp -r nome-do-projeto-recursos/data ./data
\`\`\`
```

**Solução 3: Usar Dados de Amostra**

Fornecer apenas dados de amostra em pequena escala no repositório principal:

```python
# Explicar no README
## Descrição de Dados

- `data/sample.csv`: Dados de amostra (100 registros)
- Conjunto de dados completo (100.000 registros) baixar de [aqui](link)
```

**(3) Exemplo de Melhor Prática**

```
seu-nome-de-usuario-nome-do-projeto/
├── README.md              # Contém links de recursos externos
├── requirements.txt
├── main.ipynb
├── .gitignore            # Ignorar arquivos grandes
├── data/
│   └── sample.csv        # Somente dados de amostra (<1MB)
└── outputs/
    └── demo_result.png   # Somente resultados de demonstração (<1MB)
```

Explicação README:

```markdown
## Dados e Recursos

### Dados de Amostra
Projeto inclui dados de amostra em pequena escala para testes rápidos (localizados em `data/sample.csv`)

### Conjunto de Dados Completo
Conjunto de dados completo (500MB) baixar do seguinte link:
- Baidu Netdisk: [Link] Código de extração: xxxx
- Extrair para diretório `data/` após download

### Vídeo de Demonstração
- Bilibili: [Vídeo de Demonstração do Projeto](link)
- YouTube: [Vídeo de Demonstração](link)
```

## 16.5 Submetendo Pull Request

### 16.5.1 Submetendo Código ao GitHub

**Passo 1: Verificar Modificações**

```bash
# Ver arquivos modificados
git status
```

**Passo 2: Adicionar Arquivos**

```bash
# Adicionar todos os arquivos modificados
git add .

# Ou adicionar arquivos específicos
git add Co-creation-projects/seu-nome-de-usuario-nome-do-projeto/
```

**Passo 3: Fazer Commit das Mudanças**

Mensagens de commit devem seguir este formato:

```bash
# Formato: tipo: descrição breve
git commit -m "feat: Adicionar projeto de conclusão XXX"
```

**Especificações de Tipo de Commit:**

- `feat`: Nova característica ou projeto (use este tipo para projetos de conclusão)
- `fix`: Correção de bug
- `docs`: Atualização de documentação
- `style`: Ajuste de formato de código (não afeta funcionalidade)
- `refactor`: Refatoração de código
- `test`: Relacionado a testes
- `chore`: Outras modificações (ex.: atualizações de dependências)

**Passo 4: Enviar para GitHub**

```bash
# Enviar para seu repositório forkado
git push origin feature/seu-nome-de-projeto
```

### 16.5.2 Criando Pull Request

**Passo 1: Visitar GitHub**

1. Visite seu repositório forkado: `https://github.com/seu-nome-de-usuario/Hello-Agents`
2. Clique na aba "Pull requests", conforme mostrado na Figura 16.3
3. Clique no botão "New pull request"

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/16-figures/16-3.png" alt="" width="85%"/>
  <p>Figura 16.3 Criando Pull Request</p>
</div>

**Passo 2: Selecionar Branches**

- Repositório base: `datawhalechina/Hello-Agents`
- Branch base: `main`
- Repositório head: `seu-nome-de-usuario/Hello-Agents`
- Branch de comparação: `feature/seu-nome-de-projeto`

**Passo 3: Preencher Informações do PR**

**⚠️ Importante: Formato Unificado de Título do PR**

Para fácil gerenciamento e recuperação, todos os títulos de PR de projetos de conclusão devem seguir este formato:

```
[Projeto de Conclusão] Nome do Projeto - Descrição Breve
```

Exemplos:
- `[Projeto de Conclusão] CodeReviewAgent - Assistente Inteligente de Revisão de Código`
- `[Projeto de Conclusão] StudyBuddy - Parceiro de Aprendizado IA`
- `[Projeto de Conclusão] DataAnalyst - Analista Inteligente de Dados`

**Template de Descrição do PR:**

```markdown
## Informações do Projeto

- **Nome do Projeto**: XXX
- **Autor**: @seu-nome-de-usuario
- **Tipo de Projeto**: Ferramenta de Produtividade/Assistência ao Aprendizado/Entretenimento Criativo/Análise de Dados/Serviço de Vida

## Introdução do Projeto

Breve descrição do seu projeto (2-3 sentenças)

## Características Principais

- [ ] Característica 1
- [ ] Característica 2
- [ ] Característica 3

## Destaques Técnicos

- Usou paradigma XXX
- Implementou funcionalidade XXX
- Otimizou desempenho XXX

## Efeitos de Demonstração

(Opcional) Adicionar capturas de tela ou GIFs para mostrar efeitos do projeto

## Lista de Auto-Verificação

- [ ] Código executa normalmente
- [ ] Documentação README completa
- [ ] requirements.txt completo
- [ ] Exemplos de uso claros fornecidos
- [ ] Código tem comentários apropriados

## Outras Observações

(Opcional) Outro conteúdo que precisa de explicação
```

**Passo 4: Submeter PR**

Conforme mostrado na Figura 16.4, clique no botão "Create pull request" para submeter.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/16-figures/16-4.png" alt="" width="85%"/>
  <p>Figura 16.4 Submeter Pull Request</p>
</div>

### 16.5.3 Respondendo a Comentários de Revisão

Após submeter o PR, membros da comunidade irão revisar seu código e fornecer sugestões. Por favor responda prontamente:

1. **Ver Comentários**: Verificar comentários de revisores na página do PR
2. **Modificar Código**: Modificar código baseado em sugestões
3. **Submeter Atualizações**:
   ```bash
   git add .
   git commit -m "fix: Modificar XXX baseado em comentários de revisão"
   git push origin feature/seu-nome-de-projeto
   ```
4. **Responder a Comentários**: Responder a revisores no GitHub, explicando suas modificações

## 16.6 Exemplo de Projeto Demonstrativo

Para ajudá-lo a entender melhor os requisitos do projeto de conclusão, aqui está um exemplo de projeto completo. Não se preocupe - pequenas ideias criativas também podem ser incluídas. Qualquer trabalho que você criar é digno de ser valorizado.

**Informações do Projeto**

- **Nome do Projeto**: CodeReviewAgent
- **Autor**: @jjyaoao
- **Caminho do Projeto**: `Co-creation-projects/jjyaoao-CodeReviewAgent/`

**Estrutura do Projeto**

```
jjyaoao-CodeReviewAgent/
├── README.md              # Documentação do projeto
├── requirements.txt       # Lista de dependências
├── main.ipynb            # Programa principal (inclui demonstração rápida e características completas)
├── .env.example          # Exemplo de variáveis de ambiente
├── .gitignore            # Regras de ignorar Git
├── data/
│   └── sample_code.py    # Código de amostra
└── outputs/
    └── review_report.md  # Relatório de amostra
```

**Trecho de Código Principal (main.ipynb)**

```python
# ========================================
# Assistente Inteligente de Revisão de Código
# ========================================

from hello_agents import SimpleAgent, HelloAgentsLLM, ToolRegistry
from hello_agents.tools import Tool, ToolParameter
from typing import Dict, Any, List
import ast
import os

# ========================================
# 0. Configurar Parâmetros LLM
# ========================================

os.environ["LLM_MODEL_ID"] = "Qwen/Qwen2.5-72B-Instruct"
os.environ["LLM_API_KEY"] = "sua_chave_api_aqui"
os.environ["LLM_BASE_URL"] = "https://api-inference.modelscope.cn/v1/"
os.environ["LLM_TIMEOUT"] = "60"

# ========================================
# 1. Definir Ferramentas de Análise de Código
# ========================================

class CodeAnalysisTool(Tool):
    """Ferramenta de análise estática de código"""

    def __init__(self):
        super().__init__(
            name="code_analysis",
            description="Analisar estrutura de código Python, complexidade e problemas potenciais"
        )

    def run(self, parameters: Dict[str, Any]) -> str:
        """Analisar código e retornar resultados"""
        code = parameters.get("code", "")
        if not code:
            return "Erro: Código não pode estar vazio"

        try:
            tree = ast.parse(code)
            functions = [node for node in ast.walk(tree)
                        if isinstance(node, ast.FunctionDef)]
            classes = [node for node in ast.walk(tree)
                      if isinstance(node, ast.ClassDef)]

            result = {
                "Número de funções": len(functions),
                "Número de classes": len(classes),
                "Linhas de código": len(code.split('\n')),
                "Lista de funções": [f.name for f in functions],
                "Lista de classes": [c.name for c in classes]
            }
            return str(result)
        except SyntaxError as e:
            return f"Erro de sintaxe: {str(e)}"

    def get_parameters(self) -> List[ToolParameter]:
        return [
            ToolParameter(
                name="code",
                type="string",
                description="Código Python a analisar",
                required=True
            )
        ]

class StyleCheckTool(Tool):
    """Ferramenta de verificação de estilo de código"""

    def __init__(self):
        super().__init__(
            name="style_check",
            description="Verificar se o código está em conformidade com padrões PEP 8"
        )

    def run(self, parameters: Dict[str, Any]) -> str:
        """Verificar estilo de código"""
        code = parameters.get("code", "")
        if not code:
            return "Erro: Código não pode estar vazio"

        issues = []
        lines = code.split('\n')
        for i, line in enumerate(lines, 1):
            if len(line) > 79:
                issues.append(f"Linha {i}: Excede 79 caracteres")
            if line.startswith(' ') and not line.startswith('    '):
                if len(line) - len(line.lstrip()) not in [0, 4, 8, 12]:
                    issues.append(f"Linha {i}: Indentação não padrão")

        if not issues:
            return "Estilo de código é bom, em conformidade com padrões PEP 8"
        return "Encontrados os seguintes problemas:\n" + "\n".join(issues)

    def get_parameters(self) -> List[ToolParameter]:
        return [
            ToolParameter(
                name="code",
                type="string",
                description="Código Python a verificar",
                required=True
            )
        ]

# ========================================
# 2. Criar Registro de Ferramentas e Agente
# ========================================

# Criar registro de ferramentas
tool_registry = ToolRegistry()
tool_registry.register_tool(CodeAnalysisTool())
tool_registry.register_tool(StyleCheckTool())

# Inicializar LLM
llm = HelloAgentsLLM()

# Definir prompt do sistema
system_prompt = """Você é um especialista experiente em revisão de código. Suas tarefas são:

1. Usar ferramenta code_analysis para analisar estrutura de código
2. Usar ferramenta style_check para verificar estilo de código
3. Baseado em resultados de análise, fornecer relatório de revisão detalhado

O relatório de revisão deve incluir:
- Análise de estrutura de código
- Problemas de estilo
- Bugs potenciais
- Sugestões de otimização de desempenho
- Recomendações de melhores práticas

Por favor produza o relatório em formato Markdown."""

# Criar agente
agent = SimpleAgent(
    name="Assistente de Revisão de Código",
    llm=llm,
    system_prompt=system_prompt,
    tool_registry=tool_registry
)

# ========================================
# 3. Executar Exemplo
# ========================================

# Ler código de amostra
with open("data/sample_code.py", "r", encoding="utf-8") as f:
    sample_code = f.read()

print("=== Código a Revisar ===")
print(sample_code)
print("\n" + "="*50 + "\n")

# Executar revisão de código
print("=== Iniciando Revisão de Código ===")
review_result = agent.run(f"Por favor revise o seguinte código Python:\n\n```python\n{sample_code}\n```")

print(review_result)

# Salvar relatório de revisão
with open("outputs/review_report.md", "w", encoding="utf-8") as f:
    f.write(review_result)

print("\nRelatório de revisão salvo em outputs/review_report.md")
```

**Exemplo README.md**

```markdown
# CodeReviewAgent - Assistente Inteligente de Revisão de Código

> Ferramenta inteligente de revisão de código baseada no framework HelloAgents

## 📝 Introdução do Projeto

CodeReviewAgent é um assistente inteligente de revisão de código que pode analisar automaticamente a qualidade de código Python, descobrir problemas potenciais e fornecer sugestões de otimização.

### Características Principais

- ✅ Análise de estrutura de código: Contar funções, classes, linhas de código, etc.
- ✅ Verificação de estilo: Verificar conformidade com padrões PEP 8
- ✅ Sugestões inteligentes: Fornecer análise profunda e sugestões de otimização baseadas em LLM
- ✅ Geração de relatórios: Gerar relatórios de revisão em formato Markdown

## 🛠️ Pilha Tecnológica

- Framework HelloAgents (SimpleAgent + ToolRegistry)
- Módulo Python AST (análise de código)
- API ModelScope (modelo Qwen2.5-72B)

## 🚀 Início Rápido

### Instalar Dependências

\`\`\`bash
pip install -r requirements.txt
\`\`\`

### Configurar Parâmetros LLM

**Método 1: Usar arquivo .env**

\`\`\`bash
cp .env.example .env
# Editar arquivo .env e preencher sua chave API
\`\`\`

**Método 2: Definir diretamente no Notebook**

O projeto está pré-configurado com API ModelScope e pode executar diretamente. Para modificar, edite o código de configuração na Parte 1 de main.ipynb.

### Executar Projeto

\`\`\`bash
jupyter lab
# Abrir main.ipynb e executar todas as células
\`\`\`

## 📖 Exemplo de Uso

1. Colocar código a revisar em `data/sample_code.py`
2. Executar `main.ipynb`
3. Ver relatório de revisão gerado `outputs/review_report.md`

## 🎯 Destaques do Projeto

- **Automação**: Não é necessário verificação manual linha por linha, descobre problemas automaticamente
- **Inteligência**: Usa LLM para entender semântica de código e fornecer sugestões profundas
- **Extensibilidade**: Fácil adicionar novas regras de verificação e ferramentas

## 👤 Autor

- GitHub: [@jjyaoao](https://github.com/jjyaoao)
- Link do projeto: [CodeReviewAgent](https://github.com/datawhalechina/Hello-Agents/tree/main/Co-creation-projects/jjyaoao-CodeReviewAgent)

## 🙏 Agradecimentos

Obrigado à comunidade Datawhale e ao projeto Hello-Agents!
```

## 16.7 Resumo e Perspectivas

Ao completar o projeto de conclusão, você deve ter dominado o processo completo de design de sistema de agentes: projetar arquitetura de sistema a partir de requisitos, usar habilmente várias funções e componentes do framework HelloAgents, desenvolver ferramentas personalizadas para estender capacidades de agentes, completar desenvolvimento completo de projeto desde análise de requisitos até implementação de código, aprender a usar Git e GitHub para colaboração open-source, e escrever documentação técnica clara.

Neste projeto, construímos o framework HelloAgents do zero e o usamos para implementar múltiplas aplicações práticas. Completar o projeto de conclusão é apenas o começo. Você pode continuar a aprofundar seu aprendizado de mais paradigmas e algoritmos de agentes, engenharia de prompts e engenharia de contexto, mecanismos de colaboração multi-agente, e outros conhecimentos teóricos. Você também pode expandir sua pilha tecnológica aprendendo desenvolvimento web para construir aplicações completas, aprendendo bancos de dados para implementar persistência de dados, e aprendendo implantação para lançar aplicações online. Você também pode continuamente otimizar seu projeto adicionando mais características, otimizando desempenho e experiência do usuário, e melhorando testes e documentação. Mais importante, participe ativamente de contribuições da comunidade ajudando outros aprendizes, participando do desenvolvimento do framework Hello-Agents, e compartilhando suas experiências e insights.

Do agente simples no Capítulo 1 até agora sendo capaz de construir independentemente aplicações multi-agente completas, você viajou através de uma jornada de aprendizado emocionante. Mas isso não é o fim - é um novo começo.

A tecnologia de IA está mudando rapidamente, e o campo de agentes está cheio de possibilidades infinitas. Esperamos que você possa manter a curiosidade e continuamente aprender novas tecnologias, corajosamente usar tecnologia IA para resolver problemas práticos e criar valor, compartilhar voluntariamente suas experiências e conquistas com a comunidade, e constantemente refinar seu trabalho em busca da excelência.

Finalmente, obrigado por ler este projeto em sua totalidade. Esperamos que você tenha ganho algo do processo de aprendizado e que você possa aplicar o que aprendeu a projetos reais, criando aplicações de agentes incríveis. O futuro da IA está cheio de possibilidades infinitas - vamos explorar e criar juntos!

**Lembre-se: A melhor maneira de aprender é através da prática hands-on!**

Agora, comece a construir sua própria aplicação de agente! Esperamos ver seu excelente trabalho no diretório Co-creation-projects!

Se você acha o projeto Hello-Agents útil, por favor nos dê uma ⭐Star!

---
<div align="center">
  <strong>🎓 Parabéns por completar o tutorial Hello-Agents! 🎉</strong>
</div>
