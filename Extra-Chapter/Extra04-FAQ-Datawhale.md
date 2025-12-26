<div align="right">
  <a href="./Extra04-DatawhaleFAQ.md">中文</a> | Português
</div>

# FAQ do Curso Hello-Agents Datawhale

> Este documento é baseado nas perguntas e respostas da transmissão ao vivo de 01/12/2024, bem como na coleta de dúvidas da construção do primeiro curso, usado como leitura complementar do curso Datawhale "Hello-Agents Introdução do Zero". Recomenda-se estudar junto com a documentação principal do curso:
> - 🔗 [Documentação do Curso](https://datawhalechina.github.io/hello-agents/#/)
> - ⌛️ Vídeos do Curso (a serem lançados)

---

## 1. Arquitetura Multi-Agente e Agendamento Paralelo

<strong>P1. Como sistemas multi-agente implementam "paralelização multi-thread"? Para etapas que podem ser paralelizadas decompostas pelo Agente de Planejamento de Tarefas, como fazer com que múltiplos Agentes de Execução reivindiquem tarefas automaticamente e processem dependências? Existe algum framework pronto?</strong>

- Pontos-chave:
  - Primeiro, o "Agente de Planejamento de Tarefas" faz a decomposição de dependências de tarefas, formando subtarefas paralelas e dependentes.
  - A camada de execução pode ser projetada como múltiplos Agentes especializados, cada um processando apenas as subtarefas sob sua responsabilidade.
  - O agendamento paralelo geralmente é implementado através de filas/polling de API, etc. A maioria dos cenários requer customização combinada com o negócio, não há solução completa universal de um clique.
- Orientação do curso: Capítulos relacionados a paradigmas multi-agente e arquitetura de sistema (paradigmas clássicos + parte de prática de frameworks).

---

## 2. Ecossistema de Frameworks e Protocolos de Comunicação

### 2.1 Frameworks Principais e Posicionamento do Hello-Agents

<strong>P2. Quais são os principais frameworks de Agentes atualmente? Qual problema o Hello-Agents resolve principalmente?</strong>

- Pontos-chave:
  - A comparação sistemática e ritmo de atualização dos principais frameworks é discutida intensivamente no capítulo seis do curso, não será repetido aqui.
  - <strong>Posicionamento do Hello-Agents</strong>: Focado principalmente em ensino e aprendizado, enfatizando "estrutura clara + implementável + fácil de generalizar", ajudando iniciantes a estabelecer um framework completo de conhecimento e prática de Agentes.
- Orientação do curso: Capítulo seis "Prática de Desenvolvimento de Frameworks".

<strong>P7. Hello-Agents parece ter funcionalidades completas. Se quiser usar em produção, quais capacidades ainda precisam ser complementadas?</strong>

- Pontos-chave:
  - O framework em si é orientado a "ensino + utilizável". A implementação real em produção ainda requer desenvolvimento secundário combinado com o negócio.
  - Pontos principais de aprimoramento geralmente estão em:
    - Modelagem de conhecimento de negócio e compreensão de cenários;
    - Mecanismos mais robustos de logging, monitoramento, avaliação e rollback;
    - Otimização de desempenho e controle de custos.
- Orientação do curso: Parte de frameworks + capítulos de prática de projetos.

### 2.2 Integração do Hello-Agents com LangGraph / Outros Frameworks

<strong>P4. Como usar Hello-Agents e LangGraph juntos? Quem chama quem?</strong>

<strong>P11 / P15. Gostaria de saber como usar A2A para integrar Hello-Agents e LangGraph?</strong>

- Pontos-chave (resposta combinada):
  - Pode usar <strong>protocolo A2A + Agent Card</strong> para expor "Agentes em um framework" como "capacidade remota" chamável por outro framework.
  - Analogia com Function Calling: Nós do LangGraph podem chamar Agentes do Hello-Agents como "funções remotas", e vice-versa.
- Orientação do curso: Capítulo dez "Protocolos de Comunicação de Agentes Inteligentes". Capítulo seis "Prática de Desenvolvimento de Frameworks".

<strong>P17. O aprendizado incluirá DeepResearch e outros frameworks open-source/prontos?</strong>

- Pontos-chave:
  - DeepResearch pertence ao conteúdo de Workflow, será complementado posteriormente.
  - Conteúdos relacionados a frameworks estão concentrados nos <strong>capítulos cinco e seis</strong>, desde "framework único" até "ecossistema multi-framework", todos serão abordados.
- Orientação do curso: Capítulo cinco, Capítulo seis (frameworks e casos de aplicação).

<strong>P18. Ao escrever Agentes com LangGraph, parece mais um "script de workflow de modelo grande". O que precisa ser considerado para implementar em um projeto real? Isso está no curso?</strong>

- Pontos-chave:
  - De "script" para "projeto", é preciso considerar mais: limites de módulos, gerenciamento de configuração, monitoramento e avaliação, recuperação de erros e colaboração em equipe, entre outros problemas de engenharia.
  - A <strong>quarta parte</strong> do curso conduz especificamente a construção de um projeto completo do zero, pode ser referenciada na prática.
- Orientação do curso: Quarta parte "Construindo Projetos".

### 2.3 Protocolos de Comunicação: A2A & ANP

<strong>P5. A2A e ANP foram explicados muito rapidamente. Pode ser mais detalhado e com exemplos de código?</strong>

- Pontos-chave:
  - O curso expandirá gradualmente desde "motivação → abstração → campos do protocolo → exemplos de código".
  - Recomenda-se leitura complementar do capítulo "Protocolos de Comunicação de Agentes Inteligentes" no livro Datawhale "Desenvolvimento Prático de Aplicações de Agentes" (explicação dos autores da comunidade ANP).
- Orientação do curso: Capítulo dez; pode ser linkado na "leitura complementar" do README à documentação oficial do Datawhale.

<strong>P6. Na prática, é mais recomendado implantação local ou usar API diretamente?</strong>

- Pontos-chave:
  - O <strong>capítulo sete</strong> do curso fornece ambas as rotas: implantação local e API em nuvem.
  - Fase de aprendizado: Priorizar API (baixa barreira), considerar local quando precisar controlar custos ou implantação offline.
  - Orientação do curso: Capítulo dez

Orientação de conteúdos relacionados do curso:
- 🔗 Capítulo cinco "Construção de Agentes Inteligentes Baseada em Plataforma Low-Code". Clique para ir: [Capítulo Cinco](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter5/第五章%20基于低代码平台的智能体搭建.md)
- 🔗 Capítulo seis "Prática de Desenvolvimento de Frameworks". Clique para ir: [Capítulo Seis](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter6/第六章%20框架开发实践.md)
- 🔗 Capítulo sete "Construindo Seu Framework de Agente Inteligente". Clique para ir: [Capítulo Sete](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter7/第七章%20构建你的Agent框架.md)
- 🔗 Capítulo dez "Protocolos de Comunicação de Agentes Inteligentes". Clique para ir: [Capítulo Dez](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter10/第十章%20智能体通信协议.md)


---

## 3. Posicionamento do Curso, Caminho de Aprendizado e Público Adequado

<strong>P9. Em quais plataformas o curso será disponibilizado posteriormente?</strong>

- Pontos-chave:
  - Repositório Github (código e documentação do curso)
  - Bilibili Datawhale (vídeos)
  - Site oficial Datawhale (entrada de curso em formato de artigo)

<strong>P10. É adequado para iniciantes absolutos?</strong>

- Pontos-chave:
  - Pode aprender, mas precisa "ir devagar + ver mais decomposição de código".
  - Estudantes sem base em Python precisam gastar energia extra para complementar sintaxe. O curso em si não é completamente de nível "zero programação".
  - Python recomenda o curso exclusivo Datawhale: 🔗 [Aprenda Python da Forma Inteligente](https://datawhalechina.github.io/learn-python-the-smart-way-v2/)
  - Para este curso, com base em Python, o caminho de aprendizado sugerido é o seguinte:
    1. Configuração de ambiente, prefácio
    2. 🔗 [Capítulo Um: Introdução aos Agentes Inteligentes](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter1/第一章%20初识智能体.md)
    3. 🔗 [Capítulo Dois: História do Desenvolvimento de Agentes Inteligentes](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter2/第二章%20智能体发展史.md)
    4. 🔗 [Capítulo Três: Fundamentos de Modelos de Linguagem Grande](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter3/第三章%20大语言模型基础.md)
    5. 🔗 [Capítulo Quatro: Construção de Paradigmas Clássicos de Agentes Inteligentes](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter4/第四章%20智能体经典范式构建.md)
    6. 🔗 [Capítulo Cinco: Construção de Agentes Inteligentes Baseada em Plataforma Low-Code](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter5/第五章%20基于低代码平台的智能体搭建.md)
    7. 🔗 [Capítulo Seis: Prática de Desenvolvimento de Aplicações com Frameworks](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter6/第六章%20框架开发实践.md)

<strong>P12. Os capítulos 1.1 e 2.1 parecem semelhantes. Qual é o foco de cada um?</strong>

- Pontos-chave:
  - <strong>2.1</strong>: Organiza sistematicamente Agentes de acordo com a linha do tempo de desenvolvimento, expandindo para casos como sistemas especialistas, mais orientado a "contexto histórico + perspectiva panorâmica".
  - <strong>1.1</strong>: Como introdução de abertura, apresenta brevemente conceitos básicos e contexto.

<strong>P13. Como estudantes que já trabalham devem aprender? Como usar no trabalho após aprender? Qual a diferença de ferramentas de workflow como n8n?</strong>

- Pontos-chave:
  - Sugestão de aprendizado: Combine com seu cenário de negócio, priorize fazer uma aplicação de Agente "pequena mas completa", mesmo que substitua apenas um pequeno segmento do workflow.
  - Diferença central com workflow (n8n, etc.):
    - Workflow: Processos e ramificações basicamente fixos, usados para resolver tarefas "estruturadas e bem determinadas".
    - Agente: Adequado para tarefas mais complexas e incertas, pode fazer tomadas de decisão autônomas até certo grau durante a execução (chamar ferramentas, planejar subtarefas, etc.).
- Orientação do curso: Capítulos de conceitos + paradigmas clássicos e parte de prática de projetos.

<strong>P14. Quais capítulos do curso são dedicados especificamente a testes e avaliação de Agentes?</strong>

- Pontos-chave:
  - Há um capítulo específico de avaliação (como capítulo doze), e também haverá casos práticos de "avaliação em ciclo fechado" em outros capítulos.
- Orientação do curso: Capítulo específico de testes e avaliação + prática de projetos relacionados.
- 🔗 [Capítulo Doze](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter12/第十二章%20智能体性能评估.md)

<strong>P16. Para fazer os experimentos do curso, qual é a configuração mínima de hardware? É necessária placa de vídeo?</strong>

- Pontos-chave:
  - <strong>Capítulo Agentic RL</strong>: Recomenda-se ambiente GPU com memória ≥ 4G para melhor experiência.
  - Outros capítulos usam API e podem completar todo o aprendizado, não é obrigatório ter GPU local.
- Orientação do curso: Explicação dos capítulos relacionados a Agentic RL.

<strong>P20. Estudantes universitários são adequados para aprender este curso?</strong>

- Pontos-chave:
  - Muito adequado, trate-o como um projeto de introdução na direção de "AI + engenharia de software" cruzada.
  - Recomenda-se combinar com seu projeto de conclusão / pequeno projeto de pesquisa para aumentar o valor prático.
  - Resultados de projeto de conclusão podem ser submetidos ao projeto através de PR:
    - 🔗 [Co-creation-projects](https://github.com/datawhalechina/hello-agents/tree/main/Co-creation-projects).

---

## 4. Conhecimento e Ferramentas: RAG / KAG / Grafos de Conhecimento / RL, etc.

<strong>P3. É necessário usar RAG ao construir Agentes?</strong>

- Pontos-chave:
  - RAG não é uma opção obrigatória para Agentes, mas é uma categoria comum de "ferramenta de conhecimento externo".
  - O <strong>capítulo oito</strong> do curso implementará uma solução RAG do zero, ajudando a entender as diferenças no design de sistema "com RAG / sem RAG".
- Orientação do curso: Capítulo oito.
- 🔗 [Capítulo Oito: Memória e Recuperação](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter8/第八章%20记忆与检索.md)

<strong>P21. Qual a relação entre KAG / grafos de conhecimento e Agentes?</strong>

- Pontos-chave:
  - Pode-se entender KAG / grafos de conhecimento como uma "ferramenta" ou "base de conhecimento" do Agente:
    - Agente é responsável por tomada de decisão e chamadas;
    - Grafo de conhecimento fornece conhecimento estruturado e capacidade de recuperação.

<strong>P22. RL, LLM, RLHF, de acordo com o critério de classificação do capítulo 1 "Agente de Aprendizado e Agente baseado em LLMs", pertencem a qual categoria respectivamente?</strong>

- Pontos-chave:
  - RL, LLM, RLHF são mais como <strong>componentes ou tecnologias de implementação</strong> de Agentes, não um "tipo de Agente" individual.
  - Por exemplo: LLM pode servir como cérebro do Agente; RL / RLHF podem ser usados para treinar ou ajustar fino a estratégia do Agente.

---

## 5. Desempenho e Gerenciamento de Contexto

<strong>P19. Prompts de sistema de tarefas complexas são muito longos, muitos tokens por chamada, causando lentidão na resposta; quanto mais longo o contexto, mais grave o problema. Como equilibrar?</strong>

- Pontos-chave:
  - A chave está em "corte e gerenciamento de contexto": histórico de diálogo, resultados de chamadas de ferramentas, prompts de sistema, etc., devem ser preservados em camadas e níveis.
  - Pode-se reduzir pressão de tokens através de "resumo + módulo de memória + contexto baseado em recuperação".
  - O <strong>capítulo nove</strong> do curso aborda especificamente estratégias de processamento de contexto.
- Orientação do curso: Capítulo nove.
- 🔗 [Capítulo Nove: Engenharia de Contexto](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter9/第九章%20上下文工程.md)

---

## 6. Projetos e Desenvolvimento de Carreira

<strong>P8. Os casos de projeto do curso podem ser incluídos no currículo?</strong>

- Pontos-chave:
  - Sim, desde que você realmente compreenda e possa explicar claramente:
    - Qual problema o projeto resolveu;
    - Quais designs de Agente foram usados;
    - Que trabalho específico foi feito em chamadas de ferramentas / avaliação / implantação.
  - Recomenda-se descrever esses projetos no currículo usando a estrutura "problema-solução-resultado", e anexar link do Github.

---

## 7. Configuração de Ambiente, Uso de Modelos e Problemas Relacionados a Chamadas de API

<strong>P17. Como as APIs do curso são configuradas? Existem situações de falha de chamada</strong>

- Pontos-chave:
  - Suporte de API de modelos do projeto do curso:
    - [API de Inferência do SiliconFlow](https://modelscope.cn/models);
    - [API do Deepseek](https://platform.deepseek.com/usage);
    - [API do OpenAI](https://platform.openai.com/docs/quickstart);
    - Outros ...
  - Processo de configuração: obter API_KEY, MODEL_ID, BASE_URL e definir no arquivo de variáveis de ambiente `.env`.
  - Método para obter API de modelos da comunidade modelscope: https://www.modelscope.cn/models/Qwen/Qwen3-VL-8B-Instruct
    - Clique na biblioteca de modelos, encontre modelos que suportam API-Inference, clique para entrar na página de detalhes do modelo, encontre API-Inference
    - ![alt text](./images/Extra04-figures/3f1b68eedc9d9e556fbb51358bf49f9d.png)
    - ![alt text](./images/Extra04-figures/e7dd177f-4867-4af0-bd0e-03771a3a040e.png)


<strong>P18. Ao implementar o workflow ReAct, existe substituto para a ferramenta de busca web serpApi?</strong>

- Pontos-chave:
  - Requer VPN, se não puder usar VPN, mude de solução;
  - Pode considerar outros mecanismos de busca, como: duckduckgo, googlesearch, etc.

<strong>P21. O modelo de inferência usado só suporta saída em streaming, não consegue entrar no loop subsequente do agente</strong>

- Pontos-chave:
  - Modelos de inferência como DeepSeek, Qwen, etc., por padrão, só fornecem API em streaming, é necessário fazer a concatenação correta e não apenas concatenação simples de strings (para detalhes sobre como concatenar, consulte capítulo sete)

<strong>P23. Como entender a associação entre memória e base de conhecimento?</strong>

- Pontos-chave:
  - Meu entendimento é: por exemplo, normalmente agentes têm memória de curto e longo prazo. Memória de curto prazo é equivalente ao que fazemos em um dia, podemos usar contexto diretamente como input para o modelo. Mas memória de longo prazo é como tomar notas, não podemos inserir tanta informação no contexto de uma vez, então fazemos uma ferramenta para o modelo chamar. O modelo pode gerar uma query e então chamar nossa base de conhecimento através de RAG, ou seja, memória de longo prazo.

<strong>P24. Erro 403 ao solicitar mirror da Tsinghua</strong>

- Pontos-chave:
  - Problema de rede, mude para USTC:
    - https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
    - https://mirrors.ustc.edu.cn/anaconda/pkgs/free/

<strong>P25. Erro 401 ao chamar API do modelo</strong>

- Pontos-chave:
  - Saldo do modelo insuficiente, precisa recarregar.

## 8. Problemas de Fundamentos Matemáticos

<strong>P22. Na fórmula de probabilidade, como entender P(w_2∣w_1)?</strong>

- Pontos-chave:
  - Probabilidade condicional em probabilidade, ou seja, a probabilidade de w_2 ocorrer dado que w_1 ocorreu

## 9. Outros Problemas

### 9.1 Relacionados a Projeto de Conclusão

<strong>P26. Após submeter o projeto de conclusão, como exibir no currículo e repositório pessoal?</strong>

- Pontos-chave:
  - Ver detalhes na seção 5 do [Capítulo Dezesseis](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter16/第十六章%20毕业设计.md)
  - No currículo, recomenda-se escrever assim (exemplo):
    - "Assistente Inteligente de Planejamento de Roteiros Turísticos Baseado no Framework Hello-Agents"
      - Responsabilidades: Design de papéis de Agente, orquestração de chamadas de ferramentas, recuperação RAG e avaliação de diálogo
      - Resultados: Gerar automaticamente planos de roteiro de vários dias, suportar restrições orçamentárias e preferências personalizadas
      - Link: `https://github.com/<your-id>/hello-agents/tree/main/projects/<your-folder>`
  - Durante a entrevista, foque em explicar claramente:
    - Por que projetar a estrutura do Agente dessa forma;
    - Quais métodos de avaliação foram usados;
    - Quais problemas foram encontrados (como contexto muito longo, custos de chamada, etc.) e como você lidou com eles.

---
