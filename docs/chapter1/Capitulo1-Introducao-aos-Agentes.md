# Capítulo 1: Introdução aos Agentes

<div align="right">
  <a href="./Chapter1-Introduction-to-Agents.md">English</a> | <a href="./第一章%20初识智能体.md">中文</a> | Português
</div>

Bem-vindo ao mundo dos agentes! Na era atual, onde a onda da inteligência artificial está varrendo o globo, os **Agentes** tornaram-se um dos conceitos centrais que impulsionam a transformação tecnológica e a inovação em aplicações. Seja sua aspiração tornar-se um pesquisador ou engenheiro no campo da IA, ou você espera compreender profundamente a vanguarda da tecnologia como observador, dominar a essência dos agentes será uma parte indispensável do seu sistema de conhecimento.

Portanto, neste capítulo, vamos retornar aos fundamentos e explorar juntos várias questões: O que é um agente? Quais são seus principais tipos? Como ele interage com o mundo em que vivemos? Através dessas discussões, esperamos estabelecer uma base sólida para seu aprendizado e exploração futuros.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-0.png" alt="Figure description" width="90%"/>
  <p>Figura 1.1 Loop básico de interação entre agente e ambiente</p>
</div>

## 1.1 O que é um Agente?

Ao explorar qualquer conceito complexo, é melhor começar com uma definição concisa. No campo da inteligência artificial, um agente é definido como qualquer entidade que pode perceber seu **Ambiente** através de **Sensores**, e **autonomamente** tomar **Ações** através de **Atuadores** para alcançar objetivos específicos.

Esta definição contém quatro elementos fundamentais da existência de um agente. O ambiente é o mundo externo no qual o agente opera. Para um veículo autônomo, o ambiente é o tráfego rodoviário em constante mudança; para um algoritmo de negociação, o ambiente é o mercado financeiro em constante transformação. O agente não está isolado do ambiente - ele percebe continuamente o estado ambiental através de seus sensores. Câmeras, microfones, radar ou fluxos de dados retornados por várias **Interfaces de Programação de Aplicações (APIs)** são todas extensões de suas capacidades perceptivas.

Após adquirir informações, o agente precisa tomar ações para influenciar o ambiente, mudando seu estado através de atuadores. Atuadores podem ser dispositivos físicos (como braços robóticos ou volantes) ou ferramentas virtuais (como executar código ou chamar um serviço).

No entanto, o que realmente dota um agente de "inteligência" é sua **Autonomia**. Um agente não é meramente um programa que responde passivamente a estímulos externos ou executa estritamente instruções predefinidas; ele pode tomar decisões independentes baseadas em suas percepções e estado interno para alcançar seus objetivos de design. Este loop fechado da percepção à ação forma a base de todo comportamento do agente, conforme mostrado na Figura 1.1.

### 1.1.1 Agentes da Perspectiva Tradicional

Antes da onda atual de **Grandes Modelos de Linguagem (Large Language Models, LLMs)**, os pioneiros da inteligência artificial já haviam passado décadas explorando e construindo o conceito de "agentes". Esses paradigmas, que agora chamamos de "agentes tradicionais", não são um conceito estático único, mas passaram por um caminho evolutivo claro do simples ao complexo, da reação passiva ao aprendizado ativo.

O ponto de partida dessa evolução é o estruturalmente mais simples **Agente Reflexo Simples (Simple Reflex Agent)**. Seu núcleo de tomada de decisão consiste em regras "condição-ação" explicitamente projetadas por engenheiros, conforme mostrado na Figura 1.2. Um termostato automático clássico funciona assim: se o sensor percebe que a temperatura ambiente está acima do valor definido, ele ativa o sistema de resfriamento.

Este tipo de agente depende inteiramente da entrada perceptiva atual e não possui memória ou capacidade preditiva. É como um instinto digitalizado - confiável e eficiente, mas, portanto, incapaz de lidar com tarefas complexas que requerem compreensão de contexto. Suas limitações levantam uma questão chave: O que um agente deve fazer se o estado atual do ambiente for insuficiente como única base para tomada de decisão?

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-1.png" alt="Figure description" width="90%"/>
  <p>Figura 1.2 Diagrama lógico de decisão de um agente reflexo simples</p>
</div>

Para responder a esta questão, pesquisadores introduziram o conceito de "estado" e desenvolveram **Agentes Reflexos Baseados em Modelo (Model-Based Reflex Agents)**. Este tipo de agente possui um **Modelo de Mundo (World Model)** interno usado para rastrear e compreender aspectos do ambiente que não podem ser diretamente percebidos. Ele tenta responder: "Como é o mundo agora?" Por exemplo, um veículo autônomo dirigindo através de um túnel, mesmo que sua câmera temporariamente não possa perceber o veículo à frente, seu modelo interno ainda manterá um julgamento sobre a existência, velocidade e posição estimada daquele veículo. Este modelo interno dá ao agente uma forma primitiva de "memória", tornando suas decisões não mais dependentes apenas da percepção instantânea, mas baseadas em uma compreensão mais coerente e completa do estado do mundo.

No entanto, meramente compreender o mundo não é suficiente - um agente precisa de objetivos claros. Isso levou ao desenvolvimento de **Agentes Baseados em Objetivos (Goal-Based Agents)**. Diferentemente dos dois tipos anteriores, seu comportamento não é mais reagir passivamente ao ambiente, mas selecionar ativamente e proativamente ações que possam levar a um estado futuro específico. A questão que este tipo de agente precisa responder é: "O que devo fazer para alcançar meu objetivo?" Um exemplo clássico é um sistema de navegação GPS: seu objetivo é chegar ao escritório, e o agente planejará uma rota ótima usando algoritmos de busca (como A*) baseados em dados de mapa (modelo de mundo). A capacidade central deste tipo de agente se reflete em sua consideração e planejamento para o futuro.

Indo além, os objetivos do mundo real muitas vezes não são singulares. Não queremos apenas chegar ao escritório, mas também queremos o menor tempo, a rota mais econômica em combustível e evitar congestionamentos. Quando múltiplos objetivos precisam ser equilibrados, surgem os **Agentes Baseados em Utilidade (Utility-Based Agents)**. Eles atribuem um valor de utilidade a cada possível estado do mundo, representando o nível de satisfação. O objetivo central do agente não é mais simplesmente alcançar um estado específico, mas maximizar a utilidade esperada. Ele precisa responder uma questão mais complexa: "Qual comportamento me trará o resultado mais satisfatório?" Esta arquitetura permite que o agente aprenda a equilibrar objetivos conflitantes, tornando suas decisões mais próximas da escolha racional humana.

Até agora, os agentes que discutimos, embora cada vez mais complexos em funcionalidade, ainda dependem do conhecimento prévio de designers humanos para sua lógica central de tomada de decisão, sejam regras, modelos ou funções de utilidade. E se um agente pudesse aprender autonomamente através da interação com o ambiente sem depender de predefinições?

Esta é a ideia central dos **Agentes de Aprendizagem (Learning Agents)**, e o **Aprendizado por Reforço (Reinforcement Learning, RL)** é o caminho mais representativo para realizar esta ideia. Um agente de aprendizagem contém um elemento de desempenho (os vários tipos de agentes que discutimos anteriormente) e um elemento de aprendizagem. O elemento de aprendizagem modifica continuamente a estratégia de tomada de decisão do elemento de desempenho observando os resultados das ações do elemento de desempenho no ambiente.

Imagine uma IA aprendendo a jogar xadrez. Ela pode começar fazendo movimentos aleatórios, mas quando finalmente vencer um jogo, o sistema lhe dá uma recompensa positiva. Através de extenso auto-jogo, o elemento de aprendizagem descobre gradualmente quais movimentos têm maior probabilidade de levar à vitória final. AlphaGo Zero é uma conquista marco dessa filosofia. No jogo complexo de Go, através do aprendizado por reforço, descobriu muitas estratégias eficazes que superam o conhecimento humano existente.

Do termostato simples aos carros com modelos internos, à navegação que pode planejar rotas, aos tomadores de decisão que sabem ponderar prós e contras, e finalmente aos aprendizes que podem evoluir por si mesmos através da experiência. Este caminho evolutivo demonstra a trajetória de desenvolvimento que a inteligência artificial tradicional passou na construção da inteligência de máquina. Eles estabeleceram uma base sólida e necessária para nossa compreensão de paradigmas de agentes mais avançados hoje.

### 1.1.2 Novo Paradigma Impulsionado por Grandes Modelos de Linguagem

O surgimento de grandes modelos de linguagem representados pelo **GPT (Generative Pre-trained Transformer)** está mudando significativamente os métodos de construção e limites de capacidade dos agentes. Agentes LLM impulsionados por grandes modelos de linguagem têm mecanismos de tomada de decisão fundamentalmente diferentes dos agentes tradicionais, dotando-os assim de uma série de características inteiramente novas.

Esta transformação pode ser claramente vista na comparação dos dois em múltiplas dimensões como motor central, fonte de conhecimento e método de interação, conforme mostrado na Tabela 1.1. Em resumo, as capacidades dos agentes tradicionais derivam da programação explícita e construção de conhecimento dos engenheiros, e seus padrões de comportamento são determinísticos e limitados; enquanto os agentes LLM, através do pré-treinamento em dados massivos, adquiriram modelos de mundo implícitos e poderosas capacidades emergentes, permitindo-lhes lidar com tarefas complexas de maneira mais flexível e geral.

<div align="center">
  <p>Tabela 1.1 Comparação central entre agentes tradicionais e agentes impulsionados por LLM</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-2.png" alt="Figure description" width="90%"/>
</div>

Esta diferença permite que agentes LLM processem diretamente instruções de linguagem natural de alto nível, ambíguas e ricas em contexto. Vamos usar um "assistente de viagem inteligente" como exemplo para ilustrar.

Antes do surgimento dos agentes LLM, planejar uma viagem geralmente significava que os usuários precisavam alternar manualmente entre múltiplas aplicações dedicadas (como clima, mapas, sites de reserva), com o próprio usuário desempenhando o papel de integração de informações e tomada de decisão. Um agente LLM, no entanto, pode integrar esse processo. Ao receber uma instrução ambígua como "planejar uma viagem para Xiamen", seu método de trabalho reflete os seguintes pontos:

- **Planejamento e Raciocínio**: O agente primeiro decompõe este objetivo de alto nível em uma série de subtarefas lógicas, por exemplo: `[Confirmar preferências de viagem] -> [Consultar informações do destino] -> [Rascunhar itinerário] -> [Reservar passagens e acomodação]`. Este é um processo de planejamento interno, impulsionado pelo modelo.
- **Uso de Ferramentas**: Ao executar o plano, o agente identifica lacunas de informação e chama proativamente ferramentas externas para preenchê-las. Por exemplo, ele chamará uma interface de consulta climática para obter clima em tempo real e, com base na informação "previsão de chuva", tenderá a recomendar atividades internas no planejamento subsequente.
- **Ajuste Dinâmico**: Durante a interação, o agente trata o feedback do usuário (como "este hotel excede o orçamento") como novas restrições e ajusta ações subsequentes de acordo, pesquisando novamente e recomendando opções que atendam aos novos requisitos. Todo o processo de "**verificar clima → ajustar itinerário → reservar hotel**" demonstra sua capacidade de modificar dinamicamente seu comportamento com base no contexto.

Em resumo, estamos mudando do desenvolvimento de ferramentas de automação especializadas para a construção de sistemas que podem resolver problemas autonomamente. O núcleo não é mais escrever código, mas orientar um "cérebro" geral para planejar, agir e aprender.

### 1.1.3 Tipos de Agentes

Seguindo a revisão da evolução dos agentes acima, esta seção classificará os agentes a partir de três dimensões complementares.

(1) **Classificação Baseada na Arquitetura de Decisão Interna**

A primeira dimensão de classificação é baseada na complexidade da arquitetura de decisão interna do agente. Esta perspectiva foi sistematicamente proposta em "Inteligência Artificial: Uma Abordagem Moderna"<sup>[1]</sup>. Como descrito na Seção 1.1.1, o caminho evolutivo dos agentes tradicionais em si constitui a escada de classificação mais clássica, cobrindo desde agentes **reativos** simples até agentes **baseados em modelo** que introduzem modelos internos, e depois para agentes **baseados em objetivos** e **baseados em utilidade** mais prospectivos. Adicionalmente, a **capacidade de aprendizagem** é uma meta-capacidade que pode ser dotada a todos os tipos acima, permitindo-lhes auto-melhorar através da experiência.

(2) **Classificação Baseada em Tempo e Reatividade**

Além da complexidade da arquitetura interna, agentes também podem ser classificados a partir da dimensão temporal do processamento de tomada de decisão. Esta perspectiva foca em se um agente age imediatamente após receber informações ou age após planejamento deliberado. Isso revela uma troca central no design de agentes: o equilíbrio entre **Reatividade**, que busca velocidade, e **Deliberação**, que busca soluções ótimas, conforme mostrado na Figura 1.3.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-3.png" alt="Figure description" width="90%"/>
  <p>Figura 1.3 Relação entre tempo de decisão e qualidade do agente</p>
</div>

- **Agentes Reativos**

Este tipo de agente faz respostas quase instantâneas a estímulos ambientais com latência de decisão extremamente baixa. Eles normalmente seguem um mapeamento direto da percepção à ação, sem ou com planejamento futuro mínimo. Os agentes **reativos simples** e **baseados em modelo** mencionados acima pertencem a esta categoria.

Sua vantagem central está na **velocidade rápida e baixo custo computacional**, o que é crucial em ambientes dinâmicos que requerem tomada de decisão rápida. Por exemplo, o sistema de airbag de um veículo deve reagir em milissegundos após uma colisão - qualquer atraso poderia levar a consequências sérias; similarmente, robôs de negociação de alta frequência devem confiar na tomada de decisão reativa para capturar oportunidades de mercado fugazes. No entanto, o custo dessa velocidade é a "miopia". Devido à falta de planejamento de longo prazo, agentes reativos caem facilmente em ótimos locais e lutam para completar tarefas complexas que requerem coordenação de múltiplos passos.

- **Agentes Deliberativos**

Em contraste com os agentes reativos, agentes deliberativos (ou de planejamento) se envolvem em pensamento e planejamento complexos antes de agir. Eles não reagem imediatamente às percepções, mas primeiro usam seu modelo de mundo interno para explorar sistematicamente várias possibilidades futuras, avaliar as consequências de diferentes sequências de ações, na esperança de encontrar um caminho ótimo para alcançar objetivos. Agentes **baseados em objetivos** e **baseados em utilidade** são agentes deliberativos típicos.

Seu processo de tomada de decisão pode ser comparado a um jogador de xadrez. Eles não olham apenas para o movimento imediato, mas antecipam possíveis respostas do oponente e planejam movimentos subsequentes, até dezenas de movimentos adiante. Esta capacidade deliberativa permite que lidem com tarefas complexas que requerem visão de longo prazo, como formular um plano de negócios ou planejar uma viagem de longa distância. Sua vantagem está na natureza estratégica e previsão de suas decisões. No entanto, o outro lado dessa vantagem são altos custos de tempo e computacionais. Em ambientes que mudam rapidamente, quando um agente deliberativo ainda está profundamente pensando, o melhor momento para agir pode ter passado há muito.

- **Agentes Híbridos**

Tarefas complexas no mundo real frequentemente requerem tanto reações imediatas quanto planejamento de longo prazo. Por exemplo, o assistente de viagem inteligente que mencionamos anteriormente precisa ajustar recomendações baseado no feedback imediato do usuário (como "este hotel é muito caro") (reatividade), enquanto também ser capaz de planejar um itinerário de viagem completo de múltiplos dias (deliberação). Portanto, agentes híbridos surgiram, visando combinar as vantagens de ambos e alcançar um equilíbrio entre reação e planejamento.

Uma arquitetura híbrida clássica é o design hierárquico: a camada inferior é um módulo reativo rápido que lida com emergências e ações básicas; a camada superior é um módulo de planejamento deliberativo responsável por formular objetivos de longo prazo. Agentes LLM modernos demonstram um modo híbrido mais flexível. Eles normalmente operam em um loop "pensar-agir-observar", integrando habilmente ambos os modos:

- **Raciocínio**: Na fase de "pensamento", o LLM analisa a situação atual e planeja a próxima ação razoável. Este é um processo deliberativo.
- **Agir e Observar**: Nas fases de "agir" e "observar", o agente interage com ferramentas externas ou o ambiente e recebe feedback imediatamente. Este é um processo reativo.

Através dessa abordagem, o agente decompõe uma grande tarefa que requer planejamento de longo prazo em uma série de micro-loops "planejamento-reação". Isso permite que ele responda flexivelmente a mudanças ambientais imediatas enquanto completa objetivos complexos de longo prazo através de passos coerentes.

**(3) Classificação Baseada em Representação de Conhecimento**

Esta é uma dimensão de classificação mais fundamental que explora em que forma o conhecimento usado pelos agentes para tomada de decisão existe em suas "mentes". Esta questão está no centro de um debate que durou mais de meio século no campo da inteligência artificial e moldou duas culturas de IA distintamente diferentes.

- **IA Simbólica**

Simbolismo, frequentemente chamado de inteligência artificial tradicional, tem uma crença central: a inteligência deriva de operações lógicas sobre símbolos. Os símbolos aqui são entidades legíveis por humanos (como palavras, conceitos), e as operações seguem regras lógicas estritas, conforme mostrado no lado esquerdo da Figura 1.4. Isso é como um bibliotecário meticuloso organizando o conhecimento do mundo em bases de regras claras e grafos de conhecimento.

Sua principal vantagem está na transparência e interpretabilidade. Como os passos de raciocínio são explícitos, seu processo de tomada de decisão pode ser totalmente rastreado, o que é crucial em campos de alto risco como finanças e saúde. No entanto, seu "calcanhar de Aquiles" está na fragilidade: depende de um sistema de regras completo, mas no mundo real cheio de ambiguidade e exceções, qualquer situação nova não coberta pode levar à falha do sistema, que é o chamado "gargalo de aquisição de conhecimento".

- **IA Sub-simbólica**

Sub-simbolismo, ou conexionismo, fornece uma imagem completamente diferente. Aqui, o conhecimento não são regras explícitas, mas está implicitamente distribuído em uma rede complexa composta por numerosos neurônios, representando padrões estatísticos aprendidos de dados massivos. Redes neurais e aprendizado profundo são seus representantes.

Conforme mostrado no meio da Figura 1.4, se a IA simbólica é um bibliotecário, então a IA sub-simbólica é como uma criança balbuciante. Eles não aprendem a reconhecer gatos aprendendo regras como "gatos têm quatro pernas, são peludos e miam", mas depois de ver milhares de fotos de gatos, a rede neural em seu cérebro pode identificar o padrão visual do conceito "gato". O poder desta abordagem está em sua capacidade de reconhecimento de padrões e robustez a dados ruidosos. Ela pode facilmente lidar com dados não estruturados como imagens e sons, que são tarefas extremamente difíceis para a IA simbólica.

No entanto, essa poderosa capacidade intuitiva também vem com opacidade. Sistemas sub-simbólicos são tipicamente vistos como uma **Caixa Preta**. Ele pode identificar um gato em uma imagem com precisão surpreendente, mas se você perguntar "por que você acha que isso é um gato?", provavelmente não conseguirá fornecer uma explicação logicamente sólida. Adicionalmente, ele tem desempenho ruim em tarefas de raciocínio lógico puro e às vezes produz alucinações que parecem razoáveis mas são factualmente incorretas.

- **IA Neuro-Simbólica**

Por muito tempo, os dois campos de simbolismo e sub-simbolismo se desenvolveram como duas linhas paralelas. Para superar as limitações dos dois paradigmas acima, uma ideia de "grande reconciliação" começou a emergir, que é a IA neuro-simbólica, também chamada híbrida neuro-simbólica. Seu objetivo é fundir as vantagens de ambos os paradigmas, criando um agente híbrido que pode tanto aprender com dados como redes neurais quanto realizar raciocínio lógico como sistemas simbólicos. Ela tenta preencher a lacuna entre percepção e cognição, intuição e racionalidade. A teoria do sistema dual do economista ganhador do Prêmio Nobel Daniel Kahneman proposta em seu livro "Pensando, Rápido e Devagar" fornece uma excelente analogia para entender o neuro-simbolismo<sup>[2]</sup>, conforme mostrado na Figura 1.4:

- **Sistema 1** é um modo de pensamento rápido, intuitivo e paralelo, similar à poderosa capacidade de reconhecimento de padrões da IA sub-simbólica.
- **Sistema 2** é pensamento deliberativo lento, metódico, baseado em lógica, assim como o processo de raciocínio da IA simbólica.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-4.png" alt="Figure description" width="90%"/>
  <p>Figura 1.4 Paradigmas de representação de conhecimento de simbolismo, sub-simbolismo e híbrido neuro-simbólico</p>
</div>

A inteligência humana deriva do trabalho colaborativo desses dois sistemas. Similarmente, uma IA verdadeiramente robusta também precisa combinar os pontos fortes de ambos. Agentes impulsionados por grandes modelos de linguagem são um excelente exemplo prático de neuro-simbolismo. Seu núcleo é uma enorme rede neural, dando-lhe capacidades de reconhecimento de padrões e geração de linguagem. No entanto, quando funciona, gera uma série de passos intermediários estruturados, como pensamentos, planos ou chamadas de API, que são todos símbolos explícitos e operáveis. Através dessa abordagem, alcança uma fusão preliminar de percepção e cognição, intuição e racionalidade.

## 1.2 Composição e Princípios Operacionais dos Agentes

### 1.2.1 Definição do Ambiente de Tarefa

Para entender como um agente opera, devemos primeiro entender o **ambiente de tarefa** no qual ele opera. No campo da inteligência artificial, o **modelo PEAS** é tipicamente usado para descrever precisamente um ambiente de tarefa, analisando sua **Medida de desempenho (Performance measure), Ambiente (Environment), Atuadores (Actuators) e Sensores (Sensors)**. Tomando o assistente de viagem inteligente mencionado acima como exemplo, a Tabela 1.2 abaixo mostra como usar o modelo PEAS para especificar seu ambiente de tarefa.

<div align="center">
  <p>Tabela 1.2 Descrição PEAS do assistente de viagem inteligente</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-6.png" alt="Figure description" width="90%"/>
</div>

Na prática, o ambiente digital no qual os agentes LLM operam exibe várias características complexas que afetam diretamente o design do agente.

Primeiro, o ambiente é tipicamente **parcialmente observável**. Por exemplo, quando um assistente de viagem consulta voos, ele não pode obter todas as informações de assentos em tempo real de todas as companhias aéreas de uma só vez. Ele só pode ver dados parciais retornados pela API de reserva de voo que chama, o que requer que o agente tenha memória (lembrando rotas consultadas) e capacidades de exploração (tentando diferentes datas de consulta).

Segundo, os resultados das ações nem sempre são determinísticos. Baseado na previsibilidade dos resultados, ambientes podem ser divididos em **determinísticos** e **estocásticos**. O ambiente de tarefa de um assistente de viagem é um ambiente estocástico típico. Quando ele busca preços de passagens, duas chamadas adjacentes podem retornar preços de passagens e números de assentos restantes diferentes, exigindo que o agente tenha a capacidade de lidar com incerteza, monitorar mudanças e tomar decisões oportunas.

Adicionalmente, pode haver outros atores no ambiente, formando um ambiente **multi-agente**. Para um assistente de viagem, comportamentos de reserva de outros usuários, outros scripts automatizados e até sistemas de precificação dinâmica de companhias aéreas são todos outros "agentes" no ambiente. Suas ações (por exemplo, reservar o último bilhete com desconto) mudam diretamente o estado do ambiente no qual o assistente de viagem opera, colocando maiores demandas na resposta rápida e seleção de estratégia do agente.

Finalmente, quase todas as tarefas ocorrem em ambientes **sequenciais** e **dinâmicos**. "Sequencial" significa que ações atuais afetam o futuro; enquanto "dinâmico" significa que o próprio ambiente pode mudar enquanto o agente está tomando decisões. Isso requer que o loop "perceber-pensar-agir-observar" do agente seja capaz de se adaptar rápida e flexivelmente a um mundo em constante mudança.

### 1.2.2 Mecanismo Operacional do Agente

Após definir o ambiente de tarefa no qual um agente opera, vamos explorar seu mecanismo operacional central. Um agente não completa tarefas de uma só vez, mas interage com o ambiente através de um loop contínuo. Este mecanismo central é chamado de **Loop do Agente (Agent Loop)**. Conforme mostrado na Figura 1.5, este loop descreve o processo de interação dinâmica entre o agente e o ambiente, formando a base de seu comportamento autônomo.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-5.png" alt="Figure description" width="90%"/>
  <p>Figura 1.5 Loop básico de interação agente-ambiente</p>
</div>

Este loop contém principalmente os seguintes estágios interconectados:

1. **Percepção**: Este é o ponto de partida do loop. O agente recebe informações de entrada do ambiente através de seus sensores (por exemplo, portas de escuta de API, interfaces de entrada do usuário). Esta informação, ou seja, **Observação**, pode ser tanto a instrução inicial do usuário quanto feedback sobre mudanças de estado ambiental causadas pela ação anterior.
2. **Pensamento**: Após receber informações de observação, o agente entra em seu estágio central de tomada de decisão. Para agentes LLM, este é tipicamente um processo de raciocínio interno impulsionado por grandes modelos de linguagem. Conforme mostrado na figura, o estágio de "pensamento" pode ser subdividido em dois elos-chave:
   - **Planejamento**: Baseado em observações atuais e sua memória interna, o agente atualiza sua compreensão da tarefa e ambiente e formula ou ajusta um plano de ação. Isso pode envolver decompor objetivos complexos em uma série de subtarefas mais específicas.
   - **Seleção de Ferramenta**: Baseado no plano atual, o agente seleciona a ferramenta mais adequada de sua biblioteca de ferramentas disponíveis para executar o próximo passo e determina os parâmetros específicos necessários para chamar aquela ferramenta.
3. **Ação**: Após a tomada de decisão estar completa, o agente executa ações específicas através de seus atuadores. Isso tipicamente se manifesta como chamar uma ferramenta selecionada (como um interpretador de código ou API de mecanismo de busca), influenciando assim o ambiente com a intenção de mudar seu estado.

Ação não é o fim do loop. A ação do agente causa uma **mudança de estado** no **ambiente**, que então produz uma nova **observação** como feedback de resultado. Esta nova observação é capturada pelo sistema de percepção do agente na próxima rodada do loop, formando um loop fechado contínuo "perceber-pensar-agir-observar". É através da repetição contínua deste loop que o agente avança gradualmente a tarefa, evoluindo do estado inicial em direção ao estado de objetivo.

### 1.2.3 Percepção e Ação do Agente

Na prática de engenharia, para permitir que LLMs impulsionem efetivamente este loop, precisamos de um **Protocolo de Interação** claro para regular a troca de informações entre ele e o ambiente.

Em muitos frameworks de agentes modernos, este protocolo é incorporado na definição estruturada de cada saída do agente. A saída do agente não é mais uma única resposta em linguagem natural, mas um pedaço de texto seguindo um formato específico que mostra explicitamente seu processo de raciocínio interno e decisão final.

Esta estrutura tipicamente contém duas partes centrais:

- **Thought (Pensamento)**: Este é um "instantâneo" da tomada de decisão interna do agente. Ele articula em linguagem natural como o agente analisa a situação atual, revisa os resultados da observação do passo anterior, se envolve em auto-reflexão e decomposição de problemas, e finalmente planeja a próxima ação específica.
- **Action (Ação)**: Esta é a operação específica que o agente decide impor ao ambiente baseado em seu pensamento, tipicamente expressa como uma chamada de função.

Por exemplo, um agente planejando uma viagem pode gerar a seguinte saída formatada:

```Bash
Thought: O usuário quer saber o clima em Pequim. Preciso chamar a ferramenta de consulta climática.
Action: get_weather("Beijing")
```

O campo `Action` aqui constitui uma instrução para o mundo externo. Um **Analisador (Parser)** externo capturará esta instrução e chamará a função `get_weather` correspondente.

Após a ação ser executada, o ambiente retorna um resultado. Por exemplo, a função `get_weather` pode retornar um objeto JSON contendo dados climáticos detalhados. No entanto, dados brutos legíveis por máquina (como JSON) tipicamente contêm informações redundantes que o LLM não precisa focar, e o formato não está conforme os hábitos de processamento de linguagem natural.

Portanto, uma responsabilidade importante do sistema de percepção é desempenhar o papel de um sensor: processar e encapsular esta saída bruta em texto de linguagem natural conciso e claro, ou seja, observação.

```Bash
Observation: O clima atual de Pequim é ensolarado, temperatura 25 graus Celsius, brisa leve.
```

Este texto `Observation` é devolvido ao agente como a principal informação de entrada para a próxima rodada do loop, para ele conduzir uma nova rodada de `Thought` e `Action`.

Em resumo, através deste loop rigoroso composto por Thought, Action e Observation, agentes LLM podem efetivamente combinar suas capacidades internas de raciocínio de linguagem com informações reais e capacidades de operação de ferramentas do ambiente externo.

## 1.3 Experiência Prática: Implementando Seu Primeiro Agente em 5 Minutos

Nas seções anteriores, aprendemos sobre o ambiente de tarefa do agente, mecanismo operacional central e o paradigma de interação `Thought-Action-Observation`. Embora o conhecimento teórico seja importante, a melhor maneira de aprender é através da prática. Nesta seção, vamos guiá-lo a construir um assistente de viagem inteligente funcional do zero usando algumas linhas simples de código Python. Este processo seguirá o loop teórico que acabamos de aprender, permitindo que você experimente intuitivamente como um agente "pensa" e interage com "ferramentas" externas. Vamos começar!

Neste caso, nosso objetivo é construir um assistente de viagem inteligente que pode lidar com tarefas passo a passo. A tarefa do usuário a ser resolvida é definida como: "Olá, por favor me ajude a verificar o clima de hoje em Pequim, e então recomendar uma atração turística adequada baseada no clima." Para completar esta tarefa, o agente deve demonstrar capacidades claras de planejamento lógico. Ele precisa primeiro chamar a ferramenta de consulta climática e usar os resultados da observação obtidos como base para o próximo passo. Na próxima rodada do loop, então chama a ferramenta de recomendação de atrações para chegar à sugestão final.

### 1.3.1 Preparação

Para acessar APIs da web de um programa Python, precisamos de uma biblioteca HTTP. `requests` é a escolha mais popular e fácil de usar na comunidade Python. `tavily-python` é um poderoso cliente de API de busca de IA para obter resultados de busca na web em tempo real, que pode ser obtido registrando-se no [site oficial](https://www.tavily.com/). `openai` é o SDK oficial Python fornecido pela OpenAI para chamar serviços de grandes modelos de linguagem como GPT. Por favor, instale-os primeiro com o seguinte comando:

```bash
pip install requests tavily-python openai
```

(1) Template de Instrução

A chave para impulsionar um LLM real está na **Engenharia de Prompts (Prompt Engineering)**. Precisamos projetar um "template de instrução" que diga ao LLM que papel ele deve desempenhar, quais ferramentas ele tem e como formatar seu pensamento e ações. Este é o "manual" para nosso agente, que será passado ao LLM como `system_prompt`.

```
AGENT_SYSTEM_PROMPT = """
Você é um assistente de viagem inteligente. Sua tarefa é analisar solicitações de usuários e usar ferramentas disponíveis para resolver problemas passo a passo.

# Ferramentas Disponíveis:
- `get_weather(city: str)`: Consultar clima em tempo real para uma cidade especificada.
- `get_attraction(city: str, weather: str)`: Buscar atrações turísticas recomendadas baseadas em cidade e clima.

# Formato de Ação:
Sua resposta deve seguir estritamente o seguinte formato. Primeiro é seu processo de pensamento, depois a ação específica que você deseja executar. Cada resposta deve gerar apenas um par Thought-Action:
Thought: [Aqui está seu processo de pensamento e plano do próximo passo]
Action: [Aqui está a ferramenta que você deseja chamar, no formato function_name(arg_name="arg_value")]

# Conclusão da Tarefa:
Quando você tiver coletado informações suficientes para responder à pergunta final do usuário, você deve usar `finish(answer="...")` após o campo Action: para gerar a resposta final.

Vamos começar!
"""
```

(2) Ferramenta 1: Consultar Clima Real

Vamos usar o serviço gratuito de consulta climática `wttr.in`, que pode retornar dados climáticos para uma cidade especificada em formato JSON. Aqui está o código para implementar esta ferramenta:

```python
import requests
import json

def get_weather(city: str) -> str:
    """
    Consultar informações climáticas reais chamando a API wttr.in.
    """
    # Endpoint da API, solicitamos dados em formato JSON
    url = f"https://wttr.in/{city}?format=j1"

    try:
        # Fazer solicitação de rede
        response = requests.get(url)
        # Verificar se o código de status da resposta é 200 (sucesso)
        response.raise_for_status()
        # Analisar dados JSON retornados
        data = response.json()

        # Extrair condições climáticas atuais
        current_condition = data['current_condition'][0]
        weather_desc = current_condition['weatherDesc'][0]['value']
        temp_c = current_condition['temp_C']

        # Formatar como retorno em linguagem natural
        return f"{city} clima atual: {weather_desc}, temperatura {temp_c} graus Celsius"

    except requests.exceptions.RequestException as e:
        # Lidar com erros de rede
        return f"Erro: Problema de rede encontrado ao consultar clima - {e}"
    except (KeyError, IndexError) as e:
        # Lidar com erros de análise de dados
        return f"Erro: Falha ao analisar dados climáticos, nome da cidade pode ser inválido - {e}"
```

(3) Ferramenta 2: Buscar e Recomendar Atrações Turísticas

Vamos definir uma nova ferramenta `search_attraction` que busca na internet atrações adequadas baseadas em cidade e condições climáticas:

```python
import os
from tavily import TavilyClient

def get_attraction(city: str, weather: str) -> str:
    """
    Baseado em cidade e clima, usar API de Busca Tavily para buscar e retornar recomendações de atrações otimizadas.
    """
    # 1. Ler chave de API da variável de ambiente
    api_key = os.environ.get("TAVILY_API_KEY")
    if not api_key:
        return "Erro: Variável de ambiente TAVILY_API_KEY não configurada."

    # 2. Inicializar cliente Tavily
    tavily = TavilyClient(api_key=api_key)

    # 3. Construir uma consulta precisa
    query = f"'{city}' atrações turísticas mais valiosas e razões em clima '{weather}'"

    try:
        # 4. Chamar API, include_answer=True retornará uma resposta abrangente
        response = tavily.search(query=query, search_depth="basic", include_answer=True)

        # 5. Resultados retornados pelo Tavily já são muito limpos e podem ser usados diretamente
        # response['answer'] é uma resposta resumida baseada em todos os resultados de busca
        if response.get("answer"):
            return response["answer"]

        # Se não houver resposta abrangente, formatar resultados brutos
        formatted_results = []
        for result in response.get("results", []):
            formatted_results.append(f"- {result['title']}: {result['content']}")

        if not formatted_results:
             return "Desculpe, nenhuma recomendação de atração turística relevante encontrada."

        return "Baseado em busca, encontrei as seguintes informações para você:\n" + "\n".join(formatted_results)

    except Exception as e:
        return f"Erro: Problema ocorreu ao executar busca Tavily - {e}"
```

Finalmente, colocamos todas as funções de ferramentas em um dicionário para o loop principal chamar:

```python
# Colocar todas as funções de ferramentas em um dicionário para fácil chamada subsequente
available_tools = {
    "get_weather": get_weather,
    "get_attraction": get_attraction,
}
```

### 1.3.2 Conectando a Grandes Modelos de Linguagem

Atualmente, muitos provedores de serviços LLM (incluindo OpenAI, Azure, e numerosos frameworks de serviço de modelo de código aberto como Ollama, vLLM, etc.) seguem especificações de interface similares à API OpenAI. Esta padronização traz grande conveniência para desenvolvedores. A capacidade de decisão autônoma do agente vem do LLM. Vamos implementar um cliente universal `OpenAICompatibleClient` que pode se conectar a qualquer serviço LLM compatível com a especificação de interface OpenAI.

```python
from openai import OpenAI

class OpenAICompatibleClient:
    """
    Um cliente para chamar qualquer serviço LLM compatível com a interface OpenAI.
    """
    def __init__(self, model: str, api_key: str, base_url: str):
        self.model = model
        self.client = OpenAI(api_key=api_key, base_url=base_url)

    def generate(self, prompt: str, system_prompt: str) -> str:
        """Chamar API LLM para gerar resposta."""
        print("Chamando grande modelo de linguagem...")
        try:
            messages = [
                {'role': 'system', 'content': system_prompt},
                {'role': 'user', 'content': prompt}
            ]
            response = self.client.chat.completions.create(
                model=self.model,
                messages=messages,
                stream=False
            )
            answer = response.choices[0].message.content
            print("Grande modelo de linguagem respondeu com sucesso.")
            return answer
        except Exception as e:
            print(f"Erro ocorreu ao chamar API LLM: {e}")
            return "Erro: Erro ocorreu ao chamar serviço de modelo de linguagem."
```

Para instanciar esta classe, você precisa fornecer três peças de informação: `API_KEY`, `BASE_URL` e `MODEL_ID`. Os valores específicos dependem do provedor de serviço que você usa (como OpenAI oficial, Azure ou modelos locais como Ollama). Se você ainda não tem acesso a estes, pode consultar [1.2 Configuração de API](https://datawhalechina.github.io/handy-multi-agent/#/chapter1/1.2.api-setup) em outro tutorial do Datawhale.

### 1.3.3 Executando o Loop de Ação

O loop principal abaixo integrará todos os componentes e impulsionará o LLM a tomar decisões através de prompts formatados.

```python
import re

# --- 1. Configurar cliente LLM ---
# Por favor substitua isto pelas credenciais e endereço correspondentes para o serviço que você usa
API_KEY = "YOUR_API_KEY"
BASE_URL = "YOUR_BASE_URL"
MODEL_ID = "YOUR_MODEL_ID"
TAVILY_API_KEY="YOUR_Tavily_KEY"
os.environ['TAVILY_API_KEY'] = "YOUR_TAVILY_API_KEY"

llm = OpenAICompatibleClient(
    model=MODEL_ID,
    api_key=API_KEY,
    base_url=BASE_URL
)

# --- 2. Inicializar ---
user_prompt = "Olá, por favor me ajude a verificar o clima de hoje em Pequim, e então recomendar uma atração turística adequada baseada no clima."
prompt_history = [f"Solicitação do usuário: {user_prompt}"]

print(f"Entrada do usuário: {user_prompt}\n" + "="*40)

# --- 3. Executar loop principal ---
for i in range(5): # Definir número máximo de loops
    print(f"--- Loop {i+1} ---\n")

    # 3.1. Construir Prompt
    full_prompt = "\n".join(prompt_history)

    # 3.2. Chamar LLM para pensar
    llm_output = llm.generate(full_prompt, system_prompt=AGENT_SYSTEM_PROMPT)
    # Truncar pares Thought-Action extras que o modelo pode gerar
    match = re.search(r'(Thought:.*?Action:.*?)(?=\n\s*(?:Thought:|Action:|Observation:)|\Z)',
                    llm_output, re.DOTALL)
    if match:
        truncated = match.group(1).strip()
        if truncated != llm_output.strip():
            llm_output = truncated
            print("Truncados pares Thought-Action extras")
    print(f"Saída do modelo:\n{llm_output}\n")
    prompt_history.append(llm_output)

    # 3.3. Analisar e executar ação
    action_match = re.search(r"Action: (.*)", llm_output, re.DOTALL)
    if not action_match:
        print("Erro de análise: Action não encontrada na saída do modelo.")
        break
    action_str = action_match.group(1).strip()

    if action_str.startswith("finish"):
        final_answer = re.search(r'finish\(answer="(.*)"\)', action_str).group(1)
        print(f"Tarefa completa, resposta final: {final_answer}")
        break

    tool_name = re.search(r"(\w+)\(", action_str).group(1)
    args_str = re.search(r"\((.*)\)", action_str).group(1)
    kwargs = dict(re.findall(r'(\w+)="([^"]*)"', args_str))

    if tool_name in available_tools:
        observation = available_tools[tool_name](**kwargs)
    else:
        observation = f"Erro: Ferramenta indefinida '{tool_name}'"

    # 3.4. Registrar resultados de observação
    observation_str = f"Observation: {observation}"
    print(f"{observation_str}\n" + "="*40)
    prompt_history.append(observation_str)
```

Através dos passos acima, construímos um agente completo impulsionado por um LLM real. Seu núcleo está na combinação de "ferramentas" e "engenharia de prompts", que é precisamente a essência de design dos atuais frameworks de agentes mainstream (como LangChain, LlamaIndex, etc.).

### 1.3.4 Análise de Caso em Execução

A seguinte saída demonstra completamente um processo de execução bem-sucedido do agente. Através da análise deste loop de três rodadas, podemos ver claramente as capacidades centrais do agente em resolver problemas.

```bash
Entrada do usuário: Olá, por favor me ajude a verificar o clima de hoje em Pequim, e então recomendar uma atração turística adequada baseada no clima.
========================================
--- Loop 1 ---

Chamando grande modelo de linguagem...
Grande modelo de linguagem respondeu com sucesso.
Saída do modelo:
Thought: Primeiro preciso obter o clima de Pequim hoje, então recomendar atrações turísticas baseadas no clima.
Action: get_weather(city="Beijing")

Observation: Pequim clima atual: Ensolarado, temperatura 26 graus Celsius
========================================
--- Loop 2 ---

Chamando grande modelo de linguagem...
Grande modelo de linguagem respondeu com sucesso.
Saída do modelo:
Thought: Agora sei que o clima de Pequim hoje é ensolarado com temperatura moderada, posso recomendar uma atração turística adequada baseada nesta informação.
Action: get_attraction(city="Beijing", weather="Sunny")

Observation: As atrações turísticas mais valiosas em Pequim em dias ensolarados são o Palácio de Verão por suas belas vistas do lago e arquitetura antiga. Outra recomendação é a Grande Muralha por sua paisagem espetacular e significado histórico.
========================================
--- Loop 3 ---

Chamando grande modelo de linguagem...
Grande modelo de linguagem respondeu com sucesso.
Saída do modelo:
Thought: Obtive duas sugestões de atrações adequadas para dias ensolarados, agora posso fornecer uma resposta satisfatória ao usuário baseada nesta informação.
Action: finish(answer="O clima de hoje em Pequim é ensolarado com temperatura de 26 graus Celsius, muito adequado para atividades ao ar livre. Recomendo que você visite o Palácio de Verão para aproveitar as belas vistas do lago e arquitetura antiga, ou vá à Grande Muralha para experimentar sua paisagem espetacular e profundo significado histórico. Espero que você tenha uma viagem agradável!")

Tarefa completa, resposta final: O clima de hoje em Pequim é ensolarado com temperatura de 26 graus Celsius, muito adequado para atividades ao ar livre. Recomendo que você visite o Palácio de Verão para aproveitar as belas vistas do lago e arquitetura antiga, ou vá à Grande Muralha para experimentar sua paisagem espetacular e profundo significado histórico. Espero que você tenha uma viagem agradável!
```

Este caso simples de assistente de viagem concentra-se em demonstrar as quatro capacidades básicas de um agente baseado no paradigma `Thought-Action-Observation`: decomposição de tarefa, invocação de ferramenta, compreensão de contexto e síntese de resultado. É através da iteração contínua deste loop que o agente pode transformar uma intenção vaga do usuário em uma série de passos específicos e executáveis e finalmente alcançar o objetivo.

## 1.4 Modos de Colaboração de Aplicações de Agentes

Na seção anterior, obtivemos uma compreensão profunda do loop operacional interno de um agente construindo um nós mesmos. No entanto, em cenários de aplicação mais amplos, nosso papel está cada vez mais se transformando em usuários e colaboradores. Baseado no papel do agente em tarefas e grau de autonomia, seus modos de colaboração são divididos principalmente em dois tipos: um é como uma ferramenta eficiente profundamente integrada em nosso fluxo de trabalho; o outro é como um colaborador autônomo trabalhando com outros agentes para completar objetivos complexos.

### 1.4.1 Agentes como Ferramentas de Desenvolvedor

Neste modo, agentes são profundamente integrados nos fluxos de trabalho dos desenvolvedores como poderosas ferramentas auxiliares. Eles aprimoram em vez de substituir o papel do desenvolvedor, automatizando tarefas tediosas e repetitivas para que os desenvolvedores possam se concentrar mais no trabalho criativo central. Esta abordagem de colaboração humano-máquina melhora muito a eficiência e qualidade do desenvolvimento de software.

Atualmente, o mercado viu o surgimento de múltiplas excelentes ferramentas de assistência de programação de IA. Embora todas melhorem a eficiência de desenvolvimento, diferem em caminhos de implementação e foco funcional:

- **GitHub Copilot**: Como um dos produtos mais influentes neste campo, Copilot foi desenvolvido conjuntamente pelo GitHub e OpenAI. Está profundamente integrado em editores mainstream como Visual Studio Code e é renomado por suas poderosas capacidades de autocompletar código. Quando desenvolvedores escrevem código, Copilot pode fornecer sugestões em tempo real para linhas inteiras ou até blocos de função inteiros. Nos últimos anos, também expandiu capacidades de programação conversacional através do Copilot Chat, permitindo que desenvolvedores resolvam problemas de programação através de chat dentro do editor.
- **Claude Code**: Claude Code é um assistente de programação de IA desenvolvido pela Anthropic, projetado para ajudar desenvolvedores a completar eficientemente tarefas de codificação no terminal através de instruções em linguagem natural. Ele pode entender estruturas completas de base de código, realizar operações como edição de código, testes e depuração, e suporta desenvolvimento de processo completo desde descrever funcionalidade até implementação de código. Claude Code também fornece um modo headless adequado para CI, hooks de pré-commit, scripts de build e outros cenários de automação, fornecendo aos desenvolvedores uma poderosa experiência de programação em linha de comando.
- **Trae**: Como uma ferramenta de programação de IA emergente, Trae foca em fornecer aos desenvolvedores serviços inteligentes de geração e otimização de código. Ela analisa padrões de código através de tecnologia de aprendizado profundo e pode fornecer aos desenvolvedores sugestões de código precisas e soluções de refatoração automatizadas. A característica distintiva de Trae é seu design leve e capacidade de resposta rápida, particularmente adequado para cenários que requerem iteração frequente e prototipagem rápida.
- **Cursor**: Diferentemente das ferramentas acima que existem principalmente como plugins ou recursos integrados, Cursor escolheu um caminho mais integrado - é ele mesmo um editor de código nativo de IA. Em vez de adicionar funcionalidade de IA a editores existentes, fez a interação com IA um recurso central desde a fase de design. Além de geração de código de primeira linha e capacidades de chat, enfatiza deixar a IA entender o contexto de toda a base de código, alcançando assim Q&A mais profundo, refatoração e depuração.

Claro, há muitas outras excelentes ferramentas não listadas aqui, mas todas apontam para uma tendência clara: a IA está se integrando profundamente em todo o ciclo de vida do desenvolvimento de software, remodelando profundamente os limites de eficiência e paradigmas de desenvolvimento da engenharia de software construindo fluxos de trabalho colaborativos humano-máquina eficientes.

### 1.4.2 Agentes como Colaboradores Autônomos

Diferentemente de servir como ferramentas para assistir humanos, o segundo modo de interação eleva o nível de automação dos agentes a um patamar inteiramente novo: colaboradores autônomos. Neste modo, não mais guiamos IA passo a passo através de cada ação, mas delegamos um objetivo de alto nível a ela. O agente, como um verdadeiro membro da equipe do projeto, planeja independentemente, raciocina, executa e reflete até finalmente entregar resultados. Esta transformação de assistente para colaborador trouxe agentes LLM mais profundamente à vista do público. Marca a evolução de nosso relacionamento com IA de "comando-execução" para "objetivo-delegação". Agentes não são mais ferramentas passivas, mas perseguidores ativos de objetivos.

Atualmente, abordagens para alcançar esta colaboração autônoma estão florescendo, com numerosos excelentes frameworks e produtos emergindo, desde os primeiros BabyAGI e AutoGPT até agora frameworks mais maduros como CrewAI, AutoGen, MetaGPT e LangGraph, impulsionando coletivamente o rápido desenvolvimento neste campo. Embora implementações específicas variem muito, suas arquiteturas paradigmáticas podem ser grosseiramente resumidas em várias direções mainstream:

1. **Loop Autônomo de Agente Único**: Este é um paradigma típico inicial, representado por modelos como **AgentGPT**. Seu núcleo é um agente geral que continuamente se auto-provoca e itera através de um loop fechado "pensar-planejar-executar-refletir" para completar um objetivo de alto nível de final aberto.
2. **Colaboração Multi-Agente**: Esta é atualmente a direção de exploração mais mainstream, visando resolver problemas complexos simulando modos de colaboração de equipe humana. Pode ser subdividida em diferentes modos: **Diálogo de Interpretação de Papéis**: Como o framework **CAMEL**, que atribui papéis claros e protocolos de comunicação a dois agentes (por exemplo, "programador" e "gerente de produto"), permitindo que colaborem para completar tarefas em um diálogo estruturado. **Fluxo de Trabalho Organizado**: Como **MetaGPT** e **CrewAI**, que simulam uma "equipe virtual" com divisão clara de trabalho (como uma empresa de software ou grupo de consultoria). Cada agente tem responsabilidades e fluxos de trabalho (SOPs) predefinidos, colaborando de maneira hierárquica ou sequencial para produzir saídas complexas de alta qualidade (como bases de código completas ou relatórios de pesquisa). **AutoGen** e **AgentScope** fornecem modos de diálogo mais flexíveis, permitindo que desenvolvedores personalizem redes de interação complexas entre agentes.
3. **Arquitetura de Fluxo de Controle Avançada**: Frameworks como **LangGraph** focam mais em fornecer aos agentes fundações de engenharia subjacentes mais poderosas. Eles modelam o processo de execução do agente como um grafo de estado, permitindo implementação mais flexível e confiável de processos complexos como loops, ramificações, retrocesso e intervenção humana.

Esses diferentes paradigmas arquiteturais impulsionam coletivamente agentes autônomos de conceitos teóricos em direção a aplicações práticas mais amplas, permitindo que lidem com tarefas do mundo real cada vez mais complexas. Em nossos capítulos subsequentes, também experimentaremos as diferenças e vantagens entre diferentes tipos de frameworks.

### 1.4.3 Diferenças Entre Fluxo de Trabalho e Agente

Após entender os dois modos de agentes como "ferramentas" e "colaboradores", é necessário discutir as diferenças entre Fluxo de Trabalho e Agente. Embora ambos visem alcançar automação de tarefas, sua lógica subjacente, características centrais e cenários aplicáveis são fundamentalmente diferentes.

Simplificando, **Fluxo de Trabalho faz a IA executar instruções passo a passo, enquanto Agente dá à IA liberdade para alcançar autonomamente objetivos.**

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/1-figures/1757242319667-18.png" alt="Figure description" width="90%"/>
  <p>Figura 1.6 Diferenças entre Fluxo de Trabalho e Agente</p>
</div>

Conforme mostrado na Figura 1.6, fluxo de trabalho é um paradigma de automação tradicional cujo núcleo é **orquestração pré-definida e estruturada de uma série de tarefas ou passos**. É essencialmente um fluxograma preciso e estático que especifica quais operações executar sob quais condições e em que ordem. Um caso típico: processo de aprovação de reembolso de despesas de uma empresa. Funcionário submete formulário de reembolso (gatilho) -> Se valor for menor que 500 yuans, diretamente aprovado pelo gerente de departamento -> Se valor for maior que 500 yuans, primeiro aprovado pelo gerente de departamento, depois encaminhado ao CFO para aprovação -> Após aprovação, notificar departamento financeiro para fazer pagamento. Cada passo e cada condição de julgamento de todo o processo é precisamente predefinido.

Diferentemente dos fluxos de trabalho, agentes baseados em grandes modelos de linguagem são **sistemas autônomos orientados a objetivos**. Eles não apenas executam instruções predefinidas, mas também podem compreender o ambiente até certo ponto, raciocinar, formular planos e tomar ações dinamicamente para alcançar objetivos finais. LLMs desempenham o papel do "cérebro" neste processo. Um exemplo típico é o assistente de viagem inteligente que escrevemos na Seção 1.3. Quando damos uma nova instrução, por exemplo: **"Olá, por favor me ajude a verificar o clima de hoje em Pequim, e então recomendar uma atração turística adequada baseada no clima."** Seu processamento demonstra completamente sua autonomia:

1. **Planejamento e Invocação de Ferramenta:** O agente primeiro divide a tarefa em dois passos: ① Consultar clima; ② Recomendar atrações baseadas no clima. Então, seleciona autonomamente e chama a "API de consulta climática", passando "Pequim" como parâmetro.
2. **Raciocínio e Tomada de Decisão:** Suponha que a API retorne "ensolarado, brisa leve." O cérebro LLM do agente raciocinará baseado nesta informação: "Dias ensolarados são adequados para atividades ao ar livre." Então, baseado neste julgamento, filtrará atrações ao ar livre em Pequim de sua base de conhecimento ou através de ferramentas de mecanismo de busca, como a Cidade Proibida, Palácio de Verão, Parque do Templo do Céu, etc.
3. **Gerar Resultados:** Finalmente, o agente sintetizará as informações e fornecerá uma resposta completa e humanizada: "O clima de hoje em Pequim é ensolarado com brisa leve, muito adequado para atividades ao ar livre. Recomendo que você visite o Palácio de Verão, onde você pode andar de barco no Lago Kunming e aproveitar a bela paisagem do jardim real."

Neste processo, não há regras codificadas como `if weather=sunny then recommend Summer Palace`. Se o clima for "chuvoso", o agente raciocinará autonomamente e recomendará locais internos como o Museu Nacional ou Museu da Capital. **Esta capacidade de raciocinar dinamicamente e tomar decisões baseadas em informações em tempo real é o valor central dos agentes.**

## 1.4 Resumo do Capítulo

Neste capítulo, embarcamos em uma jornada introdutória para explorar agentes. Nossa jornada começou com as questões mais fundamentais:

- **O que são agentes impulsionados por grandes modelos de linguagem?** Primeiro esclarecemos sua definição e entendemos que agentes modernos são entidades com capacidades. Não são mais apenas scripts executando programas predefinidos, mas tomadores de decisão capazes de raciocínio autônomo e uso de ferramentas.
- **Como funcionam os agentes?** Nos aprofundamos no mecanismo operacional de interação agente-ambiente. Aprendemos que este loop fechado contínuo é a base para agentes processarem informações, tomarem decisões, influenciarem o ambiente e ajustarem seu comportamento baseado em feedback.
- **Como construir um agente?** Este foi o núcleo prático deste capítulo. Usando o "assistente de viagem inteligente" como exemplo, construímos um agente completo impulsionado por um LLM real.
- **Quais são os paradigmas de aplicação mainstream dos agentes?** Finalmente, lançamos nossa visão em direção a domínios de aplicação mais amplos. Exploramos dois modos de interação de agentes mainstream: um são "ferramentas de desenvolvedor" representadas pelo GitHub Copilot e Cursor que aprimoram fluxos de trabalho humanos; o outro são "colaboradores autônomos" representados por frameworks como CrewAI, MetaGPT e AgentScope que podem completar independentemente objetivos de alto nível. Também explicamos as diferenças entre Fluxo de Trabalho e Agente.

Através do aprendizado deste capítulo, estabelecemos um framework cognitivo fundamental sobre agentes. Então, como evoluiu passo a passo desde sua concepção inicial até o presente? No próximo capítulo, exploraremos a história de desenvolvimento dos agentes - uma jornada para rastrear de volta às origens está prestes a começar!

## Exercícios

> **Nota**: Alguns dos seguintes exercícios não têm respostas padrão. O foco está em cultivar o pensamento crítico aprofundado e as habilidades práticas dos aprendizes em relação aos sistemas de agentes.

1. Por favor analise se o **sujeito** nos seguintes quatro `casos` se qualifica como um agente. Se sim, a que tipo de agente ele pertence (pode ser analisado a partir de múltiplas dimensões de classificação), e explique seu raciocínio:

   `Caso A`: **Um supercomputador conforme a arquitetura von Neumann**, com poder de computação de pico de até 2 EFlops por segundo

   `Caso B`: **Sistema de direção autônoma da Tesla** está dirigindo em uma rodovia quando de repente detecta um obstáculo à frente e precisa tomar uma decisão de frenagem ou mudança de faixa em milissegundos

   `Caso C`: **AlphaGo** está jogando contra um jogador humano e precisa avaliar a situação atual e planejar a estratégia ótima para dezenas de movimentos à frente

   `Caso D`: **ChatGPT atuando como atendimento ao cliente inteligente** está lidando com uma reclamação de usuário e precisa consultar informações do pedido, analisar a causa do problema, fornecer soluções e acalmar emoções do usuário

2. Suponha que você precise projetar um ambiente de tarefa para um "treinador de fitness inteligente". Este agente pode:
   - Monitorar dados fisiológicos dos usuários como frequência cardíaca e intensidade de exercício através de dispositivos vestíveis
   - Ajustar dinamicamente planos de treinamento baseados nos objetivos de fitness dos usuários (perda de gordura/ganho muscular/melhoria de resistência)
   - Fornecer orientação de voz em tempo real e correção de movimento durante exercício do usuário
   - Avaliar eficácia do treinamento e fornecer recomendações dietéticas

   Por favor use o modelo PEAS para descrever completamente o ambiente de tarefa deste agente e analisar que características este ambiente tem (como parcialmente observável, estocástico, dinâmico, etc.).

3. Uma empresa de e-commerce está considerando duas abordagens para lidar com solicitações de reembolso pós-venda:

   Abordagem A (`Fluxo de Trabalho`): Projetar um processo fixo, por exemplo:

   A.1 Para produtos gerais dentro de 7 dias, valores `< 100 RMB` são automaticamente aprovados; `100-500 RMB` são revisados por atendimento ao cliente; `> 500 RMB` requerem aprovação de supervisor; produtos especiais (como itens personalizados) são sempre rejeitados

   A.2 Para produtos além de 7 dias, independentemente do valor, só podem ser revisados por atendimento ao cliente ou aprovados por supervisores;

   Abordagem B (`Agente`): Construir um sistema de agente que compreende políticas de reembolso, analisa comportamento histórico do usuário, avalia condições do produto e decide autonomamente se aprova reembolsos

   Por favor analise:
   - Quais são as vantagens e desvantagens dessas duas abordagens?
   - Sob quais circunstâncias `Fluxo de Trabalho` é mais adequado? Quando `Agente` tem vantagens? Se você fosse o chefe desta empresa de e-commerce, qual abordagem preferiria?
   - Existe uma Abordagem C que pode combinar ambas abordagens para alcançar forças complementares?

4. Baseado no assistente de viagem inteligente na Seção 1.3, por favor considere como adicionar os seguintes recursos (você pode apenas descrever as ideias de design ou tentar implementação de código):

   > **Dica**: Pense em como modificar o loop `Thought-Action-Observation` para implementar esses recursos.

   - Adicionar um recurso de "memória" que permite ao agente lembrar preferências do usuário (como gostar de atrações históricas e culturais, faixa de orçamento, etc.)
   - Quando ingressos de atração recomendada estiverem esgotados, o agente pode automaticamente recomendar opções alternativas
   - Se o usuário rejeitar consecutivamente 3 recomendações, o agente pode refletir e ajustar sua estratégia de recomendação

5. A teoria "Sistema 1" (intuição rápida) e "Sistema 2" (raciocínio lento) de Kahneman<sup>[2]</sup> fornece uma boa analogia para IA neuro-simbólica. Por favor primeiro conceba um cenário de aplicação de agente específico, então explique no cenário:

   > **Dica**: Assistentes de diagnóstico médico, robôs de consultoria jurídica, sistemas de controle de risco financeiro, etc., são todos cenários de aplicação comuns

   - Quais tarefas devem ser tratadas pelo "Sistema 1"?
   - Quais tarefas devem ser tratadas pelo "Sistema 2"?
   - Como esses dois sistemas trabalham juntos para alcançar o objetivo final?

6. Embora sistemas de agentes impulsionados por grandes modelos de linguagem demonstrem capacidades poderosas, ainda têm muitas limitações. Por favor analise as seguintes questões:
   - Por que agentes ou sistemas de agentes às vezes produzem "alucinações" (gerando informações aparentemente razoáveis mas na verdade incorretas)?
   - No caso na Seção 1.3, definimos o número máximo de loops para 5. Sem este limite, quais problemas o agente pode encontrar?
   - Como avaliar o nível de "inteligência" de um agente? Usar apenas métricas de precisão é suficiente?

## Referências

[1] RUSSELL S, NORVIG P. Artificial Intelligence: A Modern Approach[M]. 4th ed. London: Pearson, 2020.

[2] KAHNEMAN D. Thinking, Fast and Slow[M]. New York: Farrar, Straus and Giroux, 2011.

---

## 💬 Discussão e Comunicação

Tem dúvidas ao aprender este capítulo? Quer compartilhar insights com outros aprendizes?

**📝 Visite GitHub Discussions:**
- [💬 Discussão de Exercícios e Q&A](https://github.com/datawhalechina/Hello-Agents/discussions)
- Aqui você pode:
  - ✅ Fazer perguntas sobre exercícios
  - ✅ Compartilhar suas soluções e ideias
  - ✅ Trocar experiências com outros aprendizes
  - ✅ Obter ajuda e feedback da comunidade

**💡 Dica:** Também há uma seção de comentários na parte inferior de cada página para discussão direta!

---
