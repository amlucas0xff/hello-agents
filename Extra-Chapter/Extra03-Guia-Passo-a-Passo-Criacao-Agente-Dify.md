<div align="right">
  <a href="./Extra03-Dify智能体创建保姆级操作流程.md">中文</a> | Português
</div>

# Guia Prático de Construção de Agente Inteligente Dify:<br>Construindo um Assistente Pessoal Completo do Zero (Tutorial Passo a Passo)

<div align="center">
  <img src="https://github.com/Tasselszcx.png" width="80" height="80" style="border-radius: 50%;" />
  <br />
  <strong>Autor:</strong> <a href="https://github.com/Tasselszcx">Tasselszcx</a>
  <br />
  <em>Tutorial Original | Guia Passo a Passo | Prática Completa</em>
</div>

## 1. Instalação de Plugins Necessários

Antes de construir o agente inteligente, é necessário concluir a instalação dos plugins necessários e a configuração do MCP. Conforme mostrado na Figura 1, siga as instruções de texto na imagem passo a passo para instalar os plugins necessários para este capítulo.

<div align="center">
  <img src="./images/Extra03-figures/image1.jpg" alt="Diagrama de instalação de plugins" width="90%"/>
  <p>Figura 1 Diagrama de instalação de plugins</p>
</div>

## 2. Configuração do MCP (Model Context Protocol)

Não vamos nos aprofundar nos princípios detalhados do MCP aqui, vamos focar em demonstrar como usar o serviço MCP implantado na nuvem. Este caso usa o mercado MCP da comunidade ModelScope na China para demonstração. As etapas específicas são as seguintes:

<strong>(1) Acesse a Comunidade ModelScope</strong>: [https://www.modelscope.cn/home](https://www.modelscope.cn/home)

<strong>(2) Registre uma conta e faça login</strong>, conforme mostrado na Figura 2

<div align="center">
  <img src="./images/Extra03-figures/image2.jpg" alt="Interface de registro e login do ModelScope" width="90%"/>
  <p>Figura 2 Interface de registro e login do ModelScope</p>
</div>

<strong>(3) Acesse a página de configuração MCP do Mapa Gaode</strong>
   - Após o login, siga a Figura 3 passo a passo para acessar a página de configuração MCP do Mapa Gaode
   - A página deve aparecer conforme a Figura 4

<div align="center">
  <img src="./images/Extra03-figures/image3.jpg" alt="Guia de entrada do MCP do Mapa Gaode" width="90%"/>
  <p>Figura 3 Guia de entrada do MCP do Mapa Gaode</p>
</div>

<div align="center">
  <img src="./images/Extra03-figures/image4.jpg" alt="Página de configuração MCP do Mapa Gaode" width="90%"/>
  <p>Figura 4 Página de configuração MCP do Mapa Gaode</p>
</div>

<strong>(4) Acesse a Plataforma Aberta Gaode</strong>: [https://console.amap.com/dev/index](https://console.amap.com/dev/index)
   - Siga as instruções de texto na Figura 5 para criar uma nova aplicação

<div align="center">
  <img src="./images/Extra03-figures/image5.jpg" alt="Criação de nova aplicação na Plataforma Aberta Gaode" width="90%"/>
  <p>Figura 5 Criação de nova aplicação na Plataforma Aberta Gaode</p>
</div>

<strong>(5) Criar api_key</strong>
   - Conforme mostrado na Figura 6, crie o api_key passo a passo
   - Insira o api_key criado na caixa vermelha da Figura 4 para exibir a configuração bem-sucedida
   - A página de configuração bem-sucedida aparece conforme a Figura 7

<div align="center">
  <img src="./images/Extra03-figures/image6.jpg" alt="Etapas para criar api_key" width="90%"/>
  <p>Figura 6 Etapas para criar api_key</p>
</div>

<div align="center">
  <img src="./images/Extra03-figures/image7.jpg" alt="Página de configuração MCP bem-sucedida" width="90%"/>
  <p>Figura 7 Página de configuração MCP bem-sucedida</p>
</div>

<strong>Até aqui, toda a configuração MCP do Mapa Gaode está completa!</strong>

## 3. Design do Agente e Demonstração de Resultados

Este caso criará um assistente pessoal abrangente, cobrindo os seguintes módulos de funcionalidade:

- Perguntas e respostas do dia a dia
- Otimização e aprimoramento de textos
- Geração de conteúdo multimodal (imagens, vídeos)
- Integração de ferramentas MCP (Mapa Gaode, recomendações alimentares, notícias)
- Consulta de dados e análise com visualização

A arquitetura de orquestração completa do agente inteligente é mostrada na Figura 8.

<div align="center">
  <img src="./images/Extra03-figures/image8.jpg" alt="Diagrama de arquitetura de orquestração do agente inteligente" width="90%"/>
  <p>Figura 8 Diagrama de arquitetura de orquestração do agente inteligente</p>
</div>

A seguir, apresentamos como construir um Chatflow de agente inteligente como este:

### (1) Criar Aplicação Chatflow em Branco
- Siga as Figuras 9 e 10 passo a passo para criar uma aplicação Chatflow em branco

<div align="center">
  <img src="./images/Extra03-figures/image9.jpg" alt="Etapa 1 de criação de Chatflow" width="90%"/>
  <p>Figura 9 Etapa 1 de criação de Chatflow</p>
</div>

<div align="center">
  <img src="./images/Extra03-figures/image10.jpg" alt="Etapa 2 de criação de Chatflow" width="90%"/>
  <p>Figura 10 Etapa 2 de criação de Chatflow</p>
</div>

### (2) Criar Classificador de Perguntas
- Primeiro, crie um classificador de perguntas para classificar as perguntas de entrada
- O conteúdo preenchido no classificador é mostrado na Figura 11

<div align="center">
  <img src="./images/Extra03-figures/image11.jpg" alt="Configuração do classificador de perguntas" width="80%"/>
  <p>Figura 11 Configuração do classificador de perguntas</p>
</div>

### (3) Implementação do Módulo de Assistente Diário

Este é um módulo de diálogo básico que configura o modelo de linguagem grande e ferramentas de tempo, servindo como um serviço de perguntas e respostas geral de fallback.

<strong>Descrição da Configuração</strong>:
- Consulte a Figura 12 para instruções de configuração e conexões
- Os nós específicos no flow são "Início-Classificador de Perguntas-LLM-Resposta Direta"
- <strong>A seguir, usaremos diretamente o flow de nós para explicar o flow de cada módulo</strong>

<div align="center">
  <img src="./images/Extra03-figures/image12.jpg" alt="Configuração do módulo de assistente diário" width="90%"/>
  <p>Figura 12 Configuração do módulo de assistente diário</p>
</div>

<strong>O system_prompt do nó LLM é o seguinte</strong>:
```
# Papel: Especialista em Consultas sobre Questões Cotidianas

## Perfil
- idioma: Chinês
- descrição: Especializado em responder perguntas gerais do dia a dia dos usuários, fornecendo conselhos e respostas práticas, precisas e compreensíveis
- background: Possui rica experiência de vida e amplo conhecimento, hábil em simplificar questões complexas
- personalidade: Amigável, paciente e atencioso, prático e confiável
- expertise: Vida diária, saúde e bem-estar, gestão doméstica, relações interpessoais, dicas práticas


## Habilidades

1. Capacidade de análise de problemas
   - Compreensão rápida: Captar rapidamente os pontos centrais das perguntas dos usuários
   - Identificação de categorias: Julgar com precisão a área da vida a que a pergunta pertence
   - Descoberta de necessidades: Compreender profundamente as necessidades potenciais dos usuários
   - Priorização: Avaliar razoavelmente a importância e urgência das questões

2. Capacidade de fornecer respostas
   - Integração de conhecimento: Aplicar conhecimento de múltiplas áreas de forma abrangente para fornecer respostas
   - Formulação de soluções: Fornecer soluções específicas e viáveis
   - Decomposição de etapas: Dividir questões complexas em etapas simples
   - Soluções alternativas: Preparar várias opções alternativas para escolha do usuário

3. Capacidade de comunicação e expressão
   - Linguagem popular: Usar linguagem cotidiana simples e compreensível
   - Lógica clara: Organizar o conteúdo da resposta de forma clara e ordenada
   - Exemplificação: Ajudar a compreensão através de casos concretos
   - Destaque de pontos-chave: Enfatizar informações-chave e precauções

## Regras

1. Princípios de resposta:
   - Prioridade à praticidade: Garantir que os conselhos fornecidos sejam operacionais
   - Garantia de precisão: Fornecer respostas baseadas em informações confiáveis e senso comum
   - Neutralidade objetiva: Evitar preconceitos pessoais e especulações subjetivas
   - Conselhos moderados: Fornecer respostas de profundidade apropriada de acordo com a complexidade da questão

2. Código de conduta:
   - Resposta oportuna: Responder rapidamente às perguntas dos usuários
   - Paciência e atenção: Manter paciência com perguntas repetidas ou simples
   - Orientação ativa: Incentivar usuários a fornecer mais informações de contexto
   - Melhoria contínua: Otimizar a qualidade das respostas com base no feedback


## Fluxos de Trabalho

- Objetivo: Fornecer soluções práticas e confiáveis para questões cotidianas dos usuários
- Etapa 1: Ler e compreender cuidadosamente as questões cotidianas apresentadas pelo usuário
- Etapa 2: Analisar o tipo de problema e as necessidades potenciais do usuário
- Etapa 3: Fornecer conselhos específicos e viáveis baseados em senso comum e experiência
- Etapa 4: Organizar o conteúdo da resposta em linguagem popular e compreensível
- Etapa 5: Verificar a praticidade e segurança da resposta


## Inicialização
Como especialista em consultas sobre questões cotidianas, você deve seguir as Regras acima e executar tarefas de acordo com os Fluxos de Trabalho.
```

<strong>Efeito de demonstração</strong>:
Conforme mostrado na Figura 13:

<div align="center">
  <img src="./images/Extra03-figures/image13.png" alt="Efeito de demonstração do assistente diário" width="80%"/>
  <p>Figura 13 Efeito de demonstração do assistente diário</p>
</div>

### (4) Implementação do Módulo de Otimização de Texto

De acordo com o relatório de dados da OpenAI, mais de 60% dos usuários usam o ChatGPT para tarefas relacionadas à otimização de texto, incluindo refinamento, modificação, expansão, resumo, etc. Portanto, a otimização de texto é um cenário de alta demanda, e vamos torná-la nosso segundo módulo de funcionalidade principal.

<strong>Configuração Específica</strong>:
- Os nós específicos no flow são "Início-Classificador de Perguntas-LLM-Resposta Direta", igual a (3)

<strong>O system_prompt do nó LLM é o seguinte</strong>:
```
# I. Papel

Você é um especialista profissional em otimização de textos, com rica experiência em redação e otimização de textos de marketing, especializado em melhorar a atratividade, taxa de conversão e legibilidade dos textos. Sua perspectiva é do ponto de vista do público-alvo e dos objetivos de marketing, com expertise limitada ao campo de otimização de textos, sem envolver implementação técnica ou desenvolvimento de produtos.

# II. Contexto

O usuário forneceu um texto original que precisa ser otimizado para melhorar seu efeito geral. As informações de contexto incluem: o texto pode ser usado para marketing, promoção de marca ou transmissão de informações, mas o uso específico não foi detalhado. A condição conhecida é que o usuário deseja que o texto seja mais atraente, claro ou persuasivo, mas não forneceu o conteúdo do texto original, portanto você precisa trabalhar com base em princípios de otimização universais.

# III. Objetivos da Tarefa

- Analisar e otimizar a estrutura, linguagem e estilo do texto para torná-lo mais adequado às preferências do público-alvo.
- Melhorar a atratividade, legibilidade e potencial de conversão do texto, garantindo transmissão clara da informação.
- Fazer ajustes de acordo com princípios comuns de otimização (como concisão, ressonância emocional, chamadas à ação, etc.), sem reescrever o conteúdo, a menos que necessário.
- Expandir e enriquecer adequadamente o conteúdo do texto mantendo a informação central, fornecendo uma versão otimizada mais abrangente.

# IV. Restrições

- Evite alterar a informação central ou intenção do texto original, a menos que o usuário solicite explicitamente.
- Não adicione conteúdo fictício ou irrelevante, garantindo que a otimização seja baseada em lógica e melhores práticas.
- Evite usar terminologia excessivamente técnica ou especializada, a menos que o público-alvo seja profissional.
- Não envolva otimização de imagens, layout ou outros elementos não textuais.

# V. Requisitos de Formato de Saída

A saída deve ser o texto otimizado, com estrutura clara, linguagem fluente e conteúdo detalhado. Por exemplo:
- Se o texto original for "Nosso produto é muito bom, venha comprar"
A otimização pode ser: "Nesta era cheia de escolhas, o que realmente toca os corações nunca é propaganda exagerada, mas bons produtos que resistem ao teste do tempo e dos usuários. Nosso produto é exatamente assim. Ele não apenas presta atenção aos detalhes e qualidade no design, mas também continua a refinar e inovar em funcionalidade, apenas para trazer uma melhor experiência de uso a cada usuário. Seja a textura da aparência ou a estabilidade do desempenho, sempre insistimos em altos padrões e requisitos rigorosos, esforçando-nos para fazer com que cada cliente que nos escolhe sinta a surpresa de um valor excepcional.
Sabemos que comprar um produto não é apenas um simples consumo, mas uma escolha de estilo de vida. Portanto, de cada etapa de seleção de materiais, artesanato ao serviço pós-venda, dedicamos sinceridade e profissionalismo, cuidando de cada uma de suas experiências. Se você busca praticidade, valoriza qualidade ou quer personalização diferenciada, nosso produto pode fornecer a solução ideal.
Agora, vamos provar tudo com ação. Produtos realmente bons não precisam de muitos adornos, eles são os melhores porta-vozes de si mesmos. Aja imediatamente, escolha-nos, deixe a qualidade mudar a vida e tenha uma experiência diferenciada a partir de agora!"
- A saída deve apresentar diretamente o conteúdo otimizado, sem explicações ou anotações adicionais, a menos que o usuário solicite. Certifique-se de que o conteúdo do texto otimizado seja mais rico e completo, o texto otimizado deve exceder 500 palavras.
```


<strong>Efeito de demonstração</strong>:
Conforme mostrado na Figura 14:

<div align="center">
  <img src="./images/Extra03-figures/image14.png" alt="Efeito de demonstração da otimização de texto" width="80%"/>
  <p>Figura 14 Efeito de demonstração da otimização de texto</p>
</div>

### (5) Módulo de Geração Multimodal (Imagens, Vídeos)

A geração de imagens e vídeos é outro cenário de aplicação de alta frequência. Com a evolução de modelos como Doubao (geração de imagens), Google Imagen, e avanços em tecnologias de geração de vídeo como Keling, Google Veo 3 e OpenAI Sora 2, a qualidade da geração de conteúdo multimodal atingiu um nível prático.

<strong>Configuração de Geração de Imagens</strong>:
- Este caso usa o plugin Doubao para implementar a geração de imagens e vídeos
- Para obter permissões de geração de imagens e vídeos do plugin Doubao e api_key, consulte este blog, que explica extremamente claramente. Recomenda-se ver diretamente as partes 3 e 4 do blog:
  [https://blog.csdn.net/sjkflw121150/article/details/148480867#:~:text=3.-,%E8%B0%83%E7%94%A8Doubao%E6%96%87%E7%94%9F%E5%9B%BE%E5%B7%A5%E5%85%B7,-%E8%B0%83%E7%94%A8%20Doubao](https://blog.csdn.net/sjkflw121150/article/details/148480867#:~:text=3.-,%E8%B0%83%E7%94%A8Doubao%E6%96%87%E7%94%9F%E5%9B%BE%E5%B7%A5%E5%85%B7,-%E8%B0%83%E7%94%A8%20Doubao)
- Consulte a Figura 15 para criar o flow desta parte de geração de imagens Doubao
- Os nós no flow são "Início-Classificador de Perguntas-Doubao T2I-Resposta Direta"

<div align="center">
  <img src="./images/Extra03-figures/image15.jpg" alt="Configuração do flow de geração de imagens Doubao" width="90%"/>
  <p>Figura 15 Configuração do flow de geração de imagens Doubao</p>
</div>

<strong>Efeito de geração de imagens</strong>:
Conforme mostrado na Figura 16:

<div align="center">
  <img src="./images/Extra03-figures/image16.png" alt="Demonstração de efeito de geração de imagens Doubao" width="80%"/>
  <p>Figura 16 Demonstração de efeito de geração de imagens Doubao</p>
</div>

<strong>Configuração de Geração de Vídeo</strong>:
- A geração de vídeo é semelhante à geração de imagens, basta ativar as permissões de texto para vídeo no Volcano Engine, veja as instruções da Figura 17
- Os nós no flow de texto para vídeo são "Início-Classificador de Perguntas-Doubao T2V-Resposta Direta"

<div align="center">
  <img src="./images/Extra03-figures/image17.jpg" alt="Ativação de permissões de texto para vídeo" width="90%"/>
  <p>Figura 17 Ativação de permissões de texto para vídeo</p>
</div>

<strong>Efeito de geração de vídeo</strong>:
Conforme mostrado na Figura 18:

<div align="center">
  <img src="./images/Extra03-figures/image18.png" alt="Demonstração de efeito de geração de vídeo Doubao" width="80%"/>
  <p>Figura 18 Demonstração de efeito de geração de vídeo Doubao</p>
</div>

### (6) Integração de Ferramentas MCP (Mapa Gaode, Recomendações Alimentares, Notícias)

Anteriormente, já concluímos a configuração do MCP. Agora vamos integrá-lo ao agente inteligente.

<strong>Etapas de Configuração</strong> (consulte a Figura 19):

1. Selecionar o nó Agent que suporta chamadas MCP
2. Selecionar o modo ReAct
3. Adicionar a ferramenta "Obter timestamp"
4. Configurar o serviço MCP (encontre a Figura 7, selecione o modo SSE, copie as outras informações após deletar o prefixo mcp-server)
5. Preencher o prompt correspondente

<div align="center">
  <img src="./images/Extra03-figures/image19.jpg" alt="Etapas de configuração de integração de ferramentas MCP" width="90%"/>
  <p>Figura 19 Etapas de configuração de integração de ferramentas MCP</p>
</div>

<strong>Configuração Específica</strong>:
- As informações finais de preenchimento do nó Agent podem ser consultadas na Figura 20
- Os nós no flow de chamada de serviço MCP são "Início-Classificador de Perguntas-Agent-Resposta Direta"

<div align="center">
  <img src="./images/Extra03-figures/image20.jpg" alt="Detalhes de configuração do nó Agent" width="50%"/>
  <p>Figura 20 Detalhes de configuração do nó Agent</p>
</div>

<strong>Demonstração de Efeitos</strong>:
- Efeito do assistente Gaode: Conforme mostrado na Figura 21

<div align="center">
  <img src="./images/Extra03-figures/image21.png" alt="Demonstração de efeito do assistente Gaode" width="80%"/>
  <p>Figura 21 Demonstração de efeito do assistente Gaode</p>
</div>

- Efeito do assistente alimentar: Conforme mostrado na Figura 22

<div align="center">
  <img src="./images/Extra03-figures/image22.png" alt="Demonstração de efeito do assistente alimentar" width="80%"/>
  <p>Figura 22 Demonstração de efeito do assistente alimentar</p>
</div>

- Efeito do assistente de notícias: Conforme mostrado na Figura 23

<div align="center">
  <img src="./images/Extra03-figures/image23.png" alt="Demonstração de efeito do assistente de notícias" width="50%"/>
  <p>Figura 23 Demonstração de efeito do assistente de notícias</p>
</div>

### (7) Módulo de Consulta e Análise de Dados

<strong>Módulo de Consulta e Análise de Dados</strong>

O processamento de dados é uma das capacidades importantes do agente inteligente. Este módulo demonstra como conectar um banco de dados no Dify para realizar consultas de dados e análise com visualização.

Primeiro, instale o plugin de ferramenta de consulta de dados. Este caso usa o plugin `rookie-text2data`. A chave para consulta de dados é fornecer ao modelo grande uma estrutura de tabela e informações de campo claras, permitindo que ele gere declarações de consulta SQL precisas. Práticas comuns incluem:

- Fornecer diretamente declarações DDL da tabela de dados
- Fornecer explicação da correspondência entre nomes de tabelas e campos

Configure as informações de conexão do banco de dados (endereço IP, nome do banco de dados, porta, conta, senha, etc.), conforme mostrado na Figura 24. Os resultados da consulta precisam ser organizados através do nó do modelo grande, convertidos em saída de linguagem natural fácil de entender.

<div align="center">
  <img src="./images/Extra03-figures/image24.png" alt="Configuração do banco de dados" width="50%"/>
  <p>Figura 24 Configuração do banco de dados</p>
</div>

<strong>Configuração de Prompt:</strong>
```
# I. Papel

Você é um consultor de dados profissional, especializado em organização de dados, com pensamento lógico claro e capacidade de expressão concisa.

# II. Contexto

O usuário forneceu dados brutos consultados do banco de dados. Esses dados podem ter formatos inconsistentes, campos ausentes, registros duplicados e outros problemas, que precisam ser organizados profissionalmente antes de serem exibidos efetivamente.

# III. Objetivos da Tarefa

1. Organizar e resumir os dados brutos
2. Classificar e ordenar os dados de acordo com a lógica correta
3. A exibição de dados deve destacar informações-chave e insights de dados
4. Fornecer exibição de dados fácil de entender

# IV. Restrições

1. Não deve deletar dados importantes arbitrariamente
2. Evite usar terminologia estatística excessivamente complexa ou profissional
3. Não deve alterar os valores reais dos dados originais
4. Evite exibir muita informação redundante, mantenha conciso e claro
5. Não deve vazar dados sensíveis ou informações de privacidade pessoal

# V. Requisitos de Formato de Saída

Visão geral dos dados: Basta explicar brevemente o conteúdo dos dados
```

Demonstração de efeito conforme mostrado na Figura 25:

<div align="center">
  <img src="./images/Extra03-figures/image25.png" alt="Efeito do assistente de consulta de dados" width="80%"/>
  <p>Figura 25 Assistente de consulta de dados</p>
</div>

<strong>Configuração de Prompt:</strong>
```
# I. Papel

Você é um analista de dados profissional com capacidades de organização, limpeza e visualização de dados, capaz de extrair informações-chave de dados brutos e transformá-las em exibição visual intuitiva.

# II. Contexto

O usuário já consultou um lote de dados brutos do banco de dados. Esses dados podem conter múltiplos campos, valores ausentes ou formatos inconsistentes, que precisam ser organizados antes de gerar gráficos de visualização.

# III. Objetivos da Tarefa

# Fluxo de trabalho
1. Análise de dados
Analisar, organizar e resumir os dados de acordo com regras razoáveis
2. Análise e Visualização
Gerar pelo menos 1 gráfico (gráfico de barras/linha/pizza, escolha 1 ou mais)
Ferramentas disponíveis: "generate_pie_chart" | "generate_column_chart" | "generate_line_chart"

# IV. Restrições

1. Evite usar tipos de gráficos excessivamente complexos, garanta que os resultados de visualização sejam fáceis de entender
2. Não ignore problemas de qualidade de dados, deve realizar limpeza de dados necessária
3. Evite usar muitas cores ou elementos na visualização, mantenha simples e claro
4. Não omita anotações e explicações de dados-chave
5. Deve realizar resumo e geração de gráficos, independentemente da quantidade de dados

# V. Requisitos de Formato de Saída

Por favor, siga o seguinte formato de saída:
1. Resumo da visão geral dos dados (não exiba nomes de campos, não divida em pontos, apenas um pequeno parágrafo)
2. Exibir os gráficos gerados
```

<div align="center">
  <img src="./images/Extra03-figures/image26.png" alt="Efeito do assistente de análise de dados" width="80%"/>
  <p>Figura 26 Assistente de análise de dados</p>
</div>

A única diferença nesta parte do assistente de análise de dados é que adicionamos ferramentas de visualização de dados, ou seja, os plugins de ferramentas de geração de gráficos BI como "generate_pie_chart" | "generate_column_chart" | "generate_line_chart". Acredito que todos já instalaram conforme os requisitos anteriores e podem adicionar e usar diretamente, e adicionar descrições correspondentes como no prompt acima. Todos podem tentar conectar com SQL posteriormente, não vamos nos aprofundar aqui.

---

<strong>Até aqui, completamos um super agente inteligente de assistente pessoal com funcionalidades abrangentes.</strong>

Este assistente cobre vários aspectos da vida:
- Quando precisar de roupas novas, pode deixar o Doubao gerar designs
- Antes de sair, pode deixar o assistente Gaode planejar a rota
- Quando não souber o que comer, pode obter recomendações alimentares
- Quando quiser entender a situação de estudos, pode realizar análise de dados

<strong>Este agente inteligente pode lidar com várias tarefas de trabalho e vida. Esperamos ver todos construindo mais assistentes inteligentes pessoais criativos.</strong>

## Referências
1. Comunidade ModelScope. https://www.modelscope.cn/home

2. Plataforma Aberta Gaode. https://console.amap.com/dev/index

3. sjkflw121150. Armadilhas na construção de assistente de geração de imagens AI com Dify!. Blog CSDN. https://blog.csdn.net/sjkflw121150/article/details/148480867#:~:text=3.-,%E8%B0%83%E7%94%A8Doubao%E6%96%87%E7%94%9F%E5%9B%BE%E5%B7%A5%E5%85%B7,-%E8%B0%83%E7%94%A8%20Doubao
