# Capítulo 5: Construindo Agentes com Plataformas Low-Code

<div align="right">
  <a href="./Chapter5-Building-Agents-with-Low-Code-Platforms.md">English</a> | <a href="./第五章%20基于低代码平台的智能体搭建.md">中文</a> | Português
</div>

No capítulo anterior, escrevendo código Python, implementamos diversos fluxos de trabalho clássicos de agentes do zero, incluindo ReAct, Plan-and-Solve e Reflection. Este processo estabeleceu uma base técnica sólida para nós e nos deu um profundo entendimento dos mecanismos internos dos agentes. No entanto, para um campo em rápido desenvolvimento, o desenvolvimento puramente em código nem sempre é a escolha mais eficiente, especialmente em cenários onde as ideias precisam ser rapidamente validadas ou onde desenvolvedores não profissionais querem participar da construção.

## 5.1 A Ascensão da Construção Baseada em Plataformas

À medida que a tecnologia amadurece, vemos cada vez mais capacidades sendo "plataformizadas". Assim como o desenvolvimento de sites evoluiu de escrever manualmente HTML/CSS/JS para usar plataformas de construção de sites como WordPress e Wix, a construção de agentes também inaugurou uma onda de plataformização. Este capítulo se concentrará em como usar plataformas low-code gráficas e modulares para rapidamente e intuitivamente construir, depurar e implantar aplicações de agentes, mudando nosso foco de "detalhes de implementação" para "lógica de negócios".

### 5.1.1 Por Que Plataformas Low-Code São Necessárias

"Reinventar a roda" é crucial para o aprendizado profundo, mas no trabalho prático que busca eficiência de engenharia e inovação, frequentemente precisamos nos apoiar nos ombros de gigantes. Embora tenhamos encapsulado classes reutilizáveis como `ReActAgent` e `PlanAndSolveAgent` no Capítulo 4, quando a lógica de negócios se torna complexa, o custo de manutenção e o ciclo de desenvolvimento do código puro aumentarão drasticamente. O surgimento de plataformas low-code é precisamente para resolver esses pontos problemáticos.

Seu valor central é refletido principalmente nos seguintes aspectos:

1. **Reduzindo Barreiras Técnicas**: Plataformas low-code encapsulam detalhes técnicos complexos (como chamadas de API, gerenciamento de estado, controle de concorrência) em "nós" ou "módulos" fáceis de entender. Os usuários não precisam ser proficientes em programação; eles só precisam arrastar e conectar esses nós para construir fluxos de trabalho poderosos. Isso permite que pessoal não técnico, como gerentes de produto, designers e especialistas em negócios, participem do design e criação de agentes, expandindo enormemente as fronteiras da inovação.

2. **Melhorando a Eficiência de Desenvolvimento**: Para desenvolvedores profissionais, as plataformas também podem trazer enormes melhorias de eficiência. Nos estágios iniciais de um projeto, quando uma ideia precisa ser rapidamente validada ou um protótipo precisa ser construído, usar uma plataforma low-code pode completar trabalho que originalmente levaria dias de codificação em horas ou até minutos. Os desenvolvedores podem investir mais energia na organização da lógica de negócios e otimização de engenharia de prompt, em vez de implementação de engenharia de baixo nível.

3. **Fornecendo Melhor Visualização e Observabilidade**: Comparado a imprimir logs no terminal, plataformas gráficas naturalmente fornecem visualização de ponta a ponta das trajetórias de execução do agente. Você pode ver claramente como os dados fluem entre cada nó, qual link leva mais tempo e qual chamada de ferramenta falha. Esta experiência intuitiva de depuração é incomparável ao desenvolvimento puramente em código.

4. **Padronização e Acumulação de Melhores Práticas**: Excelentes plataformas low-code geralmente têm muitas melhores práticas da indústria embutidas. Por exemplo, elas fornecem templates ReAct predefinidos, mecanismos otimizados de recuperação de base de conhecimento, especificações padronizadas de integração de ferramentas, etc. Isso não só evita que os desenvolvedores "pisem em minas" como também torna a colaboração em equipe mais fluida porque todos desenvolvem com base no mesmo conjunto de padrões e componentes.

Em resumo, plataformas low-code não são destinadas a substituir código, mas fornecer um nível mais alto de abstração. Elas nos permitem nos libertar da implementação tediosa de baixo nível e focar mais na lógica do "pensamento" e "ação" do agente em si, transformando assim ideias em realidade mais rápida e melhor.

### 5.1.2 Escolhendo uma Plataforma Low-Code

Atualmente, o mercado de plataformas low-code para agentes e aplicações LLM apresenta uma situação florescente, com cada plataforma tendo seu posicionamento e vantagens únicos. Qual plataforma escolher frequentemente depende de suas necessidades principais, formação técnica e objetivo final do projeto. No conteúdo subsequente deste capítulo, vamos nos concentrar em introduzir e praticar três plataformas representativas: Coze, Dify e n8n. Antes disso, vamos dar uma breve introdução a elas.

**Coze**

- **Posicionamento Central**: Lançado pela ByteDance, Coze<sup>[1]</sup> foca em experiência de construção de Agentes zero-código/low-code, permitindo que usuários sem formação em programação criem facilmente.
- **Análise de Recursos**: Coze tem uma interface visual extremamente amigável. Os usuários podem criar agentes arrastando e soltando plugins, configurando bases de conhecimento e definindo fluxos de trabalho, assim como construir blocos LEGO. Tem uma biblioteca de plugins muito rica embutida e suporta publicação com um clique em plataformas mainstream como Douyin, Feishu e WeChat Official Accounts, simplificando enormemente o processo de distribuição.
- **Público-Alvo**: Usuários de nível iniciante de aplicações de IA, gerentes de produto, pessoal de operações e criadores individuais que querem rapidamente transformar ideias em produtos interativos.

**Dify**

- **Posicionamento Central**: Dify é uma plataforma de desenvolvimento e operação de aplicações LLM de código aberto e completa<sup>[2]</sup>, visando fornecer aos desenvolvedores uma solução completa desde construção de protótipo até implantação em produção.
- **Análise de Recursos**: Integra os conceitos de serviços backend e operações de modelo, suportando múltiplas capacidades como fluxos de trabalho de Agentes, RAG Pipeline, anotação de dados e fine-tuning. Para aplicações de nível empresarial que buscam profissionalismo, estabilidade e escalabilidade, Dify fornece uma base sólida.
- **Público-Alvo**: Desenvolvedores com alguma formação técnica, equipes que precisam construir aplicações de IA escaláveis de nível empresarial.

**n8n**

- **Posicionamento Central**: n8n é essencialmente uma ferramenta de automação de workflow de código aberto<sup>[3]</sup>, não uma plataforma LLM pura. Nos últimos anos, tem integrado ativamente capacidades de IA.

- **Análise de Recursos**: A força do n8n está na "conexão". Tem centenas de nós predefinidos que podem facilmente conectar vários serviços SaaS, bancos de dados e APIs em processos de negócios automatizados complexos. Você pode incorporar nós LLM neste processo, tornando-o parte de toda a cadeia de automação. Embora não seja tão especializado em funcionalidade LLM quanto os dois primeiros, sua capacidade de automação geral é única. No entanto, sua curva de aprendizado também é relativamente íngreme.

- **Público-Alvo**: Desenvolvedores e empresas que precisam integrar profundamente capacidades de IA em processos de negócios existentes e alcançar automação altamente customizada.

Nas subseções seguintes, vamos ter experiência prática com essas plataformas uma por uma, e sentir mais intuitivamente seus respectivos encantos através de operações reais.

## 5.2 Plataforma Um: Coze

Coze é uma ferramenta super legal de criação de agentes de IA! É também atualmente a plataforma de agentes mais amplamente usada no mercado. Com sua interface visual intuitiva e módulos funcionais ricos, a plataforma permite que usuários criem facilmente vários tipos de aplicações de agentes, como chatbots que podem conversar com você, máquinas criativas que escrevem histórias automaticamente e até mesmo ajudam diretamente a transformar histórias em MVs de filmes! Um de seus destaques é sua poderosa capacidade de integração de ecossistema. Agentes desenvolvidos podem ser publicados em plataformas mainstream como WeChat, Feishu e Doubao com um clique, alcançando implantação cross-platform sem costura. Para usuários empresariais, Coze também fornece interfaces API flexíveis, suportando a integração de capacidades de agentes em sistemas de negócios existentes, alcançando construção de aplicações de IA "estilo blocos de construção".

### 5.2.1 Módulos Funcionais do Coze

(1) Visão Geral da Interface da Plataforma

Introdução geral ao layout: Recentemente, Coze atualizou sua interface UI novamente, como mostrado na Figura 5.1. Agora a barra lateral mais à esquerda é o espaço de trabalho de desenvolvimento da página inicial da plataforma Coze, incluindo desenvolvimento de projeto central, biblioteca de recursos, avaliação de efeitos e configuração de espaço. A área abaixo é o espaço de material de suporte para desenvolvimento Coze, incluindo templates oficiais para cópia com um clique, a maior vantagem do Coze - uma loja de plugins rica e diversa, a maior comunidade de agentes com uma variedade deslumbrante, gerenciamento de API para teste de API, bem como documentação detalhada de tutoriais e gerenciamento geral para empresas. No lado direito, há quatro templates. No topo está o anúncio de atualização mais recente do Coze, informando sobre o progresso mais recente do Coze para que você possa aprender sobre as ferramentas e recursos mais recentes. Abaixo está o tutorial para iniciantes. Clique nele e você encontrará a documentação do tutorial para iniciantes, e você pode começar a construir agentes em minutos. A seguir estão seus seguidores e recomendações de agentes. Aqui você também pode seguir seus desenvolvedores de IA favoritos e marcar seus agentes para seu próprio uso.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-01.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.1 Esquema Geral da Plataforma Coze Agent</p>
</div>

(2) Introdução às Funções Principais

Primeiro, clicamos no sinal de mais na barra lateral esquerda para ver o ponto de entrada para criar agentes. Atualmente, existem dois tipos de aplicações de IA: um é criar agentes, e o outro é chamado de aplicações. Entre eles, os agentes são divididos em modo de planejamento autônomo de agente único, modo de fluxo de diálogo de agente único e modo multi-agente. Aplicações de IA também são divididas em dois tipos: não só você pode projetar interfaces de usuário para desktop e web, mas também pode facilmente construir interfaces para mini-programas e H5, como mostrado na Figura 5.2.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-02.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.2 Entrada de Criação de Agente Coze</p>
</div>

O espaço de projeto é seu repositório de agentes, onde todos os agentes ou aplicações que você desenvolveu ou copiou são armazenados. É também o lugar que você visitará com mais frequência ao desenvolver agentes no Coze, como mostrado na Figura 5.3.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-03.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.3 Espaço de Projeto do Agente Coze</p>
</div>

A biblioteca de recursos é seu arsenal central para desenvolver agentes Coze. A biblioteca de recursos armazena seus fluxos de trabalho, bases de conhecimento, cartões, bibliotecas de prompts e uma série de outras ferramentas para desenvolver agentes. Que tipo de agente você pode fazer depende primeiro das capacidades do modelo, mas o mais importante, depende de como você equipa o agente com "equipamento e habilidades". O modelo determina o limite inferior do agente, mas a biblioteca de recursos Coze lhe dá limites superiores infinitos para as capacidades do agente, permitindo que você desenvolva de acordo com suas próprias ideias, imaginação e criatividade, como mostrado na Figura 5.4.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-04.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.4 Biblioteca de Recursos do Agente Coze</p>
</div>

A configuração de espaço inclui um canal de gerenciamento unificado para agentes, plugins, fluxos de trabalho e canais de publicação, bem como gerenciamento de modelo onde você pode ver os vários modelos grandes que você chama, como mostrado na Figura 5.5.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-05.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.5 Canais de Publicação do Agente Coze</p>
</div>

Se eu fosse fazer um resumo simples do desenvolvimento de agentes do Coze, eu o compararia aos vários componentes de um jogo. A combinação de cada parte para criar agentes maravilhosos é muito parecido com jogar um "jogo". Cada vez que você completa um agente, é como derrotar um chefe e ganhar muito, seja "experiência" ou "equipamento".

- Workflow: Mapa de rota de liberação de nível
- Fluxo de Diálogo: Liberação de diálogo NPC
- Plugins: Cartas de habilidade de personagem
- Base de conhecimento: Enciclopédia do jogo
- Cartões: Barra de itens rápidos
- Prompts: Teclas de movimento de personagem
- Banco de dados: "Salvamento na nuvem"
- Gerenciamento de publicação: Revisor de nível
- Gerenciamento de modelo: Biblioteca de personagens do jogo ou sistema de criação de personagem
- Avaliação de efeito: Sistema de pontuação de nível

### 5.2.2 Construindo um Assistente "Resumo Diário de IA"

**Descrição do Caso:** Este caso prático visa analisar profundamente as capacidades de integração de plugins da plataforma Coze e guiar os leitores a construir um poderoso agente "Resumo Diário de IA" do zero. Este agente pode capturar automaticamente as principais manchetes mais recentes do campo de IA, artigos acadêmicos e atualizações de projetos de código aberto de múltiplas fontes de informação (incluindo 36Kr, Huxiu, IT Home, InfoQ, GitHub, arXiv) e integrá-los em um resumo vívido e conciso de forma estruturada e profissional.

Através deste caso, você dominará sistematicamente as seguintes habilidades principais:

  * **Agregação de Informação Multi-Fonte:** Use o ecossistema de plugins do Coze para alcançar integração sem costura de fluxos de dados cross-platform e cross-type.
  * **Definição de Comportamento do Agente:** Através de configuração de papel e engenharia de prompt, controle precisamente a execução de tarefas do agente e geração de conteúdo para garantir que a saída atenda aos padrões profissionais predefinidos.
  * **Construção de Workflow Automatizado:** Aprenda como vincular múltiplas etapas como aquisição de dados, processamento de conteúdo e saída formatada em um workflow eficiente e automatizado.

**Passo 1: Adicionar e Configurar Plugins de Fonte de Informação**

A tarefa primária de construir um agente "Resumo Diário de IA" é conectá-lo a fontes de informação ricas e autoritativas. Na plataforma Coze, isso é alcançado adicionando e configurando plugins correspondentes.

1.  **Integração de Plugin:** Na biblioteca de plugins do Coze, procure e adicione os plugins necessários. Por exemplo, assine feeds RSS de plataformas de mídia através do plugin **RSS** (como mostrado na Figura 5.6), rastreie projetos de código aberto através do plugin **GitHub** (como mostrado na Figura 5.7) e obtenha os resultados de pesquisa acadêmica mais recentes através do plugin **arXiv** (como mostrado na Figura 5.8).

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-06.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.6 Plugin de Fonte RSS para Plataformas de Mídia</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-07.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.7 Plugin GitHub</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-08.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.8 Plugin Arxiv</p>
</div>

2.  **Configuração Personalizada:** Execute configuração de granularidade fina para cada plugin para garantir que ele possa obter com precisão os dados necessários. Por exemplo, no plugin RSS, insira links de assinatura RSS específicos para sites como 36Kr e Huxiu; no plugin GitHub, defina quantidades de consulta de palavras-chave e configurações de atualização mais recentes a serem monitoradas; no plugin arXiv, defina palavras-chave de interesse como "LLM", "AI", etc., e defina quantidades e configurações de atualização mais recentes.

```
Configuração de Link RSS

- **36Kr:** https://www.36kr.com/feed
- **Huxiu:** https://rss.huxiu.com/
- **IT Home:** http://www.ithome.com/rss/
- **InfoQ:** https://feed.infoq.com/ai-ml-data-eng/

Configuração do Plugin GitHub

- q:AI
- per_page:10
- sort:updated

Configuração do Plugin Arxiv

- count: 5
- search_query: AI
- sort_by: 2
```

3.  **Orquestração e Conexão:** Na interface de orquestração visual do agente, use esses plugins de fonte de informação configurados (como `rss_24Hbj`, `searchRepository`, `arxiv`, etc.) como nós de entrada de dados e conecte-os a módulos de processamento lógico subsequentes (como o módulo **Large Model**) para construir um caminho completo de processamento de dados, como mostrado na Figura 5.9.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-09.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.9 Fluxograma de Orquestração do Resumo Diário de IA</p>
</div>

**Passo 2: Definir Papel do Agente e Prompts**

A configuração de papel e escrita de prompt são as etapas centrais na definição do comportamento do agente e qualidade de saída. Esta etapa visa transformar instruções abstratas em tarefas específicas que o agente pode entender e executar.

(1) Configuração de Papel

Definimos o agente como um **editor de mídia de tecnologia sênior e autoritativo**. Este papel dá ao agente um posicionamento profissional claro, permitindo que ele imite o modo de pensamento de editores profissionais na criação de conteúdo subsequente, realizando triagem, integração e resumo eficientes de informação.

(2) Escrita e Estruturação de Prompt

Prompts são o manual de instruções para o agente executar tarefas. Nós os dividimos em **System Prompt e User Prompt** para garantir que as instruções sejam claras, completas e controláveis.

**System Prompt**

O system prompt é usado para definir as diretrizes comportamentais de longo prazo do agente e especificações de formato de saída.

```
# Papel
Você é um editor de mídia de tecnologia sênior e autoritativo, habilidoso em integrar eficientemente e precisamente e criar resumos de tecnologia altamente profissionais, com capacidades profundas de análise e integração especialmente em desenvolvimentos técnicos do campo de IA, resultados de pesquisa acadêmica de ponta e projetos de código aberto populares.

## Workflow
### Formato de Saída do Relatório Diário
1. O relatório diário deve exibir proeminentemente "Relatório Diário de IA", "por@jasonhuang", e a data atual no início, por exemplo: "Relatório Diário de IA | 24 de setembro de 2025 | por@jasonhuang".
2. <!!!importante!!!> Adicione um símbolo Emoji único no início de cada título com base no conteúdo diferente de cada notícia de tecnologia de IA, cada artigo acadêmico de IA e cada projeto de código aberto de IA.
3. Todo o conteúdo de saída deve ser altamente relevante para IA, LLM, AIGC, modelos grandes e outros tópicos técnicos, excluindo firmemente qualquer informação irrelevante, anúncios e conteúdo de marketing.
4. Deve fornecer o link original para cada item (incluindo notícias de tecnologia de IA, artigos acadêmicos de IA, projetos de código aberto de IA).
5. Forneça uma descrição resumida breve e precisa para cada item de notícia ou saída de projeto.
```

**User Prompt**

O user prompt é usado para definir instruções de tarefas específicas e fontes de dados.

```
- **Extração e Integração de Informação:** Das fontes de entrada `{{articles}}`, `{{articles1}}`, `{{articles2}}`, e `{{articles3}}`, filtre e extraia títulos de artigos e links correspondentes relacionados a IA, modelos grandes, AIGC, LLM e outros tópicos, e organize-os no módulo **"Notícias de Tecnologia de IA"**.
- **Resumo de Artigo Acadêmico:** Da fonte de entrada `{{arxiv}}`, baseado nos campos `arxiv_title` e `arxiv_link`, resuma e organize o conteúdo de artigo mais recente para formar o módulo **"Artigos Acadêmicos de IA"**.
- **Filtragem de Projeto de Código Aberto:** Da fonte de entrada `{{GitHub}}`, filtre os **5 projetos de código aberto de IA mais proeminentes e influentes**. Extraia os títulos e links correspondentes desses projetos e organize-os no módulo **"Projetos de Código Aberto de IA"**.

# Atenção
- Siga estritamente o formato de saída do relatório diário definido no system prompt.
- O conteúdo total de saída deve ser: **10 itens de notícias de tecnologia de IA, 5 artigos acadêmicos de IA, 5 projetos de código aberto de IA**.
```

**Passo 3: Teste, Depuração e Publicação Multi-Canal**

Após completar a construção da lógica central do agente, testes e depuração rigorosos devem ser realizados para garantir que sua saída atenda às expectativas.

**Executar Pré-visualização:** Execute o agente na interface de pré-visualização da plataforma Coze e observe o conteúdo do resumo que ele gera.

```
# Relatório Diário de IA por@jasonhuang 2025-09-24

## 🚀 Notícias de Tecnologia de IA

🤖 **Modelo de Fundação Incorporado Universal GO-1 do Zhiyuan Robot Totalmente Open-Sourced**
Link: https://36kr.com/p/3479085489708163?f=rss
Resumo: Zhiyuan Robot anunciou a abertura completa de código aberto de seu modelo de fundação incorporado universal GO-1, fornecendo capacidades de IA fundamentais poderosas para o campo da robótica.

🔬 **Microsoft Supera Gargalo de Resfriamento de Chip de Data Center: Microfluídica + Resfriamento de Precisão de IA**
Link: https://www.ithome.com/0/885/391.htm
Resumo: Microsoft alcança controle preciso de temperatura de chips de data center através da combinação de tecnologia microfluídica e algoritmos de IA, melhorando a eficiência energética.
......

## 📚 Artigos Acadêmicos de IA

🧪 **Lyra: Reconstrução de Cena 3D Generativa via Autodestilação de Modelo de Difusão de Vídeo**
Link: http://arxiv.org/pdf/2509.19296v1
Resumo: Propõe uma estrutura inovadora para geração de cena 3D através de autodestilação de modelo de difusão de vídeo, sem exigir dados de treinamento multi-visualização.

📊 **O Experimento de Ranking ICML 2023: Examinando Autoavaliação de Autor na Revisão por Pares de ML/IA**
Link: http://arxiv.org/pdf/2408.13430v3
Resumo: Estuda a efetividade da autoavaliação de autor em processos de revisão de conferência de aprendizado de máquina e propõe métodos para melhorar mecanismos de revisão.
......

## 💻 Projetos de Código Aberto de IA

🤖 **llmling-agent - Framework de Workflow Multi-Agente**
Link: https://github.com/phil65/llmling-agent
Resumo: Framework de interação multi-agente suportando configuração YAML e métodos de programação, integrando suporte de protocolo MCP e ACP.

🚌 **College_EV_AI_Transportation - Sistema de Transporte Elétrico de IA de Campus**
Link: https://github.com/LuisMc2005v/College_EV_AI_Transportation
Resumo: Sistema de otimização de transporte elétrico de campus impulsionado por IA, alcançando rastreamento em tempo real e serviços eficientes de carona compartilhada.
......
```

Verifique cuidadosamente a precisão do conteúdo, completude do formato e estilo de linguagem do resumo. Se partes forem encontradas que não atendem às expectativas, retorne ao prompt ou estágio de configuração do plugin para ajustes detalhados. Por exemplo, se o conteúdo não for conciso o suficiente, modifique os requisitos de resumo no prompt; se a aquisição de dados for imprecisa, verifique os parâmetros de configuração do plugin.

Publicação Multi-Canal: Coze fornece a capacidade de publicar agentes em múltiplas plataformas de aplicação mainstream (como WeChat, Doubao, Feishu, etc.) com um clique, expandindo enormemente os cenários de aplicação de agentes, como mostrado na Figura 5.10.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-10.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.10 Canais de Publicação Diversos da Plataforma Coze</p>
</div>

Após o agente ser publicado, podemos ver o agente de IA que criamos na loja Coze, e ele também pode ser integrado em aplicações de IA para fornecer serviços aos usuários, como mostrado nas Figuras 5.11 e 5.12. Aqui está também o [Link de Experiência do Agente de Notícias Diárias de IA](https://www.coze.cn/store/agent/7506052197071962153?bot_id=true&bid=6hkt3je8o2g16)

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-11.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.11 Agente de IA - Notícias Diárias de IA</p>
</div>

Além disso, podemos clicar neste [link de experiência](https://www.coze.cn/store/project/7458678213078777893?from=store_search_suggestion&bid=6gu3cmr7k5g1i) para visualizar Notícias Diárias de IA na aplicação de IA.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-12.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.12 Notícias Diárias de IA na Aplicação de IA</p>
</div>

**Configuração de Publicação:** Se você quiser publicar seu próprio agente, você também precisa configurar um nome apropriado, avatar e mensagem de boas-vindas para o agente antes de publicar para fornecer uma experiência de usuário mais amigável, como mostrado nas Figuras 5.13 e 5.14.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-13.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.13 Configurar Informações Básicas para o Agente</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/coze-14.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.14 Configurar Observações de Abertura e Perguntas Predefinidas para o Agente</p>
</div>

### 5.2.3 Análise das Vantagens e Limitações do Coze

**Vantagens:**

  * **Ecossistema de Plugin Poderoso:** A vantagem central da plataforma Coze está em sua rica biblioteca de plugins, que permite aos agentes acessar facilmente serviços externos e fontes de dados, alcançando alta extensibilidade de funções.
  * **Orquestração Visual Intuitiva:** A plataforma fornece uma interface de orquestração de workflow visual de baixo limite. Os usuários podem construir fluxos de trabalho complexos através de "arrastar e soltar" sem conhecimento profundo de programação, reduzindo enormemente a dificuldade de desenvolvimento.
  * **Controle de Prompt Flexível:** Através de configuração precisa de papel e escrita de prompt, os usuários podem executar controle de granularidade fina sobre o comportamento do agente e geração de conteúdo, alcançando saída altamente customizada. Também suporta gerenciamento e templates de prompt, facilitando enormemente os desenvolvedores no desenvolvimento de agentes.
  * **Implantação Multi-Plataforma Conveniente:** Suporta publicação do mesmo agente em diferentes plataformas de aplicação, alcançando integração e aplicação cross-platform sem costura. Além disso, Coze está continuamente integrando novas plataformas em seu ecossistema, com cada vez mais fabricantes de telefones celulares e fabricantes de hardware gradualmente suportando a publicação de agentes Coze.

**Limitações:**

  * **Não Suporta MCP:** Eu acho que isso é o mais fatal. Embora o mercado de plugins do Coze seja extremamente rico e atraente, não suportar MCP pode se tornar um grilhão limitando seu desenvolvimento. Se aberto, será outro recurso matador.
  * **Alta Complexidade de Algumas Configurações de Plugin:** Para plugins que requerem API Keys ou outros parâmetros avançados, os usuários podem precisar de alguma formação técnica para completar a configuração correta. Orquestração de workflow complexa também não é algo que possa ser dominado com base zero; requer algumas noções básicas de JavaScript ou Python.
  * **Incapaz de importar arquivos JSON:** Anteriormente, o app não tinha função de exportar/importar, mas a versão paga agora tem. No entanto, o arquivo exportado/importado não é um arquivo JSON como Dify ou N8n; é um arquivo ZIP. Isso significa que você só pode exportar do app e então importar o arquivo ZIP. No entanto, você pode usar uma solução alternativa: na interface de layout, pressione Ctrl+A para selecionar tudo, então Ctrl+C para copiar o layout, e então cole em outro workflow em branco ou outros workflows.

## 5.3 Plataforma Dois: Dify

### 5.3.1 Introdução ao Dify e Seu Ecossistema

Dify é uma plataforma de desenvolvimento de aplicação de modelo de linguagem grande (LLM) de código aberto que integra os conceitos de Backend as a Service (BaaS) e LLMOps, fornecendo suporte de processo completo desde design de protótipo até implantação em produção, como mostrado na Figura 5.15. Adota uma arquitetura modular em camadas, dividida em camada de dados, camada de desenvolvimento, camada de orquestração e camada de fundação, com cada camada desacoplada para fácil expansão.

Dify é altamente neutro e compatível com modelos: sejam modelos de código aberto ou comerciais, os usuários podem integrá-los através de configuração simples e chamar suas capacidades de inferência através de uma interface unificada. Tem suporte embutido para integração com centenas de LLMs de código aberto ou proprietários, cobrindo modelos como GPT, Deepseek, Llama, bem como qualquer modelo compatível com a API OpenAI.

Ao mesmo tempo, Dify suporta implantação local (Docker Compose oficial de início com um clique) e implantação em nuvem. Os usuários podem escolher auto-implantar Dify em ambientes locais/privados (garantindo privacidade de dados) ou usar o serviço SaaS em nuvem oficial (detalhado na seção de modelo de negócio abaixo). Essa flexibilidade de implantação o torna adequado para ambientes de intranet empresarial com requisitos de segurança ou grupos de desenvolvedores com requisitos de conveniência operacional.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-01.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.15 Site Oficial do Dify</p>
</div>

Ecossistema de Plugin do Marketplace: Dify Marketplace fornece gerenciamento de plugin one-stop e funcionalidade de implantação com um clique, permitindo que desenvolvedores descubram, estendam ou enviem plugins, trazendo mais possibilidades para a comunidade, como mostrado na Figura 5.16.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-02.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.16 Ecossistema de Plugin do Dify Marketplace</p>
</div>

Marketplace inclui:

- Modelos
- Ferramentas
- Estratégias de Agente
- Extensões
- Bundles

Atualmente, Dify Marketplace tem mais de 8.677 plugins cobrindo várias funções e cenários de aplicação. Entre eles, plugins oficialmente recomendados incluem:
- Google Search: langgenius/google
- Azure OpenAI: langgenius/azure_openai
- Notion: langgenius/notion
- DuckDuckGo: langgenius/duckduckgo

Dify fornece suporte de desenvolvimento poderoso para desenvolvedores de plugin, incluindo funcionalidade de depuração remota que colabora perfeitamente com IDEs populares, exigindo configuração mínima de ambiente. Desenvolvedores podem se conectar ao serviço SaaS do Dify enquanto encaminham todas as operações de plugin para o ambiente local para teste. Esta abordagem amigável ao desenvolvedor visa empoderar criadores de plugin e acelerar a inovação no ecossistema Dify. Esta é também a razão pela qual Dify pode se tornar uma das plataformas de agentes de maior sucesso atualmente, porque modelos podem todos ser integrados, prompts e orquestração podem ser copiados, mas a presença e riqueza de plugins de ferramenta determinam diretamente se seu agente pode alcançar melhores resultados ou funções inesperadamente poderosas.

### 5.3.2 Construindo um Assistente Pessoal Super Agente

> **✨✨ Guia de Operação Detalhado**: Por favor, consulte **[Tutorial Passo a Passo de Criação de Agente Dify](https://github.com/datawhalechina/hello-agents/blob/main/Extra-Chapter/Extra03-Dify智能体创建保姆级操作流程.md)**

No caso anterior do Coze, construímos um agente de resumo diário de IA. Embora sua função seja clara, sua capacidade única de geração de resumo é um tanto limitada. Esta seção usará Dify para construir um assistente pessoal super agente totalmente funcional, cobrindo múltiplos cenários como Q&A diário, otimização de copywriting, geração multimodal e análise de dados. Antes de começar, vamos entender brevemente a interface principal do Dify e módulos funcionais.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-14.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.17 Página Inicial de Construção de Agente Dify</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-18.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.18 Biblioteca de Templates Oficiais do Dify</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-15.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.19 Base de Conhecimento Dify</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-16.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.20 Mercado de Plugin Dify</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-17.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.21 Configuração de Modelo Grande Dify</p>
</div>

**(1) Criando Plugins e Configurando MCP**

Antes de construir o agente, instalação necessária de plugin e configuração MCP devem ser completadas primeiro. Como mostrado na Figura 5.22, estes são os plugins principais necessários para este caso.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-19.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.22 Configuração de Instalação de Plugin Dify</p>
</div>

Os plugins marcados com caixas vermelhas na figura precisam ser procurados e instalados do mercado de plugin Dify. Os usuários podem clicar para ver detalhes para entender as funções específicas de cada plugin.

Em seguida, configure MCP (Model Context Protocol). Não vamos expandir nos princípios detalhados do MCP aqui; vamos focar em demonstrar como usar serviços MCP implantados em nuvem. Este caso usa o mercado MCP da comunidade ModelScope doméstica para demonstração, como mostrado na Figura 5.23.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-20.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.23 Mercado MCP da Comunidade ModelScope</p>
</div>

Abra o mercado MCP da comunidade ModelScope e selecione o tipo hospedado. Tomando Amap MCP como exemplo, após entrar em sua página inicial, selecione o modo SSE no lado direito e clique em configuração de conexão para gerar um JSON de configuração MCP dedicado, como mostrado na Figura 5.24. MCP suporta múltiplos modos de comunicação, mas usar comunicação em modo SSE no Dify é mais suave e estável, então o modo SSE é recomendado.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-21.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.24 Exemplo de Configuração Amap MCP</p>
</div>

**(2) Design do Agente e Exibição de Efeito**

Este caso criará um assistente pessoal abrangente cobrindo os seguintes módulos funcionais:

- Q&A de vida diária
- Polimento e otimização de copywriting
- Geração de conteúdo multimodal (imagens, vídeos)
- Consulta de dados e análise de visualização
- Integração de ferramenta MCP (Amap, recomendações dietéticas, informações de notícias)

A arquitetura geral de orquestração do agente é mostrada na Figura 5.25.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-12.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.25 Orquestração do Agente</p>
</div>

Para a arquitetura multi-agente, usamos um classificador de perguntas para roteamento inteligente. No classificador, defina as funções principais e escopo de tarefa para cada agente para garantir que solicitações de usuário possam ser distribuídas com precisão para os módulos de processamento correspondentes.

**Módulo de Assistente Diário**

Este é um módulo de diálogo básico configurado com um modelo de linguagem grande e ferramentas de tempo, servindo como um serviço de Q&A geral de fallback.

Configuração de prompt:
```
# Papel: Especialista em Consulta de Perguntas Diárias

## Perfil
- idioma: Chinês
- descrição: Especializa-se em responder perguntas gerais na vida diária dos usuários, fornecendo conselhos e respostas práticos, precisos e fáceis de entender
- background: Possui rica experiência de vida e extensas reservas de conhecimento, habilidoso em simplificar problemas complexos
- personalidade: Gentil e amigável, paciente e meticuloso, pragmático e confiável
- expertise: Vida diária, saúde e bem-estar, gerenciamento familiar, relacionamentos interpessoais, dicas práticas

## Habilidades

1. Capacidade de Análise de Problema
   - Compreensão Rápida: Compreender rapidamente os pontos centrais das perguntas do usuário
   - Reconhecimento de Classificação: Julgar com precisão o domínio de vida ao qual a pergunta pertence
   - Mineração de Demanda: Compreender profundamente as necessidades potenciais dos usuários
   - Ordenação de Prioridade: Avaliar razoavelmente a importância e urgência dos problemas

2. Capacidade de Fornecimento de Resposta
   - Integração de Conhecimento: Aplicar abrangentemente conhecimento multi-domínio para fornecer respostas
   - Formulação de Solução: Fornecer soluções específicas e viáveis
   - Decomposição de Passos: Dividir problemas complexos em passos simples
   - Soluções Alternativas: Preparar múltiplas soluções de backup para os usuários escolherem

3. Capacidade de Comunicação e Expressão
   - Linguagem Popular: Usar linguagem cotidiana simples e fácil de entender
   - Lógica Clara: Organizar conteúdo de resposta de forma bem organizada
   - Exemplos Ilustrativos: Ajudar a compreensão através de casos específicos
   - Destacar Pontos-Chave: Enfatizar informações-chave e precauções

## Regras

1. Princípios de Resposta:
   - Praticidade em Primeiro Lugar: Garantir que os conselhos fornecidos sejam acionáveis
   - Garantia de Precisão: Dar respostas baseadas em informações confiáveis e senso comum
   - Neutro e Objetivo: Evitar viés pessoal e suposições subjetivas
   - Conselho Moderado: Fornecer profundidade apropriada de respostas baseado na complexidade do problema

2. Código de Conduta:
   - Resposta Oportuna: Responder rapidamente às perguntas dos usuários
   - Paciente e Meticuloso: Manter paciência com perguntas repetitivas ou simples
   - Orientação Ativa: Encorajar usuários a fornecer mais informações de fundo
   - Melhoria Contínua: Otimizar qualidade de resposta baseado em feedback

## Workflows

- Objetivo: Fornecer aos usuários soluções de problemas diários práticas e confiáveis
- Passo 1: Ler cuidadosamente e entender as perguntas diárias levantadas pelos usuários
- Passo 2: Analisar o tipo de problema e necessidades potenciais dos usuários
- Passo 3: Fornecer sugestões específicas e viáveis baseadas em senso comum e experiência
- Passo 4: Organizar conteúdo de resposta em linguagem fácil de entender
- Passo 5: Verificar a praticidade e segurança da resposta

## Inicialização
Como um especialista em consulta de perguntas diárias, você deve obedecer às Regras acima e executar tarefas de acordo com Workflows.
```

A demonstração de efeito é mostrada na Figura 5.26:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-03.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.26 Assistente Diário</p>
</div>

**Módulo de Otimização de Copywriting**

De acordo com o relatório de dados da OpenAI, mais de 60% dos usuários usam ChatGPT para tarefas relacionadas a otimização de texto, incluindo polimento, modificação, expansão e abreviação. Portanto, otimização de copywriting é um cenário de demanda de alta frequência, e nós a tornamos o segundo módulo funcional central.

Configuração de prompt:
```
# I. Configuração de Papel (Role)
Você é um especialista profissional em otimização de copywriting com rica experiência em copywriting de marketing e otimização, habilidoso em melhorar a atratividade, taxa de conversão e legibilidade de copy. Sua perspectiva é do ângulo do público-alvo e objetivos de marketing, com limites profissionais limitados ao campo de otimização de copywriting, não envolvendo implementação técnica ou desenvolvimento de produto.

# II. Background
O usuário forneceu um copy original que precisa de sua otimização para melhorar sua eficácia geral. Informações de fundo incluem: o copy pode ser usado para marketing, promoção de marca ou cenários de comunicação de informação, mas o uso específico não é detalhado. A condição conhecida é que o usuário espera que o copy seja mais atraente, claro ou persuasivo, mas não forneceu o conteúdo do copy original, então você precisa trabalhar baseado em princípios gerais de otimização.

# III. Objetivos da Tarefa (Task)
- Analisar e otimizar a estrutura, linguagem e estilo do copy para torná-lo mais alinhado com as preferências do público-alvo.
- Melhorar a atratividade, legibilidade e potencial de conversão do copy, garantindo entrega clara de informação.
- Fazer ajustes de acordo com princípios comuns de otimização (como concisão, ressonância emocional, chamada para ação, etc.), sem reescrita de conteúdo a menos que necessário.
- Enquanto mantém informação central, expandir e enriquecer apropriadamente conteúdo de copy para fornecer uma versão otimizada mais abrangente.

# IV. Prompts de Limitação (Limit)
- Evitar mudar a informação central ou intenção do copy original a menos que explicitamente solicitado pelo usuário.
- Não adicionar conteúdo fictício ou irrelevante, garantindo otimização baseada em lógica e melhores práticas.
- Evitar usar terminologia excessivamente técnica ou profissional a menos que o público-alvo seja profissionais.
- Não envolver otimização de imagens, layouts ou outros elementos não textuais.

# V. Requisitos de Formato de Saída (Example)
A saída deve ser texto de copy otimizado com estrutura clara, linguagem fluente e conteúdo substancial. Por exemplo:
- Se o copy original for "Nosso produto é muito bom, venha e compre"
A versão otimizada pode ser: "Nesta era cheia de escolhas, o que verdadeiramente toca os corações das pessoas nunca é propaganda exagerada, mas produtos bons que podem resistir ao teste do tempo e usuários. Nosso produto é exatamente isso. Ele não só presta atenção aos detalhes e qualidade no design, mas também continuamente pule e inova em função, apenas para trazer uma experiência de usuário melhor para cada usuário. Seja a textura da aparência ou a estabilidade do desempenho, sempre aderimos a altos padrões e requisitos rigorosos, esforçando-nos para fazer cada cliente que nos escolhe sentir a surpresa de valor pelo dinheiro.
Entendemos profundamente que comprar um produto não é apenas um consumo simples, mas uma escolha de estilo de vida. Portanto, da seleção de material, artesanato ao serviço pós-venda, derramamos sinceridade e profissionalismo total em cada link, guardando cuidadosamente cada experiência sua. Se você persegue praticidade, valoriza qualidade ou quer personalização única, nossos produtos podem fornecer soluções ideais para você.
Agora, deixe-nos provar tudo com ação. Um produto verdadeiramente bom não precisa de muito embelezamento; ele mesmo é o melhor porta-voz. Aja agora, escolha-nos, deixe a qualidade mudar a vida e tenha uma experiência diferente a partir de agora!"
- A saída deve apresentar diretamente conteúdo otimizado sem explicações ou anotações adicionais a menos que solicitado pelo usuário. Por favor, garanta que o conteúdo do copy otimizado seja mais rico e completo, e o texto do copy otimizado deve exceder 500 palavras.
```

A demonstração de efeito é mostrada na Figura 5.27:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-04.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.27 Assistente de Copywriting</p>
</div>

**Módulo de Geração Multimodal**

Geração de imagem e vídeo é outro cenário de aplicação de alta frequência. Com a evolução de modelos como geração de imagem Doubao e Google Imagen, bem como avanços em tecnologias de geração de vídeo como Keling, Google Veo 3 e OpenAI Sora 2, a qualidade da geração de conteúdo multimodal atingiu um nível prático.

Este caso usa o plugin Doubao para implementar geração de imagem e vídeo. As etapas de configuração são as seguintes:

1. Adicionar plugin de geração de imagem/vídeo Doubao no workflow
2. Configurar parâmetros (como proporção de imagem 1:1, seleção de modelo doubao seedream)
3. Saída do arquivo gerado

Configuração e efeitos de geração de imagem são mostrados nas Figuras 5.28 e 5.29.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-13.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.28 Configurações de Geração de Imagem</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-05.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.29 Assistente de Geração de Imagem</p>
</div>

O efeito de geração de vídeo é mostrado na Figura 5.30.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-06.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.30 Assistente de Vídeo</p>
</div>

**Módulo de Consulta e Análise de Dados**

Processamento de dados é uma das capacidades importantes dos agentes. Este módulo demonstra como se conectar a um banco de dados no Dify para implementar consulta de dados e análise de visualização.

Primeiro, instale o plugin de ferramenta de consulta de dados; este caso usa o plugin `rookie-text2data`. A chave para consulta de dados é fornecer ao modelo grande informações claras de estrutura de tabela e campo para que ele possa gerar instruções SQL de consulta precisas. Práticas comuns incluem:

- Fornecer diretamente a instrução DDL da tabela de dados
- Fornecer uma descrição da correspondência entre nomes de tabela e nomes de campo

Configure informações de conexão do banco de dados (endereço IP, nome do banco de dados, porta, conta, senha, etc.), como mostrado na Figura 5.31. Resultados de consulta precisam ser organizados através de um nó de modelo grande e convertidos em saída de linguagem natural fácil de entender.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-22.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.31 Configuração do Banco de Dados</p>
</div>

Configurações de prompt:

```
# I. Configuração de Papel (Role)
Você é um especialista profissional em consulta de dados, habilidoso em organização de dados, com pensamento lógico claro e capacidade de expressão concisa.

# II. Background
O usuário forneceu dados brutos consultados do banco de dados. Estes dados podem ter problemas como formatos inconsistentes, campos faltantes e registros duplicados, e precisam de organização profissional antes de exibição efetiva.

# III. Objetivos da Tarefa (Task)
1. Resumir e organizar dados brutos
2. Classificar e ordenar dados de acordo com lógica correta
3. Exibição de dados destaca informações-chave e insights de dados
4. Fornecer exibição de dados fácil de entender

# IV. Prompts de Limitação (Limit)
1. Não deve arbitrariamente deletar dados importantes
2. Evitar usar terminologia estatística excessivamente complexa ou profissional
3. Não deve adulterar os verdadeiros valores dos dados brutos
4. Evitar exibir muita informação redundante, manter conciso e claro
5. Não deve vazar dados sensíveis ou informações de privacidade pessoal

# V. Requisitos de Formato de Saída (Example)
 Visão Geral de Dados: Simplesmente explicar brevemente o conteúdo dos dados
```

A exibição de efeito é mostrada na Figura 5.32:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-07.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.32 Assistente de Consulta de Dados</p>
</div>

Configurações de prompt:

```
# I. Configuração de Papel (Role)
Você é um analista de dados profissional com organização de dados, limpeza e capacidades de visualização, capaz de extrair informações-chave de dados brutos e transformá-las em exibições visuais intuitivas.

# II. Background
O usuário consultou um lote de dados brutos do banco de dados. Estes dados podem conter múltiplos campos, valores faltantes ou formatos inconsistentes, e precisam ser organizados antes de gerar gráficos de visualização.

# III. Objetivos da Tarefa (Task)
# Workflow
1. Análise de Dados
Analisar, organizar e resumir dados de acordo com regras razoáveis
2. Análise & Visualização
Gerar pelo menos 1 gráfico (escolha um ou mais de barra / linha / gráfico de pizza)
Pode chamar ferramentas: "generate_pie_chart" | "generate_column_chart" | "generate_line_chart"

# IV. Prompts de Limitação (Limit)
1. Evitar usar tipos de gráficos excessivamente complexos, garantir resultados de visualização fáceis de entender
2. Não ignorar problemas de qualidade de dados, deve realizar limpeza de dados necessária
3. Evitar usar muitas cores ou elementos na visualização, manter conciso e claro
4. Não omitir rotulação e explicação de dados-chave
5. Deve realizar resumo e geração de gráfico, independentemente do volume de dados

# V. Requisitos de Formato de Saída (Example)
Por favor, saída no seguinte formato:
1. Resumo de visão geral de dados (não saída de nomes de campo, não liste pontos, apenas um parágrafo curto)
2. Exibir gráficos gerados
```

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-08.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.33 Assistente de Análise de Dados</p>
</div>

A única diferença no assistente de análise de dados é que adicionamos ferramentas de visualização de dados, nomeadamente os plugins de ferramenta de geração de gráfico BI "generate_pie_chart" | "generate_column_chart" | "generate_line_chart". Se você os instalou conforme requerido anteriormente, você pode adicioná-los e usá-los diretamente, e adicionar descrições correspondentes como no prompt acima.

**Integração de Ferramenta MCP**

Finalmente, a aplicação de integração de ferramentas MCP. Já completamos a configuração MCP anteriormente, agora vamos integrá-la ao agente. As etapas de configuração são as seguintes:

1. Selecionar uma estratégia de agente que suporte chamadas MCP
2. Selecionar modo ReAct
3. Configurar serviço MCP (note para deletar o prefixo `mcp-server`, selecionar modo SSE)
4. Preencher os prompts correspondentes

A interface de configuração é mostrada na Figura 5.34.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-23.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.34 Configuração MCP do Agente</p>
</div>

Os efeitos de assistente Amap, assistente dietético e assistente de notícias são mostrados nas Figuras 5.35, 5.36 e 5.37 respectivamente.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-09.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.35 Assistente Amap</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-10.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.36 Assistente Dietético</p>
</div>

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/dify-11.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.37 Assistente de Notícias</p>
</div>

Neste ponto, completamos um assistente pessoal super agente totalmente funcional. Este assistente cobre múltiplos aspectos da vida: quando você precisa de roupas novas, você pode ter o Doubao gerando designs; antes de sair, você pode ter o assistente Amap planejando rotas; quando você não sabe o que comer, você pode obter recomendações dietéticas; quando você quer entender situações de aprendizado, você pode realizar análise de dados. Este agente pode lidar com várias tarefas de trabalho e vida, e esperamos ver todos construindo assistentes de agente pessoal mais criativos.

### 5.3.3 Análise das Vantagens e Limitações do Dify

Como uma plataforma líder de desenvolvimento de aplicação de IA, Dify demonstra vantagens significativas em múltiplos aspectos:

1. Vantagens Principais

- Experiência de Desenvolvimento Full-Stack: Dify integra pipelines RAG, fluxos de trabalho de IA, gerenciamento de modelo e outras funções em uma plataforma, fornecendo uma experiência de desenvolvimento one-stop
- Equilíbrio Entre Low-Code e Alta Extensibilidade: Dify alcança um bom equilíbrio entre a conveniência do desenvolvimento low-code e a flexibilidade do desenvolvimento profissional

- Segurança e Conformidade de Nível Empresarial: Dify fornece criptografia AES-256, controle de permissão RBAC e logs de auditoria, atendendo requisitos rigorosos de segurança e conformidade

- Rica Capacidade de Integração de Ferramentas: Dify suporta 9000+ ferramentas e extensões de API, fornecendo ampla extensibilidade funcional
- Comunidade Open-Source Ativa: Dify tem uma comunidade open-source ativa, fornecendo ricos recursos de aprendizado e suporte

2. Limitações Principais
- Curva de Aprendizado Íngreme: Para usuários sem formação técnica, ainda há uma certa curva de aprendizado

- Gargalos de Desempenho: Pode enfrentar desafios de desempenho em cenários de alta concorrência, exigindo otimização apropriada. Os componentes principais do lado do servidor do sistema Dify são implementados em Python, que tem desempenho relativamente pobre comparado a linguagens como C++, Golang e Rust

- Suporte Multimodal Insuficiente: Atualmente focado principalmente em processamento de texto, com suporte limitado para imagens, vídeos, HTML, etc.

- Alto Custo da Edição Enterprise: O preço da edição enterprise do Dify é relativamente alto, o que pode exceder o orçamento de pequenas equipes

- Problemas de Compatibilidade de API: O formato de API do Dify não é compatível com OpenAI, o que pode limitar a integração com certos sistemas de terceiros

## 5.4 Plataforma Três: n8n

Como introduzimos anteriormente, a identidade central do n8n é uma plataforma geral de automação de workflow, não uma ferramenta pura de construção de aplicação LLM. Entender isso é chave para dominar n8n. Ao usar n8n para construir aplicações inteligentes, estamos na verdade projetando um processo de automação mais grandioso, e o modelo de linguagem grande é apenas um (ou múltiplos) "nó(s) de processamento" poderoso(s) neste processo.

### 5.4.1 Nós e Workflows do n8n

O mundo do n8n é composto de dois conceitos mais básicos: **Node** e **Workflow**.

- **Node**: Um nó é a menor unidade que realiza operações específicas em um workflow. Você pode pensar nele como um "bloco de construção" com funções específicas. n8n fornece centenas de nós predefinidos cobrindo várias operações comuns desde enviar emails, ler e escrever bancos de dados, chamar APIs até processar arquivos. Cada nó tem entradas e saídas e fornece uma interface de configuração gráfica. Nós podem ser divididos grosseiramente em duas categorias:
  - **Trigger Node**: É o ponto de partida de todo o workflow, responsável por iniciar o processo. Por exemplo, "quando um novo email Gmail é recebido," "acionado uma vez a cada hora," ou "quando uma solicitação Webhook é recebida." Um workflow deve ter um e somente um nó de gatilho.
  - **Regular Node**: Responsável por processar dados e lógica específicos. Por exemplo, "ler planilha Google Sheets," "chamar modelo OpenAI," ou "inserir um registro no banco de dados."
- **Workflow**: Um workflow é um fluxograma de automação composto de múltiplos nós conectados. Define o caminho completo de como os dados começam do nó de gatilho, passam passo a passo entre diferentes nós, são processados e finalmente completam a tarefa predefinida. Dados são passados entre nós em formato JSON estruturado, o que nos permite controlar precisamente a entrada e saída de cada link.

O poder real do n8n está em sua forte capacidade de "conexão". Ele pode vincular aplicações e serviços originalmente isolados (como CRM interno da empresa, plataformas de mídia social externas, seu banco de dados e modelos de linguagem grandes) para alcançar automação de processo de negócios de ponta a ponta que anteriormente exigia codificação complexa. Na prática próxima, vamos pessoalmente experimentar como usar este sistema de nó e workflow para construir uma aplicação automatizada integrada com capacidades de IA.

### 5.4.2 Construindo um Assistente de Email Inteligente

Sobre configuração de ambiente do n8n e uso mais básico, documentação foi criada na pasta `Additional-Chapter` do projeto, então não vamos introduzir muito aqui. Na seção anterior, aprendemos sobre os conceitos básicos do n8n. Este caso demonstrará claramente a diferença central entre Agentes de IA modernos e fluxos de trabalho de automação tradicionais. Processos tradicionais são lineares, enquanto o Agente que estamos prestes a construir será capaz de receber emails de usuários, "pensar" através de um nó **AI Agent** central, entender autonomamente a intenção do usuário, tomar decisões e escolhas entre múltiplas "ferramentas" disponíveis, e finalmente gerar e enviar automaticamente respostas altamente relevantes.

O processo inteiro simula uma lógica de decisão mais avançada: `Receber -> AI Agent (Pensar -> Decidir -> Tool Call) -> Responder`, como mostrado na Figura 5.38.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-01.png" alt="Descrição da imagem" width="90%"/>
  <p>Figura 5.38 Diagrama de Arquitetura do Agente de Email Inteligente Integrado</p>
</div>

Ao contrário do método tradicional de dividir ferramentas em múltiplos sub-workflows, o nó `AI Agent` do n8n nos permite integrar componentes como modelos de linguagem grandes (LLM), memória e ferramentas em uma interface unificada, simplificando enormemente o processo de construção.

O processo inteiro de construção é dividido em dois passos centrais:

1. **Preparar "Memória" do Agente**: Criar um processo independente para carregar uma base de conhecimento privada para o Agente.
2. **Construir Corpo Principal do Agente**: Criar o workflow principal que recebe emails, pensa e responde.

### 5.4.3 Construindo Base de Conhecimento Privada do Agente

Para habilitar o Agente a responder perguntas sobre domínios específicos (como suas informações pessoais ou documentação de projeto), precisamos primeiro preparar um "cérebro externo" para ele, uma base de conhecimento vetorial.

No n8n, podemos usar o nó `Simple Vector Store` para construir rapidamente uma base de conhecimento na memória. Este processo de preparação geralmente só precisa ser executado uma vez ao atualizar conhecimento.

**(1) Definir Fonte de Conhecimento**

Primeiro, usamos o nó `Code` para armazenar nosso texto de conhecimento bruto. Esta é uma maneira simples e rápida; em projetos reais, dados também podem vir de arquivos, bancos de dados, etc.

- **Node**: `Code`
- **Content**: Escreva seu conhecimento em formato JSON.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-02.png" alt="Captura de tela de texto JSON de base de conhecimento preenchido em nó Code" width="90%"/>
  <p>Figura 5.39 Definindo Fonte de Conhecimento em Nó Code</p>
</div>

```javascript
return [
  {
    "doc_id": "work-schedule-001",
    "content": "Meu horário de trabalho é de segunda a sexta, das 9h às 17h. O fuso horário é Horário Padrão do Leste da Austrália (AEST)."
  },
  {
    "doc_id": "off-hours-policy-001",
    "content": "Durante horário não comercial (incluindo fins de semana e feriados públicos), não posso responder emails imediatamente."
  },
  {
    "doc_id": "auto-reply-instruction-001",
    "content": "Se um email for recebido durante horário não comercial, o assistente de IA deve informar o remetente que o email foi recebido e processarei e responderei assim que possível entre 9h e 17h no próximo dia útil."
  }
];
```

**(2) Vetorização de Texto (Embeddings)**

Computadores não podem entender diretamente texto e precisam convertê-lo em vetores. Usamos o nó `Embeddings` para completar este trabalho de "tradução".

- **Node**: `Embeddings Google Gemini`, selecionar modelo como `gemini-embedding-exp-03-07`. Aqui usamos API Google para demonstração; se você não sabe como obter API Google, você pode consultar a Seção 5.5.3.
- **Configuration**: Conecte-o após o nó `Code`, e ele converterá automaticamente o texto passado do upstream em dados vetoriais.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-03.png" alt="" width="90%"/>
  <p>Figura 5.40 Vetorizando Dados em Code</p>
</div>

**(3) Armazenar em Armazenamento Vetorial**

Finalmente, armazenamos o conhecimento vetorizado em um banco de dados em memória, como mostrado na Figura 5.41.

- **Node**: `Simple Vector Store`
- **Configuration**:
  - **Operation Mode**: `Insert Documents` (modo de escrita).
  - **Memory Key**: Dê a esta base de conhecimento um nome único, por exemplo `my-dailytime`. Este Key é equivalente ao "nome da tabela" do banco de dados, e o Agente o usará para encontrar informações mais tarde.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-04.png" alt="" width="90%"/>
  <p>Figura 5.41 Armazenando Dados de Code em Armazenamento Vetorial</p>
</div>

Após completar a configuração, **execute manualmente este processo uma vez**. Após o sucesso, seu conhecimento privado é carregado na memória do n8n, como mostrado na Figura 5.42.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-05.png" alt="" width="90%"/>
  <p>Figura 5.42 Workflow Completo de Carregamento de Base de Conhecimento</p>
</div>

### 5.4.4 Criando Workflow Principal do Agente

Com as ferramentas prontas, agora começamos a construir o processo principal do Agente. Ele será responsável por receber emails, pensar e tomar decisões, chamar as ferramentas que acabamos de criar no momento certo, e finalmente executar respostas de email.

(1) Configurar Gatilho Gmail

Crie um novo workflow chamado `Agent: Customer Support`. Use o nó `Gmail` como um gatilho, defina seu **Event** para `Message Received`, e configure sua conta de email. Desta forma, sempre que um novo email entrar na caixa de entrada, o workflow será automaticamente acionado, como mostrado na Figura 5.43.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-06.png" alt="" width="90%"/>
  <p>Figura 5.43 Criando Nó Gmail</p>
</div>

O processo de configuração pode se referir à [documentação oficial do n8n](https://docs.n8n.io/integrations/builtin/credentials/google/oauth-single-service/?utm_source=n8n_app&utm_medium=credential_settings&utm_campaign=create_new_credentials_modal#enable-apis). A API do Gmail é configurada [aqui](https://console.cloud.google.com/apis/library/gmail.googleapis.com?project=apt-entropy-471905-b9). Você precisa criar credenciais, selecionar tipo de aplicação Web, e finalmente obter o ID de cliente e cliente secreto necessários. Você também precisa adicionar a URL de Redirecionamento OAuth dada pelo n8n às URIs de redirecionamento autorizadas. Ao mesmo tempo, você também precisa adicionar seu próprio endereço de email em Adicionar usuários em [Audience](https://console.cloud.google.com/auth/audience?project=apt-entropy-471905-b9). A página finalmente configurada é mostrada na Figura 5.44.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-07.png" alt="" width="90%"/>
  <p>Figura 5.44 Conta Gmail Carregada com Sucesso</p>
</div>

Agora podemos clicar `Fetch Test Event` para obter emails, como mostrado na Figura 5.45!

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-08.png" alt="" width="90%"/>
  <p>Figura 5.45 Obtendo Emails em Tempo Real</p>
</div>

(2) Configurar Nó AI Agent

Este é o cérebro de todo o workflow. Arraste um nó `AI Agent` do menu de nós e configure-o da seguinte forma:

- **Chat Model**: Conecte seu modelo de linguagem grande escolhido, como `Google Gemini Chat Model`. Este é o "núcleo de pensamento" do Agente.
- **Memory**: Conecte um nó `Simple Memory`. Isso permite ao Agente lembrar do histórico de conversa anterior ao processar múltiplos emails de ida e volta sob o mesmo tópico de email.
- **Tools**: Podemos conectar múltiplas ferramentas aqui. No nosso caso, conectamos duas ferramentas:
  1. `SerpAPI`: Esta é a API que usamos no caso do Capítulo 4, dando ao Agente a capacidade de procurar informações públicas online.
  2. `Simple Vector Store`: Dá ao Agente a capacidade de consultar a base de conhecimento privada que criamos na primeira parte.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-09.png" alt="" width="90%"/>
  <p>Figura 5.46 Configurações do Nó AI Agent</p>
</div>

Este é o primeiro passo do "pensamento" do Agente. Adicione um nó `Gemini` (ou outro nó LLM), defina o modo para `Chat`. Nosso objetivo é fazê-lo analisar conteúdo de email e julgar intenção do usuário. Design de prompt é crucial; uma instrução clara pode fazer o LLM completar a tarefa mais precisamente. Passamos o corpo e assunto do email (`{{ $json.snippet }}{{ $json.Subject }}`) como variáveis no Prompt. Se você não tem uma API, você pode ir para [Google AI Studio](https://aistudio.google.com/prompts/new_chat) e clicar em Obter chave API para criar uma disponível.

Para o nó AI Agent, precisamos principalmente preencher as seções `User Message` e `System Message`, como mostrado na Figura 5.47.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-10.png" alt="" width="90%"/>
  <p>Figura 5.47 Detalhes do Nó AI Agent</p>
</div>

Aqui está o Prompt usado em nosso caso:

```json
# Prompt (User Message)
# Informação de Contexto
- Hora Atual: {{ new Date().toLocaleString('en-AU', { timeZone: 'Australia/Sydney', hour12: false }) }} (horário de Sydney, Austrália)
- Remetente: {{ $json.From }}
- Assunto: {{ $json.Subject }}
- Corpo do Email: {{ $json.snippet }}

# System Message
# Papel e Objetivo
Você é um assistente de email de IA disponível 24/7, profissional e eficiente. Sua tarefa é: fazer o seu melhor para responder todas as perguntas em emails usando informação pública na primeira oportunidade, e adicionar lembretes de status contextuais no início das respostas baseado em meu horário de trabalho.

# Informação de Contexto
- Hora Atual: {{ new Date().toLocaleString('en-AU', { timeZone: 'Australia/Sydney', hour12: false }) }} (horário de Sydney, Austrália)
- Informação de email está nos dados de entrada.

# Ferramentas Disponíveis
- Simple Vector Store2: Usado para consultar meu horário de trabalho exato (por exemplo, segunda a sexta, 9h às 17h).
- SerpAPI: **[Fonte de Informação Primária]** Priorize usar esta ferramenta para procurar na internet para responder perguntas específicas em emails.

# Passos de Execução
1.  **Analisar a Pergunta**: Primeiro, leia cuidadosamente o conteúdo do email e extraia a pergunta central do remetente.

2.  **Coleta de Informação Paralela**: Execute as duas operações seguintes simultaneamente para coletar informação:
    a. Use a ferramenta `SerpAPI` para procurar online por respostas às perguntas do remetente.
    b. Use a ferramenta `Simple Vector Store2` para obter meu horário de trabalho exato definido.

3.  **Rascunhar Resposta Central**: Baseado na informação coletada por `SerpAPI`, responda clara e diretamente à pergunta do remetente. Esta parte servirá como o corpo principal da resposta de email.

4.  **Adicionar Prefixo de Status e Integrar**:
    a. Compare "Hora Atual" com o horário de trabalho que obtive da ferramenta.
    b. **Se atualmente "Horário Não Comercial"**: Crie um prefixo de lembrete de status. Este prefixo **deve incluir** o horário de trabalho específico obtido de `Simple Vector Store2`.
        * **Exemplo de Prefixo**: "Olá, obrigado pelo seu email. Você me contatou durante meu horário não comercial (meu horário de trabalho é: [inserir horário de trabalho consultado aqui]). Revisarei pessoalmente este email no próximo dia útil. Enquanto isso, aqui está uma resposta preliminar encontrada para você baseada em informação pública:**<br><br>---<br><br>**"
    c. **Se atualmente "Horário Comercial"**: Apenas use uma saudação simples.
        * **Exemplo de Prefixo**: "Olá, sobre sua pergunta, a resposta é a seguinte:**<br><br>---<br><br>**"
    d. Concatene o prefixo gerado e a resposta central que você rascunhou (resultado do passo 3) para formar o corpo final do email.

5.  **Saída Formatada**: Você deve produzir o conteúdo de email finalmente gerado em um formato JSON estrito. O formato é o seguinte, não adicione nenhuma explicação ou texto adicional:
    {
      "shouldReply": true,
      "subject": "Re: [Assunto do Email Original]",
      "body": "[Aqui está o corpo de resposta de email completo concatenado, **todas as quebras de linha devem usar tags HTML <br>**]"
    }

# Regras e Restrições
- **Sempre Tente Responder Primeiro**: A qualquer momento, sua tarefa primária é usar `SerpAPI` para fornecer respostas valiosas aos usuários.
- **Deve Declarar Status**: Se responder durante horário não comercial, você deve declarar claramente isso no início do email e anexar meu horário de trabalho exato.
- **Fontes de Informação Devem Ser Precisas**: Horário de trabalho deve seguir estritamente os resultados de `Simple Vector Store2`; respostas a perguntas vêm principalmente de `SerpAPI`, não fabrique informação.
- **Formato de Saída**: **No JSON de saída final, todas as quebras de linha no campo `body` devem usar tags `<br>`, não `\n`.**
```

(3) Configurar Ferramentas do Agente

Para a ferramenta `Simple Vector Store`, precisamos realizar configurações-chave para garantir que ela possa "ler" corretamente o conhecimento que armazenamos anteriormente:

- **Operation Mode**: `Retrieve Documents (As Tool for AI Agent)` (modo de leitura como ferramenta).
- **Memory Key**: Deve preencher a **exata mesma** Key como na primeira parte, i.e., `my_private_knowledge`.
- **Embeddings**: Deve usar o **exato mesmo** modelo `Embeddings Google Gemini` como na primeira parte.

Apenas quando o `Memory Key` e modelo `Embeddings` estão completamente consistentes o Agente pode usar a "chave" e "linguagem" corretas para acessar a base de conhecimento, como mostrado na Figura 5.48.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-11.png" alt="" width="90%"/>
  <p>Figura 5.48 Configuração de Ferramenta Simple Vector Store</p>
</div>

O parâmetro Description é a definição de descrição da ferramenta quando o AI Agent a chama. Aqui está o Prompt correspondente:

```json
Esta é a ferramenta Simple Vector Store2, usada para consultar minhas informações pessoais, especialmente meu horário de trabalho e política de resposta de email. Quando você precisa determinar se é atualmente horário de trabalho, ou precisa informar a outra parte quando responderei emails, você deve usar esta ferramenta.
```

Para Memory, a única coisa a notar é que aqui usamos o nome do tópico de cada caixa de correio como um identificador único para garantir unicidade de armazenamento. A Key é definida como `{{ $('Gmail').item.json.threadId }}`

(4) Enviar Resposta Final

O último passo é execução. Conecte a saída do nó `AI Agent` a um nó `Gmail`, defina **Operation** para `Send`. Use expressões n8n para associar o destinatário, assunto e corpo com os campos correspondentes nos dados JSON de saída por `AI Agent` para alcançar resposta automática de email, como mostrado na Figura 5.49.

- **To**: `{{ $('Gmail').item.json.From }}` (ou campo de remetente em outros gatilhos)
- **Subject**: `Re:  {{ $('Gmail').item.json.Subject }}`
- **Message**: `{{ $json.output }}`

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-12.png" alt="" width="90%"/>
  <p>Figura 5.49 Diagrama de Ferramenta de Resposta Final</p>
</div>

E quando o envio é bem-sucedido, você também pode receber informação de email de retorno real em sua caixa de correio pessoal, como mostrado na Figura 5.50.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-13.png" alt="" width="90%"/>
  <p>Figura 5.50 Formato de Email de Retorno de Caixa de Correio Pessoal</p>
</div>

Neste ponto, um atendimento ao cliente inteligente integrado baseado no nó `AI Agent` está completo. Você pode enviar um email de teste para verificar seus resultados de trabalho. Esta arquitetura tem extensibilidade extremamente forte. No futuro, você pode adicionar diretamente mais ferramentas (como calendários, bancos de dados, CRM, etc.) ao nó `AI Agent`. Você só precisa ensinar ao Agente como usá-las no Prompt para continuamente empoderar seu Agente com capacidades mais poderosas.

### 5.4.5 Análise das Vantagens e Limitações do n8n

Através da prática de construir um assistente de email inteligente do zero, ganhamos uma compreensão intuitiva do modo de trabalho do n8n. Como uma plataforma de automação low-code poderosa, n8n performa excelentemente em empoderar desenvolvimento de aplicação de Agente, mas não é onipotente. Como mostrado na Tabela 5.1, vamos objetivamente analisar suas vantagens e limitações potenciais.

<div align="center">
  <p>Tabela 5.1 Resumo das Vantagens e Limitações da Plataforma n8n</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/5-figures/n8n-14.png" alt="" width="90%"/>
</div>

Primeiro, a vantagem mais significativa do n8n está em sua **eficiência de desenvolvimento**. Ele abstrai lógica complexa em fluxos de trabalho visuais intuitivos. Seja recepção de email, decisão de IA, invocação de ferramenta ou resposta final, todo o fluxo de dados e cadeia de processamento são claros à primeira vista no canvas. Esta característica low-code reduz enormemente o limite técnico, permitindo que desenvolvedores construam e verifiquem rapidamente a lógica central de Agentes, encurtando enormemente a distância da ideia ao protótipo.

Segundo, a plataforma é **poderosa e altamente integrada**. n8n tem uma biblioteca de nós embutida rica que pode facilmente conectar centenas de serviços comuns como Gmail e Google Gemini. Mais importante, seu nó avançado `AI Agent` integra altamente modelo, memória e gerenciamento de ferramenta, permitindo-nos implementar tomada de decisão autônoma complexa com um nó, que é muito mais elegante e poderoso do que roteamento manual multi-nó tradicional. Ao mesmo tempo, para cenários que funções embutidas não podem cobrir, o nó `Code` também fornece a flexibilidade de escrever código customizado, garantindo o limite superior de funcionalidade.

Finalmente, no nível de **implantação e operação**, n8n suporta **implantação privada**, e é atualmente uma solução de Agente privada relativamente simples que pode implantar uma versão completa do projeto. Isso é crucial para empresas que valorizam segurança e privacidade de dados. Podemos implantar todo o serviço em nossos próprios servidores para garantir que informações sensíveis como emails internos e dados de clientes não saiam de nosso próprio ambiente, fornecendo uma base sólida para conformidade de aplicações de Agente.

Claro, toda ferramenta tem suas compensações. Enquanto aproveitamos a conveniência trazida pelo n8n, também devemos reconhecer suas limitações.

Por trás da **eficiência de desenvolvimento** está **depuração e tratamento de erro relativamente complicados**. Quando workflows se tornam complexos, uma vez que erros de formato de dados ocorrem, desenvolvedores podem precisar verificar a entrada e saída de cada nó um por um para localizar o problema, o que às vezes não é tão direto quanto definir breakpoints em código.

Em termos de funcionalidade, a maior limitação é refletida em seu **armazenamento embutido não persistente**. A `Simple Memory` e `Simple Vector Store` que usamos no caso são ambas baseadas em memória, o que significa que uma vez que o serviço n8n reinicia, todo histórico de conversa e bases de conhecimento serão perdidos. Isso é fatal para aplicações de ambiente de produção. Portanto, em implantação real, eles devem ser substituídos por bancos de dados persistentes externos como Redis e Pinecone, o que também aumenta custos adicionais de configuração e manutenção.

Além disso, em termos de **implantação e operação** e colaboração em equipe, o **controle de versão e colaboração multi-pessoa do n8n não são tão maduros quanto código tradicional**. Embora workflows possam ser exportados como arquivos JSON para gerenciamento, comparar suas mudanças está longe de ser tão claro quanto `git diff` código, e múltiplas pessoas editando o mesmo workflow ao mesmo tempo podem facilmente causar conflitos.

Finalmente, sobre **desempenho**, n8n pode totalmente atender a vasta maioria de automação empresarial e tarefas de Agente de média a baixa frequência. No entanto, para cenários que precisam lidar com solicitações ultra-alta concorrência, seu mecanismo de agendamento de nó pode trazer certa sobrecarga de desempenho, o que pode ser ligeiramente inferior a serviços implementados em código puro.

## 5.5 Resumo do Capítulo

Este capítulo introduziu sistematicamente os conceitos, métodos e práticas de construir aplicações de agente baseadas em plataformas low-code, marcando nossa importante transição de "código escrito à mão" para "desenvolvimento baseado em plataforma".

Na primeira seção, elaboramos sobre o background e valor da ascensão de plataformas low-code. Comparado com agentes puramente implementados em código no Capítulo 4, plataformas low-code reduzem significativamente o limite técnico, melhoram a eficiência de desenvolvimento e fornecem uma melhor experiência de depuração visual através de abordagens gráficas e modulares. Este "nível mais alto de abstração" permite aos desenvolvedores focar sua energia em lógica de negócios e engenharia de prompt em vez de detalhes de implementação subjacentes.

Subsequentemente, praticamos profundamente três plataformas representativas distintas:

**Coze** se destaca com sua experiência amigável zero-código e rico ecossistema de plugin. Através do caso "Resumo Diário de IA", experimentamos como integrar rapidamente informação multi-fonte através de configuração arrastar e soltar e publicar em múltiplas plataformas mainstream com um clique. Coze é particularmente adequado para usuários sem formação técnica e cenários que precisam verificar rapidamente ideias, mas suas limitações de não suportar MCP e incapacidade de exportar arquivos de configuração padronizados também valem a pena notar.

**Dify**, como uma plataforma de nível empresarial de código aberto, demonstra capacidades de desenvolvimento full-stack. O caso "Assistente Pessoal Super Agente" cobre múltiplos módulos como Q&A diário, otimização de copywriting, geração multimodal, análise de dados e integração de ferramenta MCP, mostrando completamente as poderosas capacidades de orquestração do Dify em cenários de negócios complexos. Seu rico mercado de plugin (8000+), métodos de implantação flexíveis e recursos de segurança de nível empresarial o tornam uma escolha ideal para desenvolvedores profissionais e equipes empresariais. No entanto, a curva de aprendizado relativamente íngreme e desafios de desempenho em cenários de alta concorrência também precisam ser pesados.

**n8n** abre outro caminho com sua capacidade única de "conexão". Através do caso "Assistente de Email Inteligente", vimos como incorporar perfeitamente capacidades de IA em processos de automação de negócios complexos. O nó AI Agent do n8n integra altamente modelos, memória e ferramentas, e combinado com suas centenas de nós predefinidos, pode alcançar soluções de automação altamente customizadas. Seu suporte para implantação privada é particularmente importante para empresas que valorizam segurança de dados. No entanto, a não persistência de armazenamento embutido e a imaturidade de controle de versão requerem processamento de engenharia adicional em ambientes de produção.

Através da prática comparativa das três plataformas, podemos tirar as seguintes sugestões de seleção:
- **Validação rápida de protótipo, usuários não técnicos**: Priorize Coze
- **Aplicações de nível empresarial, lógica de negócios complexa**: Priorize Dify
- **Integração profunda de negócios, processos de automação**: Priorize n8n

Vale a pena enfatizar que plataformas low-code não são destinadas a substituir desenvolvimento em código, mas fornecer uma escolha complementar. Em projetos reais, podemos mudar flexivelmente de acordo com as necessidades de diferentes estágios: usar plataformas low-code para verificar rapidamente ideias, usar código para alcançar controle de granularidade fina; usar plataformas para lidar com processos padronizados, usar código para lidar com lógica especial. Esta mentalidade de "desenvolvimento híbrido" é a melhor prática para engenharia de agente.

No próximo capítulo, exploraremos ainda mais frameworks de agente subjacentes para ajudar os leitores a construir aplicações mais confiáveis e interessantes.

## Exercícios

1. Este capítulo introduz três plataformas low-code distintas: `Coze`, `Dify` e `n8n`. Por favor, analise:

   - Quais são as diferenças em posicionamento central e filosofia de design entre essas três plataformas? Que pontos problemáticos no desenvolvimento de agentes eles respectivamente resolvem?
   - Plataformas low-code e desenvolvimento em código puro cada um tem suas vantagens e desvantagens. Além disso, há também um modo de "desenvolvimento híbrido" onde algumas funções são implementadas usando plataformas e algumas usando código. Pense sobre quais cenários cada um dos três modos de desenvolvimento é adequado? Por favor, dê exemplos.

2. No caso `Coze` na Seção 5.2, construímos um agente "Resumo Diário de IA". Por favor, estenda seu pensamento baseado neste caso:

   > **Dica**: Esta é uma questão de prática hands-on, operação real é recomendada

   - A geração atual de resumo é passivamente acionada (usuários perguntam ativamente). Como transformar este agente para que ele possa gerar automaticamente resumos e empurrá-los para grupos Feishu designados ou contas oficiais WeChat às 8h todos os dias?
   - A qualidade do resumo depende altamente do design de prompt. Por favor, tente otimizar o prompt na Seção 5.2.2 para tornar o resumo gerado mais profissional, com uma estrutura mais clara, ou adicionar novas funções como "análise de ponto quente" e "previsão de tendência".
   - `Coze` atualmente não suportar o protocolo `MCP` é considerado uma limitação importante (durante a escrita dos exercícios, embora `feature-mcp` esteja no [`Coze Studio Q4 2025 Product Roadmap`](https://github.com/coze-dev/coze-studio/issues/2218), ainda não foi implementado). Por favor, descreva brevemente o que é o protocolo `MCP`? Por que é importante? Se `Coze` suportar `MCP` no futuro, que novas possibilidades ele trará?

3. No caso `Dify` na Seção 5.3, construímos um "Assistente Pessoal Super Agente" totalmente funcional. Por favor, analise em profundidade:

   - O caso usa um "classificador de perguntas" para roteamento inteligente, distribuindo diferentes tipos de solicitações para diferentes sub-agentes. Quais são as vantagens desta arquitetura multi-agente? Se você não usar um classificador mas deixar um único agente lidar com todas as tarefas, que problemas você encontrará?
   - O módulo de consulta de dados precisa fornecer ao modelo grande informações claras de estrutura de tabela. Se o banco de dados tem 50 tabelas, cada uma com 20 campos, colocar diretamente todas as instruções `DDL` no prompt causará que o contexto seja muito longo. Por favor, projete uma solução mais inteligente para resolver este problema.
   - `Dify` suporta tanto modos de implantação local quanto em nuvem. Por favor, compare as diferenças entre esses dois modos em termos de segurança de dados, custo, desempenho e dificuldade de manutenção, e explique os cenários aplicáveis para cada.

4. No caso `n8n` na Seção 5.4, construímos um "Assistente de Email Inteligente". Por favor, pense sobre as seguintes perguntas:

   > **Dica**: Esta é uma questão de prática hands-on, operação real é recomendada

   - A `Simple Vector Store` e `Simple Memory` usadas no caso são ambas baseadas em memória, e dados serão perdidos após reinicialização do serviço. Por favor, consulte a documentação `n8n`, tente substituí-las por soluções de armazenamento persistente (como `Pinecone`, `Redis`, etc.), e explique o processo de configuração.
   - O assistente de email atual só pode lidar com emails de texto. Se o email enviado pelo usuário contém anexos (como documentos `PDF`, imagens), como você estenderia este workflow para habilitar o agente a entender conteúdo de anexo e fazer respostas correspondentes?
   - A vantagem central do `n8n` está em sua capacidade de "conexão". Por favor, projete um cenário de automação mais complexo: quando um cliente faz um pedido em uma plataforma de e-commerce, acionar automaticamente uma série de operações (enviar email de confirmação, atualizar banco de dados de inventário, notificar sistema de logística, registrar informação de cliente em `CRM`). Por favor, desenhe o diagrama de conexão de nó do workflow e explique configurações-chave.

5. Engenharia de prompt é igualmente crucial em plataformas low-code. Este capítulo mostra múltiplos casos de design de prompt de plataforma. Por favor, analise:

   - Compare os designs de prompt na Seção 5.2.2 (`Coze`), Seção 5.3.2 (`Dify`) e Seção 5.4.4 (`n8n`). Quais são as diferenças em estrutura, estilo e foco? Essas diferenças estão relacionadas a características de plataforma?
   - No "Módulo de Otimização de Copywriting" do `Dify`, o prompt requer saída "excedendo 500 palavras". Este requisito rígido sobre comprimento de saída é razoável? Em que situações o comprimento de saída deve ser limitado, e em que situações o modelo deve ser permitido a se expressar livremente?

6. Ferramentas e plugins são os métodos centrais de extensão de capacidade de plataformas low-code. Por favor, pense:

   - `Coze` tem uma loja de plugin rica, `Dify` tem um mercado de plugin de 8000+, e `n8n` tem centenas de nós predefinidos. Se nenhuma dessas três plataformas tem uma ferramenta específica que você precisa (como "conectar ao `API` do sistema interno da empresa"), como você resolveria isso?
   - Na Seção 5.3.2, usamos o protocolo `MCP` para integrar serviços como Amap e recomendações dietéticas. Por favor, pesquise e explique: Quais são as diferenças entre o protocolo `MCP` e `RESTful API` tradicional e `Tool Calling`? Por que `MCP` é chamado de "novo padrão" para invocação de ferramenta de agente?
   - Suponha que você queira desenvolver um plugin customizado para `Dify` para habilitá-lo a chamar o sistema de base de conhecimento interno de sua empresa. Por favor, consulte a documentação de desenvolvimento de plugin do `Dify` e delineie o processo de desenvolvimento e pontos técnicos-chave.

7. Seleção de plataforma é uma das decisões-chave para o sucesso de produtos de agente. Suponha que você seja o líder técnico de uma empresa startup, e a empresa planeja desenvolver as três aplicações de IA seguintes. Por favor, selecione a plataforma mais adequada para cada aplicação (`Coze`, `Dify`, `n8n`, ou desenvolvimento em código puro) e explique em detalhe:

   **Aplicação A**: Um mini-programa "Assistente de Escrita de IA" para usuários C-end, precisa ser lançado rapidamente para verificar demanda de mercado, com orçamento limitado, e a equipe tem apenas 1 engenheiro front-end e 1 gerente de produto.

   **Aplicação B**: Um "Sistema de Revisão de Contrato Inteligente" para clientes empresariais, precisa lidar com documentos legais sensíveis, requer que dados não possam sair do ambiente privado do cliente, e precisa integração profunda com o sistema OA existente do cliente e sistema de gerenciamento de documentos.

   **Aplicação C**: Uma "Ferramenta de Melhoria de Eficiência de R&D" interna, precisa automatizar múltiplos links de processo de R&D como revisão de código, geração de relatório de teste, rastreamento de bug e sincronização de progresso de projeto. A equipe tem fortes capacidades técnicas.

   Para cada aplicação, por favor, analise das seguintes dimensões (incluindo mas não limitado a):

   > **Dica**: Se capacidades de plataforma atendem requisitos, quão rápido pode ser lançado, custos de desenvolvimento, custos operacionais, dificuldade de iterações subsequentes, espaço para expansão de função futura

   - Viabilidade técnica
   - Eficiência de desenvolvimento
   - Controle de custo
   - Manutenibilidade
   - Escalabilidade
   - Segurança de dados e conformidade

## Referências

[1] Coze - Plataforma de desenvolvimento de aplicação de IA de próxima geração. https://www.coze.cn/

[2] Dify - Plataforma de desenvolvimento de aplicação LLM de código aberto. https://dify.ai/

[3] n8n - Ferramenta de automação de workflow. https://n8n.io/
