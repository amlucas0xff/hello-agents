<div align="right">
  <a href="./Extra02-上下文工程补充知识.md">中文</a> | Português
</div>

# Conhecimento Complementar de Engenharia de Contexto

## Introdução

Por que a Engenharia de Contexto (Context Engineering) voltou a ficar popular recentemente? Tudo começou com uma conversa de Jeff, fundador e CEO da Chroma, no podcast [Len Space](https://youware.app/project/7529x70z4p).
Chroma é o líder open-source no campo de bancos de dados vetoriais. Até o famoso artigo do Voyager utiliza o Chroma.
O título da conversa com o CEO Jeff era sobre a ideia de que "RAG está morto" ("RAG is dead"). No vídeo, ele explica claramente as limitações do RAG original e a importância da engenharia de contexto atual.

![alt text](./images/Extra02-figures/image-1.png)

Neste capítulo, explicaremos de forma abrangente o conceito de "Engenharia de Contexto" e, ao final do artigo, discutiremos a visão sobre "RAG is dead".

## O que é Engenharia de Contexto?

Podemos fazer uma analogia: um Agente (Agent) é como um [novo tipo de sistema operacional](https://www.youtube.com/watch?si=-aKY-x57ILAmWTdw&t=620&v=LCEmiRjPEtQ&feature=youtu.be&ref=blog.langchain.com). O LLM é como a CPU, e sua [janela de contexto](https://docs.anthropic.com/en/docs/build-with-claude/context-windows?ref=blog.langchain.com) é como a RAM, servindo como a memória de trabalho do modelo. Assim como a RAM, a janela de contexto do LLM tem [capacidade limitada](https://lilianweng.github.io/posts/2023-06-23-agent/?ref=blog.langchain.com) e não consegue lidar com contextos de todas as fontes. A engenharia de contexto é como o sistema operacional gerenciando a RAM da CPU: ela gerencia a janela de contexto do LLM, decidindo quando preencher e com qual conteúdo. [Karpathy resumiu muito bem](https://x.com/karpathy/status/1937902205765607626?ref=blog.langchain.com):
*"Engenharia de Contexto é... a arte e ciência sutil de preencher a janela de contexto com as informações exatas para o próximo passo."*

![llm_context_engineering](https://blog.langchain.com/content/images/2025/07/image-1.png)

## [Conceitos de Engenharia de Contexto](https://blog.langchain.com/context-engineering-for-agents/`)

![alt text](./images/Extra02-figures/image-2.png)

Contexto é tudo o que o modelo "vê". O modelo não responde apenas com base no prompt que inserimos, mas também gera respostas com o auxílio de outras informações. A Engenharia de Contexto funciona como um termo guarda-chuva para vários tipos diferentes de contexto:

- **Instructions (Contexto de Instruções)**: Prompt engineering, como prompts, memória, few-shot examples, incluindo:
  - Prompt do Sistema: Define o papel da IA, diretrizes de comportamento e estilo de resposta.
  - Instruções do Usuário: Descrevem tarefas e requisitos específicos.
  - Exemplos Few-Shot: Exemplos de entrada e saída para ajudar a entender o formato esperado.
  - Descrição de Ferramentas: Especificações e instruções de uso de funções ou ferramentas.
  - Restrições de Formato: Requisitos de formato e estrutura da saída.
- **Knowledge (Contexto de Conhecimento)**: Fatos, bases de conhecimento, etc. (RAG), incluindo:
  - Conhecimento de Domínio: Informações factuais de uma indústria ou especialidade específica.
  - Memória: Preferências do usuário, interações históricas e registros de conversas.
  - Base de Conhecimento: Obtenção de informações relevantes de bancos de dados ou bases de conhecimento.
  - Dados em Tempo Real: Informações de estado atual atualizadas dinamicamente.
- **Tools (Contexto de Ferramentas)**: Descrições de ferramentas e feedback de chamadas de ferramentas (Agente), incluindo:
  - Resultados de Chamadas de Função: Respostas de API ou resultados de consultas.
  - Estado de Execução da Ferramenta: Feedback de sucesso, falha ou erro.
  - Cadeia de Ferramentas de Múltiplas Etapas: Dependências e transferência de dados entre ferramentas.
  - Histórico de Execução: Registros e resultados de chamadas de ferramentas.

### Exemplo — Assistente Inteligente de APP de Viagem

![alt text](./images/Extra02-figures/image-5.png)

Para distinguir claramente esses quatro conceitos, vamos definir um cenário prático unificado e ver como cada método resolve o problema.

**Cenário: Um assistente inteligente de APP de viagem**

**Demanda do Usuário:** "Ajude-me a planejar uma viagem em família de três dias para Pequim. Somos dois adultos e uma criança de 5 anos. Gostamos de história e cultura, mas também queremos algumas atividades relaxantes e divertidas. Nosso orçamento total é de 8000 yuans."

---

#### 1. Engenharia de Prompt (Prompt Engineering)

Este é o método mais básico e direto. Seu núcleo é **como fazer uma boa pergunta ao Modelo de Linguagem (LLM)**, esperando que ele forneça a melhor resposta baseando-se apenas em sua base de conhecimento interna geral.

*   **Ideia Central:** Otimizar as instruções (Prompt) enviadas ao modelo para que ele produza resultados mais alinhados com as expectativas.
*   **Como funciona:**
    1.  O desenvolvedor ou usuário elabora cuidadosamente todas as necessidades em um prompt detalhado.
    2.  Esse prompt é enviado diretamente para um grande modelo de linguagem geral (como o GPT-4).
    3.  O modelo responde dependendo inteiramente de seu conhecimento interno até a data de treinamento (por exemplo, 2023).

*   **Exemplo:**
    ```
    Você é um planejador de viagens profissional. Por favor, projete um itinerário detalhado para uma viagem em família de três dias em Pequim.
    
    # Membros da Família
    - 2 adultos
    - 1 criança de 5 anos
    
    # Interesses e Preferências
    - História e Cultura (Cidade Proibida, Grande Muralha, etc.)
    - Atividades infantis relaxantes e divertidas
    
    # Orçamento
    - Orçamento total não superior a 8000 yuans RMB, por favor, forneça uma estimativa aproximada dos custos.
    
    # Requisitos de Saída
    - Cronograma diário (manhã, tarde, noite)
    - Sugestões de transporte
    - Recomendações de restaurantes (incluindo restaurantes adequados para crianças)
    - Detalhamento do orçamento
    ```

*   **Limitações:**
    *   **Informações Desatualizadas:** Não é possível fornecer preços de ingressos em tempo real, horários de funcionamento ou informações de trânsito atualizadas.
    *   **Informações Imprecisas:** A estimativa de orçamento pode ser muito grosseira, pois ele não sabe os preços atuais de hotéis e passagens aéreas.
    *   **Falta de Personalização:** Não é possível fazer recomendações com base nas preferências históricas do usuário.
    *   **"Alucinação Séria":** Pode inventar "parques infantis" ou restaurantes inexistentes.

---

#### 2. Geração Aumentada por Recuperação (RAG)

Para resolver o problema de "conhecimento obsoleto" da engenharia de prompt, o RAG introduz uma **base de conhecimento externa**.

*   **Ideia Central:** Antes de gerar a resposta, recupere informações relevantes de um banco de dados específico e confiável e, em seguida, forneça essas informações ao modelo junto com a pergunta do usuário.
*   **Como funciona:**
    1.  **Preparação da Base de Conhecimento:** Prepare com antecedência um banco de dados contendo os guias de viagem mais recentes, introduções de atrações, listas de hotéis e avaliações de restaurantes (como um monte de PDFs, páginas da web ou registros de banco de dados).
    2.  **Recuperação (Retrieve):** Quando o usuário faz uma pergunta, o sistema primeiro pesquisa fragmentos de documentos relacionados a "viagem em família em Pequim" e "atrações históricas e culturais" na base de conhecimento.
    3.  **Aumento (Augment):** As informações recuperadas (por exemplo: "O preço atual do ingresso da Cidade Proibida é 60 yuans, fechado às segundas-feiras", "Universal Studios Beijing é um projeto popular para famílias") e a pergunta original do usuário são combinadas em um novo prompt mais rico em conteúdo.
    4.  **Geração (Generate):** Envie este prompt aprimorado para o LLM, para que ele gere o itinerário com base nesses materiais "frescos".

*   **Exemplo:**
    O sistema encontrou três trechos de texto na base de conhecimento interna: A) Horário de funcionamento e preços do site oficial da Cidade Proibida; B) Um blog sobre "Visitar o Templo do Céu com crianças"; C) Uma lista de "Hotéis familiares em Pequim".
    Em seguida, ele envia uma instrução ao LLM: "Com base nas seguintes informações: [Conteúdo dos trechos A, B, C], planeje uma viagem em família de três dias em Pequim para o usuário, com orçamento de 8000 yuans."

*   **Limitações:**
    *   **Resposta Passiva:** Ele só pode responder com base nas informações que você fornece e não pode executar tarefas ativamente. Ele não pode "verificar" passagens aéreas, apenas usar as informações de passagens que "existem" no seu banco de dados.
    *   **Interação Unidirecional:** Termina após uma recuperação e geração, não sendo possível realizar raciocínio e ação em várias etapas.
    *   **Dependência da Base de Conhecimento:** A qualidade do resultado depende muito da qualidade e frequência de atualização da base de conhecimento.

---

#### 3. Agente (Agent)

O Agente evolui a IA de um "robô de perguntas e respostas" para um **"ator" que pode pensar e usar ferramentas**.

*   **Ideia Central:** Dotar o modelo de um ciclo de "Pensamento-Ação" (Reasoning-Action Loop), permitindo que ele planeje passos autonomamente e use ferramentas externas (como APIs) para completar tarefas complexas.
*   **Como funciona:**
    1.  **Pensamento e Planejamento:** O LLM (como o cérebro do Agente) recebe a demanda do usuário e pensa primeiro: "Para completar esta tarefa, preciso: 1. Verificar preços de passagens e hotéis; 2. Verificar ingressos para atrações; 3. Planejar rotas; 4. Resumir em um itinerário."
    2.  **Escolha de Ferramenta (Action):** Ele decide usar a primeira ferramenta: `search_flight_api(from="Xangai", to="Pequim", date="...")`.
    3.  **Observar Resultado (Observation):** A API retorna o preço da passagem: 5000 yuans.
    4.  **Pensar Novamente:** "As passagens custaram 5000, restam 3000 no orçamento. Preciso encontrar hotéis com preço inferior a 800 yuans por noite."
    5.  **Ação Novamente:** Usa a ferramenta `search_hotel_api(city="Pequim", price_max=800, family_friendly=true)`.
    6.  Esse ciclo continua até que ele colete todas as informações necessárias e finalmente complete o planejamento.

*   **Exemplo:**
    Este assistente funcionará como um verdadeiro assistente humano:
    *   "Certo, estou verificando para você... Descobri que as passagens para Pequim na próxima sexta-feira custam cerca de 5000 yuans."
    *   "Considerando o orçamento, selecionei alguns hotéis familiares muito bem avaliados com preços entre 600-800 yuans/noite."
    *   "Os ingressos para a Cidade Proibida foram verificados via `ticket_api`, crianças não pagam. Adicionei esta informação ao itinerário."

*   **Limitações:**
    *   **Complexo e Instável:** O caminho de comportamento do Agente não é fixo e pode cometer erros (como entrar em loop, usar ferramentas incorretamente), dificultando a depuração e o controle.
    *   **Custo Alto:** Cada passo de pensamento e chamada de ferramenta pode ser uma chamada de API do LLM, o que tem um custo elevado.

---

#### 4. Engenharia de Contexto (Context Engineering)

A Engenharia de Contexto é **uma disciplina mais macro e rigorosa**, que foca em **como construir a "janela de contexto" ideal para o modelo (seja um RAG simples ou um Agente complexo)**. É a otimização e sublimação de todos os métodos acima.

*   **Ideia Central:** Projetar e orquestrar cuidadosamente todas as informações que entram no contexto do modelo (instruções, dados recuperados, histórico de conversas, saída de ferramentas, etc.) para alcançar a saída mais eficiente e confiável. É uma ciência sobre "o que alimentar" e "como alimentar".
*   **Como funciona:**
    Não é um tipo de sistema independente, mas uma metodologia para otimizar RAG e Agentes. Voltando ao exemplo do planejamento de viagem:
    1.  **Fase de Coleta (Gather):**
        *   **Recuperação Paralela:** Não apenas recupera da base de guias de viagem (RAG), mas também simultaneamente:
            *   Chama `weather_api` para consultar o clima em Pequim nos próximos dias.
            *   Chama `events_api` para verificar se há exposições ou atividades especiais para crianças.
            *   Recupera do banco de dados de perfil do usuário (CRM) que "o usuário reservou ingressos de museu na última viagem".
            *   Realiza busca multipath para a pergunta vaga do usuário "atividades relaxantes e divertidas", incluindo "parques de diversão em Pequim", "Museu de Ciência e Tecnologia de Pequim", "espetáculos adequados para crianças".
    2.  **Fase de Triagem e Compressão (Glean & Compact):**
        *   **Reordenação:** Descobre que a previsão do tempo mostra chuva no dia seguinte, então reduz a prioridade da Grande Muralha (ao ar livre) e aumenta o peso da recomendação do Museu de Ciência e Tecnologia (interno).
        *   **Compressão:** Não joga um longo artigo de avaliação de hotel para o modelo, mas extrai as informações principais: "O hotel tem área de recreação infantil e oferece berços."
        *   **Formatação:** Integra todas as informações coletadas e desordenadas (clima, passagens, preferências do usuário, introdução de atrações) em um objeto JSON altamente estruturado e conciso.
    3.  **Entrega Final:** Por fim, entrega esse pacote de contexto "perfeito" ao cérebro do Agente (LLM), e a instrução pode ser: "Com base nestes dados estruturados validados e organizados `[JSON object]`, gere o itinerário final para o usuário."

*   **Exemplo:**
    O produto da engenharia de contexto não é o itinerário direto para o usuário, mas um "mapa de batalha" otimizado para o modelo ver. Como passou pela otimização da engenharia de contexto, o trabalho do Agente torna-se extremamente simples e eficiente; ele não precisa se esforçar para tentar e errar passo a passo, mas gera o planejamento final diretamente com base em um briefing perfeito.

#### Resumo Comparativo

| Conceito | Ideia Central | Modo de Trabalho | Limitações |
| :--- | :--- | :--- | :--- |
| **Engenharia de Prompt** | Fazer a pergunta certa | Projetar cuidadosamente um Prompt perfeito | Conhecimento obsoleto, sem interação com o mundo externo |
| **RAG** | Fornecer materiais de referência | Recuperar informações relevantes da base de conhecimento antes de perguntar | Resposta passiva, não executa tarefas, depende da base de conhecimento |
| **Agente** | Dar capacidade de ação | Usar ferramentas e completar tarefas através do ciclo "Pensar-Agir" | Complexo, instável, alto custo |
| **Engenharia de Contexto** | Criar a entrada perfeita | Coletar, selecionar, comprimir e formatar sistematicamente todas as informações para fornecer o melhor contexto ao modelo | É uma metodologia/disciplina, não um sistema específico, implementação complexa |

Simplificando, eles são uma progressão de capacidades:
*   **Engenharia de Prompt** é um **interlocutor**.
*   **RAG** é um **interlocutor** que trouxe um livro para consulta.
*   **Agente** é um **assistente** que pode fazer ligações, pesquisar na internet e reservar ingressos para você.
*   **Engenharia de Contexto** é o **chefe de gabinete** por trás desse assistente, responsável por coletar e organizar todas as informações com antecedência, garantindo que o assistente possa tomar as decisões mais sábias.

## Por que surgiu o Context Engineer?

![alt text](./images/Extra02-figures/image-3.png)

À medida que os LLMs melhoram no raciocínio e na chamada de ferramentas, o interesse em Agentes cresceu muito. Agentes entrelaçam chamadas de LLM e chamadas de ferramentas, geralmente para tarefas de longa duração. O Agente usa o feedback da ferramenta para decidir a próxima ação.

No entanto, tarefas de longa duração e o feedback acumulado de chamadas de ferramentas significam que o Agente geralmente usa muitos tokens. Isso pode levar a muitos problemas: pode exceder o tamanho da janela de contexto, aumentar o custo/latência ou reduzir o desempenho do Agente.

À medida que as janelas de contexto ficam mais longas, pensávamos que "jogar todo o histórico de conversas e materiais no modelo" resolveria o problema da memória. Mas experimentos mostram que a realidade é muito mais complexa do que imaginávamos. Com o crescimento do comprimento do contexto, torna-se cada vez mais difícil para o modelo manter a precisão e a consistência das informações, comportando-se como uma "**podridão da memória**" (Memory Rot).

![alt text](./images/Extra02-figures/image-4.png)

Esses fenômenos são chamados de Context Rot na pesquisa da Chroma — ou seja, a "corrosão" do desempenho do modelo em contextos longos. Esta é a razão fundamental para o nascimento do papel de Context Engineer: alguém precisa combater e consertar essa "podridão de contexto", através de corte, compressão, reorganização e aumento de recuperação, para manter o desempenho confiável do modelo com recursos de atenção limitados.

## Desafios do Contexto

Os desafios do contexto existem principalmente em [quatro aspectos](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html?ref=blog.langchain.com), descritos como:

- Poluição de Contexto (Context Poisoning) - Quando alucinações entram no contexto
- Distração de Contexto (Context Distraction) - Quando o contexto sobrecarrega os dados de treinamento
- Confusão de Contexto (Context Confusion) - Quando contexto supérfluo influencia a resposta
- Conflito de Contexto (Context Clash) - Quando partes do contexto são inconsistentes

### Context Poisoning: When a Hallucination Makes It into the Context

A Poluição de Contexto (Context Poisoning) refere-se a quando alucinações (hallucinations, ou seja, informações erradas ou fictícias geradas pelo modelo) ou outros erros entram na janela de contexto e são repetidamente citados, incorporando assim informações erradas e fazendo com que o desempenho do agente descarrile. Essa situação pode "envenenar" partes cruciais, como objetivos ou resumos, fazendo com que o modelo se fixe em objetivos impossíveis ou irrelevantes, levando a comportamentos repetitivos e sem sentido.

### Context Distraction: When the Context Overwhelms the Training

A Distração de Contexto (Context Distraction) ocorre quando o contexto cresce demais (por exemplo, excedendo 100 mil tokens), levando o modelo a depender excessivamente de detalhes históricos e ignorar seu conhecimento pré-treinado ou a capacidade de gerar soluções inovadoras. Isso pode desencadear ações repetitivas em vez de resolução criativa de problemas, e o desempenho cai antes mesmo de a janela de contexto estar cheia.

Quando o modelo enfrenta uma entrada de centenas de milhares de tokens, ele não consegue lembrar todas as informações uniformemente como um disco rígido. Experimentos descobriram que entradas simplificadas (apenas algumas centenas de tokens) na verdade têm desempenho melhor do que entradas completas (mais de cem mil tokens). Os resultados da pesquisa mostram que o desempenho do modelo na versão simplificada é significativamente superior ao da versão completa. Isso indica que, quando a entrada é muito longa e há muito ruído, mesmo os modelos mais avançados têm dificuldade em capturar as informações principais.

### Context Confusion: When Superfluous Context Influences the Response

A Confusão de Contexto (Context Confusion) refere-se a quando informações irrelevantes ou supérfluas (como definições de ferramentas redundantes) são incluídas no contexto, forçando o modelo a considerá-las, produzindo assim uma resposta subótima. Mesmo que o conteúdo extra seja inofensivo, ele dilui o foco e reduz a qualidade.
Em conversas e materiais reais, muitas vezes existe "ruído" que é semanticamente semelhante, mas irrelevante. Em contextos curtos, o modelo consegue distinguir, mas em contextos longos é mais fácil ser induzido ao erro. Isso exige que alguém faça a filtragem e a remoção de ruído do contexto, permitindo que o modelo foque nas informações verdadeiramente relevantes. Em contextos longos, o modelo não apenas precisa encontrar informações relevantes, mas também ser capaz de distinguir "qual é a agulha (needle) correta e qual é apenas um item de distração".

### Context Clash: When Parts of the Context Disagree

O Conflito de Contexto (Context Clash) é uma forma mais grave de confusão, referindo-se a quando as informações no contexto conflitam entre si (como novas ferramentas ou fatos contradizendo o conteúdo existente), destruindo assim o raciocínio, geralmente porque o modelo fica preso em suposições iniciais. Isso é mais destrutivo do que a simples irrelevância: "This is a more problematic version of Context Confusion: the bad context here isn’t irrelevant, it directly conflicts with other information in the prompt." Em interações de várias etapas, os erros iniciais se propagam e o modelo depende de premissas defeituosas.

Falta de confiabilidade "tipo computador"
Esperamos que o LLM produza saídas de qualidade consistente. Mesmo na tarefa de cópia mais simples, o modelo cometerá erros sob entradas longas. Ele não é um processador de símbolos bit a bit, mas um gerador de linguagem impulsionado por probabilidade. Portanto, não se pode esperar que ele processe contextos longos com precisão como um banco de dados ou computador; deve-se recorrer a um design estruturado para compensar.

Portanto, o gerenciamento eficaz da janela de contexto e a engenharia de contexto são indispensáveis.

## Estratégias de Engenharia de Contexto

A seção anterior mencionou que o contexto enfrenta tantos desafios, então como superá-los? Isso depende da engenharia de contexto. Entre elas, as estratégias de engenharia de contexto dividem-se principalmente em quatro tipos: Gravação (armazenamento), Seleção, Compressão e Isolamento.

![alt text](./images/Extra02-figures/image-6.png)

### Gravar no Contexto (Write)

**Gravar no contexto** significa preservá-lo fora da janela de contexto para ajudar o Agente a executar tarefas.
Divide-se principalmente em dois tipos:
- **Bloco de Notas Temporário (Scratchpad)**
Um espaço de trabalho temporário que registra o raciocínio intermediário do modelo, tornando o processo de pensamento visível. Fazer anotações através do "bloco de notas temporário" é uma maneira de persistir informações enquanto o Agente executa uma tarefa. A ideia é manter as informações fora da janela de contexto para que estejam disponíveis para o Agente.
- **Memória**
O Agente combina o novo contexto ocorrido (new context) com as memórias existentes (existing memories) e, após o processamento, escreve como uma memória atualizada (updated memory).

![alt text](./images/Extra02-figures/image-8.png)

### Selecionar Contexto (Select)

Quando a quantidade de informação aumenta, como selecionar é mais importante do que como armazenar. Selecionar o contexto é escolher, a cada chamada do modelo, as partes verdadeiramente relevantes de todas as fontes de informação disponíveis para colocar na janela.

Os contextos específicos disponíveis para seleção são:

- **Bloco de Notas Temporário (Scratchpad)**: Como mencionado acima, serve como o espaço de "memória de trabalho" do modelo, usado para registrar processos de raciocínio, resultados intermediários e etapas de pensamento. Em tarefas de várias etapas, o modelo pode gravar o estado atual do raciocínio, subtarefas concluídas, problemas pendentes, etc., no bloco de notas, facilitando a referência e o ajuste de estratégias em etapas subsequentes.

- **Memória (Memory)**: Inclui dois níveis: memória de curto prazo e memória de longo prazo. A memória de curto prazo preserva o histórico de conversas e informações de contexto da sessão atual, garantindo a coerência do diálogo; a memória de longo prazo armazena preferências do usuário, padrões históricos de interação, configurações personalizadas e outras informações persistentes entre sessões, ajudando o modelo a fornecer uma experiência de serviço mais personalizada e consistente.

- **Ferramentas (Tools)**: No sistema de Agentes, a ferramenta em si é um tipo de contexto. Quando o modelo chama uma API, plugin ou função externa, ele deve entender a descrição da ferramenta (incluindo instruções de função, requisitos de parâmetros, formato de retorno, etc.) e escolher a ferramenta correta no cenário apropriado. O resultado do feedback após a chamada da ferramenta também servirá como nova entrada de contexto, orientando a próxima decisão do modelo. A disponibilidade, o estado de execução e o histórico de chamadas das ferramentas são informações de contexto importantes.

- **Conhecimento (Knowledge)**: Refere-se principalmente à base de conhecimento externa no RAG (Geração Aumentada por Recuperação). Inclui dados estruturados (como tabelas de banco de dados), documentos não estruturados (como documentos técnicos, manuais de produtos), resultados de recuperação semântica em bancos de dados vetoriais, etc. Esse conhecimento externo compensa as limitações de atualidade dos dados de treinamento do modelo e a falta de cobertura de conhecimento, aumentando a precisão e o profissionalismo das respostas do modelo através da recuperação dinâmica de informações relevantes.

### Comprimir Contexto (Compress)

![alt text](./images/Extra02-figures/image-9.png)

A compressão de contexto envolve manter apenas os tokens necessários para executar a tarefa, otimizando a eficiência do uso da janela de contexto ao reduzir informações redundantes.

#### Resumo de Contexto

**Resumo de Diálogo:**
Em interações longas de várias rodadas, manter todo o histórico de conversas completo consumirá rapidamente a janela de contexto. Através da tecnologia de resumo de diálogo, é possível comprimir as rodadas de conversas anteriores em um formato de resumo conciso, preservando informações-chave (como preferências do usuário, decisões importantes, problemas a serem resolvidos, etc.), enquanto descarta saudações redundantes e conteúdo repetitivo. Isso mantém a coerência do diálogo e libera espaço suficiente para novas interações.

**Resumo de Ferramentas:**
Chamadas de ferramentas geralmente retornam grandes quantidades de dados brutos (como respostas completas de API, resultados de consultas de banco de dados, etc.). Através do resumo de ferramentas, é possível extrair e manter os campos de resultados mais relevantes, filtrando metadados, informações de depuração e outros conteúdos desnecessários. Por exemplo, uma API de clima pode retornar parâmetros meteorológicos detalhados, mas após o resumo, apenas a temperatura, a condição do tempo e outras informações essenciais são mantidas, reduzindo drasticamente o consumo de tokens.

#### Poda de Contexto (Pruning)

**Poda Baseada em Regras:**
Métodos heurísticos codificados podem ser usados para excluir ativamente contextos obsoletos ou de baixa prioridade. Estratégias comuns incluem:
- Excluir mensagens mais antigas do histórico de conversas, mantendo as N rodadas mais recentes.
- Remover registros de subtarefas concluídas, mantendo apenas informações relacionadas à tarefa atual.
- Excluir dados temporários expirados ou resultados de chamadas de ferramentas inválidos.

**Poda Inteligente:**
Métodos mais avançados podem selecionar dinamicamente quais fragmentos de contexto manter com base em pontuações de relevância. Através do cálculo de similaridade semântica ou pontuação de importância, prioriza-se a manutenção das informações mais relevantes para a tarefa atual, eliminando automaticamente o conteúdo histórico de baixa relevância.

### Isolar Contexto (Isolate)

Isolar o contexto envolve dividir o contexto para ajudar o Agente a executar tarefas.

#### Arquitetura Multi-Agente

![alt text](./images/Extra02-figures/image-10.png)

**Separação de Preocupações:**
Dividir uma tarefa grande e complexa em várias subtarefas independentes, cada uma sendo responsabilidade de um Agente especializado. Esse design segue o princípio da responsabilidade única, permitindo que cada Agente se concentre em um domínio específico, melhorando a manutenibilidade e escalabilidade do sistema como um todo.

**Características de Isolamento do Agente:**
Cada sub-Agente possui recursos e configurações independentes:
- **Conjunto de Ferramentas Dedicado**: Cada Agente só pode acessar ferramentas específicas necessárias para completar sua tarefa, evitando a dificuldade de escolha causada pela proliferação de ferramentas.
- **Instruções de Sistema Independentes**: Prompt de sistema personalizado para tarefas específicas, esclarecendo o posicionamento do papel e as diretrizes de comportamento do Agente.
- **Janela de Contexto Isolada**: Cada Agente mantém seu próprio espaço de contexto, sem interferência mútua, evitando a poluição por informações irrelevantes.

**Mecanismo de Colaboração do Agente:**
Vários Agentes se comunicam e transferem dados através de interfaces claras. O Agente principal ou camada de roteamento é responsável pela distribuição de tarefas e integração de resultados, formando um fluxo de trabalho colaborativo.

#### Isolamento do Ambiente de Execução

![alt text](./images/Extra02-figures/image-11.png)

**Separação de Contexto e Execução:**
Isolar o ambiente de execução de código da janela de contexto do LLM, para que o LLM não precise ter contato direto com todos os dados de saída brutos das ferramentas.

**Design da Camada de Processamento:**
Adicionar uma camada de processamento entre a execução da ferramenta e o LLM:
- As ferramentas são executadas em um ambiente sandbox independente, produzindo saída bruta.
- A camada de processamento filtra, converte e resume os resultados brutos.
- Apenas as informações-chave refinadas são passadas para o contexto do LLM.

Esse isolamento aumenta a segurança e reduz o consumo de tokens, permitindo que o LLM se concentre em decisões de alto nível em vez de lidar com detalhes de baixo nível.

### Resumo

As quatro ações da engenharia de contexto — Gravar, Selecionar, Comprimir, Isolar — não são truques isolados, mas um conjunto de métodos sistemáticos.
Elas resolvem, respectivamente, os problemas de perda de informação, redundância de informação, sobrecarga de informação e conflito de informação.
Quando essas quatro estratégias são executadas sistematicamente, o Agente pode operar de forma estável em ambientes complexos.

## Implementação da Engenharia de Contexto

Para usar LangSmith e LangGraph na engenharia de contexto, consulte o Capítulo 9 para obter detalhes sobre este conteúdo.

## Resumo e Reflexão: RAG is Dead?

![alt text](./images/Extra02-figures/image.png)

Jeff criticou principalmente o fato de o RAG tradicional forçar a união de três conceitos diferentes: "Recuperação (Retrieval), Aumento (Augmented), Geração (Generation)", levando a confusão conceitual e imprecisão na prática. Reexaminando o RAG sob a perspectiva da engenharia de contexto, ele pode ser desmontado em etapas mais claras:

**RAG Tradicional vs Perspectiva da Engenharia de Contexto (RAG Avançado):**

| Fase | RAG Tradicional | Método de Engenharia de Contexto |
|------|-----------------|----------------------------------|
| **Recuperação** | Busca simples por similaridade vetorial | Recuperação híbrida: combina busca vetorial, correspondência de palavras-chave, reordenação e outras estratégias |
| **Filtragem** | Geralmente ausente ou rudimentar | Filtragem inteligente: remove conteúdo redundante, obsoleto ou irrelevante para a tarefa |
| **Ordenação** | Baseada em uma única pontuação de similaridade | Ordenação multidimensional: considera relevância, frescor, credibilidade e outros fatores, priorizando o envio das informações mais críticas |
| **Avaliação** | Falta de avaliação sistemática | Construção de conjuntos de dados "golden", avaliação quantitativa da qualidade da recuperação, precisão da resposta e eficiência do uso do contexto |

**Principais Melhorias:**
- **Diversificação da Estratégia de Recuperação**: Não depende mais de uma única recuperação vetorial, mas combina o uso de recuperação densa, recuperação esparsa, reordenação semântica e outras tecnologias de acordo com as características da tarefa.
- **Prioridade na Qualidade do Contexto**: Enfatiza que o que é enviado ao LLM não é "quanto mais, melhor", mas "quanto mais preciso, melhor", garantindo a alta qualidade do contexto através de filtragem e ordenação.
- **Otimização em Circuito Fechado**: Otimiza continuamente estratégias de recuperação, regras de filtragem e algoritmos de ordenação através de conjuntos de dados de avaliação, formando um processo de engenharia mensurável e aprimorável.

Essa perspectiva transforma o RAG de um processo de caixa preta em um problema de engenharia de contexto desmontável e otimizável, tornando-o mais operável e escalável.

Portanto, a engenharia de contexto é tanto uma prática de engenharia sistemática quanto uma arte que requer compensações. Ela exige que julguemos com precisão as seguintes 4 questões em meio a uma enorme quantidade de informações:

- **Write (Gravar)** —— Quais informações devem ser incluídas no contexto?
- **Select (Selecionar)** —— Qual conteúdo é mais relevante e necessário?
- **Compress (Comprimir)** —— O que pode ser resumido ou simplificado?
- **Isolate (Isolar)** —— O que precisa ser separado em um espaço independente?

Só quem entende essas questões pode realizar uma engenharia de contexto eficaz, alcançando a combinação perfeita de arte e engenharia.

![alt text](./images/Extra02-figures/image-12.png)

## Referências

Canghai Jiusu. Context Engineering: Key Technology for Optimizing Agent Performance [EB/OL]. (2025-07-10)[2025-10-21]. https://www.bilibili.com/video/BV1w3GNzeEHb/?spm_id_from=333.1387.upload.video_card.click&vd_source=0f47ed6b43bae0b240e774a8fd72e3e4

Drew Breunig. How Long Contexts Fail[EB/OL]. (2025-06-22)[2025-10-21]. https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html?ref=blog.langchain.com

Latent.Space, Jeff Huber, Swyx. RAG is Dead, Context Engineering is King[EB/OL]. (2025-08-19)[2025-10-21]. https://www.latent.space/p/chroma

Ten Thousand Words Dismantling. Is RAG Dead? Why Context Engineering is King? [EB/OL]. (2025-09-03)[2025-10-21]. https://www.woshipm.com/ai/6264065.html
