<div align="right">
  <a href="./README_EN.md">English</a> | <a href="./README.md">Português</a> | <a href="./README_ZH.md">中文</a>
</div>

<div align='center'>
  <img src="./docs/images/hello-agents.png" alt="alt text" width="100%">
  <h1>Hello-Agents</h1>
  <h3>"Construindo Sistemas de Agentes do Zero"</h3>
  <div align="center">
  <a href="https://trendshift.io/repositories/15520" target="_blank">
    <img src="https://trendshift.io/api/badge/repositories/15520" alt="datawhalechina%2Fhello-agents | Trendshift" style="width: 250px; height: 55px;" width="250" height="55"/>
  </a>
  </div>
  <p><em>Da teoria fundamental às aplicações práticas, domine o design e implementação de sistemas de agentes</em></p>
  <img src="https://img.shields.io/github/stars/datawhalechina/Hello-Agents?style=flat&logo=github" alt="GitHub stars"/>
  <img src="https://img.shields.io/github/forks/datawhalechina/Hello-Agents?style=flat&logo=github" alt="GitHub forks"/>
  <img src="https://img.shields.io/badge/language-Português-brightgreen?style=flat" alt="Language"/>
  <a href="https://github.com/datawhalechina/Hello-Agents"><img src="https://img.shields.io/badge/GitHub-Projeto-blue?style=flat&logo=github" alt="GitHub Project"></a>
  <a href="https://datawhalechina.github.io/hello-agents/"><img src="https://img.shields.io/badge/Leitura%20Online-green?style=flat&logo=gitbook" alt="Online Reading"></a>
</div>

---

## Introdução ao Projeto

&emsp;&emsp;Se 2024 foi o ano da "Batalha dos Cem Modelos", então 2025 inaugurou sem dúvida o "Ano dos Agentes". O foco da tecnologia está mudando do treinamento de modelos de fundação maiores para a construção de aplicações de agentes mais inteligentes. No entanto, tutoriais sistemáticos e orientados à prática são extremamente escassos. Por essa razão, lançamos o projeto Hello-Agents, esperando fornecer à comunidade um guia abrangente para construir sistemas de agentes do zero, equilibrando teoria e prática.

&emsp;&emsp;Hello-Agents é um **tutorial sistemático de aprendizado de agentes** da comunidade Datawhale. Hoje, o desenvolvimento de agentes está dividido principalmente em duas escolas: uma são agentes orientados à engenharia de software como Dify, Coze e n8n, que são essencialmente desenvolvimento de software orientado a processos com LLMs servindo como backends de processamento de dados; a outra são agentes AI-nativos, agentes verdadeiramente orientados por IA. Este tutorial visa levá-lo a entender profundamente e construir o último — Agentes verdadeiramente AI Nativos. O tutorial irá guiá-lo através da superfície dos frameworks, começando pelos princípios fundamentais dos agentes, aprofundando-se em sua arquitetura central, entendendo seus paradigmas clássicos e, finalmente, construindo suas próprias aplicações multi-agente. Acreditamos que a melhor maneira de aprender é através da prática. Esperamos que este tutorial possa ser seu ponto de partida para explorar o mundo dos agentes, transformando você de um "usuário" de grandes modelos de linguagem em um "construtor" de sistemas de agentes.

## Início Rápido

### Leitura Online
**[Clique aqui para começar a leitura online](https://datawhalechina.github.io/hello-agents/)** - Sem necessidade de download, aprenda a qualquer hora, em qualquer lugar

**[Cookbook (Beta)](https://book.heterocat.com.cn/)**

### Leitura Local
Se você deseja ler localmente ou contribuir com conteúdo, consulte o guia de aprendizado abaixo.

### O Que Você Vai Aprender?

- **Datawhale Open Source e Gratuito** - Aprenda todo o conteúdo do projeto completamente grátis, cresça com a comunidade
- **Entenda os Princípios Fundamentais** - Compreenda profundamente conceitos de agentes, história e paradigmas clássicos
- **Implementação Prática** - Domine plataformas low-code populares e frameworks de código de agentes
- **Framework Auto-desenvolvido [HelloAgents](https://github.com/jjyaoao/helloagents)** - Construa seu próprio framework de agente do zero baseado na API nativa da OpenAI
- **Domine Habilidades Avançadas** - Implementação passo a passo de engenharia de contexto, Memória, protocolos, avaliação e outras tecnologias sistemáticas
- **Treinamento de Modelos** - Domine Agentic RL, do SFT ao GRPO com treinamento prático completo de LLM
- **Impulsione Casos Reais** - Desenvolvimento prático de assistentes de viagem inteligentes, cyber towns e outros projetos abrangentes
- **Entrevistas de Emprego** - Aprenda perguntas de entrevista relacionadas a agentes para busca de emprego

## Navegação de Conteúdo

| Capítulo                                                                                                               | Conteúdo Principal                                                         | Status |
| --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ------ |
| [Prefácio](./docs/Prefacio.md)                                                                                        | Origem do projeto, contexto e sugestões para leitores                      | ✅      |
| **Parte 1: Fundamentos de Agentes e Modelos de Linguagem**                                                            |                                                                            |        |
| [Capítulo 1: Introdução aos Agentes](./docs/chapter1/Capitulo1-Introducao-aos-Agentes.md)                             | Definição, tipos, paradigmas e aplicações de agentes                       | ✅      |
| [Capítulo 2: História dos Agentes](./docs/chapter2/Capitulo2-Historia-dos-Agentes.md)                                 | Evolução do simbolismo aos agentes orientados por LLM                      | ✅      |
| [Capítulo 3: Fundamentos de LLMs](./docs/chapter3/Capitulo3-Fundamentos-de-LLMs.md)                                   | Transformer, prompts, LLMs mainstream e suas limitações                    | ✅      |
| **Parte 2: Construindo seu Agente LLM**                                                                               |                                                                            |        |
| [Capítulo 4: Paradigmas Clássicos de Agentes](./docs/chapter4/Capitulo4-Paradigmas-Classicos-de-Agentes.md)           | Implementação prática de ReAct, Plan-and-Solve, Reflection                 | ✅      |
| [Capítulo 5: Plataformas Low-Code](./docs/chapter5/Capitulo5-Plataformas-Low-Code.md)                                 | Entendendo Coze, Dify, n8n e outras plataformas low-code                   | ✅      |
| [Capítulo 6: Prática de Frameworks](./docs/chapter6/Capitulo6-Pratica-de-Frameworks.md)                               | AutoGen, AgentScope, LangGraph e outras aplicações de frameworks           | ✅      |
| [Capítulo 7: Construindo seu Framework](./docs/chapter7/Capitulo7-Construindo-seu-Framework.md)                       | Construindo um framework de agente do zero                                 | ✅      |
| **Parte 3: Conhecimento Avançado**                                                                                    |                                                                            |        |
| [Capítulo 8: Memória e Recuperação](./docs/chapter8/Capitulo8-Memoria-e-Recuperacao.md)                               | Sistemas de memória, RAG, armazenamento                                    | ✅      |
| [Capítulo 9: Engenharia de Contexto](./docs/chapter9/Capitulo9-Engenharia-de-Contexto.md)                             | "Compreensão contextual" para interação contínua                           | ✅      |
| [Capítulo 10: Protocolos de Comunicação](./docs/chapter10/Capitulo10-Protocolos-de-Comunicacao.md)                    | Análise de MCP, A2A, ANP e outros protocolos                               | ✅      |
| [Capítulo 11: Agentic-RL](./docs/chapter11/Capitulo11-Agentic-RL.md)                                                  | Treinamento prático de LLM do SFT ao GRPO                                  | ✅      |
| [Capítulo 12: Avaliação de Desempenho](./docs/chapter12/Capitulo12-Avaliacao-de-Desempenho.md)                        | Métricas principais, benchmarks e frameworks de avaliação                  | ✅      |
| **Parte 4: Estudos de Caso**                                                                                          |                                                                            |        |
| [Capítulo 13: Assistente de Viagem Inteligente](./docs/chapter13/Capitulo13-Assistente-de-Viagem.md)                  | Aplicações reais de MCP e colaboração multi-agente                         | ✅      |
| [Capítulo 14: Agente de Pesquisa Profunda](./docs/chapter14/Capitulo14-Pesquisa-Profunda.md)                          | Reprodução e análise do DeepResearch Agent                                 | ✅      |
| [Capítulo 15: Construindo Cyber Town](./docs/chapter15/Capitulo15-Cyber-Town.md)                                      | Combinando agentes com jogos, simulando dinâmicas sociais                  | ✅      |
| **Parte 5: Projeto Final**                                                                                            |                                                                            |        |
| [Capítulo 16: Projeto de Conclusão](./docs/chapter16/Capitulo16-Projeto-de-Conclusao.md)                              | Construa sua própria aplicação multi-agente completa                       | ✅      |

## Como Aprender

&emsp;&emsp;Bem-vindo, futuro construtor de sistemas inteligentes! Antes de embarcar nesta jornada emocionante, permita-nos dar algumas orientações claras.

&emsp;&emsp;Este projeto equilibra teoria e prática, visando ajudá-lo a dominar sistematicamente todo o processo de design e desenvolvimento desde agentes únicos até sistemas multi-agente. Portanto, é especialmente adequado para **desenvolvedores de IA, engenheiros de software, estudantes** com alguma base de programação, bem como **autodidatas** com forte interesse em tecnologia de IA de ponta.

&emsp;&emsp;O projeto é dividido em cinco partes principais:

- **Parte 1: Fundamentos de Agentes e Modelos de Linguagem** (Capítulos 1-3), começamos pela definição, tipos e história de desenvolvimento dos agentes. Em seguida, consolidamos rapidamente o conhecimento central dos grandes modelos de linguagem.

- **Parte 2: Construindo seu Agente LLM** (Capítulos 4-7), este é o ponto de partida da sua prática. Você implementará pessoalmente paradigmas clássicos como ReAct, experimentará a conveniência de plataformas low-code como Coze e dominará a aplicação de frameworks mainstream como LangGraph.

- **Parte 3: Extensão de Conhecimento Avançado** (Capítulos 8-12), nesta parte, seu agente "aprenderá" a pensar e colaborar. Exploraremos profundamente tecnologias centrais como memória e recuperação, engenharia de contexto e treinamento de Agentes.

- **Parte 4: Estudos de Caso Abrangentes** (Capítulos 13-15), esta é a interseção de teoria e prática. Você integrará o que aprendeu e criará pessoalmente assistentes de viagem inteligentes, agentes de pesquisa profunda automatizados e até uma cyber town que simula dinâmicas sociais.

- **Parte 5: Projeto Final e Perspectivas Futuras** (Capítulo 16), no final da jornada, você enfrentará um projeto de conclusão, construindo uma aplicação multi-agente completa própria.

## Como Contribuir

Somos uma comunidade open-source e damos boas-vindas a qualquer forma de contribuição!

- **Reportar Bugs** - Encontrou problemas de conteúdo ou código, por favor submeta uma Issue
- **Fazer Sugestões** - Tem boas ideias para o projeto, seja bem-vindo para iniciar discussões
- **Melhorar Conteúdo** - Ajude a melhorar o tutorial, submeta seu Pull Request
- **Compartilhar Prática** - Compartilhe suas notas de aprendizado e projetos em "Contribuições da Comunidade"

## Agradecimentos

### Contribuidores Principais
- [Chen Sizhou - Líder do Projeto](https://github.com/jjyaoao) (Membro Datawhale, escrita e revisão do texto completo)
- [Sun Tao - Líder do Projeto](https://github.com/fengju0213) (Membro Datawhale, conteúdo do Capítulo 9 e revisão)
- [Jiang Shufan - Líder do Projeto](https://github.com/Tsumugii24) (Membro Datawhale, design de exercícios e revisão)

### Agradecimentos Especiais
- Obrigado a [@Sm1les](https://github.com/Sm1les) pela ajuda e suporte a este projeto
- Obrigado a todos os desenvolvedores que contribuíram para este projeto

<div align=center style="margin-top: 30px;">
  <a href="https://github.com/datawhalechina/Hello-Agents/graphs/contributors">
    <img src="https://contrib.rocks/image?repo=datawhalechina/Hello-Agents" />
  </a>
</div>

## Sobre Datawhale

<div align='center'>
    <img src="./docs/images/datawhale.png" alt="Datawhale" width="30%">
    <p>Escaneie o código QR para seguir a conta oficial do Datawhale e obter mais conteúdo open source de qualidade</p>
</div>

---

## Licença Open Source

Este trabalho está licenciado sob uma [Licença Creative Commons Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional](http://creativecommons.org/licenses/by-nc-sa/4.0/).

