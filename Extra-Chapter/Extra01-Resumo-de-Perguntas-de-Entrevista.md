<div align="right">
  <a href="./Extra01-面试问题总结.md">中文</a> | Português
</div>

# Resumo de Perguntas de Entrevista para LLM, VLM e Agentes

Este documento é uma coleção de "perguntas essenciais" (conhecidas como "bagu" em chinês) de entrevista, compiladas durante a preparação para a temporada de recrutamento de outono de 2025.

O autor candidatou-se principalmente a cargos como: Engenheiro de Algoritmos de Grandes Modelos (Large Model Algorithm Engineer), Engenheiro de Agentes, Engenheiro de Desenvolvimento de IA, Engenheiro de Avaliação de Algoritmos, etc. As empresas entrevistadoras foram principalmente grandes e médias empresas de internet nacionais. Portanto, a profundidade e a abrangência das perguntas neste documento giram em torno dos requisitos desses cargos, cobrindo todo o stack tecnológico, desde teorias centrais de LLM/VLM, passando pelo desenvolvimento de aplicações RAG/Agent, até tecnologias de alinhamento RLHF e avaliação de Modelos/Agentes. Todas as perguntas foram organizadas a partir de experiências reais em múltiplas entrevistas técnicas online.

【Sugestões de Uso】
Este documento serve apenas para aprendizado e referência. Para obter os melhores resultados, recomenda-se fortemente pensar de forma independente sobre cada pergunta primeiro, tentar construir sua própria resposta e, em seguida, compará-la com as ideias de referência (caso disponíveis) ou pesquisar para preencher as lacunas. Saber o "quê" não é suficiente; é preciso entender o "porquê". A memorização direta é a maneira menos eficiente.

Desejo a todos sucesso na busca por emprego e que consigam as ofertas desejadas!

---

### 1. Fundamentos de LLM

1.  Por favor, explique detalhadamente como funciona o mecanismo de autoatenção (Self-Attention) no modelo Transformer. Por que ele é mais adequado para lidar com sequências longas do que as RNNs?
2.  O que é codificação posicional (Positional Encoding)? Por que ela é necessária no Transformer? Liste pelo menos duas formas de implementação.
3.  Apresente detalhadamente o RoPE (Rotary Positional Embedding). Quais são suas vantagens e desvantagens em comparação com a codificação posicional absoluta?
4.  Você conhece as diferenças entre MHA (Multi-Head Attention), MQA (Multi-Query Attention) e GQA (Grouped-Query Attention)? Explique detalhadamente.
5.  Compare algumas arquiteturas comuns de LLM, como Encoder-Only, Decoder-Only e Encoder-Decoder, e explique em quais tipos de tarefas cada uma se destaca.
6.  O que são as "Scaling Laws" (Leis de Escala)? Qual relação elas revelam entre o desempenho do modelo, o volume de computação e a quantidade de dados? Qual é o significado disso para a pesquisa e desenvolvimento de LLMs?
7.  Na fase de inferência de um LLM, quais são as estratégias de decodificação comuns? Explique os princípios, vantagens e desvantagens de Greedy Search, Beam Search, Top-K Sampling e Nucleus Sampling (Top-P).
8.  O que é tokenização (Tokenization)? Compare dois algoritmos principais de segmentação de subpalavras: BPE (Byte Pair Encoding) e WordPiece.
9.  Qual você acha que é a maior diferença entre NLP (Processamento de Linguagem Natural) tradicional e LLM? Quais são as semelhanças e diferenças entre eles?
10. O que são regularização L1 e L2, respectivamente, e em quais cenários são adequadas?
11. "Habilidades emergentes" (Emergent Abilities) é um fenômeno muito discutido em grandes modelos. Como você entende esse conceito? Geralmente, em que escala de modelo ele aparece?
12. Você conhece funções de ativação? Quais funções de ativação são comumente usadas em LLMs? Por que elas são escolhidas?
13. Como o modelo de Mistura de Especialistas (MoE - Mixture of Experts) expande efetivamente a escala de parâmetros do modelo sem aumentar significativamente o custo de inferência? Descreva brevemente seu princípio de funcionamento.
14. Ao treinar um LLM com centenas de bilhões ou trilhões de parâmetros, quais são os principais desafios de engenharia e algoritmos que você enfrentaria? (Ex: memória de vídeo, comunicação, instabilidade de treinamento, etc.)
15. Quais frameworks open source você conhece? Você já estudou os artigos do Qwen ou Deepseek? Quais são as principais inovações apresentadas neles?
16. Quais artigos recentes e de ponta sobre LLM você leu? Fale sobre os métodos relacionados, quais problemas eles abordam, quais métodos propõem e quais foram os experimentos comparativos.

---

### 2. Fundamentos de VLM

1.  Qual é o principal desafio dos Grandes Modelos Multimodais (como VLM)? Ou seja, como realizar o alinhamento e a fusão eficazes de informações de diferentes modalidades (como visual e linguagem)?
2.  Explique o princípio de funcionamento do modelo CLIP. Como ele conecta imagens e textos através do aprendizado contrastivo?
3.  Como modelos como LLaVA ou MiniGPT-4 conectam um codificador visual (Vision Encoder) pré-treinado a um grande modelo de linguagem (LLM)? Descreva seu design arquitetônico principal.
4.  O que é ajuste fino de instrução visual (Visual Instruction Tuning)? Por que ele é um passo fundamental para permitir que o VLM tenha boas capacidades de diálogo e seguimento de instruções?
5.  Ao lidar com dados multimodais como vídeo, em comparação com imagens estáticas, quais problemas adicionais o VLM precisa resolver? (Ex: como representar informações temporais?)
6.  Explique o significado de "Grounding" no campo de VLM. Como avaliamos se um VLM consegue corresponder com precisão uma descrição textual a uma região específica na imagem?
7.  Compare pelo menos dois paradigmas de arquitetura de VLM diferentes (como codificador compartilhado vs. fusão de atenção transmodal) e analise seus prós e contras.
8.  Em aplicações de VLM, como lidar com imagens de entrada de alta resolução? Quais desafios de computação e design de modelo isso traz?
9.  Ao gerar conteúdo, o VLM também enfrenta o problema de "alucinação" (Hallucination), mas como sua manifestação difere da de um LLM puramente textual? Dê exemplos.
10. Além de descrição de imagens e perguntas e respostas visuais (VQA), quais outras direções de aplicação de ponta ou potenciais para VLMs você consegue listar?
11. Você já realizou ajuste fino (fine-tuning) relacionado a VLM? Qual modelo?

---

### 3. Fundamentos de RLHF

1.  Em comparação com o SFT (Supervised Fine-Tuning) tradicional, quais problemas centrais dos modelos de linguagem o RLHF (Reinforcement Learning from Human Feedback) visa resolver? Por que o SFT por si só não é suficiente para alcançar nossos objetivos de "alinhamento"?
2.  Descreva detalhadamente as três fases principais do fluxo clássico de RLHF. Em cada fase, qual é a entrada, qual é a saída e qual é o objetivo principal?
3.  Na fase de treinamento do RM (Reward Model - Modelo de Recompensa), geralmente coletamos dados de comparação em pares, em vez de pedir aos anotadores humanos que deem uma pontuação absoluta diretamente para a resposta. Quais são as principais vantagens e desvantagens potenciais dessa abordagem?
4.  O design do modelo de recompensa é crucial. Como sua arquitetura de modelo é geralmente escolhida? Qual é a relação dele com o LLM que queremos otimizar? Ao treinar o modelo de recompensa, qual função de perda é comumente usada? Explique os princípios matemáticos por trás dela (por exemplo, pode-se explicar combinando com o modelo Bradley-Terry).
5.  Na terceira fase do RLHF, o PPO (Proximal Policy Optimization) é o algoritmo de aprendizado por reforço mais comum. Por que escolher o PPO em vez de outros algoritmos de gradiente de política mais simples (como REINFORCE) ou algoritmos baseados em Q-learning? Qual papel fundamental desempenha o termo de penalidade de divergência KL no PPO?
6.  Se durante o treinamento com PPO, o coeficiente $\beta$ do termo de penalidade de divergência KL for definido muito alto ou muito baixo, quais problemas isso causaria, respectivamente? Como você ajustaria esse hiperparâmetro através de experimentos e observação?
7.  O que é "Reward Hacking" (Trapaça de Recompensa)? Dê um exemplo combinando com um cenário específico de aplicação de LLM e discuta algumas possíveis estratégias de mitigação.
8.  O processo de RLHF é complexo e instável. Nos últimos anos, surgiram algumas alternativas, como o DPO (Direct Preference Optimization). Explique a ideia central do DPO e compare suas principais diferenças e vantagens em relação ao RLHF tradicional (baseado em PPO).
9.  Imagine que seu modelo RLHF treinado teve um excelente desempenho na avaliação offline, com pontuações altas no modelo de recompensa, mas após o lançamento, o feedback dos usuários é que as respostas se tornaram cada vez mais "padronizadas", bajuladoras e com falta de informação. O que você acha que pode ser a causa? A partir de quais aspectos você analisaria e resolveria esse problema?
10. Você conhece o GRPO da Deepseek? Qual é a principal diferença entre ele e o PPO? Quais são as vantagens e desvantagens?
11. Já ouviu falar de GSPO e DAPO? Qual a diferença entre eles e o GRPO?
12. Como resolver o problema de atribuição de crédito? Qual a diferença entre recompensa em nível de token e em nível de sequência (seq level)?
13. Além do feedback humano, também podemos usar o feedback da própria IA para fazer o alinhamento, ou seja, RLAIF (Reinforcement Learning from AI Feedback). Fale sobre sua compreensão do RLAIF, quais são seus potenciais e riscos?

---

### 4. Agentes (Agent)

1.  Como você define um Agente inteligente baseado em LLM? De quais componentes principais ele geralmente é constituído?
2.  Explique detalhadamente o framework ReAct. Como ele combina a cadeia de pensamento (Chain-of-Thought) e a ação para completar tarefas complexas?
3.  No design de Agentes, a "capacidade de planejamento" é crucial. Fale sobre quais métodos principais existem atualmente para conferir capacidade de planejamento aos LLMs? (Ex: CoT, ToT - Tree of Thoughts, GoT - Graph of Thoughts, etc.)
4.  A Memória é um módulo chave de um Agente. Como projetar sistemas de memória de curto e longo prazo para um Agente? Quais ferramentas ou tecnologias externas podem ser utilizadas?
5.  O Uso de Ferramentas (Tool Use) é uma maneira eficaz de expandir as capacidades do Agente. Explique como o LLM aprende a chamar APIs ou ferramentas externas? (Pode explicar do ponto de vista de Function Calling)
6.  Compare dois frameworks populares de desenvolvimento de Agentes, como LangChain e LlamaIndex. Quais são as diferenças em seus cenários principais de aplicação?
7.  Ao construir um Agente complexo, qual você considera ser o principal desafio?
8.  O que é um sistema multiagente? Quais são as vantagens de ter vários Agentes LLM trabalhando em colaboração em comparação com um único Agente? E quais novas complexidades isso introduz?
9.  Quando um Agente precisa executar tarefas em um ambiente real ou simulado (como robôs, jogos), qual é a diferença essencial em relação a um Agente puramente baseado em ferramentas de software?
10. Como garantir que o comportamento de um Agente seja seguro, controlável e alinhado com a intenção humana? No design do Agente, quais métodos de garantia de alinhamento existem?
11. Conhece o framework A2A (Agent-to-Agent)? Qual a diferença entre ele e frameworks de Agentes comuns? Cite um ponto de diferença fundamental.
12. Quais frameworks de Agente você já usou? Como foi feita a seleção? Qual é o indicador de avaliação do seu cenário final?
13. Você já fez ajuste fino (fine-tuning) de capacidade de Agente? Como o conjunto de dados foi coletado?

---

### 5. RAG (Geração Aumentada por Recuperação)

1.  Explique o princípio de funcionamento do RAG. Em comparação com o ajuste fino (fine-tuning) direto do LLM, qual problema principal o RAG resolve? Quais são as vantagens?
2.  Quais passos principais compõem um pipeline RAG completo? Descreva todo o processo, desde a preparação dos dados até a geração final.
3.  Ao construir uma base de conhecimento, a estratégia de fragmentação de texto (chunking) é crucial. Como você escolheria o tamanho adequado do fragmento e o comprimento da sobreposição? Qual é o compromisso (trade-off) por trás disso?
4.  Como escolher um modelo de embedding adequado? Quais indicadores avaliam a qualidade de um modelo de Embedding?
5.  Além da recuperação vetorial básica, quais outras técnicas você conhece que podem melhorar a qualidade da recuperação no RAG?
6.  Explique o problema "Lost in the Middle" (Perdido no Meio). Que fenômeno ele descreve no RAG? Quais métodos existem para mitigar esse problema?
7.  Como avaliar de forma abrangente o desempenho de um sistema RAG? Proponha indicadores de avaliação separadamente para as fases de recuperação e geração.
8.  Em que cenário você optaria por usar bancos de dados gráficos ou grafos de conhecimento para aprimorar ou substituir a recuperação tradicional em banco de dados vetoriais?
9.  O fluxo tradicional de RAG é "primeiro recuperar, depois gerar". Você conhece paradigmas de RAG mais complexos, como realizar múltiplas recuperações durante o processo de geração ou recuperação adaptativa?
10. Quais desafios um sistema RAG pode enfrentar na implantação real?
11. Conhece sistemas de busca? Qual a diferença em relação ao RAG?
12. Conhece ou já usou algum framework RAG open source, como o Ragflow? Como escolher o cenário adequado?

---

### 6. Avaliação de Modelos e Avaliação de Agentes

1.  Por que os indicadores tradicionais de avaliação de NLP (como BLEU, ROUGE) têm grandes limitações para avaliar a qualidade de geração dos LLMs modernos?
2.  Apresente alguns benchmarks abrangentes de LLM amplamente utilizados na indústria atualmente e explique seus respectivos focos. (Ex: MMLU, Big-Bench, HumanEval)
3.  O que é "LLM-as-a-Judge" (LLM como Juiz)? Quais são as vantagens e os potenciais vieses de usar um LLM para avaliar a saída de outro LLM?
4.  Como projetar um esquema de avaliação para medir uma capacidade específica de um LLM, como "factualidade/nível de alucinação", "capacidade de raciocínio" ou "segurança"?
5.  Por que avaliar um Agente é mais difícil e complexo do que avaliar um LLM básico? Quais dimensões de avaliação são diferentes?
6.  Você conhece benchmarks especificamente usados para avaliar as capacidades de Agentes? Como esses benchmarks geralmente constroem o ambiente de teste e as tarefas?
7.  Ao avaliar a conclusão de tarefas de um Agente, além da correção do resultado final, quais indicadores de processo valem a pena observar? (Ex: eficiência, custo, robustez)
8.  O que é "Red Teaming" (Teste de Equipe Vermelha)? Qual papel ele desempenha na descoberta de vulnerabilidades de segurança e vieses em LLMs e Agentes?
9.  Ao realizar avaliação humana, como projetar critérios e fluxos de avaliação razoáveis para garantir a objetividade e consistência dos resultados da avaliação?
10. Como monitorar e avaliar continuamente o desempenho de uma aplicação LLM ou serviço de Agente já implantado, para lidar com possíveis degradações de desempenho ou desvios de comportamento?

---

### 7. Perspectivas e Desenvolvimento de LLM

1.  Quão longe você acha que os LLMs atuais estão da Inteligência Artificial Geral (AGI)? Qual é a capacidade ausente mais crítica?
2.  Do GPT-4 para os modelos futuros, para onde você acha que a fusão multimodal irá? Será apenas a combinação de texto e imagem, ou se expandirá para mais dimensões sensoriais?
3.  Como você vê a competição e a coexistência entre ecossistemas de modelos open source e closed source? Quais são as vantagens de cada um e como eles evoluirão no futuro?
4.  Com o aumento da capacidade dos modelos, o "modelo de mundo" ou capacidade de simulação interna dos LLMs também tem recebido muita atenção. Como você entende esse conceito? Qual o significado disso para alcançar raciocínio e planejamento de ordem superior?
5.  "Dados" são o combustível para treinar LLMs. Qual papel você acha que dados sintéticos de alta qualidade desempenharão no treinamento futuro de modelos?
6.  A Inteligência Incorporada (Embodied AI), ou seja, a combinação de LLM com robôs, é considerada a próxima onda da IA. Como você acha que o LLM capacitará os robôs e quais desafios isso trará?
7.  A personalização é uma direção importante para aplicações de LLM. No processo de realizar Agentes ou assistentes altamente personalizados, como devemos equilibrar eficácia, privacidade e segurança?
8.  Você acha que a arquitetura Transformer dominará esse campo por muito tempo? Ou você vê potencial em novas arquiteturas como Modelos de Espaço de Estados (SSM, como Mamba)?
9.  Olhando para os próximos 3-5 anos, em qual setor ou área você acha que as tecnologias de LLM e Agentes têm maior probabilidade de alcançar aplicações disruptivas primeiro? Por quê?

---

### 8. Outros

1.  Qual você considera ser o maior gargalo limitando a capacidade e a popularização dos Agentes atualmente? (Ex: capacidade do modelo, custo, confiabilidade ou outros?)
2.  Nos últimos seis meses, qual artigo sobre Agentes ou qual projeto open source mais te impressionou? Por quê?
3.  Como você vê a "capacidade emergente" no campo dos Agentes? Devemos buscar modelos básicos mais poderosos ou arquiteturas de Agentes mais engenhosas?
4.  Em qual setor ou cenário você acha que a tecnologia de Agentes tem maior probabilidade de alcançar implementação comercial em larga escala nos próximos 1-2 anos?
5.  Se você pudesse explorar livremente, que tipo de Agente você mais gostaria de criar e para resolver qual problema?
6.  Para iniciantes que desejam entrar no campo de Agentes, que conselho você daria? Quais tecnologias devem ser o foco do aprendizado?
7.  Resumindo, quais qualidades essenciais você acha que um Engenheiro de Agentes de IA de ponta deve possuir?
8.  Você costuma usar IA? Para que a utiliza? Se eu quisesse usar IA, por exemplo, na área de codificação (coding), que sugestão você me daria?
```
