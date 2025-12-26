<div align="right">
  <a href="./Chapter2-History-of-Agents.md">English</a> | <a href="./第二章%20智能体发展史.md">中文</a> | Português
</div>

# Capítulo 2: História dos Agentes

Para compreender profundamente por que os agentes modernos apresentam sua forma atual e as origens de suas filosofias de design centrais, este capítulo remontará através da história: começando pela era clássica da inteligência artificial, explorando como a primeira "inteligência" foi definida dentro de sistemas de regras de lógica e símbolos; depois testemunhando a grande mudança de modelos de inteligência única e centralizada para pensamento de inteligência distribuída e colaborativa; e finalmente entendendo como o paradigma de "aprendizagem" transformou completamente a forma como os agentes adquirem capacidades, dando origem aos agentes modernos que vemos hoje.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-00.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.1 A escada evolutiva dos agentes de IA</p>
</div>

Como mostra a Figura 2.1, **o surgimento de cada novo paradigma visa resolver os "pontos problemáticos" centrais ou limitações fundamentais do paradigma da geração anterior.** Enquanto novas soluções trazem saltos de capacidade, elas também introduzem novas "limitações" difíceis de superar na época, que por sua vez lançam as bases para o nascimento do próximo paradigma geracional. Compreender este processo iterativo "orientado por problemas" nos ajuda a captar de forma mais profunda as razões profundas e a inevitabilidade histórica por trás das escolhas tecnológicas de agentes modernos.

## 2.1 Agentes Iniciais Baseados em Símbolos e Lógica

As explorações iniciais no campo da inteligência artificial foram profundamente influenciadas pela lógica matemática e princípios fundamentais da ciência da computação. Naquela época, os pesquisadores geralmente mantinham uma crença: a inteligência humana, especialmente a capacidade de raciocínio lógico, poderia ser capturada e reproduzida por sistemas simbólicos formalizados. Esta ideia central deu origem ao primeiro paradigma importante da inteligência artificial—Simbolismo, também conhecido como "IA Lógica" ou "IA Tradicional".

Na visão do simbolismo, o núcleo do comportamento inteligente é operar com símbolos baseado em um conjunto de regras explícitas. Portanto, um agente pode ser visto como um sistema de símbolos físicos: ele representa o mundo externo através de símbolos internos e planeja ações através de raciocínio lógico. A "sabedoria" dos agentes nesta era vinha inteiramente de bases de conhecimento e regras de raciocínio pré-codificadas por designers, em vez de adquiridas através de aprendizagem autônoma.

### 2.1.1 Hipótese do Sistema de Símbolos Físicos

A fundação teórica da era do simbolismo foi a **Hipótese do Sistema de Símbolos Físicos (Physical Symbol System Hypothesis - PSSH)**<sup>[1]</sup>, proposta conjuntamente por **Allen Newell** e **Herbert A. Simon** em 1976. Estes dois ganhadores do Prêmio Turing forneceram orientação teórica e critérios para implementar inteligência artificial geral em computadores através desta hipótese.

A hipótese contém duas afirmações centrais:

1. **Afirmação de Suficiência**: Qualquer sistema de símbolos físicos tem meios suficientes para produzir comportamento inteligente geral.
2. **Afirmação de Necessidade**: Qualquer sistema capaz de exibir comportamento inteligente geral deve essencialmente ser um sistema de símbolos físicos.

Um sistema de símbolos físicos aqui refere-se a um sistema que pode existir no mundo físico, composto por um conjunto de símbolos distinguíveis e uma série de processos que operam sobre esses símbolos, com elementos constituintes conforme mostra a Figura 2.2. Esses símbolos podem ser combinados em estruturas mais complexas (como expressões), enquanto processos podem criar, modificar, copiar e destruir essas estruturas simbólicas.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-0.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.2 Elementos constituintes de um sistema de símbolos físicos</p>
</div>

Em resumo, a PSSH declarou ousadamente: **A essência da inteligência é a computação e processamento de símbolos.**

Esta hipótese teve influência de longo alcance. Ela transformou o estudo do vago e complexo problema filosófico da mente humana em um problema concreto que poderia ser projetado e implementado em computadores. Ela instilou forte confiança nos primeiros pesquisadores de inteligência artificial de que, desde que pudéssemos encontrar a maneira certa de representar conhecimento e projetar algoritmos de raciocínio eficazes, poderíamos definitivamente criar inteligência de máquina comparável à humana. Quase toda pesquisa na era do simbolismo, de sistemas especialistas a planejamento automatizado, foi conduzida sob a orientação desta hipótese.

### 2.1.2 Sistemas Especialistas

Sob a influência direta da hipótese do sistema de símbolos físicos, **Sistemas Especialistas (Expert Systems)** se tornaram a conquista de aplicação mais importante e bem-sucedida da era do simbolismo. O objetivo central dos sistemas especialistas era simular a capacidade de especialistas humanos para resolver problemas em domínios específicos. Ao codificar conhecimento e experiência de especialistas em programas de computador, eles poderiam fornecer conclusões ou recomendações comparáveis ou até superiores às de especialistas humanos quando enfrentando problemas similares.

Um sistema especialista típico geralmente consiste de vários componentes centrais incluindo uma base de conhecimento, motor de inferência e interface de usuário, com uma arquitetura geral conforme mostra a Figura 2.3.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-1.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.3 Arquitetura geral de sistemas especialistas</p>
</div>

Esta arquitetura incorpora claramente a filosofia de design de separar conhecimento de raciocínio, uma característica importante da IA simbolista.

**Base de Conhecimento e Motor de Inferência**

A "inteligência" dos sistemas especialistas vem principalmente de seus dois componentes centrais: a base de conhecimento e o motor de inferência.

- **Base de Conhecimento**: Este é o centro de armazenamento de conhecimento do sistema especialista, usado para armazenar conhecimento e experiência de especialistas de domínio. **Representação de Conhecimento** é chave para construir uma base de conhecimento. Em sistemas especialistas, o método de representação de conhecimento mais comumente usado é **Regras de Produção**, ou seja, uma série de declarações condicionais na forma "SE-ENTÃO". Por exemplo: SE o paciente tem sintomas de febre E tosse ENTÃO pode ter infecção respiratória. Essas regras associam situações específicas (parte SE, condições) com conclusões ou ações correspondentes (parte ENTÃO, conclusões). Um sistema especialista complexo pode conter centenas ou milhares de tais regras, formando coletivamente uma vasta rede de conhecimento.
- **Motor de Inferência**: O motor de inferência é o núcleo computacional do sistema especialista. É um programa geral cuja tarefa é encontrar e aplicar regras relevantes na base de conhecimento com base em fatos fornecidos por usuários, derivando assim novas conclusões. O motor de inferência trabalha principalmente de duas maneiras:
  - **Encadeamento para Frente (Forward Chaining)**: Partindo de fatos conhecidos, continuamente correspondendo as partes SE das regras, acionando conclusões da parte ENTÃO, e adicionando novas conclusões à base de fatos até finalmente derivar o objetivo ou nenhuma nova regra pode ser correspondida. Esta é uma abordagem de raciocínio "orientada por dados".
  - **Encadeamento para Trás (Backward Chaining)**: Partindo de um objetivo hipotético (como "o paciente tem pneumonia"), encontrando regras que podem derivar aquele objetivo, então tomando a parte SE daquela regra como um novo sub-objetivo, recursando desta forma até que todos os sub-objetivos possam ser provados por fatos conhecidos. Esta é uma abordagem de raciocínio "orientada por objetivo".

**Caso de Aplicação e Análise: Sistema MYCIN**

MYCIN é um dos sistemas especialistas mais famosos e influentes da história, desenvolvido pela Universidade de Stanford nos anos 1970<sup>[2]</sup>. Foi projetado para auxiliar médicos no diagnóstico de infecções bacterianas no sangue e recomendar planos de tratamento antibiótico apropriados.

- **Princípio de Funcionamento**: MYCIN coletava sintomas do paciente, histórico médico e resultados de testes através de interações de perguntas e respostas com médicos. Sua base de conhecimento continha cerca de 600 regras "SE-ENTÃO" fornecidas por especialistas médicos. O motor de inferência trabalhava principalmente em encadeamento para trás: partindo do objetivo máximo de "determinar o patógeno", ele derivava para trás qual evidência e condições eram necessárias, então fazia perguntas aos médicos para obter esta informação. Seu fluxo de trabalho simplificado é mostrado na Figura 2.4.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-2.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.4 Diagrama esquemático do processo de raciocínio de encadeamento para trás do MYCIN</p>
</div>

- **Tratamento de Incerteza**: O diagnóstico médico é cheio de incerteza. Uma inovação importante do MYCIN foi introduzir o conceito de **Fator de Certeza (Certainty Factor - CF)**, usando um valor numérico entre -1 e 1 para representar a credibilidade de uma conclusão. Isso habilitou o sistema a lidar com conhecimento médico incerto e ambíguo e fornecer resultados de diagnóstico com avaliações de credibilidade, o que era mais próximo do mundo real do que lógica booleana simples.
- **Conquistas e Significado**: Em uma avaliação, o desempenho do MYCIN em diagnóstico de infecção no sangue excedeu o de médicos não especialistas e até alcançou o nível de especialistas humanos. Seu sucesso provou eloquentemente a validade da hipótese do sistema de símbolos físicos: através de cuidadosa engenharia de conhecimento e raciocínio simbólico, máquinas poderiam de fato exibir excelente "inteligência" em domínios profissionais altamente complexos. MYCIN não foi apenas um marco na história de desenvolvimento de sistemas especialistas, mas também pavimentou o caminho para aplicações comerciais subsequentes de inteligência artificial em vários domínios verticais.

### 2.1.3 SHRDLU

Se sistemas especialistas demonstraram a "profundidade" da IA simbólica em domínios profissionais, então o projeto SHRDLU<sup>[3]</sup> desenvolvido por **Terry Winograd** de 1968-1970 alcançou um avanço revolucionário em "amplitude". Como mostra a Figura 2.5, SHRDLU objetivava construir um agente inteligente abrangente que pudesse interagir fluentemente com humanos através de linguagem natural no micro-ambiente do "mundo dos blocos". O "mundo dos blocos" é um espaço virtual tridimensional simulado contendo blocos de diferentes formas, cores e tamanhos, bem como um braço robótico virtual que pode agarrá-los e movê-los. Usuários emitem comandos ou fazem perguntas ao SHRDLU em linguagem natural, e o SHRDLU executa ações no mundo virtual ou fornece respostas de texto.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-3.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.5 Interface de interação do "mundo dos blocos" do SHRDLU</p>
</div>

SHRDLU atraiu atenção generalizada na época principalmente porque foi o primeiro a integrar múltiplos módulos independentes de inteligência artificial (como análise de linguagem, planejamento, memória) em um sistema unificado e fazê-los trabalhar colaborativamente:

- **Compreensão de Linguagem Natural**: SHRDLU podia analisar sentenças em inglês estruturalmente complexas e ambíguas. Ele podia não apenas entender comandos diretos (como `Pegue um bloco vermelho grande.`) mas também lidar com instruções mais complexas, tais como:
  - Resolução de referência: `Encontre um bloco que seja mais alto que o que você está segurando e coloque-o na caixa.` Nesta instrução, o sistema precisa entender que `o que você está segurando` refere-se ao objeto atualmente agarrado pelo braço robótico.
  - Memória contextual: Usuários podiam dizer `Agarre a pirâmide.`, então perguntar `O que a caixa contém?`, e o sistema poderia responder conectando o contexto.
- **Planejamento e Ação**: Depois de entender instruções, SHRDLU podia autonomamente planejar uma série de ações necessárias para completar tarefas. Por exemplo, se a instrução fosse "coloque o bloco azul sobre o bloco vermelho", e já houvesse outro bloco verde sobre o bloco vermelho, o sistema planejaria a sequência de ações de "primeiro mova o bloco verde para fora, então coloque o bloco azul em cima".
- **Memória e Perguntas e Respostas**: SHRDLU tinha memória sobre seu ambiente e seu próprio comportamento. Usuários podiam fazer perguntas sobre isso, tais como:
  - Inquirindo sobre estado do mundo: `Há um bloco grande atrás de uma pirâmide?`
  - Inquirindo sobre histórico de comportamento: `Você tocou alguma pirâmide antes de colocar a verde sobre o cubinho?`
  - Inquirindo sobre motivação de comportamento: `Por que você pegou o bloco vermelho?` SHRDLU poderia responder: `PORQUE VOCÊ ME PEDIU PARA.`

O status histórico e influência do SHRDLU são refletidos principalmente em três aspectos:

- **Paradigma de Inteligência Abrangente**: Antes do SHRDLU, pesquisa de IA focava principalmente em funções únicas. Foi o primeiro a integrar múltiplos módulos de IA como compreensão de linguagem, planejamento de raciocínio e memória de ação em um sistema unificado. Seu design de ciclo fechado "perceber-pensar-agir" lançou a fundação para pesquisa moderna de agentes.
- **Popularização de Métodos de Pesquisa de Micro-Mundo**: Seu sucesso provou a viabilidade de explorar e verificar princípios básicos de agentes complexos em um ambiente simplificado com regras claras, um método que influenciou profundamente pesquisa subsequente de robótica e planejamento de IA.
- **Otimismo e Reflexão Desencadeados**: O sucesso do SHRDLU despertou expectativas otimistas iniciais para AGI, mas suas capacidades eram estritamente limitadas ao mundo dos blocos. Esta limitação desencadeou especulação de longo prazo no campo da IA sobre a diferença entre "processamento de símbolos" e "verdadeira compreensão", revelando desafios profundos no caminho para inteligência geral.

### 2.1.4 Desafios Fundamentais Enfrentando o Simbolismo

Apesar de conquistas significativas em projetos iniciais, começando nos anos 1980, IA simbólica encontrou dificuldades fundamentais inerentes à sua metodologia quando se movendo de "micro-mundos" para o mundo real aberto e complexo. Essas dificuldades podem principalmente ser resumidas em duas grandes categorias:

**(1) Conhecimento de Senso Comum e Gargalo de Aquisição de Conhecimento**

A "inteligência" de agentes simbólicos depende inteiramente da qualidade e completude de suas bases de conhecimento. No entanto, como construir uma base de conhecimento que possa suportar interação no mundo real provou ser uma tarefa extremamente árdua, refletida principalmente em dois aspectos:

- **Gargalo de Aquisição de Conhecimento**: O conhecimento de sistemas especialistas precisa ser construído por especialistas humanos e engenheiros de conhecimento através de processos tediosos de entrevistas, refinamento e codificação. Este processo é custoso, demorado e difícil de escalar. Mais importante, muito do conhecimento de especialistas humanos é implícito e intuitivo, difícil de ser claramente expresso como regras "SE-ENTÃO". Tentar simbolizar manualmente todo conhecimento do mundo inteiro é considerado uma tarefa quase impossível.
- **Problema de Senso Comum**: Comportamento humano depende de um vasto pano de fundo de senso comum (por exemplo, "água é molhada", "cordas podem puxar mas não empurrar"), mas sistemas simbólicos não sabem nada sobre isso a menos que explicitamente codificado. Estabelecer uma base de conhecimento completa para senso comum amplo e vago permanece um grande desafio até hoje. O projeto Cyc<sup>[4]</sup>, após décadas de esforço, ainda tem resultados e aplicações muito limitados.

**(2) Problema do Quadro e Fragilidade do Sistema**

Além de desafios a nível de conhecimento, o simbolismo também encontrou dilemas lógicos ao lidar com um mundo dinamicamente mutável.

- **Problema do Quadro (Frame Problem)**: Em um mundo dinâmico, como determinar eficientemente o que as coisas não mudaram depois que um agente executa uma ação é um quebra-cabeça lógico<sup>[5]</sup>. Declarar explicitamente todos os estados invariantes para cada ação é computacionalmente inviável, ainda assim humanos podem esforçadamente ignorar mudanças irrelevantes.
- **Fragilidade (Brittleness)**: Sistemas simbólicos dependem inteiramente de regras pré-definidas, tornando seu comportamento muito "frágil". Uma vez encontrando qualquer mudança menor ou nova situação fora das regras, o sistema pode falhar completamente, incapaz de se adaptar flexivelmente como humanos. O sucesso do SHRDLU foi precisamente porque operava em um mundo fechado com regras completas, enquanto o mundo real é cheio de exceções.

## 2.2 Construindo Chatbots Baseados em Regras

Depois de explorar os desafios teóricos do simbolismo, nesta seção experimentaremos intuitivamente como sistemas baseados em regras funcionam através de uma prática de programação específica. Tentaremos reproduzir ELIZA, um chatbot inicial extremamente influente na história da inteligência artificial.

### 2.2.1 Filosofia de Design da ELIZA

ELIZA foi um programa de computador lançado em 1966 pelo cientista de computação do MIT **Joseph Weizenbaum**<sup>[6]</sup>, uma das famosas tentativas iniciais no campo do processamento de linguagem natural. ELIZA não era um único programa mas um framework que podia executar diferentes "scripts". Entre eles, o script mais amplamente conhecido e bem-sucedido foi "DOCTOR", que imitava um psicoterapeuta rogeriano não-diretivo.

O método de trabalho da ELIZA era extremamente inteligente: ela nunca respondia diretamente perguntas ou fornecia informação, mas identificava palavras-chave na entrada do usuário, então aplicava um conjunto de regras de transformação pré-definidas para converter declarações do usuário em perguntas abertas. Por exemplo, quando um usuário dizia "Estou triste sobre meu namorado", ELIZA poderia identificar a palavra-chave "Estou triste sobre..." e aplicar uma regra para gerar a resposta: "Por que você está triste sobre seu namorado?"

A filosofia de design de Weizenbaum não era criar um agente que pudesse verdadeiramente "compreender" emoções humanas; pelo contrário, ele queria provar que através de algumas técnicas simples de transformação de sentenças, máquinas poderiam criar uma ilusão de "inteligência" e "empatia" sem entender o conteúdo da conversa de forma alguma. No entanto, para sua surpresa, muitas pessoas que interagiram com ELIZA (incluindo sua secretária) desenvolveram dependência emocional dela, acreditando profundamente que ela podia compreendê-las.

O objetivo prático desta seção é reproduzir o mecanismo central da ELIZA para compreender profundamente as vantagens e limitações fundamentais desta abordagem orientada por regras.

### 2.2.2 Correspondência de Padrões e Substituição de Texto

O fluxo de algoritmo da ELIZA é baseado em **Correspondência de Padrões e Substituição de Texto**, que pode ser claramente decomposto nos seguintes quatro passos:

1. **Identificação e Classificação de Palavras-Chave:** A base de regras define uma prioridade para cada palavra-chave (como `mãe`, `sonhou`, `deprimido`). Quando a entrada contém múltiplas palavras-chave, o programa seleciona a regra correspondente à palavra-chave com maior prioridade para processamento.
2. **Regras de Decomposição:** Depois de encontrar uma palavra-chave, o programa usa regras de decomposição com curingas (`*`) para capturar o resto da sentença.
   1. **Exemplo de Regra**: `* minha *`
   2. **Entrada do Usuário**: `"Minha mãe tem medo de mim"`
   3. **Resultado da Captura**: `["", "mãe tem medo de mim"]`
3. **Regras de Remontagem:** O programa seleciona uma de um conjunto de regras de remontagem associadas à regra de decomposição para gerar uma resposta (geralmente selecionada aleatoriamente para aumentar diversidade), e opcionalmente usa o conteúdo capturado no passo anterior.
   1. **Exemplo de Regra**: `"Me conte mais sobre sua família."`
   2. **Saída Gerada**: `"Me conte mais sobre sua família."`
4. **Conversão de Pronomes:** Antes da remontagem, o programa realiza conversão simples de pronomes (como `eu` → `você`, `meu` → `seu`) para manter coerência na conversa.

O fluxo de trabalho inteiro pode ser representado por uma simples ideia de pseudocódigo:

```Python
FUNÇÃO gerar_resposta(entrada_usuario):
    // 1. Dividir entrada do usuário em palavras
    palavras = DIVIDIR(entrada_usuario)

    // 2. Encontrar a regra de palavra-chave com maior prioridade
    melhor_regra = ENCONTRAR_MELHOR_REGRA(palavras)
    SE melhor_regra é NULO:
        RETORNAR uma_resposta_genérica() // Por exemplo: "Por favor, continue."

    // 3. Usar regra para decompor entrada do usuário
    partes_decompostas = DECOMPOR(entrada_usuario, melhor_regra.padrão_decomposição)
    SE decomposição_falhou:
        RETORNAR uma_resposta_genérica()

    // 4. Realizar conversão de pronomes em partes decompostas
    partes_transformadas = TRANSFORMAR_PRONOMES(partes_decompostas)

    // 5. Usar regras de remontagem para gerar resposta
    resposta = REMONTAR(partes_transformadas, melhor_regra.padrões_remontagem)

    RETORNAR resposta
```

Através deste mecanismo, ELIZA simplificou com sucesso o complexo problema de compreensão de linguagem natural em um jogo operável de correspondência de padrões baseado em regras.

### 2.2.3 Implementação da Lógica Central

Agora, transformaremos os princípios técnicos descritos na seção anterior em uma função Python simples e executável. O código a seguir implementa uma versão mini da ELIZA, que contém um pequeno conjunto de regras mas é suficiente para demonstrar seu mecanismo de trabalho central.

```Python
import re
import random

# Definir base de regras: padrão (expressão regular) -> lista de modelos de resposta
rules = {
    r'Eu preciso (.*)': [
        "Por que você precisa {0}?",
        "Realmente ajudaria você conseguir {0}?",
        "Tem certeza que precisa {0}?"
    ],
    r'Por que você não (.*)\?': [
        "Você realmente acha que eu não {0}?",
        "Talvez eventualmente eu {0}.",
        "Você realmente quer que eu {0}?"
    ],
    r'Por que não consigo (.*)\?': [
        "Você acha que deveria ser capaz de {0}?",
        "Se você pudesse {0}, o que faria?",
        "Não sei -- por que você não consegue {0}?"
    ],
    r'Eu sou (.*)': [
        "Você veio a mim porque é {0}?",
        "Há quanto tempo você é {0}?",
        "Como você se sente sobre ser {0}?"
    ],
    r'.* mãe .*': [
        "Me conte mais sobre sua mãe.",
        "Como era seu relacionamento com sua mãe?",
        "Como você se sente sobre sua mãe?"
    ],
    r'.* pai .*': [
        "Me conte mais sobre seu pai.",
        "Como seu pai te fazia sentir?",
        "O que seu pai te ensinou?"
    ],
    r'.*': [
        "Por favor, me conte mais.",
        "Vamos mudar o foco um pouco... Me conte sobre sua família.",
        "Pode elaborar sobre isso?"
    ]
}

# Definir regras de conversão de pronomes
pronoun_swap = {
    "eu": "você", "você": "eu", "me": "você", "meu": "seu",
    "sou": "é", "é": "sou", "era": "estava", "eu gostaria": "você gostaria",
    "eu tenho": "você tem", "eu vou": "você vai", "seus": "meus",
    "meus": "seus"
}

def swap_pronouns(phrase):
    """
    Realizar conversão de primeira/segunda pessoa em pronomes na frase de entrada
    """
    words = phrase.lower().split()
    swapped_words = [pronoun_swap.get(word, word) for word in words]
    return " ".join(swapped_words)

def respond(user_input):
    """
    Gerar resposta baseada na base de regras
    """
    for pattern, responses in rules.items():
        match = re.search(pattern, user_input, re.IGNORECASE)
        if match:
            # Capturar parte correspondida
            captured_group = match.group(1) if match.groups() else ''
            # Realizar conversão de pronomes
            swapped_group = swap_pronouns(captured_group)
            # Selecionar aleatoriamente um dos modelos e formatar
            response = random.choice(responses).format(swapped_group)
            return response
    # Se nenhuma regra específica for correspondida, usar a última regra curinga
    return random.choice(rules[r'.*'])

# Loop de chat principal
if __name__ == '__main__':
    print("Terapeuta: Olá! Como posso te ajudar hoje?")
    while True:
        user_input = input("Você: ")
        if user_input.lower() in ["sair", "exit", "tchau"]:
            print("Terapeuta: Tchau. Foi bom conversar com você.")
            break
        response = respond(user_input)
        print(f"Terapeuta: {response}")

>>>
Terapeuta: Olá! Como posso te ajudar hoje?
Você: Estou me sentindo triste hoje.
Terapeuta: Há quanto tempo você está se sentindo triste hoje?
Você: Preciso de ajuda com meu projeto.
Terapeuta: Tem certeza que precisa de ajuda com seu projeto?
Você: Minha mãe não está feliz com meu trabalho.
Terapeuta: Me conte mais sobre sua mãe.
Você: sair
Terapeuta: Tchau. Foi bom conversar com você.
```

Através da prática de programação acima, podemos intuitivamente resumir as limitações fundamentais de sistemas orientados por regras, que são confirmações diretas dos desafios teóricos do simbolismo discutidos na Seção `2.1.4`:

- **Falta de Compreensão Semântica**: O sistema não entende significados de palavras. Por exemplo, quando confrontado com a entrada "Eu **não** estou feliz", ele ainda mecanicamente corresponderá à regra `Eu sou (.*)` e gerará uma resposta semanticamente incorreta porque não pode entender o papel da palavra de negação "não".
- **Sem Memória Contextual**: O sistema é **sem estado**, com cada resposta baseada apenas na única entrada de sentença atual, incapaz de conduzir conversas coerentes de múltiplas voltas.
- **Problema de Escalabilidade de Regras**: Tentar adicionar mais regras leva a crescimento explosivo no tamanho da base de regras, e gerenciamento de conflitos e tratamento de prioridades entre regras se tornam extremamente complexos, finalmente tornando o sistema difícil de manter.

No entanto, apesar desses defeitos óbvios, ELIZA produziu o famoso "**efeito ELIZA**" na época, com muitos usuários acreditando que ela podia compreendê-los. Esta ilusão de inteligência principalmente derivava de suas estratégias inteligentes de conversa (como fazer o papel de um questionador passivo, usar modelos abertos) e psicologia inata de projeção emocional humana.

A prática da ELIZA revelou claramente a contradição central da abordagem simbolista: o desempenho aparentemente inteligente do sistema depende inteiramente de regras pré-codificadas por designers. No entanto, enfrentando as possibilidades infinitas da linguagem do mundo real, este método exaustivo está destinado a ser não escalável. O sistema não tem verdadeira compreensão, apenas executando operações de símbolos, que é a raiz de sua fragilidade.

## 2.3 Sociedade da Mente de Marvin Minsky

A exploração do simbolismo e a prática da ELIZA apontaram conjuntamente para um problema: um único motor de raciocínio centralizado construído através de regras pré-definidas parece difícil de levar à verdadeira inteligência. Não importa quão grande seja a base de regras, o sistema sempre aparece rígido e frágil quando enfrentando a ambiguidade, complexidade e mudanças infinitas do mundo real. Este dilema levou alguns dos principais pensadores a refletir sobre a filosofia de design mais fundamental da inteligência artificial. Entre eles, **Marvin Minsky** não continuou tentando adicionar mais regras a um único núcleo de raciocínio, mas propôs uma questão revolucionária em seu livro **"The Society of Mind" (A Sociedade da Mente)**<sup>[7]</sup>: "Que truque mágico nos torna inteligentes? O truque é que não há truque. O poder da inteligência deriva de nossa vasta diversidade, não de qualquer princípio único e perfeito."

### 2.3.1 Reflexão sobre Modelos de Inteligência Holística Única

Dos anos 1970 aos 1980, as limitações do simbolismo se tornaram cada vez mais aparentes. Embora sistemas especialistas alcançaram sucesso em domínios altamente verticais, eles não podiam possuir senso comum infantil; embora SHRDLU pudesse desempenhar excelentemente em um mundo de blocos fechado, não podia entender nada fora daquele mundo; embora ELIZA pudesse imitar conversação, não sabia nada sobre o conteúdo da conversa em si. Esses sistemas todos seguiram uma abordagem de design de cima para baixo: um processador central onisciente que processa informação e toma decisões de acordo com um conjunto unificado de regras lógicas.

Enfrentando este fracasso universal, Minsky começou a levantar uma série de questões fundamentais:

- **O que é "compreensão"?** Quando dizemos que compreendemos uma história, isto é uma única habilidade? Ou é na verdade o resultado de dezenas de diferentes processos mentais trabalhando juntos, como habilidade de visualização, habilidade de raciocínio lógico, habilidade de ressonância emocional e senso comum de relacionamentos sociais?
- **O que é "senso comum"?** Senso comum é uma enorme base de conhecimento contendo milhões de regras lógicas (como tentado pelo projeto Cyc)? Ou é uma rede distribuída tecida a partir de incontáveis experiências específicas e fragmentos simples de regras?
- **Como agentes deveriam ser construídos?** Deveríamos continuar perseguindo um sistema lógico perfeito e unificado, ou deveríamos reconhecer que inteligência em si é uma mistura "imperfeita" composta de muitas partes simples funcionalmente diferentes, até mesmo conflitantes?

Essas questões abordaram diretamente as falhas centrais de modelos de inteligência holística única. Tais modelos tentam resolver todos os problemas com uma representação unificada e mecanismo de raciocínio, mas isto está longe de como observamos inteligência natural (especialmente inteligência humana) operando. Minsky acreditava que forçar atividades mentais diversas em um quadro lógico rígido era a causa raiz da estagnação da pesquisa inicial de inteligência artificial.

Baseado nesta reflexão, Minsky propôs uma concepção subversiva: ele não via mais a mente como uma estrutura hierárquica tipo pirâmide, mas a via como uma "sociedade" achatada cheia de interação e colaboração.

### 2.3.2 Inteligência como Colaboração

No quadro teórico de Minsky, a definição de um agente difere dos agentes modernos que discutimos no Capítulo 1. Aqui, um agente refere-se a um processo mental extremamente simples e especializado que é em si "sem mente". Por exemplo, um agente `LINE-FINDER` responsável por identificar linhas, ou um agente `GRASP` responsável por agarrar.

Esses agentes simples são organizados para formar **Agências (Agencies)** mais poderosas. Uma agência é um grupo de agentes trabalhando juntos para completar uma tarefa mais complexa. Por exemplo, uma agência `BUILD` responsável por construir blocos poderia ser composta de múltiplos agentes ou agências de nível inferior como `SEE`, `FIND`, `GET` e `PUT`. Eles influenciam uns aos outros através de sinais descentralizados de ativação e inibição, formando fluxo de controle dinâmico.

**Emergência** é chave para compreender a teoria da sociedade da mente. Comportamento inteligente complexo e proposital não é pré-planejado por algum agente de alto nível, mas surge espontaneamente de interações locais entre numerosos agentes simples de nível inferior.

Vamos usar a clássica tarefa "construir uma torre de blocos" como exemplo para ilustrar este processo, como mostra a Figura 2.6. Quando um objetivo de alto nível (como "Eu quero construir uma torre") aparece, ele ativa uma agência de alto nível chamada `BUILD-TOWER`.

1. A agência `BUILD-TOWER` não sabe como executar ações físicas específicas; seu único papel é ativar suas agências subordinadas, como `BUILDER`.
2. A agência `BUILDER` também é muito simples; ela pode conter apenas lógica de loop: enquanto a torre não estiver terminada, ativar a agência `ADD-BLOCK`.
3. A agência `ADD-BLOCK` é responsável por coordenar subtarefas mais específicas; ela sequencialmente ativa três sub-agências: `FIND-BLOCK`, `GET-BLOCK` e `PUT-ON-TOP`.
4. Cada sub-agência é composta de agentes de nível ainda mais baixo. Por exemplo, a agência `GET-BLOCK` ativa o agente `SEE-SHAPE` no sistema visual e os agentes `REACH` e `GRASP` no sistema motor.

Neste processo, nenhum único agente ou agência tem um plano global para a tarefa inteira. `GRASP` é apenas responsável por agarrar; não sabe o que é uma torre; `BUILDER` é apenas responsável por fazer loops; não sabe como controlar o braço. No entanto, quando esta sociedade composta de incontáveis agentes "sem mente" interage através de regras simples de ativação e inibição, um comportamento aparentemente altamente inteligente—construir uma torre de blocos—emerge naturalmente.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-4.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.6 Diagrama esquemático do mecanismo de emergência do comportamento de construção de torre de blocos na "sociedade da mente"</p>
</div>

### 2.3.3 Inspiração Teórica para Sistemas Multi-Agente

A influência mais profunda da teoria da sociedade da mente é que ela forneceu uma importante fundação conceitual para **Inteligência Artificial Distribuída (Distributed Artificial Intelligence - DAI)** e posteriormente **Sistemas Multi-Agente (Multi-Agent Systems - MAS)**. Ela levou pesquisadores a pensar:

**Se inteligência dentro de uma mente emerge através de colaboração de numerosos agentes simples, então "inteligência coletiva" mais poderosa também pode emergir através de colaboração entre múltiplas entidades computacionais independentes e fisicamente separadas (computadores, robôs)?**

O levantamento desta questão diretamente mudou o foco de pesquisa de "como construir um único agente onipotente" para "como projetar um grupo de agentes colaborando eficientemente". Especificamente, a sociedade da mente diretamente inspirou pesquisa de MAS nos seguintes aspectos:

- **Controle Descentralizado**: O núcleo da teoria é que não há controlador central. Esta ideia foi completamente herdada pelo campo MAS, e como projetar mecanismos de coordenação e estratégias de alocação de tarefas sem nós centrais se tornou um dos tópicos centrais de pesquisa de MAS.
- **Computação Emergente**: Soluções para problemas complexos podem surgir espontaneamente de regras simples de interação local. Isto inspirou numerosos algoritmos baseados em emergência em MAS, como algoritmos de colônia de formigas e otimização de enxame de partículas, para resolver problemas complexos de otimização e busca.
- **Sociabilidade de Agentes**: A teoria de Minsky enfatizou interações entre agentes (ativação, inibição). O campo MAS expandiu ainda mais isto, estudando sistematicamente linguagens de comunicação entre agentes (como ACL), protocolos de interação (como redes de contrato), estratégias de negociação, modelos de confiança e até estruturas organizacionais, construindo assim verdadeiras sociedades computacionais.

Pode-se dizer que a teoria de "sociedade da mente" de Minsky forneceu um importante quadro analítico para pesquisadores de IA compreenderem a estrutura interna de "inteligência coletiva". Ela forneceu para pesquisadores posteriores uma perspectiva completamente nova para explorar sistemas complexos compostos de agentes computacionais independentes, autônomos e socialmente capazes, formalmente abrindo o prelúdio para pesquisa de sistemas multi-agente.

## 2.4 Evolução de Paradigmas de Aprendizagem e Agentes Modernos

A teoria de "sociedade da mente" discutida anteriormente apontou o caminho para inteligência coletiva e colaboração descentralizada a nível filosófico, mas o caminho de implementação permaneceu obscuro. Enquanto isso, os desafios fundamentais expostos pelo simbolismo ao lidar com complexidade do mundo real também indicaram que inteligência verdadeiramente robusta não poderia ser construída apenas em regras pré-codificadas.

Esses dois fios conjuntamente apontaram para uma questão: Se inteligência não pode ser completamente projetada, pode ser aprendida?

Esta questão abriu a era de "aprendizagem" da inteligência artificial. Seu objetivo central não era mais codificar manualmente conhecimento, mas construir sistemas que pudessem automaticamente adquirir conhecimento e capacidades de experiência e dados. Esta seção rastreará a evolução deste paradigma: da fundação de aprendizagem estabelecida pelo conexionismo, à aprendizagem interativa alcançada pela aprendizagem por reforço, até agentes modernos impulsionados por grandes modelos de linguagem hoje.

### 2.4.1 De Símbolos a Conexões

Como resposta direta às limitações do simbolismo, **Conexionismo (Connectionism)** ressurgiu nos anos 1980. Diferente da filosofia de design de cima para baixo do simbolismo dependendo de regras lógicas explícitas, conexionismo é uma abordagem de baixo para cima inspirada em imitar a estrutura de rede neural de cérebros biológicos<sup>[8]</sup>. Suas ideias centrais podem ser resumidas como segue:

1. **Representação Distribuída de Conhecimento**: Conhecimento não é armazenado em alguma base de conhecimento na forma de símbolos ou regras explícitas, mas é armazenado de maneira distribuída na forma de pesos de conexão entre numerosas unidades de processamento simples (ou seja, neurônios artificiais). O padrão de conexão da rede inteira em si constitui conhecimento.
2. **Unidades de Processamento Simples**: Cada neurônio apenas realiza computações muito simples, como receber entradas ponderadas de outros neurônios, processá-las através de uma função de ativação, e então produzir resultados para o próximo neurônio.
3. **Ajustando Pesos Através de Aprendizagem**: A inteligência do sistema não vem de programas complexos pré-escritos por designers, mas do processo de "aprendizagem". Ao ser exposto a numerosas amostras, o sistema automaticamente e iterativamente ajusta pesos de conexão entre neurônios de acordo com algum algoritmo de aprendizagem (como retropropagação), gradualmente fazendo a saída da rede inteira se aproximar do alvo desejado.

Sob este paradigma, agentes não são mais máquinas passivas de raciocínio lógico executando regras, mas sistemas adaptativos capazes de auto-otimização através de experiência. Como mostra a Figura 2.7, isto representa uma mudança fundamental na ideia central de construir agentes. Simbolismo tentou codificar explicitamente conhecimento humano para máquinas, enquanto conexionismo tentou criar máquinas que pudessem aprender conhecimento como humanos.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-5.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.7 Comparação de paradigmas simbolismo e conexionismo</p>
</div>

A ascensão do conexionismo, especialmente o sucesso da aprendizagem profunda no século 21, dotou agentes de capacidades poderosas de percepção e reconhecimento de padrões, habilitando-os a compreender diretamente o mundo de dados brutos (como imagens, sons, texto), o que era inimaginável na era do simbolismo. No entanto, como habilitar agentes a aprender a tomar decisões sequenciais ótimas em interações dinâmicas com o ambiente requereu suplementação de outro paradigma de aprendizagem.

### 2.4.2 Agentes Baseados em Aprendizagem por Reforço

Conexionismo principalmente resolveu problemas de percepção (por exemplo, "O que está nesta imagem?"), mas a tarefa mais central de agentes é tomada de decisão (por exemplo, "O que devo fazer nesta situação?"). **Aprendizagem por Reforço (Reinforcement Learning - RL)** é precisamente o paradigma de aprendizagem focado em resolver problemas de decisão sequencial. Ela não aprende diretamente de conjuntos de dados estáticos rotulados, mas aprende como maximizar seus benefícios a longo prazo através de interação direta entre agentes e o ambiente, aprendendo através de "tentativa e erro".

Tomando AlphaGo como exemplo, seu processo central de aprendizagem por auto-jogo é uma incorporação clássica de aprendizagem por reforço<sup>[9]</sup>. Neste processo, AlphaGo (o agente) observa o layout atual do tabuleiro (estado do ambiente) e decide onde colocar a próxima pedra (ação). Depois que um jogo termina, baseado no resultado de vitória-derrota, ele recebe um sinal claro: ganhar é uma recompensa positiva, perder é uma recompensa negativa. Através de milhões de tais sessões de auto-jogo, AlphaGo continuamente ajusta sua estratégia interna, gradualmente aprendendo quais ações escolher em quais situações de tabuleiro têm maior probabilidade de levar à vitória final. Este processo é completamente autônomo, não dependendo de orientação direta de registros de jogos humanos.

Este mecanismo de aprendizagem de otimizar seu próprio comportamento através de interação com o ambiente e baseado em sinais de feedback é o quadro central da aprendizagem por reforço. Abaixo detalharemos seus elementos constituintes básicos e modo de funcionamento.

O quadro de aprendizagem por reforço pode ser descrito por vários elementos centrais:

- **Agente**: O aprendiz e tomador de decisão. No exemplo de AlphaGo, é seu programa de tomada de decisão.
- **Ambiente**: Tudo externo ao agente, o objeto com o qual o agente interage. Para AlphaGo, são as regras do Go e o oponente.
- **Estado (S)**: Uma descrição específica do ambiente em um certo momento, a base para tomada de decisão do agente. Por exemplo, as posições atuais de todas as pedras no tabuleiro.
- **Ação (A)**: Operações que o agente pode tomar baseado no estado atual. Por exemplo, colocar uma pedra em uma posição legal no tabuleiro.
- **Recompensa (R)**: Um sinal escalar alimentado de volta ao agente pelo ambiente depois que o agente executa uma ação, usado para avaliar a qualidade daquela ação em um estado específico. Por exemplo, no final de um jogo, vitória recebe uma recompensa +1, derrota recebe uma recompensa -1.

Baseado nos elementos centrais acima, agentes de aprendizagem por reforço continuamente iteram em um loop fechado "perceber-agir-aprender", com seu modo de funcionamento mostrado na Figura 2.8.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-6.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.8 Loop de interação central da aprendizagem por reforço</p>
</div>

Os passos específicos deste loop são os seguintes:

1. No passo de tempo t, o agente observa o estado atual $S_{t}$ do ambiente.
2. Baseado no estado $S_{t}$, o agente seleciona uma ação $A_{t}$ de acordo com sua **Política (π)** interna e a executa. Uma política é essencialmente um mapeamento de estados para ações, definindo o comportamento do agente.
3. Depois de receber a ação $A_{t}$, o ambiente transiciona para um novo estado $S_{t+1}$.
4. Simultaneamente, o ambiente alimenta de volta uma recompensa imediata $R_{t+1}$ ao agente.
5. O agente usa este feedback (novo estado $S_{t+1}$ e recompensa $R_{t+1}$) para atualizar e otimizar sua política interna para tomar melhores decisões no futuro. Este processo de atualização é aprendizagem.

O objetivo de aprendizagem do agente não é maximizar a recompensa imediata em um certo passo de tempo, mas maximizar a **Recompensa Cumulativa** do momento atual até o futuro, também chamada **Retorno**. Isto significa que o agente precisa ter "previsão"; às vezes para obter maiores recompensas futuras, precisa sacrificar recompensas imediatas atuais (por exemplo, a estratégia de "sacrifício" no Go). Através de exploração contínua, coleta de feedback e otimização de política no loop acima, o agente pode finalmente aprender a tomar decisões autônomas e planejamento de longo prazo em ambientes dinâmicos complexos.

### 2.4.3 Pré-treinamento Baseado em Dados em Larga Escala

Aprendizagem por reforço dotou agentes da habilidade de aprender estratégias de tomada de decisão de interações, mas isto tipicamente requer dados massivos de interação específica da tarefa, resultando em agentes carecendo de conhecimento prévio no início da aprendizagem e precisando construir compreensão de tarefas do zero. Seja o senso comum que o simbolismo tentou codificar manualmente ou o conhecimento de fundo em que humanos confiam ao tomar decisões, ambos estão faltando em agentes RL. Como habilitar agentes a ter compreensão ampla do mundo antes de começar a aprender tarefas específicas? A solução para este problema finalmente emergiu no campo de **Processamento de Linguagem Natural (Natural Language Processing - NLP)**, com seu núcleo sendo **Pré-treinamento** baseado em dados em larga escala.

**De Tarefas Específicas a Modelos Gerais**

Antes do surgimento do paradigma de pré-treinamento, modelos tradicionais de processamento de linguagem natural eram tipicamente treinados do zero independentemente para tarefas específicas únicas (como análise de sentimento, tradução automática) em conjuntos de dados pequenos a médios especialmente anotados. Este modo levou a vários problemas: modelos tinham escopo de conhecimento estreito, dificuldade de generalizar conhecimento aprendido em uma tarefa para outra, e cada nova tarefa requeria esforço humano substancial para anotação de dados. A proposta do paradigma Pré-treinamento e Ajuste Fino mudou completamente esta situação. Sua ideia central é dividida em dois passos:

1. **Fase de Pré-treinamento**: Primeiro, treinar um modelo de rede neural de escala super-grande em um corpus geral contendo dados de texto massivos a nível de internet através de **Aprendizagem Auto-supervisionada**. O objetivo desta fase não é completar qualquer tarefa específica, mas aprender os padrões inerentes, estruturas gramaticais, conhecimento factual e lógica contextual da própria linguagem. O objetivo mais comum é "prever a próxima palavra".
2. **Fase de Ajuste Fino**: Depois de completar o pré-treinamento, este modelo já aprendeu conhecimento rico relacionado ao conjunto de dados. Subsequentemente, para tarefas específicas downstream, apenas uma pequena quantidade de dados anotados para aquela tarefa é necessária para ajustar finamente o modelo, permitindo que ele se adapte à tarefa correspondente.

Como mostra a Figura 2.9, isto demonstra intuitivamente o processo completo de pré-treinamento e ajuste fino: dados de texto geral formam um modelo de fundação através de aprendizagem auto-supervisionada, então ajuste fino com dados de tarefa específica finalmente se adapta a várias tarefas downstream.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-7.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.9 Diagrama esquemático do paradigma "pré-treinamento-ajuste fino"</p>
</div>

**Nascimento de Grandes Modelos de Linguagem e Habilidades Emergentes**

Através de pré-treinamento em trilhões de textos, os pesos de rede neural de grandes modelos de linguagem na verdade construíram um modelo implícito altamente comprimido de conhecimento mundial. Ele resolve o mais problemático problema de "gargalo de aquisição de conhecimento" da era do simbolismo de uma maneira completamente nova. Mais surpreendentemente, quando a escala do modelo (número de parâmetros, volume de dados, computação) cruza certo limiar, eles começam a exibir **Habilidades Emergentes** inesperadas que não foram diretamente treinadas, tais como:

- **Aprendizagem em Contexto (In-context Learning)**: Sem ajustar pesos do modelo, apenas fornecendo **alguns exemplos (Few-shot)** ou até **zero exemplos (Zero-shot)** na entrada, o modelo pode entender e completar novas tarefas.
- **Raciocínio de Cadeia de Pensamento (Chain-of-Thought)**: Ao guiar o modelo a produzir processos de raciocínio passo-a-passo antes de responder questões complexas, sua precisão em tarefas de lógica, aritmética e raciocínio de senso comum pode ser significativamente melhorada.

O surgimento dessas habilidades marca que LLMs não são mais apenas modelos de linguagem; eles evoluíram em componentes desempenhando papéis duplos tanto como bases de conhecimento massivas quanto motores de raciocínio geral.

Neste ponto, no longo rio da história de desenvolvimento de agentes, várias peças chave do quebra-cabeça técnico todas apareceram: simbolismo forneceu o quadro para raciocínio lógico, conexionismo e aprendizagem por reforço forneceram capacidades de aprendizagem e tomada de decisão, enquanto grandes modelos de linguagem forneceram conhecimento mundial sem precedentes e capacidades de raciocínio geral obtidas através de pré-treinamento. Na próxima seção, veremos como essas tecnologias são integradas no design de agentes modernos.

### 2.4.4 Agentes Baseados em Grandes Modelos de Linguagem

Com o rápido desenvolvimento da tecnologia de grandes modelos de linguagem, agentes centrados em LLM se tornaram um novo paradigma no campo da inteligência artificial. Eles podem não apenas compreender e gerar linguagem humana, mas, mais importante, podem autonomamente perceber, planejar, decidir e executar tarefas através de interação com o ambiente.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-8.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.10 Arquitetura de componente central de agentes impulsionados por LLM</p>
</div>

Como descrito no Capítulo 1, a interação entre agentes e o ambiente pode ser abstraída como um loop central. Agentes impulsionados por LLM completam tarefas através de um processo de loop fechado continuamente iterativo onde múltiplos módulos trabalham juntos. Este processo segue a arquitetura mostrada na Figura 2.10, com passos específicos como segue:

1. **Percepção**: O processo começa com o **Módulo de Percepção**. Ele recebe entrada bruta do **Ambiente** através de sensores, formando **Observações**. Esta informação de observação (como instruções do usuário, dados retornados por APIs ou mudanças no estado do ambiente) é o ponto de partida para tomada de decisão do agente e será passada para o estágio de pensamento após processamento.
2. **Pensamento**: Este é o núcleo cognitivo do agente, correspondendo ao trabalho colaborativo do **Módulo de Planejamento** e **Grande Modelo de Linguagem (LLM)** no diagrama.
   - **Planejamento e Decomposição**: Primeiro, o módulo de planejamento recebe informação de observação e formula estratégias de alto nível. Através de mecanismos como **Reflexão** e **Auto-crítica**, ele decompõe objetivos macro em passos mais específicos e executáveis.
   - **Raciocínio e Tomada de Decisão**: Subsequentemente, o **LLM** como o hub recebe instruções do módulo de planejamento e interage com o módulo de **Memória** para integrar informação histórica. O LLM realiza raciocínio profundo e finalmente decide na operação específica a executar em seguida, tipicamente manifestada como uma **Chamada de Ferramenta**.
3. **Ação**: Depois que a tomada de decisão está completa, o estágio de ação começa, gerenciado pelo **Módulo de Execução**. Instruções de chamada de ferramenta geradas pelo LLM são enviadas para o módulo de execução. Este módulo analisa instruções, seleciona e chama ferramentas apropriadas da caixa de ferramentas de **Uso de Ferramenta** (como executores de código, mecanismos de busca, APIs, etc.) para interagir com o ambiente ou executar tarefas. Esta interação real com o ambiente é a **Ação** do agente.
4. **Observação** e Loop: Ações mudam o estado do ambiente e produzem resultados.
   - Depois da execução da ferramenta, um **Resultado da Ferramenta** é retornado ao LLM, constituindo feedback direto sobre o efeito da ação. Simultaneamente, a ação do agente muda o ambiente, produzindo um completamente novo **estado do ambiente**.
   - Este "resultado da ferramenta" e "novo estado do ambiente" juntos constituem uma nova rodada de **Observação**. Esta nova observação é capturada novamente pelo módulo de percepção, enquanto o LLM **atualiza memória (Memory Update)** baseado em resultados de ação, assim iniciando a próxima rodada do loop "perceber-pensar-agir".

Este mecanismo colaborativo modular e loop iterativo contínuo constituem o fluxo de trabalho central de agentes impulsionados por LLM resolvendo problemas complexos.

### 2.4.5 Visão Geral de Marcos Chave no Desenvolvimento de Agentes

A história de desenvolvimento de agentes de inteligência artificial não é uma única faixa reta, mas um processo de entrelaçamento, competição e fusão de várias escolas ideológicas centrais ao longo de mais de meio século. Compreender este processo nos ajuda a obter insight sobre as origens profundas da formação do paradigma de arquitetura de agentes atual.

Entre estas, três grandes tendências dominaram paradigmas de pesquisa em diferentes períodos:

1. **Simbolismo**: Representado por pioneiros como **Herbert A. Simon** e **Marvin Minsky**, acreditando que o núcleo da inteligência reside em manipulação de símbolos e raciocínio lógico. Esta ideia deu origem ao SHRDLU, que podia entender instruções em linguagem natural, sistemas especialistas orientados por conhecimento, e o computador "Deep Blue" que alcançou grande sucesso no xadrez.
2. **Conexionismo**: Sua inspiração vem de simular redes neurais cerebrais. Embora o desenvolvimento inicial foi limitado, sob a promoção de pesquisadores como **Geoffrey Hinton**, o algoritmo de retropropagação lançou a fundação para o renascimento de redes neurais. Eventualmente, com a chegada da era de aprendizagem profunda, esta ideia se tornou mainstream através de modelos como redes neurais convolucionais e Transformers.
3. **Behaviorismo**: Enfatizando que agentes aprendem estratégias ótimas através de interação com o ambiente e tentativa e erro, sua encarnação moderna é aprendizagem por reforço. De TD-Gammon inicial ao AlphaGo, que combinado com aprendizagem profunda e derrotou os melhores jogadores humanos, esta escola dotou agentes da habilidade de aprender comportamentos complexos de tomada de decisão de experiência.

Entrando nos anos 2020, essas escolas ideológicas se integraram profundamente de maneiras sem precedentes. Grandes modelos de linguagem representados pela série GPT são eles mesmos produtos do conexionismo, mas se tornaram o "cérebro" central para executar raciocínio simbólico, invocação de ferramentas e decisões de planejamento, formando uma arquitetura de agente moderna combinando abordagens neurais e simbólicas. Para revisar sistematicamente este contexto de desenvolvimento, a Figura 2.11 abaixo organiza teorias chave, projetos e eventos na história de desenvolvimento de agentes de inteligência artificial dos anos 1950 até o presente, fornecendo aos leitores uma visão global clara como consolidação do conhecimento deste capítulo.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-9.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.11 Linha do tempo da evolução do desenvolvimento de agentes (versão incompleta)</p>
</div>

Graças a avanços em grandes modelos de linguagem, a pilha de tecnologia de agentes apresenta atividade e diversidade sem precedentes. A Figura 2.12 mostra uma visão completa típica da pilha de tecnologia atual do campo AI Agent, cobrindo todos os aspectos de modelos subjacentes a aplicações de camada superior.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/2-figures/1757246501849-10.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 2.12 Visão geral da pilha de tecnologia AI Agent</p>
</div>

Este diagrama de pilha de tecnologia foi lançado pela Letta em novembro de 2024<sup>[10]</sup>. Ele divide em camadas e categoriza ferramentas, plataformas e serviços relacionados a agentes de IA, fornecendo referência valiosa para compreender o panorama atual do mercado e seleção de tecnologia.

## 2.5 Resumo do Capítulo

Este capítulo revisou o contexto histórico do desenvolvimento de agentes, explorando o processo do nascimento à evolução de suas ideias centrais, cobrindo várias revoluções de paradigma chave no campo da inteligência artificial:

- **Exploração e Limitações do Simbolismo**: Começando pela era clássica da inteligência artificial, este capítulo explicou como agentes iniciais representados por sistemas especialistas tentaram simular inteligência através de "conhecimento + raciocínio". Ao construir pessoalmente um chatbot baseado em regras, experimentamos profundamente as fronteiras de capacidade deste paradigma e os desafios fundamentais que enfrentou.
- **Emergência de Pensamento de Inteligência Distribuída**: Explorou a teoria de "sociedade da mente" de Marvin Minsky. Esta ideia revolucionária revelou que inteligência holística complexa pode emergir de interações de unidades locais simples, fornecendo inspiração filosófica importante para pesquisa subsequente de sistemas multi-agente.
- **Evolução de Paradigmas de Aprendizagem**: Testemunhou mudanças fundamentais em como agentes adquirem capacidades. Do conexionismo dotando agentes da habilidade de perceber o mundo, à aprendizagem por reforço habilitando-os a aprender tomada de decisão ótima em interações com o ambiente, até grandes modelos de linguagem (LLMs) baseados em pré-treinamento de dados em larga escala fornecendo-lhes conhecimento mundial sem precedentes e capacidades de raciocínio geral.
- **Nascimento de Agentes Modernos**: Finalmente, analisamos agentes impulsionados por LLM. Através de análise de seus componentes centrais (modelos, memória, planejamento, ferramentas, etc.) e princípios de funcionamento, compreendemos como várias ideias técnicas na história alcançaram integração tecnológica em arquitetura de Agente moderna.

Através da aprendizagem deste capítulo, não apenas compreendemos de onde vieram os agentes modernos introduzidos no Capítulo 1, mas também estabelecemos um quadro cognitivo macro sobre evolução de tecnologia de agentes. Podemos descobrir que o desenvolvimento de agentes não é simples iteração técnica, mas uma revolução de pensamento sobre como definir "inteligência", adquirir "conhecimento" e tomar "decisões".

Uma vez que o núcleo de agentes modernos são grandes modelos de linguagem, compreender profundamente seus princípios subjacentes é crucial. O próximo capítulo focará em grandes modelos de linguagem em si, explorando seus conceitos básicos, lançando uma fundação sólida para aplicações avançadas subsequentes em sistemas multi-agente.

## Exercícios

> **Nota**: Alguns dos seguintes exercícios não têm respostas padrão, visando ajudar aprendizes a estabelecer compreensão sistemática da história de desenvolvimento de agentes e cultivar insight técnico "aprendendo da história".

1. A Hipótese do Sistema de Símbolos Físicos<sup>[1]</sup> é a pedra angular teórica da era do simbolismo. Por favor analise:

   - O que significam a "afirmação de suficiência" e "afirmação de necessidade" desta hipótese?
   - Combinado com o conteúdo deste capítulo, explique quais problemas encontrados por agentes simbólicos na prática desafiaram a "suficiência" desta hipótese?
   - Agentes impulsionados por grandes modelos de linguagem se conformam à Hipótese do Sistema de Símbolos Físicos?

2. O sistema especialista MYCIN<sup>[2]</sup> alcançou sucesso significativo no campo de diagnóstico médico, mas foi finalmente não amplamente aplicado na prática clínica. Por favor pense:

   > **Dica**: Pode analisar de múltiplas perspectivas incluindo tecnologia, ética, lei, aceitação do usuário, etc.

   - Além do "gargalo de aquisição de conhecimento" e "fragilidade" mencionados neste capítulo, que outros fatores podem ter impedido a aplicação de sistemas especialistas em campos de alto risco como medicina?
   - Se você fosse projetar um agente de diagnóstico médico agora, como projetaria o sistema para superar as limitações do MYCIN?
   - Em quais domínios verticais sistemas especialistas baseados em regras ainda são uma escolha melhor que aprendizagem profunda hoje? Por favor dê exemplos.

3. Na Seção 2.2, implementamos uma versão simplificada do chatbot ELIZA. Por favor expanda nesta base:

   > **Dica**: Esta é uma questão de prática hands-on; escrita real de código é recomendada

   - Adicione 3-5 novas regras à ELIZA para habilitá-la a lidar com cenários de conversa mais diversos (como discutir trabalho, estudo, hobbies, etc.)
   - Implemente uma função simples de "memória contextual": permitir que ELIZA lembre informação chave mencionada por usuários em conversas (como nome, idade, ocupação) e referencie em conversas subsequentes
   - Compare sua ELIZA expandida com [ChatGPT](https://chatgpt.com/), listando pelo menos 3 dimensões de diferenças essenciais
   - Por que a abordagem baseada em regras encontra problemas de "explosão combinatorial" e dificuldade em escalonamento e manutenção ao lidar com conversas de domínio aberto? Pode explicar usando métodos matemáticos?

4. Marvin Minsky propôs um ponto de vista revolucionário na teoria de "sociedade da mente"<sup>[7]</sup>: inteligência deriva de colaboração de numerosos agentes simples, não um único sistema perfeito.

   - No exemplo da Figura 2.6 "construir uma torre de blocos", o que aconteceria ao sistema inteiro se o agente `GRASP` de repente falhasse? Quais são as vantagens e desvantagens desta arquitetura descentralizada?
   - Compare a teoria de "sociedade da mente" com alguns sistemas multi-agente atuais (como [CAMEL-Workforce](https://docs.camel-ai.org/key_modules/workforce), [MetaGPT](https://github.com/FoundationAgents/MetaGPT), [CrewAI](https://github.com/crewAIInc/crewAI)), quais conexões e diferenças existem entre eles?
   - Marvin Minsky acreditava que agentes poderiam ser processos simples "sem mente", ainda assim modelos de linguagem grandes atuais e agentes frequentemente possuem capacidades de raciocínio poderosas. Isto significa que a teoria de "sociedade da mente" não é mais aplicável na era de grandes modelos de linguagem?

5. Aprendizagem por reforço e aprendizagem supervisionada são dois paradigmas de aprendizagem diferentes. Por favor analise:

   - Use o exemplo de AlphaGo para explicar como funciona o mecanismo de "aprendizagem por tentativa e erro" da aprendizagem por reforço
   - Por que a aprendizagem por reforço é particularmente adequada para problemas de decisão sequencial? Qual é a diferença essencial em requisitos de dados entre ela e aprendizagem supervisionada?
   - Agora precisamos treinar um agente para jogar Super Mario. Se usando aprendizagem supervisionada e aprendizagem por reforço respectivamente, quais dados são necessários para cada? Qual método é mais adequado para esta tarefa?
   - No processo de treinamento de grandes modelos de linguagem, que papel chave a aprendizagem por reforço desempenha?

6. O paradigma pré-treinamento-ajuste fino é um avanço importante no campo moderno de inteligência artificial. Por favor pense profundamente:

   - Por que o pré-treinamento resolve o problema de "gargalo de aquisição de conhecimento" da era do simbolismo? Qual é a diferença essencial em métodos de representação de conhecimento?
   - A maior parte do conhecimento de modelos pré-treinados vem de dados da internet; que problemas isto poderia trazer? Como mitigar esses problemas?
   - Você acha que o paradigma "pré-treinamento-ajuste fino" poderia ser substituído por algum novo paradigma? Ou existirá a longo prazo?

7. Suponha que você queira projetar um "assistente inteligente de revisão de código" que possa automaticamente revisar submissões de código (Pull Requests), resumir lógica de implementação de código, verificar qualidade de código, descobrir bugs potenciais e propor sugestões de melhoria.

   - Se projetando este sistema na era do simbolismo (anos 1980), como você o implementaria? Que dificuldades você encontraria?
   - Se na era de aprendizagem profunda sem grandes modelos de linguagem (por volta de 2015), como você o implementaria?
   - Na era atual de grandes modelos de linguagem e agentes, como você projetaria a arquitetura deste agente? Que módulos deveria incluir (referir à Figura 2.10)?
   - Comparando soluções dessas três eras, explique como a evolução de tecnologia de agentes fez esta tarefa mudar de "quase impossível" para "viável"

## Referências

[1] NEWELL A, SIMON H A. Computer science as empirical inquiry: symbols and search[J]. Communications of the ACM, 1976, 19(3): 113-126.

[2] BUCHANAN B G, SHORTLIFFE E H, ed. Rule-based expert systems: the MYCIN experiments of the Stanford Heuristic Programming Project[M]. Reading, Mass.: Addison-Wesley, 1984.

[3] WINOGRAD T. Understanding natural language[M]. New York: Academic Press, 1972.

[4] LENAT D B, GUHA R V. Cyc: a midterm report[J]. AI magazine, 1990, 11(3): 32.

[5] MCCARTHY J, HAYES P J. Some philosophical problems from the standpoint of artificial intelligence[C]//MELTZER B, MICHIE D, ed. Machine intelligence 4. Edinburgh: Edinburgh University Press, 1969: 463-502.

[6] WEIZENBAUM J. ELIZA: a computer program for the study of natural language communication between man and machine[J]. Communications of the ACM, 1966, 9(1): 36-45.

[7] MINSKY M. The society of mind[M]. New York: Simon & Schuster, 1986.

[8] RUMELHART D E, MCCLELLAND J L, PDP RESEARCH GROUP. Parallel distributed processing: explorations in the microstructure of cognition[M]. Cambridge, MA: MIT Press, 1986.

[9] SILVER D, HUANG A, MADDISON C J, ed. Mastering the game of Go with deep neural networks and tree search[J]. Nature, 2016, 529(7587): 484-489.

[10] LETTA. The AI agents stack[EB/OL]. (2024-11) [2025-09-07]. https://www.letta.com/blog/ai-agents-stack.
