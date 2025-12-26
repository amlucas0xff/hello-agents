<div align="right">
  <a href="./Extra05-AgentSkills解读.md">中文</a> | Português
</div>

# Agent Skills e MCP: Dois Paradigmas de Extensão de Capacidades de Agentes Inteligentes

## Introdução: Após o MCP, o que ainda precisamos?

No capítulo dez, exploramos profundamente como o MCP (Model Context Protocol - Protocolo de Contexto de Modelo) resolve o problema de conexão entre agentes inteligentes e ferramentas externas através de protocolos padronizados. Você já aprendeu como fazer agentes acessarem bancos de dados, sistemas de arquivos, serviços de API e vários outros recursos através do MCP. Vamos revisar um cenário típico de uso do MCP:

```python
from hello_agents import ReActAgent, HelloAgentsLLM
from hello_agents.tools import MCPTool

llm = HelloAgentsLLM()
agent = ReActAgent(name="Assistente de Análise de Dados", llm=llm)

# Conectar ao servidor MCP do banco de dados
db_mcp = MCPTool(server_command=["python", "database_mcp_server.py"])
agent.add_tool(db_mcp)

# Agente agora pode acessar o banco de dados
response = agent.run("Consultar os 10 funcionários com maior salário na tabela de funcionários")
```

Este código funciona muito bem, o agente inteligente se conectou com sucesso ao banco de dados. Mas quando você tenta lidar com tarefas mais complexas, descobrirá alguns problemas sutis:

```python
# Uma necessidade mais complexa
response = agent.run("""
Analisar quem tem maior influência dentro da empresa?
Precisa considerar de forma abrangente:
1. Nível gerencial e número de subordinados
2. Nível salarial e amplitude de aumentos
3. Tempo de serviço e estabilidade
4. Influência interdepartamental
""")
```

Esta tarefa requer executar múltiplas consultas ao banco de dados, onde os resultados de cada consulta afetarão a estratégia da próxima consulta. Mais crucial ainda, ela requer que o agente inteligente possua <strong>conhecimento de domínio</strong>: saber como medir "influência", saber de quais dimensões analisar dados, saber como combinar resultados de múltiplas consultas para chegar a conclusões.

Neste momento, você encontrará dois problemas fundamentais:

<strong>O primeiro problema é explosão de contexto</strong>. Para permitir que o agente consulte o banco de dados de forma flexível, servidores MCP geralmente expõem dezenas ou até centenas de ferramentas (diferentes tabelas, diferentes métodos de consulta). O JSON Schema completo dessas ferramentas é carregado no prompt do sistema quando a conexão é estabelecida, podendo ocupar dezenas de milhares de tokens. Segundo feedback de desenvolvedores da comunidade, carregar apenas um servidor MCP do Playwright pode ocupar 8% de uma janela de contexto de 200k, o que se acumula rapidamente em diálogos de múltiplas rodadas, causando custos disparados e queda na capacidade de inferência.

<strong>O segundo problema é lacuna de capacidades</strong>. O MCP resolveu o problema de "poder conectar", mas não resolveu o problema de "saber como usar". Ter capacidade de conexão ao banco de dados não significa que o agente saiba escrever SQL eficiente e seguro; poder acessar o sistema de arquivos não significa que ele entenda a estrutura de código e normas de desenvolvimento de um projeto específico. É como dar a um programador iniciante acesso a todos os sistemas, mas sem fornecer manual de operações e melhores práticas.

É exatamente isso que <strong>Agent Skills (Habilidades de Agente)</strong> visa resolver. No início de 2025, a Anthropic, após lançar o MCP, propôs ainda o conceito de Agent Skills, gerando ampla atenção na indústria. Alguns desenvolvedores comentaram: "Skills e MCP são duas coisas diferentes. Skills é conhecimento de domínio, dizendo ao modelo como fazer, essencialmente é Prompt avançado; enquanto MCP conecta ferramentas e dados externos." Outros acreditam: "De Function Call a Tool Call a MCP a Skills, o núcleo é basicamente o mesmo, apenas otimização e evolução de práticas de engenharia e formas de apresentação."

Então, o que exatamente é Agent Skills? Qual é a diferença essencial com o MCP? Os dois são competitivos ou complementares? Este capítulo explorará profundamente essas questões.

## O que é Agent Skills?

### Conceito Central de Design

<strong>Agent Skills é um formato padronizado de encapsulamento de conhecimento procedural</strong>. Se o MCP fornece "mãos" para o agente operar ferramentas, então Skills fornece o "manual de operação" ou "SOP (Procedimento Operacional Padrão)", ensinando o agente como usar corretamente essas ferramentas.

Este conceito de design origina-se de uma percepção simples mas profunda: <strong>Conectividade e Capacidade devem ser separadas</strong>. MCP foca no primeiro, Skills foca no segundo. Esta separação de responsabilidades traz vantagens arquiteturais claras:

- <strong>Responsabilidade do MCP</strong>: Fornecer interface padronizada de acesso, permitindo que o agente "alcance" dados e ferramentas do mundo externo
- <strong>Responsabilidade do Skills</strong>: Fornecer conhecimento profissional de domínio, dizendo ao agente em cenários específicos "como combinar o uso dessas ferramentas"

Use uma analogia para entender: MCP é como interface USB ou driver, define como dispositivos se conectam; enquanto Skills é como aplicativo de software, define como usar esses dispositivos conectados para completar tarefas específicas. Você pode ter um driver de impressora totalmente funcional (MCP), mas se não lhe disserem como configurar margens e impressão duplex no Word (Skill), você ainda não conseguirá completar eficientemente a tarefa de impressão.

### Divulgação Progressiva: Resolvendo o Dilema do Contexto

A inovação central do Agent Skills é o <strong>mecanismo de Divulgação Progressiva (Progressive Disclosure)</strong>. Este mecanismo divide informações de habilidades em três níveis, o agente carrega gradualmente conforme necessário, garantindo que detalhes não sejam perdidos quando necessário, evitando ao mesmo tempo inserir muito conteúdo na janela de contexto de uma vez.

<div align="center">
  <img src="./images/Extra05-figures/image1.png" alt="Arquitetura de três camadas de divulgação progressiva" width="90%"/>
  <p>Figura 1 Arquitetura de três camadas de divulgação progressiva do Agent Skills</p>
</div>


#### Primeira Camada: Metadados (Metadata)

No design de Skills, cada habilidade é armazenada em uma pasta independente, o núcleo é um arquivo Markdown chamado `SKILL.md`. Este arquivo deve começar com Frontmatter em formato YAML, definindo informações básicas da habilidade.

Quando o agente é iniciado, ele escaneia todas as pastas de habilidades instaladas, <strong>lendo apenas a parte Frontmatter de cada `SKILL.md`</strong>, carregando esses metadados no prompt do sistema. Segundo dados de testes reais, os metadados de cada habilidade consomem apenas cerca de <strong>100 tokens</strong>. Mesmo se você tiver 50 habilidades instaladas, o consumo inicial de contexto é de apenas cerca de 5.000 tokens.

Isso forma um contraste marcante com o modo de funcionamento do MCP. Em implementações típicas do MCP, quando o cliente se conecta a um servidor, geralmente obtém o JSON Schema completo de todas as ferramentas disponíveis através da solicitação `tools/list`, podendo consumir imediatamente dezenas de milhares de tokens.

#### Segunda Camada: Corpo da Habilidade (Instructions)

Quando o agente, através da análise da solicitação do usuário, julga que uma determinada habilidade está altamente relacionada à tarefa atual, ele entra no carregamento da segunda camada. Neste momento, o agente lerá o conteúdo completo do arquivo `SKILL.md` daquela habilidade, carregando instruções detalhadas, precauções, exemplos, etc., no contexto.

Neste momento, o agente obtém todo o contexto necessário para completar a tarefa: estrutura do banco de dados, padrões de consulta, precauções, etc. O consumo de tokens desta parte depende da complexidade das instruções, geralmente entre 1.000 a 5.000 tokens.

#### Terceira Camada: Recursos Adicionais (Scripts & References)

Para habilidades mais complexas, `SKILL.md` pode referenciar outros arquivos na mesma pasta: scripts, arquivos de configuração, documentação de referência, etc. O agente <strong>carrega esses recursos apenas quando necessário</strong>.

Por exemplo, a estrutura de arquivos de uma habilidade de processamento de PDF pode ser:

```
skills/pdf-processing/
├── SKILL.md              # Arquivo principal da habilidade
├── parse_pdf.py          # Script de análise de PDF
├── forms.md              # Guia de preenchimento de formulários (carregado apenas em tarefas de preenchimento de formulários)
└── templates/            # Arquivos de template PDF
    ├── invoice.pdf
    └── report.pdf
```

Em `SKILL.md`, recursos adicionais podem ser referenciados assim:

- Quando precisar executar análise de PDF, o agente executará o script `parse_pdf.py`
- Quando encontrar tarefa de preenchimento de formulário, só então carregará `forms.md` para conhecer as etapas detalhadas
- Arquivos de template são acessados apenas quando for necessário gerar documentos de formato específico

Este design tem duas vantagens principais:

1. <strong>Capacidade ilimitada de conhecimento</strong>: Através de scripts e arquivos externos, habilidades podem "carregar" conhecimento muito além dos limites do contexto. Por exemplo, uma habilidade de análise de dados pode vir com um arquivo de dados de 1GB e um script de consulta. O agente acessa os dados executando o script, sem precisar carregar todo o conjunto de dados no contexto.

2. <strong>Execução determinística</strong>: Cálculos complexos, transformação de dados, análise de formatos e outras tarefas são entregues ao código para execução, evitando incerteza e problemas de alucinação no processo de geração do LLM.

### Efeito da Divulgação Progressiva: De 16k para 500 Tokens

Casos práticos compartilhados por desenvolvedores da comunidade provam amplamente o poder da divulgação progressiva. Em um cenário real:

- <strong>Modo MCP tradicional</strong>: Conectando diretamente a um servidor MCP com grande quantidade de definições de ferramentas, carregamento inicial consome <strong>16.000 tokens</strong>
- <strong>Após empacotamento em Skills</strong>: Criando uma Skill simples como "gateway", descrevendo apenas a funcionalidade no Frontmatter, consumo inicial de apenas <strong>500 tokens</strong>

Quando o agente determina que precisa usar aquela habilidade, só então carregará instruções detalhadas e chamará as ferramentas MCP subjacentes conforme necessário. Esta arquitetura não apenas reduz drasticamente o custo inicial, mas também torna o gerenciamento de contexto durante o diálogo mais preciso e eficiente.

## Agent Skills vs MCP: Diferenças Essenciais e Relação de Colaboração

Agora podemos comparar sistematicamente as diferenças essenciais entre essas duas tecnologias.

<div align="center">
  <img src="./images/Extra05-figures/image2.png" alt="Comparação de filosofias de design MCP e Agent Skills" width="90%"/>
  <p>Figura 2 Comparação de filosofias de design MCP e Agent Skills</p>
</div>

### Compreendendo Diferenças da Perspectiva de Engenharia

Vamos entender essa diferença através de um exemplo específico. Suponha que você queira construir um agente para ajudar a equipe com revisão de código:

<strong>Responsabilidade do MCP</strong>:

```python
# MCP fornece acesso padronizado ao GitHub
github_mcp = MCPTool(server_command=["npx", "-y", "@modelcontextprotocol/server-github"])

# Ferramentas expostas pelo MCP (exemplo simplificado):
# - list_pull_requests(repo, state)
# - get_pull_request_details(pr_number)
# - list_pr_comments(pr_number)
# - create_pr_comment(pr_number, body)
# - get_file_content(repo, path, ref)
# - list_pr_files(pr_number)
```

MCP permite que o agente "possa" acessar o GitHub, possa chamar essas APIs. Mas não sabe o que "deveria" fazer.

<strong>Responsabilidade do Skills</strong>:

```markdown
---
name: code-review-workflow
description: Executar processo padrão de revisão de código, incluindo verificação de estilo de código, problemas de segurança, cobertura de testes, etc.
---

# Workflow de Revisão de Código

## Checklist de Revisão

Ao executar revisão de código, siga estas etapas:

1. **Obter informações do PR**: Chamar `get_pull_request_details` para entender o contexto das mudanças
2. **Analisar arquivos alterados**: Chamar `list_pr_files` para obter lista de arquivos
3. **Revisar arquivo por arquivo**:
   - Para arquivos `.py`: Verificar conformidade com PEP 8, verificar problemas óbvios de desempenho
   - Para arquivos `.js/.ts`: Verificar Promises não tratados, verificar uso de APIs descontinuadas
   - Para arquivos de teste: Verificar se novos caminhos de código estão cobertos
4. **Verificação de segurança**:
   - Informações sensíveis hardcoded (chaves, senhas)
   - Riscos de SQL injection ou XSS
5. **Fornecer feedback**:
   - Problemas graves: Usar `create_pr_comment` para comentar diretamente
   - Sugestões de melhoria: Apresentar no resumo

## Normas Específicas da Empresa

- Todas as consultas ao banco de dados devem usar consultas parametrizadas
- Endpoints de API devem ter decoradores de verificação de permissões
- Novas funcionalidades devem vir com testes unitários (cobertura > 80%)

## Modelo de Comentário Exemplo

**Problema grave**:

⚠️ Risco de segurança: Linha 45 concatena string SQL diretamente, existe risco de injeção.
Sugestão: usar consulta parametrizada: `cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))`

```

Skills dizem ao agente o que "deveria" fazer, como organizar o processo de revisão, quais normas específicas da empresa devem ser observadas. É um container de conhecimento de domínio e melhores práticas.

### Diferença Essencial nas Estratégias de Gerenciamento de Contexto

<div align="center">
  <img src="./images/Extra05-figures/image3.png" alt="Carregamento ansioso do MCP vs carregamento preguiçoso do Skills" width="90%"/>
  <p>Figura 3 Comparação de carregamento ansioso do MCP vs carregamento preguiçoso do Skills</p>
</div>


### Complementares, não Competitivos: Arquitetura Híbrida de Skills + MCP

Compreendendo as diferenças entre os dois, descobrimos: <strong>Skills e MCP não são competitivos, mas complementares</strong>. A melhor prática é combinar os dois, formando uma arquitetura em camadas:

<div align="center">
  <img src="./images/Extra05-figures/image4.png" alt="Arquitetura híbrida Skills + MCP" width="90%"/>
  <p>Figura 4 Design de arquitetura híbrida Skills + MCP</p>
</div>

<strong>Workflow típico</strong>:

1. Usuário pergunta: "Analisar quem tem maior influência dentro da empresa"
2. <strong>Camada Skills</strong> identifica que é uma tarefa de análise de dados, carrega a habilidade `mysql-employees-analysis`
3. <strong>Camada Skills</strong>, de acordo com as instruções da habilidade, decompõe a tarefa em subetapas: consultar relações gerenciais, comparação salarial, tempo de serviço, etc.
4. <strong>Camada MCP</strong> executa consultas SQL específicas, retorna resultados
5. <strong>Camada Skills</strong>, de acordo com o conhecimento de domínio na habilidade, interpreta dados e gera análise abrangente
6. Retorna resposta estruturada ao usuário

As vantagens desta arquitetura são:

- <strong>Separação de responsabilidades</strong>: MCP foca em "capacidade", Skills foca em "sabedoria"
- <strong>Otimização de custos</strong>: Carregamento progressivo reduz drasticamente o consumo de tokens
- <strong>Manutenibilidade</strong>: Lógica de negócio (Skills) e infraestrutura (MCP) desacoplados
- <strong>Reutilização</strong>: Um mesmo servidor MCP pode ser usado por múltiplas Skills

## Implementação Técnica: Como Criar e Usar Skills

### Especificação Detalhada do SKILL.md

Vamos entender profundamente a estrutura padrão do arquivo `SKILL.md`:

```markdown
---
# === Campos Obrigatórios ===
name: skill-name
  # Identificador único da habilidade, usar nomenclatura kebab-case

description: >
  Descrição concisa mas precisa, explicando:
  1. O que esta habilidade faz
  2. Quando deve ser usada
  3. Qual é seu valor central
  # Nota: description é a única base para o agente escolher a habilidade, deve ser clara!

# === Campos Opcionais ===
version: 1.0.0
  # Número de versão semântica

allowed_tools: [tool1, tool2]
  # Lista de ferramentas que esta habilidade pode chamar (whitelist)

required_context: [context_item1]
  # Informações de contexto que esta habilidade necessita

license: MIT
  # Protocolo de licença

author: Your Name <email@example.com>
  # Informações do autor

tags: [database, analysis, sql]
  # Tags para facilitar categorização e busca
---

# Título da Habilidade

## Visão Geral
(Introdução detalhada da habilidade, incluindo cenários de uso, contexto técnico, etc.)

## Pré-requisitos
(Configuração de ambiente, dependências necessárias para usar esta habilidade, etc.)

## Processo de Trabalho
(Instruções detalhadas de etapas, dizendo ao agente como executar tarefas)

## Melhores Práticas
(Resumo de experiências, precauções, armadilhas comuns, etc.)

## Exemplos
(Casos de uso específicos, ajudando o agente a entender)

## Solução de Problemas
(Problemas comuns e soluções)
```

### Princípios para Escrever Skills de Alta Qualidade

De acordo com a documentação oficial da Anthropic e melhores práticas da comunidade, escrever Skills eficazes requer seguir os seguintes princípios:

#### 1. Description Preciso

`description` é chave para a tomada de decisão do agente. Deve:

- <strong>Definir precisamente o escopo de aplicação</strong>: Evitar descrições vagas como "ajudar a processar dados"
- <strong>Incluir palavras-chave de ativação</strong>: Permitir que o agente corresponda à intenção do usuário
- <strong>Explicar valor único</strong>: Diferenciar de outras habilidades

❌ <strong>Description ruim</strong>:
```yaml
description: Processar consultas ao banco de dados
```

✅ <strong>Description bom</strong>:
```yaml
description: >
  Converter problemas de negócio em chinês para consultas SQL e analisar banco de dados exemplo MySQL employees.
  Adequado para cenários como consulta de informações de funcionários, estatísticas salariais, análise departamental, histórico de mudanças de cargos, etc.
  Usar esta habilidade quando o usuário perguntar sobre dados de funcionários, salários, departamentos.
```

#### 2. Modularidade e Responsabilidade Única

Uma Skill deve focar em um domínio ou tipo de tarefa claramente definido. Se uma Skill tentar fazer coisas demais, resultará em:

- Description muito amplo, precisão de correspondência diminui
- Conteúdo de instruções muito longo, desperdiçando contexto
- Difícil de manter e atualizar

<strong>Recomendação</strong>: Ao invés de criar uma habilidade "análise de dados geral", crie múltiplas habilidades especializadas:
- `mysql-employees-analysis`: Analisar especificamente banco de dados employees
- `sales-data-analysis`: Analisar especificamente dados de vendas
- `user-behavior-analysis`: Analisar especificamente dados de comportamento do usuário

#### 3. Princípio de Prioridade ao Determinismo

Para tarefas complexas que requerem execução precisa, priorize usar scripts ao invés de depender da geração do LLM. Por exemplo, em cenários de exportação de dados, ao invés de deixar o LLM gerar conteúdo binário do Excel (propenso a erros), escreva um script específico para lidar com esta tarefa. SKILL.md apenas precisa orientar o agente sobre quando chamar este script.

#### 4. Estratégia de Divulgação Progressiva

Use razoavelmente a estrutura de três camadas, dividindo informações por importância e frequência de uso:

- <strong>Corpo do SKILL.md</strong>: Colocar workflow central, padrões comuns
- <strong>Documentos adicionais</strong> (como `advanced.md`): Colocar uso avançado, casos extremos
- <strong>Arquivos de dados</strong>: Colocar dados de referência grandes, consultar conforme necessário através de scripts

### Caso Prático: Análise Detalhada da Skill de Análise de Funcionários MySQL

Vamos entender a aplicação específica do Agent Skills através de um caso real da comunidade Anthropic. Esta habilidade é usada para analisar o banco de dados exemplo `employees` oficial do MySQL.

#### Estrutura de Arquivos da Habilidade

```
skills/mysql-employees-analysis/
├── SKILL.md          # Arquivo principal da habilidade (contém metadados e instruções detalhadas)
└── db_schema.sql     # Referência de estrutura do banco de dados (opcional, carregar conforme necessário)
```

#### Exemplo de Conteúdo Central do SKILL.md

O Frontmatter (camada de metadados) desta habilidade:

```markdown
---
name: mysql-employees-analysis
description: >
  Converter problemas de negócio em chinês para consultas SQL e analisar banco de dados exemplo MySQL employees.
  Adequado para consultas de informações de funcionários (como "informações do funcionário número 12345"),
  estatísticas salariais (como "departamento com maior salário médio"),
  análise departamental (como "distribuição de número de pessoas por departamento"),
  histórico de mudanças de cargo (como "trajetória de promoção de determinado funcionário"), etc.
version: 1.0.0
allowed_tools: [execute_sql]
tags: [database, mysql, sql, employees, analysis]
---

# Habilidade de Análise de Banco de Dados de Funcionários MySQL

## Visão Geral

Esta habilidade é especialmente usada para analisar o banco de dados exemplo `employees` fornecido oficialmente pelo MySQL.
Este banco de dados contém registros de cerca de 300.000 funcionários virtuais, cobrindo dados de 1985-2000.

**Capacidades Centrais**:
- Entender problemas de negócio em linguagem natural chinesa
- Converter para consultas SQL eficientes
- Executar consultas e interpretar resultados
- Fornecer insights de negócio e interpretação de dados

## Estrutura do Banco de Dados

### Estrutura Central de Tabelas

| Nome da Tabela | Descrição | Campos Chave |
| -------------- | ------------ | ------------------------------------------------------------ |
| `employees`    | Informações básicas de funcionários | emp_no, birth_date, first_name, last_name, gender, hire_date |
| `salaries`     | Histórico salarial | emp_no, salary, from_date, to_date |
| `titles`       | Histórico de cargos | emp_no, title, from_date, to_date |
| `dept_emp`     | Relação funcionário-departamento | emp_no, dept_no, from_date, to_date |
| `dept_manager` | Gerente de departamento | emp_no, dept_no, from_date, to_date |
| `departments`  | Informações de departamentos | dept_no, dept_name |

### Convenções Importantes

⚠️ **Importante**: `to_date = '9999-01-01'` indica registro "atualmente válido".
Ao consultar estado "atual" (como funcionários atuais, salário atual), deve adicionar esta condição de filtro.

Para estrutura completa da tabela, consulte: `db_schema.sql`

## Processo de Trabalho

### Primeira Etapa: Entender Requisitos

Analise cuidadosamente a descrição em chinês do usuário, identifique:
- **Objetivo da consulta**: Quais dados consultar? (funcionários, salários, departamentos...)
- **Condições de filtro**: Quais limitações? (departamento específico, intervalo de tempo, faixa salarial...)
- **Dimensão de agregação**: Precisa de estatísticas? (média, total, ranking...)
- **Intervalo de tempo**: Dados históricos ou estado atual?

### Segunda Etapa: Construir SQL

Escolha padrão de consulta apropriado de acordo com requisitos (veja "Padrões de Consulta Comuns" abaixo).

**Princípios de escrita**:
1. Use aliases de tabela claros (como `e` para employees)
2. Ao fazer JOIN, priorize usar chaves primárias/estrangeiras
3. Atenção a filtros de data (especialmente `to_date`)
4. Use razoavelmente campos indexados
5. Adicione LIMIT para grandes conjuntos de resultados

### Terceira Etapa: Executar Consulta

Chame a ferramenta `execute_sql` para executar o SQL construído.

```python
# Exemplo de chamada (agente converterá automaticamente para chamada de ferramenta)
result = execute_sql(query="SELECT ...")
```

### Quarta Etapa: Interpretar Resultados

Transforme resultados da consulta em resposta em linguagem natural:
- Apresente dados estruturados em tabela
- Destaque pontos de dados chave
- Forneça insights de negócio (como tendências, anomalias)
- Se resultados estiverem vazios, explique possíveis razões

## Padrões de Consulta Comuns

### Padrão 1: Consulta de Informações Básicas

```sql
-- Consultar informações básicas de funcionário específico
SELECT emp_no, CONCAT(first_name, ' ', last_name) AS full_name,
       gender, birth_date, hire_date
FROM employees
WHERE emp_no = <número_funcionário>;
```

### Padrão 2: Consulta de Estado Atual

```sql
-- Consultar funcionários com maior salário atual (TOP 10)
SELECT e.emp_no,
       CONCAT(e.first_name, ' ', e.last_name) AS name,
       s.salary
FROM employees e
JOIN salaries s ON e.emp_no = s.emp_no
WHERE s.to_date = '9999-01-01'  -- Salário atual
ORDER BY s.salary DESC
LIMIT 10;
```

### Padrão 3: Análise de Tendências Históricas

```sql
-- Consultar histórico de mudanças salariais de determinado funcionário
SELECT emp_no, salary, from_date, to_date,
       salary - LAG(salary) OVER (ORDER BY from_date) AS increase
FROM salaries
WHERE emp_no = <número_funcionário>
ORDER BY from_date;
```

### Padrão 4: Consulta de Relacionamento Entre Tabelas

```sql
-- Consultar salário médio por departamento (atual)
SELECT d.dept_name,
       COUNT(DISTINCT de.emp_no) AS emp_count,
       ROUND(AVG(s.salary), 2) AS avg_salary
FROM departments d
JOIN dept_emp de ON d.dept_no = de.dept_no
JOIN salaries s ON de.emp_no = s.emp_no
WHERE de.to_date = '9999-01-01'  -- Atualmente empregado
  AND s.to_date = '9999-01-01'   -- Salário atual
GROUP BY d.dept_name
ORDER BY avg_salary DESC;
```

### Padrão 5: Análise de Negócio Complexa

```sql
-- Analisar "influência": Combinar nível gerencial, salário, tempo de serviço
WITH manager_hierarchy AS (
    -- Contar subordinados de cada gerente
    SELECT dm.emp_no, COUNT(de.emp_no) AS subordinate_count
    FROM dept_manager dm
    JOIN dept_emp de ON dm.dept_no = de.dept_no
    WHERE dm.to_date = '9999-01-01'
      AND de.to_date = '9999-01-01'
      AND de.emp_no != dm.emp_no
    GROUP BY dm.emp_no
),
current_salary AS (
    -- Salário atual
    SELECT emp_no, salary
    FROM salaries
    WHERE to_date = '9999-01-01'
),
tenure AS (
    -- Tempo de serviço (anos)
    SELECT emp_no,
           TIMESTAMPDIFF(YEAR, hire_date, CURDATE()) AS years_employed
    FROM employees
)
SELECT e.emp_no,
       CONCAT(e.first_name, ' ', e.last_name) AS name,
       COALESCE(mh.subordinate_count, 0) AS team_size,
       cs.salary,
       t.years_employed,
       -- Pontuação simples de influência (pode ajustar pesos de acordo com negócio)
       (COALESCE(mh.subordinate_count, 0) * 10 +
        cs.salary / 1000 +
        t.years_employed * 5) AS influence_score
FROM employees e
JOIN current_salary cs ON e.emp_no = cs.emp_no
JOIN tenure t ON e.emp_no = t.emp_no
LEFT JOIN manager_hierarchy mh ON e.emp_no = mh.emp_no
WHERE cs.salary > 60000  -- Filtrar funcionários de baixo salário
ORDER BY influence_score DESC
LIMIT 20;
```

## Precauções

### ⚠️ Tratamento Correto de Campos de Data

- <strong>Estado atual</strong>: Deve usar filtro `to_date = '9999-01-01'`
- <strong>Consulta histórica</strong>: Atenção ao intervalo de `from_date` e `to_date`
- <strong>Cálculos de tempo</strong>: Use funções como `TIMESTAMPDIFF`, `DATEDIFF`

### ⚠️ Otimização de Desempenho

- <strong>JOIN de tabelas grandes</strong>: Priorize uso de campos indexados (emp_no, dept_no)
- <strong>Consultas agregadas</strong>: Use razoavelmente GROUP BY e HAVING
- <strong>Limitação de resultados</strong>: Para consultas de exibição, adicione limite LIMIT
- <strong>Otimização de subconsultas</strong>: Use WITH (CTE) para melhorar legibilidade e desempenho em consultas complexas

### ⚠️ Qualidade de Dados

- <strong>Tratamento de valores NULL</strong>: Use COALESCE ou IFNULL para tratar valores vazios
- <strong>Registros duplicados</strong>: Note que funcionários podem ter múltiplas transferências, considere deduplicação ao consultar
- <strong>Intervalo de dados</strong>: Banco de dados contém apenas dados de 1985-2000, atenção aos limites de tempo ao consultar

## Solução de Problemas

<strong>Problema 1: Resultados da consulta vazios</strong>
- Verificar se usou corretamente `to_date = '9999-01-01'`
- Verificar se número de funcionário ou departamento existe
- Verificar se intervalo de datas é razoável

<strong>Problema 2: Consulta lenta</strong>
- Verificar se faltam condições WHERE em campos indexados
- Considere dividir consultas complexas em múltiplas etapas
- Use EXPLAIN para analisar plano de consulta

<strong>Problema 3: Dados estatísticos imprecisos</strong>
- Atenção para distinguir estado "histórico" e "atual"
- Verificar se condições de JOIN foram omitidas
- Verificar se uso de funções agregadas está correto
```

Este arquivo SKILL.md mostra a estrutura de uma habilidade completa:
- Metadados claros (usados pelo agente para descoberta e correspondência)
- Explicação completa da estrutura do banco de dados
- Orientação detalhada do processo de trabalho
- Exemplos ricos de padrões de consulta (templates SQL diretamente reutilizáveis)
- Precauções práticas e solução de problemas

#### Efeito de Uso da Habilidade

Quando usuário faz pergunta a agente inteligente que suporta Agent Skills (como Claude Desktop, Claude Code):

**Pergunta do usuário**:
> "Analisar quem tem maior influência dentro da empresa? Precisa considerar de forma abrangente nível gerencial, nível salarial e tempo de serviço."

<div align="center">
  <img src="./images/Extra05-figures/image5.png" alt="Fluxo de trabalho do Agent Skills" width="90%"/>
  <p>Figura 5 Diagrama de fluxo de trabalho completo do Agent Skills</p>
</div>


**Exemplo de saída**:

| Ranking | Número Funcionário | Nome | Tamanho da Equipe | Salário | Tempo de Serviço | Pontuação de Influência |
| ---- | ------ | -------------------- | -------- | ------- | -------- | ---------- |
| 1    | 110022 | Margareta Markovitch | 45       | 152,710 | 18       | 692.71     |
| 2    | 110039 | Vishwani Minakawa    | 38       | 138,273 | 16       | 598.27     |
| 3    | 110085 | Ebru Alpin           | 32       | 124,054 | 15       | 519.05     |

**Insights chave**:
1. Funcionários com maior influência geralmente gerenciam equipes grandes (30+ pessoas), salário top 1% (>120k), tempo de serviço >15 anos
2. Influência de gerentes de departamento supera em muito funcionários comuns, tamanho de gestão é fator chave
3. Funcionários de alto salário de longo prazo, mesmo não ocupando cargos gerenciais, também possuem forte influência

Durante todo o processo, a habilidade forneceu:
- **Conhecimento de domínio**: Como medir "influência" (tamanho de gestão + salário + tempo de serviço)
- **Orientação técnica**: Como escrever SQL eficiente (usar CTE, funções de janela, JOIN de múltiplas tabelas)
- **Compreensão de negócio**: Como interpretar dados e gerar insights

### Compartilhamento e Reutilização de Skills

Outra característica importante do Agent Skills é **comunitarização**. A Anthropic estabeleceu repositório oficial de Skills:

**Biblioteca oficial de habilidades**: https://github.com/anthropics/skills

Até 2025, já há centenas de habilidades contribuídas pela comunidade, cobrindo:

- **Ferramentas de desenvolvimento**: Design frontend, teste de API, revisão de código, workflow Git
- **Análise de dados**: Consulta SQL, visualização de dados, análise estatística
- **Processamento de documentos**: Análise de PDF, geração de Markdown, redação de documentação técnica
- **Processos de negócio**: Gestão de projetos, suporte ao cliente, auditoria de conformidade

Usar habilidades da comunidade é muito simples:

```bash
# Clonar biblioteca oficial de habilidades
git clone https://github.com/anthropics/skills.git

# Copiar habilidade necessária para seu projeto
cp -r skills/frontend-design ./my-project/skills/

# Agente descobrirá e carregará automaticamente
```

Você também pode compartilhar suas próprias habilidades:

```bash
# Publicar no GitHub
cd my-custom-skill
git init
git add SKILL.md
git commit -m "Add custom SQL analysis skill"
git remote add origin https://github.com/yourname/my-skill.git
git push -u origin main

# Outros desenvolvedores podem usar diretamente
# git clone https://github.com/yourname/my-skill.git
```

## Dinâmica da Indústria e Evolução do Ecossistema

### Processo de Padronização e Suporte de Fornecedores

Embora Agent Skills tenha sido proposto pela Anthropic, seu conceito de design está influenciando toda a indústria.

**Anthropic Claude**:
- Claude Desktop e Claude Code suportam Skills nativamente
- Fornecem SDK oficial e ferramentas de desenvolvimento
- Mantêm biblioteca oficial de habilidades

**Resposta da OpenAI**:
Embora a OpenAI ainda não tenha adotado oficialmente o termo "Skills", na atualização de março de 2025, o ChatGPT introduziu conceitos similares:

- **Aprimoramento de Custom Instructions**: Suporta instruções multi-etapas mais complexas
- **Memory e Context Profiles**: Permite usuários salvar e reutilizar conhecimento de domínios específicos
- **Função de "base de conhecimento" dos GPTs**: Pode anexar documentos e scripts, carregar conforme necessário

Essas funcionalidades são essencialmente diferentes formas de implementação do conceito de Skills.

**Google Vertex AI**:
Google introduziu **"Grounding with Functions"** no modelo Gemini, permitindo desenvolvedores definir "Function Packages", cada pacote contém:

- Definições de funções (similar a tools do MCP)
- Guias de uso (similar a instructions do Skills)
- Exemplos (examples)

Este design é altamente similar à arquitetura híbrida Skills + MCP.

### Inevitabilidade da Arquitetura em Camadas

Combinando opiniões de várias partes, acreditamos: **Skills e MCP representam duas camadas inevitavelmente separadas na arquitetura de agentes inteligentes**. À medida que a complexidade dos sistemas de agentes aumenta, esta divisão em camadas é inevitável:

```
Camada de Aplicação (Application Layer)
  ↓ Agent Skills
  ↓ Conhecimento de domínio, workflow, melhores práticas

Camada de Transporte (Transport Layer)
  ↓ MCP
  ↓ Interface padronizada, chamada de ferramentas, acesso a recursos

Camada de Infraestrutura (Infrastructure Layer)
  ↓ Bancos de dados, APIs, sistemas de arquivos, serviços externos
```

Isso é completamente consistente com o caminho de evolução da arquitetura de software tradicional (de monolítico a camadas a microsserviços), apenas se repetindo no campo da IA.

### Tendência de Padronização

Com a atenção da indústria à tecnologia de agentes inteligentes, prevemos as seguintes tendências:

**1. Fusão de Protocolos**

Pode surgir no futuro um protocolo unificado de descrição de capacidades de agentes, fundindo a conectividade do MCP e a expressão de conhecimento do Skills:

```yaml
# Exemplo de protocolo unificado futuro (hipotético)
apiVersion: agent.io/v1
kind: Capability
metadata:
  name: enterprise-data-analysis
spec:
  transport:
    protocol: mcp
    server: database-mcp-server
    tools: [query, schema]
  knowledge:
    type: skill
    workflow: data-analysis-workflow.md
    examples: examples/
```

**2. Mercado e Ecossistema**

Similar ao NPM, PyPI, pode surgir no futuro sistema de gerenciamento de pacotes para capacidades de agentes:

```bash
# Comandos futuros hipotéticos
agent-cli install @anthropic/frontend-design-skill
agent-cli install @google/data-analysis-suite
agent-cli install @openai/code-review-assistant
```

Desenvolvedores podem publicar, compartilhar, vender suas próprias Skills e servidores MCP, formando um ecossistema próspero.

**3. Descoberta Automática de Capacidades**

Agentes podem desenvolver mecanismos de descoberta e aprendizado automático de novas capacidades:

```python
# Agentes futuros podem ter capacidade de auto-aprendizado
agent = SelfEvolvingAgent()

# Agente descobre falta de capacidade ao executar tarefa
response = agent.run("Gerar arquivo de modelagem 3D")

# Agente busca e instala automaticamente Skill relacionada
# [Log interno] Tipo de tarefa desconhecido detectado: modelagem 3D
# [Log interno] Buscando biblioteca de habilidades... encontrado skill "blender-3d-modeling"
# [Log interno] Solicitando autorização do usuário para instalação... autorizado
# [Log interno] Instalação de habilidade concluída, reexecutando tarefa
```

### Desafios e Riscos

Ao mesmo tempo, devemos estar alertas para riscos potenciais:

<strong>Desafios de segurança</strong>:
- Skills contêm scripts executáveis, existe risco de injeção de código
- Servidores MCP podem expor interfaces de dados sensíveis
- Confiabilidade de habilidades de terceiros é difícil de verificar

<strong>Poluição de contexto</strong>:
- À medida que o número de Skills aumenta, mesmo metadados podem ocupar muito contexto
- Necessita de mecanismo mais inteligente de indexação e recuperação de habilidades

<strong>Risco de fragmentação</strong>:
- Embora MCP esteja se padronizando, o formato de Skills ainda não está unificado
- Diferentes fornecedores podem lançar especificações de Skills incompatíveis

## Conclusão

Agent Skills e MCP representam duas camadas de abstração cruciais na pilha tecnológica de agentes inteligentes:

- <strong>MCP (Model Context Protocol)</strong>: Resolve problema de "conectividade", é interface padronizada de interação do agente com o mundo externo, equivalente ao "sistema nervoso" ou "mãos"
- <strong>Agent Skills</strong>: Resolve problema de "capacidade", é encapsulamento de conhecimento de domínio e workflow, equivalente ao "córtex cerebral" ou "manual de operação"

Os dois não são competitivos, mas complementares:

<div align="center">
  <img src="./images/Extra05-figures/image6.png" alt="Resumo comparativo MCP e Agent Skills" width="90%"/>
  <p>Figura 6 Resumo comparativo abrangente de MCP e Agent Skills</p>
</div>


<strong>Insights chave</strong>:

1. <strong>Arquitetura em camadas é tendência inevitável</strong>: À medida que a complexidade dos sistemas de agentes aumenta, a separação entre "camada de conexão" e "camada de conhecimento" é inevitável

2. <strong>Eficiência de contexto é contradição central</strong>: O mecanismo de divulgação progressiva do Skills reduz o consumo de tokens em mais de 90%, esta é sua maior vantagem técnica

3. <strong>Democratização do conhecimento de domínio</strong>: Skills permitem que não desenvolvedores também contribuam capacidades de agentes, isso expandirá enormemente as fronteiras de aplicação de IA

4. <strong>Arquitetura híbrida é melhor prática</strong>: Em aplicações empresariais, MCP fornece conexão de infraestrutura, Skills fornecem lógica de negócio. Combinar os dois é que constrói sistemas de agentes eficientes e manuteníveis

<strong>Recomendações práticas</strong>:

- Para <strong>conexão de serviços externos</strong> (bancos de dados, APIs, serviços em nuvem), priorize usar MCP
- Para <strong>workflows complexos</strong> (tarefas multi-etapas, conhecimento profissional de domínio), priorize usar Skills
- Em cenários <strong>restritos de contexto</strong> (diálogos longos, muitas ferramentas), use Skills para gerenciamento progressivo
- Ao construir <strong>agentes empresariais</strong>, adote arquitetura em camadas MCP + Skills

Através do aprendizado deste capítulo, você deve ser capaz de:
- Compreender diferenças essenciais e relação de colaboração entre Agent Skills e MCP
- Dominar mecanismo de divulgação progressiva do Skills e suas vantagens
- Escrever arquivos SKILL.md de alta qualidade
- Escolher e combinar razoavelmente as duas tecnologias em projetos reais
- Construir sistemas de agentes com camadas claras, eficientes e manuteníveis

A tecnologia de agentes inteligentes ainda está evoluindo rapidamente. MCP já se tornou padrão de fato da camada de conexão, o conceito de Skills também está influenciando toda a indústria. Dominar essas duas tecnologias ajudará você a construir aplicações de agentes mais poderosas e práticas na onda da IA.

---

## Materiais de Referência

1. Documentação oficial do Anthropic Agent Skills: https://docs.anthropic.com/en/docs/agent-skills
2. Repositório GitHub Anthropic Skills: https://github.com/anthropics/skills
3. Especificação do Model Context Protocol: https://modelcontextprotocol.io/
4. Blog da Anthropic: Improving Frontend Design Through Skills: https://www.claude.com/blog/improving-frontend-design-through-skills
5. Capítulo Dez: Protocolos de Comunicação de Agentes Inteligentes (hello-agents)
