# Capítulo 3: Fundamentos de Modelos de Linguagem de Grande Escala

<div align="right">
  <a href="./Chapter3-Fundamentals-of-Large-Language-Models.md">English</a> | <a href="./第三章%20大语言模型基础.md">中文</a> | Português
</div>

Os primeiros dois capítulos introduziram a definição e a história de desenvolvimento dos agentes. Este capítulo se concentrará inteiramente nos modelos de linguagem de grande escala para responder a uma questão fundamental: Como os agentes modernos funcionam? Começaremos pela definição básica de modelos de linguagem e, através da aprendizagem desses princípios, estabeleceremos uma base sólida para compreender como os LLMs adquirem vastos repositórios de conhecimento e capacidades de raciocínio.

## 3.1 Modelos de Linguagem e Arquitetura Transformer

### 3.1.1 De N-gram a RNN

**Modelo de Linguagem (LM)** é o núcleo do processamento de linguagem natural, e sua tarefa fundamental é calcular a probabilidade de uma sequência de palavras (isto é, uma sentença) aparecer. Um bom modelo de linguagem pode nos dizer que tipo de sentenças são fluentes e naturais. Em sistemas multi-agentes, modelos de linguagem são a base para que agentes entendam instruções humanas e gerem respostas. Esta seção revisará a evolução desde métodos estatísticos clássicos até modelos modernos de aprendizagem profunda, estabelecendo uma base sólida para compreender a subsequente arquitetura Transformer.

**(1) Modelos de Linguagem Estatísticos e a Ideia de N-gram**

Antes da ascensão da aprendizagem profunda, métodos estatísticos eram o mainstream dos modelos de linguagem. A ideia central é que a probabilidade de uma sentença aparecer é igual ao produto das probabilidades condicionais de cada palavra na sentença. Para uma sentença S composta pelas palavras $w_1,w_2,\cdots,w_m$, sua probabilidade P(S) pode ser expressa como:

$$P(S)=P(w_1,w_2,…,w_m)=P(w_1)⋅P(w_2∣w_1)⋅P(w_3∣w_1,w_2)⋯P(w_m∣w_1,…,w_{m−1})$$

Esta fórmula é chamada de regra da cadeia de probabilidade. No entanto, calcular diretamente esta fórmula é quase impossível porque probabilidades condicionais como $P(w_m∣w_1,\cdots,w_{m−1})$ são muito difíceis de estimar a partir de um corpus, pois a sequência de palavras $w_1,\cdots,w_{m−1}$ pode nunca ter aparecido nos dados de treinamento.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/3-figures/1757249275674-0.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 3.1 Diagrama esquemático da suposição de Markov</p>
</div>

Para resolver este problema, pesquisadores introduziram a **Suposição de Markov**. Sua ideia central é: não precisamos rastrear todo o histórico de uma palavra; podemos aproximadamente assumir que a probabilidade de uma palavra aparecer está relacionada apenas às $n−1$ palavras limitadas antes dela, como mostrado na Figura 3.1. Modelos de linguagem construídos sobre essa suposição são chamados de **modelos N-gram**. Aqui, "N" representa o tamanho da janela de contexto que consideramos. Vejamos alguns dos exemplos mais comuns para entender este conceito:

- **Bigram (quando N=2)**: Este é o caso mais simples, onde assumimos que a aparência de uma palavra está relacionada apenas à palavra imediatamente anterior. Portanto, a complexa probabilidade condicional $P(w_i∣w_1,\cdots,w_{i−1})$ na regra da cadeia pode ser aproximada para uma forma mais facilmente calculável:

$$P(w_{i}∣w_{1},…,w_{i−1})≈P(w_{i}∣w_{i−1})$$

- **Trigram (quando N=3)**: Similarmente, assumimos que a aparência de uma palavra está relacionada apenas às duas palavras anteriores:

$$P(w_i∣w_1,…,w_{i−1})≈P(w_i∣w_{i−2},w_{i−1})$$

Essas probabilidades podem ser calculadas através de **Estimação de Máxima Verossimilhança (MLE)** em grandes corpus. Este termo soa complexo, mas sua ideia é muito intuitiva: o que é mais provável de aparecer é o que vemos com mais frequência nos dados. Por exemplo, para um modelo Bigram, queremos calcular a probabilidade $P(w_i∣w_{i−1})$ de que a próxima palavra seja $w_i$ depois que a palavra $w_{i−1}$ aparecer. De acordo com a estimação de máxima verossimilhança, essa probabilidade pode ser estimada através de contagem simples:

$$P(w_i∣w_{i−1})=\frac{Count(w_{i−1},w_i)}{Count(w_{i−1})}$$

Aqui, a função `Count()` representa "contagem":

- $Count(w_i−1,w_i)$: representa o número total de vezes que o par de palavras $(w_{i−1},w_i)$ aparece consecutivamente no corpus.
- $Count(w_{i−1})$: representa o número total de vezes que a palavra única $w_{i−1}$ aparece no corpus.

O significado da fórmula é: usamos "o número de vezes que o par de palavras $Count(w_i−1,w_i)$ aparece" dividido por "o número total de vezes que a palavra $Count(w_{i−1})$ aparece" como uma estimativa aproximada de $P(w_i∣w_{i−1})$.

Para tornar este processo mais concreto, vamos realizar manualmente um cálculo. Suponha que temos um mini corpus contendo apenas as duas sentenças seguintes: `datawhale agent learns`, `datawhale agent works`. Nosso objetivo é: usando um modelo Bigram (N=2), estimar a probabilidade de a sentença `datawhale agent learns` aparecer. De acordo com a suposição Bigram, examinamos pares consecutivos de palavras (isto é, pares de palavras) cada vez.

**Passo 1: Calcular a probabilidade da primeira palavra** $P(datawhale)$ Este é o número de vezes que `datawhale` aparece dividido pelo número total de palavras. `datawhale` aparece 2 vezes, e o número total de palavras é 6.

$$P(\text{datawhale}) = \frac{\text{Número de "datawhale" no corpus total}}{\text{Número total de palavras no corpus}} = \frac{2}{6} \approx 0.333$$

**Passo 2: Calcular probabilidade condicional** $P(agent∣datawhale)$ Este é o número de vezes que o par de palavras `datawhale agent` aparece dividido pelo número total de vezes que `datawhale` aparece. `datawhale agent` aparece 2 vezes, `datawhale` aparece 2 vezes.

$$P(\text{agent}|\text{datawhale}) =  \frac{\text{Count}(\text{datawhale agent})}{\text{Count}(\text{datawhale})} =  \frac{2}{2} = 1$$

**Passo 3: Calcular probabilidade condicional** $P(learns∣agent)$ Este é o número de vezes que o par de palavras `agent learns` aparece dividido pelo número total de vezes que `agent` aparece. `agent learns` aparece 1 vez, `agent` aparece 2 vezes.

$$P(\text{learns}|\text{agent}) =  \frac{\text{Count(agent learns)}}{\text{Count(agent)}} =  \frac{1}{2} = 0.5$$

**Finalmente: Multiplicar as probabilidades** Portanto, a probabilidade aproximada de toda a sentença é:

$$P(\text{datawhale agent learns}) \approx  P(\text{datawhale}) \cdot  P(\text{agent}|\text{datawhale}) \cdot  P(\text{learns}|\text{agent}) \approx  0.333 \cdot 1 \cdot 0.5 \approx 0.167$$

```Python
import collections

# Corpus de exemplo, consistente com o corpus na explicação do caso acima
corpus = "datawhale agent learns datawhale agent works"
tokens = corpus.split()
total_tokens = len(tokens)

# --- Passo 1: Calcular P(datawhale) ---
count_datawhale = tokens.count('datawhale')
p_datawhale = count_datawhale / total_tokens
print(f"Passo 1: P(datawhale) = {count_datawhale}/{total_tokens} = {p_datawhale:.3f}")

# --- Passo 2: Calcular P(agent|datawhale) ---
# Primeiro calcular bigramas para passos subsequentes
bigrams = zip(tokens, tokens[1:])
bigram_counts = collections.Counter(bigrams)
count_datawhale_agent = bigram_counts[('datawhale', 'agent')]
# count_datawhale já foi calculado no passo 1
p_agent_given_datawhale = count_datawhale_agent / count_datawhale
print(f"Passo 2: P(agent|datawhale) = {count_datawhale_agent}/{count_datawhale} = {p_agent_given_datawhale:.3f}")

# --- Passo 3: Calcular P(learns|agent) ---
count_agent_learns = bigram_counts[('agent', 'learns')]
count_agent = tokens.count('agent')
p_learns_given_agent = count_agent_learns / count_agent
print(f"Passo 3: P(learns|agent) = {count_agent_learns}/{count_agent} = {p_learns_given_agent:.3f}")

# --- Finalmente: Multiplicar as probabilidades ---
p_sentence = p_datawhale * p_agent_given_datawhale * p_learns_given_agent
print(f"Finalmente: P('datawhale agent learns') ≈ {p_datawhale:.3f} * {p_agent_given_datawhale:.3f} * {p_learns_given_agent:.3f} = {p_sentence:.3f}")

>>>
Passo 1: P(datawhale) = 2/6 = 0.333
Passo 2: P(agent|datawhale) = 2/2 = 1.000
Passo 3: P(learns|agent) = 1/2 = 0.500
Finalmente: P('datawhale agent learns') ≈ 0.333 * 1.000 * 0.500 = 0.167
```

Modelos N-gram, embora simples e eficazes, têm duas falhas fatais:

1. **Esparsidade de Dados**: Se uma sequência de palavras nunca apareceu no corpus, sua estimativa de probabilidade é 0, o que é obviamente não razoável. Embora isso possa ser aliviado através de técnicas de suavização, não pode ser erradicado.
2. **Capacidade de Generalização Pobre**: O modelo não consegue entender a similaridade semântica entre palavras. Por exemplo, mesmo que o modelo tenha visto `agent learns` muitas vezes no corpus, ele não consegue generalizar esse conhecimento para palavras semanticamente similares. Quando calculamos a probabilidade de `robot learns`, se a palavra `robot` nunca apareceu, ou se a combinação `robot learns` nunca apareceu, a probabilidade calculada pelo modelo também será zero. O modelo não consegue entender a similaridade semântica entre `agent` e `robot`.

**(2) Modelos de Linguagem de Redes Neurais e Embeddings de Palavras**

A falha fundamental dos modelos N-gram é que eles tratam palavras como símbolos isolados e discretos. Para superar este problema, pesquisadores recorreram a redes neurais e propuseram uma ideia: representar palavras com vetores contínuos. Em 2003, o **Modelo de Linguagem de Rede Neural Feedforward** proposto por Bengio et al. foi um marco neste campo<sup>[1]</sup>.

Sua ideia central pode ser dividida em dois passos:

1. **Construir um espaço semântico**: Criar um espaço vetorial contínuo de alta dimensão, depois mapear cada palavra no vocabulário para um ponto nesse espaço. Este ponto (isto é, vetor) é chamado de **Word Embedding** ou vetor de palavra. Neste espaço, palavras semanticamente similares têm vetores que estão próximos em posição. Por exemplo, os vetores de `agent` e `robot` estarão muito próximos, enquanto os vetores de `agent` e `apple` estarão distantes.
2. **Aprender o mapeamento do contexto para a próxima palavra**: Utilizar a poderosa capacidade de ajuste de redes neurais para aprender uma função. A entrada desta função é os vetores de palavras das $n−1$ palavras anteriores, e a saída é a distribuição de probabilidade de cada palavra no vocabulário aparecer após o contexto atual.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/3-figures/1757249275674-1.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 3.2 Diagrama esquemático da arquitetura do modelo de linguagem de rede neural</p>
</div>

Como mostrado na Figura 3.2, nesta arquitetura, embeddings de palavras são automaticamente aprendidos durante o treinamento do modelo. Para completar a tarefa de "prever a próxima palavra", o modelo ajusta continuamente a posição vetorial de cada palavra, fazendo com que esses vetores contenham informações semânticas ricas. Uma vez que convertemos palavras em vetores, podemos usar ferramentas matemáticas para medir as relações entre eles. O método mais comumente usado é **Similaridade de Cosseno (Cosine Similarity)**, que mede sua similaridade calculando o cosseno do ângulo entre dois vetores.

$$\text{similarity}(\vec{a}, \vec{b}) = \cos(\theta) = \frac{\vec{a} \cdot \vec{b}}{|\vec{a}| |\vec{b}|}$$

O significado desta fórmula é:

- Se dois vetores têm exatamente a mesma direção, o ângulo é 0°, o valor do cosseno é 1, indicando correlação completa.
- Se dois vetores são ortogonais, o ângulo é 90°, o valor do cosseno é 0, indicando nenhuma relação.
- Se dois vetores têm direções completamente opostas, o ângulo é 180°, o valor do cosseno é -1, indicando correlação negativa completa.

Através deste método, vetores de palavras podem não apenas capturar relações simples como "sinônimos" mas também capturar relações analógicas mais complexas.

Um exemplo famoso demonstra as relações semânticas capturadas por vetores de palavras: `vector('King') - vector('Man') + vector('Woman')` O resultado desta operação vetorial é surpreendentemente próximo da posição de `vector('Queen')` no espaço vetorial. Isto é como realizar uma tradução semântica: começamos do ponto "rei", subtraímos o vetor de "masculino", adicionamos o vetor de "feminino", e finalmente chegamos à posição de "rainha". Isto prova que embeddings de palavras podem aprender conceitos abstratos como "gênero" e "realeza".

```Python
import numpy as np

# Assumindo que temos vetores de palavras 2D simplificados aprendidos
embeddings = {
    "king": np.array([0.9, 0.8]),
    "queen": np.array([0.9, 0.2]),
    "man": np.array([0.7, 0.9]),
    "woman": np.array([0.7, 0.3])
}

def cosine_similarity(vec1, vec2):
    dot_product = np.dot(vec1, vec2)
    norm_product = np.linalg.norm(vec1) * np.linalg.norm(vec2)
    return dot_product / norm_product

# king - man + woman
result_vec = embeddings["king"] - embeddings["man"] + embeddings["woman"]

# Calcular similaridade entre vetor resultado e "queen"
sim = cosine_similarity(result_vec, embeddings["queen"])

print(f"Vetor resultado de king - man + woman: {result_vec}")
print(f"Similaridade deste resultado com 'queen': {sim:.4f}")

>>>
Vetor resultado de king - man + woman: [0.9 0.2]
Similaridade deste resultado com 'queen': 1.0000
```

Modelos de linguagem de redes neurais resolveram com sucesso o problema de generalização pobre dos modelos N-gram através de embeddings de palavras. No entanto, eles ainda têm uma limitação similar ao N-gram: a janela de contexto é fixa. Eles só podem considerar um número fixo de palavras precedentes, o que preparou o terreno para redes neurais recorrentes que podem lidar com sequências de comprimento arbitrário.

**(3) Redes Neurais Recorrentes (RNN) e Redes de Memória de Curto-Longo Prazo (LSTM)**

Embora o modelo de linguagem de rede neural na seção anterior tenha introduzido embeddings de palavras para resolver o problema de generalização, como modelos N-gram, sua janela de contexto é de tamanho fixo. Para prever a próxima palavra, ele só pode ver as n−1 palavras anteriores, e informações históricas anteriores são descartadas. Isto obviamente não está conforme com a forma como nós humanos entendemos linguagem. Para quebrar a limitação de janelas fixas, **Redes Neurais Recorrentes (RNN)** surgiram, com uma ideia central muito intuitiva: adicionar capacidade de "memória" à rede<sup>[2]</sup>.

Como mostrado na Figura 3.3, o design da RNN introduz um vetor de **estado oculto (hidden state)**, que podemos entender como a memória de curto prazo da rede. A cada passo do processamento da sequência, a rede lê a palavra de entrada atual e a combina com sua memória do momento anterior (isto é, o estado oculto do passo de tempo anterior), depois gera uma nova memória (isto é, o estado oculto do passo de tempo atual) para passar para o próximo momento. Este processo cíclico permite que informações se propaguem continuamente para trás através da sequência.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/3-figures/1757249275674-2.png" alt="Descrição da figura" width="90%"/>
  <p>Figura 3.3 Diagrama esquemático da estrutura RNN</p>
</div>

No entanto, RNNs padrão têm um problema sério na prática: o **Problema de Dependência de Longo Prazo**. Durante o treinamento, o modelo precisa ajustar pesos profundos na rede baseado em erros na extremidade de saída através do algoritmo de retropropagação. Para RNNs, o comprimento da sequência é a profundidade da rede. Quando a sequência é muito longa, gradientes passam por múltiplas multiplicações durante a propagação reversa, o que causa valores de gradiente a rapidamente se aproximarem de zero (**desvanecimento de gradiente**) ou se tornarem extremamente grandes (**explosão de gradiente**). O desvanecimento de gradiente impede o modelo de aprender efetivamente o impacto de informações de sequência inicial em saídas posteriores, tornando difícil capturar dependências de longa distância.

Para resolver o problema de dependência de longo prazo, **Memória de Curto-Longo Prazo (LSTM)** foi projetada<sup>[3]</sup>. LSTM é um tipo especial de RNN, e sua inovação central está em introduzir **Estado de Célula (Cell State)** e um sofisticado **Mecanismo de Porta (Gating Mechanism)**. O estado de célula pode ser visto como um caminho de informação independente do estado oculto, permitindo que informações passem mais suavemente entre passos de tempo. O mecanismo de porta consiste em várias pequenas redes neurais que podem aprender como seletivamente deixar informações passarem, controlando assim a adição e remoção de informações no estado de célula. Estas portas incluem:

- **Porta de Esquecimento (Forget Gate)**: Decide quais informações descartar do estado de célula do momento anterior.
- **Porta de Entrada (Input Gate)**: Decide quais novas informações da entrada atual armazenar no estado de célula.
- **Porta de Saída (Output Gate)**: Decide quais informações enviar ao estado oculto baseado no estado de célula atual.

### 3.1.2 Análise da Arquitetura Transformer

Na seção anterior, vimos que RNNs e LSTMs processam dados sequenciais introduzindo estruturas recorrentes, o que em certa medida resolveu o problema de capturar dependências de longa distância. No entanto, este método de computação recorrente também trouxe novos gargalos: ele deve processar dados sequencialmente. A computação no passo de tempo t deve esperar que o passo de tempo t−1 seja completado antes que possa começar. Isto significa que RNNs não podem realizar computação paralela em larga escala e são ineficientes ao processar sequências longas, o que limita enormemente a melhoria de escala do modelo e velocidade de treinamento. O Transformer foi proposto pela equipe do Google em 2017<sup>[4]</sup>. Ele abandonou completamente a estrutura recorrente e em vez disso confiou inteiramente em um mecanismo chamado **Atenção (Attention)** para capturar dependências dentro de sequências, alcançando assim computação verdadeiramente paralela.

**(1) Estrutura Geral Encoder-Decoder**

O modelo Transformer original foi projetado para a tarefa de tradução automática de ponta a ponta. Como mostrado na Figura 3.4, ele segue uma arquitetura clássica **Encoder-Decoder** no nível macro.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/3-figures/1757249275674-3.png" alt="Descrição da figura" width="50%"/>
  <p>Figura 3.4 Diagrama geral da arquitetura Transformer</p>
</div>

Podemos entender esta estrutura como uma equipe com divisão clara de trabalho:

1. **Encoder**: A tarefa é "**entender**" toda a sentença de entrada. Ele lê todos os tokens de entrada (este conceito será introduzido na Seção 3.2.2) e finalmente gera uma representação vetorial rica em informações contextuais para cada token.
2. **Decoder**: A tarefa é "**gerar**" a sentença alvo. Ele referencia o texto precedente que já gerou e "consulta" os resultados de compreensão do encoder para gerar a próxima palavra.

Para realmente entender como o Transformer funciona, o melhor método é implementá-lo você mesmo. Nesta seção, adotaremos uma abordagem "de cima para baixo": primeiro, construímos o framework de código completo do Transformer, definindo todas as classes e métodos necessários. Depois, como completar um quebra-cabeça, implementaremos as funções específicas dessas classes uma por uma.

```Python
import torch
import torch.nn as nn
import math

# --- Módulos placeholder, a serem implementados em subseções subsequentes ---

class PositionalEncoding(nn.Module):
    """
    Módulo de codificação posicional
    """
    def forward(self, x):
        pass

class MultiHeadAttention(nn.Module):
    """
    Módulo de mecanismo de atenção multi-cabeça
    """
    def forward(self, query, key, value, mask):
        pass

class PositionWiseFeedForward(nn.Module):
    """
    Módulo de rede feedforward posicional
    """
    def forward(self, x):
        pass

# --- Camada central do Encoder ---

class EncoderLayer(nn.Module):
    def __init__(self, d_model, num_heads, d_ff, dropout):
        super(EncoderLayer, self).__init__()
        self.self_attn = MultiHeadAttention() # A ser implementado
        self.feed_forward = PositionWiseFeedForward() # A ser implementado
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x, mask):
        # Conexão residual e normalização de camada serão explicadas em detalhes na Seção 3.1.2.4
        # 1. Auto-atenção multi-cabeça
        attn_output = self.self_attn(x, x, x, mask)
        x = self.norm1(x + self.dropout(attn_output))

        # 2. Rede feedforward
        ff_output = self.feed_forward(x)
        x = self.norm2(x + self.dropout(ff_output))

        return x

# --- Camada central do Decoder ---

class DecoderLayer(nn.Module):
    def __init__(self, d_model, num_heads, d_ff, dropout):
        super(DecoderLayer, self).__init__()
        self.self_attn = MultiHeadAttention() # A ser implementado
        self.cross_attn = MultiHeadAttention() # A ser implementado
        self.feed_forward = PositionWiseFeedForward() # A ser implementado
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)
        self.norm3 = nn.LayerNorm(d_model)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x, encoder_output, src_mask, tgt_mask):
        # 1. Auto-atenção multi-cabeça mascarada (em si mesmo)
        attn_output = self.self_attn(x, x, x, tgt_mask)
        x = self.norm1(x + self.dropout(attn_output))

        # 2. Atenção cruzada (na saída do encoder)
        cross_attn_output = self.cross_attn(x, encoder_output, encoder_output, src_mask)
        x = self.norm2(x + self.dropout(cross_attn_output))

        # 3. Rede feedforward
        ff_output = self.feed_forward(x)
        x = self.norm3(x + self.dropout(ff_output))

        return x
```

**(2) De Auto-Atenção a Atenção Multi-Cabeça**

Agora, vamos preencher o módulo mais crítico no esqueleto: o mecanismo de atenção.

Imagine que estamos lendo esta sentença: "O agente aprende porque **ele** é inteligente." Quando lemos o "**ele**" em negrito, para entender sua referência, nosso cérebro inconscientemente coloca mais atenção na palavra "agente" mais cedo na sentença. O mecanismo de **Auto-Atenção (Self-Attention)** é uma modelagem matemática deste fenômeno. Ele permite que o modelo considere todas as outras palavras na sentença ao processar cada palavra e atribua diferentes "pesos de atenção" a essas palavras. Quanto maior o peso de uma palavra, mais forte sua associação com a palavra atual, e maior a proporção que suas informações devem ocupar na representação da palavra atual.

Para implementar o processo acima, o mecanismo de auto-atenção introduz três papéis aprendíveis para cada vetor de token de entrada:

- **Query (Q)**: Representa o token atual, que está ativamente "consultando" outros tokens para obter informações.
- **Key (K)**: Representa o "rótulo" ou "índice" de tokens na sentença que podem ser consultados.
- **Value (V)**: Representa o "conteúdo" ou "informações" carregadas pelo próprio token.

Estes três vetores são todos obtidos multiplicando o vetor de embedding de palavra original por três matrizes de peso diferentes e aprendíveis ($W^Q,W^K,W^V$). Todo o processo de computação pode ser dividido nos seguintes passos, que podemos imaginar como um exame eficiente de livro aberto:

- Preparar "questões de exame" e "materiais": Para cada palavra na sentença, gerar seus vetores $Q,K,V$ através de matrizes de peso.
- Calcular pontuações de relevância: Para calcular a nova representação da palavra $A$, usar o vetor $Q$ da palavra $A$ para realizar operações de produto escalar com os vetores $K$ de todas as palavras na sentença (incluindo $A$ em si). Esta pontuação reflete a importância de outras palavras para entender a palavra $A$.
- Estabilização e normalização: Dividir todas as pontuações obtidas por um fator de escala $\sqrt{d_{k}}$ ($d_{k}$ é a dimensão do vetor $K$) para evitar que gradientes sejam muito pequenos, depois usar a função Softmax para converter pontuações em pesos que somam 1, que é o processo de normalização.
- Soma ponderada: Multiplicar os pesos obtidos no passo anterior pelo vetor $V$ correspondente de cada palavra, depois adicionar todos os resultados juntos. O vetor final é a nova representação da palavra $A$ após integrar informações contextuais globais.

Este processo pode ser resumido por uma fórmula concisa:

$$\text{Attention}(Q,K,V)=\text{softmax}\left(\frac{QK^{T}}{\sqrt{d_{k}}}\right)V$$

Se apenas um cálculo de atenção for realizado (isto é, cabeça única), o modelo pode apenas aprender a focar em um tipo de associação. Por exemplo, ao processar "ele", pode apenas aprender a focar no sujeito. Mas relações na linguagem são complexas, e queremos que o modelo foque simultaneamente em múltiplas relações (como relações referenciais, relações temporais, relações subordinadas, etc.). O mecanismo de atenção multi-cabeça surgiu. Sua ideia é simples: em vez de fazer tudo de uma vez, divida em vários grupos, faça-os separadamente, depois mescle.

Ele divide os vetores originais Q, K, V em h partes ao longo da dimensão (h é o número de "cabeças"), e cada parte realiza independentemente um cálculo de atenção de cabeça única. Isto é como ter h diferentes "especialistas" examinarem a sentença de diferentes perspectivas, com cada especialista capturando uma relação de recurso diferente. Finalmente, as "opiniões" (isto é, vetores de saída) destes h especialistas são concatenadas, depois integradas através de uma transformação linear para obter a saída final.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/3-figures/1757249275674-4.png" alt="Descrição da figura" width="50%"/>
  <p>Figura 3.5 Mecanismo de atenção multi-cabeça</p>
</div>

Como mostrado na Figura 3.5, este design permite que o modelo preste atenção conjuntamente a informações de diferentes posições e diferentes subespaços de representação, aumentando muito o poder expressivo do modelo. Abaixo está uma implementação simples de atenção multi-cabeça para referência.

```Python
class MultiHeadAttention(nn.Module):
    """
    Módulo de mecanismo de atenção multi-cabeça
    """
    def __init__(self, d_model, num_heads):
        super(MultiHeadAttention, self).__init__()
        assert d_model % num_heads == 0, "d_model deve ser divisível por num_heads"

        self.d_model = d_model
        self.num_heads = num_heads
        self.d_k = d_model // num_heads

        # Definir camadas de transformação linear para Q, K, V e saída
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)

    def scaled_dot_product_attention(self, Q, K, V, mask=None):
        # 1. Calcular pontuações de atenção (QK^T)
        attn_scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)

        # 2. Aplicar máscara (se fornecida)
        if mask is not None:
            # Definir posições onde máscara é 0 para um número negativo muito pequeno, então elas se aproximam de 0 após softmax
            attn_scores = attn_scores.masked_fill(mask == 0, -1e9)

        # 3. Calcular pesos de atenção (Softmax)
        attn_probs = torch.softmax(attn_scores, dim=-1)

        # 4. Soma ponderada (pesos * V)
        output = torch.matmul(attn_probs, V)
        return output

    def split_heads(self, x):
        # Transformar forma de entrada x de (batch_size, seq_length, d_model)
        # para (batch_size, num_heads, seq_length, d_k)
        batch_size, seq_length, d_model = x.size()
        return x.view(batch_size, seq_length, self.num_heads, self.d_k).transpose(1, 2)

    def combine_heads(self, x):
        # Transformar forma de entrada x de (batch_size, num_heads, seq_length, d_k)
        # de volta para (batch_size, seq_length, d_model)
        batch_size, num_heads, seq_length, d_k = x.size()
        return x.transpose(1, 2).contiguous().view(batch_size, seq_length, self.d_model)

    def forward(self, Q, K, V, mask=None):
        # 1. Realizar transformações lineares em Q, K, V
        Q = self.split_heads(self.W_q(Q))
        K = self.split_heads(self.W_k(K))
        V = self.split_heads(self.W_v(V))

        # 2. Calcular atenção de produto escalar escalado
        attn_output = self.scaled_dot_product_attention(Q, K, V, mask)

        # 3. Combinar saídas multi-cabeça e realizar transformação linear final
        output = self.W_o(self.combine_heads(attn_output))
        return output
```

**(3) Rede Neural Feedforward**

Em cada camada do Encoder e Decoder, a subcamada de atenção multi-cabeça é seguida por uma **Rede Feedforward Posicional (FFN)**. Se o papel da camada de atenção é "agregar dinamicamente" informações relevantes de toda a sequência, então o papel da rede feedforward é extrair recursos de ordem superior destas informações agregadas.

A chave deste nome é "posicional". Significa que esta rede feedforward atua independentemente em cada vetor de token na sequência. Em outras palavras, para uma sequência de comprimento `seq_len`, esta FFN é na verdade chamada `seq_len` vezes, processando um token cada vez. Importante, todas as posições compartilham o mesmo conjunto de pesos de rede. Este design mantém tanto a capacidade de processar independentemente cada posição quanto reduz muito a contagem de parâmetros do modelo. A estrutura desta rede é muito simples, consistindo em duas transformações lineares e uma função de ativação ReLU:

$$\mathrm{FFN}(x)=\max\left(0, xW_{1}+b_{1}\right) W_{2}+b_{2}$$

Onde $x$ é a saída da subcamada de atenção. $W_1,b_1,W_2,b_2$ são parâmetros aprendíveis. Tipicamente, a dimensão de saída `d_ff` da primeira camada linear é muito maior que a dimensão de entrada `d_model` (por exemplo, `d_ff = 4 * d_model`), depois após ativação ReLU, é mapeada de volta para dimensão `d_model` através da segunda camada linear. Este design de "expandir depois encolher" acredita-se ajudar o modelo a aprender representações de recursos mais ricas.

No nosso esqueleto PyTorch, podemos implementar este módulo com o seguinte código:

```Python
class PositionWiseFeedForward(nn.Module):
    """
    Módulo de rede feedforward posicional
    """
    def __init__(self, d_model, d_ff, dropout=0.1):
        super(PositionWiseFeedForward, self).__init__()
        self.linear1 = nn.Linear(d_model, d_ff)
        self.dropout = nn.Dropout(dropout)
        self.linear2 = nn.Linear(d_ff, d_model)
        self.relu = nn.ReLU()

    def forward(self, x):
        # forma de x: (batch_size, seq_len, d_model)
        x = self.linear1(x)
        x = self.relu(x)
        x = self.dropout(x)
        x = self.linear2(x)
        # Forma de saída final: (batch_size, seq_len, d_model)
        return x
```

**(4) Conexões Residuais e Normalização de Camada**

Em cada camada do encoder e decoder do Transformer, todos os submódulos (como atenção multi-cabeça e redes feedforward) são envolvidos por uma operação `Add & Norm`. Esta combinação garante que o Transformer possa treinar de forma estável.

Esta operação consiste em duas partes:

- **Conexão Residual (Add)**: Esta operação adiciona diretamente a entrada do submódulo `x` à saída do submódulo `Sublayer(x)`. Esta estrutura resolve o problema de **Desvanecimento de Gradientes** em redes neurais profundas. Durante a retropropagação, gradientes podem contornar o submódulo e propagar-se diretamente para frente, garantindo que mesmo se a rede tiver muitas camadas, o modelo possa ser efetivamente treinado. Sua fórmula pode ser expressa como: $\text{Output} = x + \text{Sublayer}(x)$.
- **Normalização de Camada (Norm)**: Esta operação normaliza todas as características de uma única amostra, fazendo sua média 0 e variância 1. Isto resolve o problema de **Mudança Covariante Interna** durante o treinamento do modelo, mantendo a distribuição de entrada de cada camada estável, acelerando assim a convergência do modelo e melhorando a estabilidade do treinamento.

**3.1.2.5 Codificação Posicional**

Já entendemos que o núcleo do Transformer é o mecanismo de auto-atenção, que captura dependências calculando relações entre quaisquer dois tokens em uma sequência. No entanto, este método de computação tem um problema inerente: ele não contém qualquer informação sobre ordem ou posição de tokens. Para a auto-atenção, as duas sequências "agente aprende" e "aprende agente" são completamente equivalentes porque ela só se preocupa com relações entre tokens e ignora seu arranjo. Para resolver este problema, o Transformer introduziu **Codificação Posicional (Positional Encoding)**.

A ideia central da codificação posicional é adicionar um "vetor de posição" adicional representando suas informações de posição absoluta e relativa a cada vetor de embedding de token na sequência de entrada. Este vetor de posição não é aprendido mas diretamente calculado através de uma fórmula matemática fixa. Desta forma, mesmo que dois tokens (por exemplo, dois tokens ambos chamados `agente`) tenham o mesmo embedding, porque eles estão em posições diferentes na sentença, os vetores que eles finalmente inputam no modelo Transformer se tornarão únicos devido à adição de diferentes codificações posicionais. A codificação posicional proposta no artigo original usa funções seno e cosseno para gerar, com a fórmula como segue:

$$PE_{(pos,2i)}=\sin\left(\frac{pos}{10000^{2i/d_{\text{model}}}}\right)，$$

$$PE_{(pos,2i+1)}=\cos\left(\frac{pos}{10000^{2i/d_{\text{model}}}}\right)$$

Onde:

- $pos$ é a posição do token na sequência (por exemplo, $0$, $1$, $2$, ...)
- $i$ é o índice de dimensão no vetor de posição (de $0$ a $d_{\text{model}}/2$)
- $d_{\text{model}}$ é a dimensão do vetor de embedding de palavra (consistente com o que definimos no modelo)

Agora, vamos implementar o módulo `PositionalEncoding` e completar a última parte do nosso código esqueleto Transformer.

```Python
class PositionalEncoding(nn.Module):
    """
    Adicionar codificação posicional aos vetores de embedding de palavra da sequência de entrada.
    """
    def __init__(self, d_model: int, dropout: float = 0.1, max_len: int = 5000):
        super().__init__()
        self.dropout = nn.Dropout(p=dropout)

        # Criar uma matriz de codificação posicional suficientemente longa
        position = torch.arange(max_len).unsqueeze(1)
        div_term = torch.exp(torch.arange(0, d_model, 2) * (-math.log(10000.0) / d_model))

        # tamanho de pe (codificação posicional) é (max_len, d_model)
        pe = torch.zeros(max_len, d_model)

        # Dimensões pares usam sin, dimensões ímpares usam cos
        pe[:, 0::2] = torch.sin(position * div_term)
        pe[:, 1::2] = torch.cos(position * div_term)

        # Registrar pe como buffer, então não será tratado como parâmetro do modelo mas se moverá com o modelo (ex., to(device))
        self.register_buffer('pe', pe.unsqueeze(0))

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        # x.size(1) é o comprimento da sequência de entrada atual
        # Adicionar codificação posicional ao vetor de entrada
        x = x + self.pe[:, :x.size(1)]
        return self.dropout(x)
```

Esta subseção ajuda principalmente a entender a estrutura macro do Transformer e os detalhes operacionais de cada módulo interno. Como é para complementar o sistema de conhecimento de modelos grandes no aprendizado de agentes, não continuaremos a implementar mais. Neste ponto, estabelecemos uma base arquitetônica sólida para entender modelos de linguagem de grande escala modernos. Na próxima seção, exploraremos a arquitetura Decoder-Only e veremos como ela evoluiu baseada nas ideias do Transformer.

### 3.1.3 Arquitetura Decoder-Only

Na seção anterior, construímos um modelo Transformer completo manualmente, que tem desempenho excelente em muitos cenários de ponta a ponta. Mas quando a tarefa muda para construir um modelo geral que possa conversar com pessoas, criar e servir como cérebro de um agente, talvez não precisemos de uma estrutura tão complexa.

A filosofia de design do Transformer é "entender primeiro, depois gerar". O encoder é responsável por entender profundamente toda a sentença de entrada, formando uma memória contextual contendo informações globais, depois o decoder gera tradução baseado nesta memória. Mas quando a OpenAI desenvolveu **GPT (Generative Pre-trained Transformer)**, eles propuseram uma ideia mais simples<sup>[5]</sup>: **Não é a tarefa central da linguagem prever a próxima palavra mais provável?**

Seja respondendo perguntas, escrevendo histórias ou gerando código, essencialmente está adicionando o conteúdo mais razoável palavra por palavra após uma sequência de texto existente. Baseado nesta ideia, o GPT fez uma simplificação ousada: **Ele abandonou completamente o encoder e apenas manteve a parte do decoder.** Esta é a origem da arquitetura **Decoder-Only**.

O modo de trabalho da arquitetura Decoder-Only é chamado **Autoregressivo (Autoregressive)**. Este termo de som profissional na verdade descreve um processo muito simples:

1. Dar ao modelo um texto inicial (por exemplo, "Datawhale Agent é").
2. O modelo prevê a próxima palavra mais provável (por exemplo, "um").
3. O modelo adiciona a palavra "um" que acabou de gerar ao final do texto de entrada, formando uma nova entrada ("Datawhale Agent é um").
4. Baseado nesta nova entrada, o modelo prevê a próxima palavra novamente (por exemplo, "poderoso").
5. Repetir continuamente este processo até que uma sentença completa seja gerada ou uma condição de parada seja alcançada.

O modelo é como jogar um jogo de "cadeia de palavras", constantemente "revisando" o conteúdo que já escreveu, depois pensando qual deveria ser a próxima palavra.

Você pode perguntar, como o decoder garante que ao prever a palavra `t`-ésima, ele não "espia" a resposta da palavra `t+1`-ésima?

A resposta é **Auto-Atenção Mascarada (Masked Self-Attention)**. Na arquitetura Decoder-Only, este mecanismo se torna crucial. Seu princípio de funcionamento é muito inteligente:

Depois que o mecanismo de auto-atenção calcula a matriz de pontuação de atenção (isto é, a pontuação de atenção de cada palavra para todas as outras palavras), mas antes de realizar normalização Softmax, o modelo aplica uma "máscara". Esta máscara substitui as pontuações correspondentes a todos os tokens localizados após a posição atual (isto é, ainda não observados) com um número negativo muito grande. Quando esta matriz com pontuações de infinito negativo passa pela função Softmax, as probabilidades nessas posições se tornam 0. Desta forma, quando o modelo calcula a saída em qualquer posição, ele é matematicamente impedido de prestar atenção a informações depois dela. Este mecanismo garante que ao prever a próxima palavra, o modelo pode e só pode confiar em todas as informações que já viu, localizadas antes da posição atual, garantindo assim justiça de previsão e coerência de lógica.

**Vantagens da Arquitetura Decoder-Only**

Esta arquitetura aparentemente simples trouxe tremendo sucesso, com vantagens incluindo:

- **Objetivo de Treinamento Unificado**: A única tarefa do modelo é "prever a próxima palavra", um objetivo simples muito adequado para pré-treinamento em dados de texto não rotulados massivos.
- **Estrutura Simples, Fácil de Escalar**: Menos componentes significam escalabilidade mais fácil. GPT-4, Llama e outros modelos gigantes de hoje com centenas de bilhões ou até trilhões de parâmetros são todos baseados nesta arquitetura concisa.
- **Naturalmente Adequado para Tarefas de Geração**: Seu modo de trabalho autoregressivo combina perfeitamente com todas as tarefas gerativas (diálogo, escrita, geração de código, etc.), o que também é a razão central pela qual pode se tornar a base para construir agentes gerais.

Em resumo, a arquitetura Decoder-Only evoluiu do decoder do Transformer, através do paradigma simples de "prever a próxima palavra", abriu a era de modelos de linguagem de grande escala em que estamos hoje.

## 3.2 Interagindo com Modelos de Linguagem de Grande Escala

### 3.2.1 Engenharia de Prompt

Se compararmos modelos de linguagem de grande escala a um "cérebro" extremamente capaz, então **Prompt** é a linguagem que usamos para comunicar com este "cérebro". Engenharia de prompt é o estudo de como projetar prompts precisos para guiar o modelo a produzir as respostas que esperamos. Para construir agentes, um prompt cuidadosamente projetado pode tornar a colaboração e divisão de trabalho entre agentes eficiente.

**(1) Parâmetros de Amostragem do Modelo**

Ao usar modelos grandes, você frequentemente vê parâmetros configuráveis como `Temperature`. Sua essência é ajustar a estratégia de amostragem do modelo para "distribuição de probabilidade" para corresponder às necessidades de cenários específicos. Configurar parâmetros apropriados pode melhorar o desempenho do Agente em cenários específicos.

A distribuição de probabilidade tradicional é calculada pela fórmula Softmax: $p_i = \frac{e^{z_i}}{\sum_{j=1}^k e^{z_j}}$. A essência dos parâmetros de amostragem é "reajustar" ou "truncar" a distribuição baseado em diferentes estratégias, mudando assim o próximo token de saída pelo modelo grande.

`Temperature`: Temperatura é um parâmetro chave controlando a "aleatoriedade" e "determinismo" da saída do modelo. Seu princípio é introduzir um coeficiente de temperatura $T\gt0$, reescrevendo Softmax como $p_i^{(T)} = \frac{e^{z_i / T}}{\sum_{j=1}^k e^{z_j / T}}$.

Quando T diminui, a distribuição se torna "mais íngreme", pesos de itens de alta probabilidade são ainda mais amplificados, gerando texto mais "conservador" com taxas de repetição mais altas. Quando T aumenta, a distribuição se torna "mais plana", pesos de itens de baixa probabilidade aumentam, gerando conteúdo mais "diverso" mas possivelmente incoerente.

- Baixa temperatura (0 $\leqslant$ Temperature $\lt$ 0.3): Saída é mais "precisa, determinística". Cenários aplicáveis: Tarefas factuais: como Q&A, cálculo de dados, geração de código; Cenários rigorosos: interpretação de texto legal, escrita de documentação técnica, explicação de conceito acadêmico, etc.

- Temperatura média (0.3 $\leqslant$ Temperature $\lt$ 0.7): Saída é "equilibrada, natural". Cenários aplicáveis: Conversação diária: como interação de atendimento ao cliente, chatbots; Criação regular: como escrita de e-mail, cópia de produto, criação de história simples.

- Alta temperatura (0.7 $\leqslant$ Temperature $\lt$ 2): Saída é "inovadora, divergente". Cenários aplicáveis: Tarefas criativas: como criação de poesia, concepção de história de ficção científica, brainstorming de slogan publicitário, inspiração artística; Pensamento divergente.

`Top-k`: Seu princípio é ordenar todos os tokens por probabilidade de alto para baixo, pegar os k tokens principais para formar um "conjunto candidato", depois "normalizar" as probabilidades dos k tokens filtrados: $ \hat{p}_i = \frac{p_i}{\sum_{j \in \text{conjunto candidato}} p_j}$

- Diferença e conexão com amostragem de temperatura: Amostragem de temperatura ajusta a distribuição de probabilidade de todos os tokens (suave ou íngreme) através da temperatura T, sem mudar o número de tokens candidatos (ainda considerando todos os N). Amostragem Top-k limita o número de tokens candidatos (apenas mantendo os k tokens de alta probabilidade principais) através do valor k, depois amostra deles. Quando k=1, saída é completamente determinística, degenerando para "amostragem gulosa".

`Top-p`: Seu princípio é ordenar todos os tokens por probabilidade de alto para baixo, começando do primeiro token após ordenação, gradualmente acumulando probabilidades até que a soma cumulativa primeiro alcance ou exceda o limiar p: $\sum_{i \in S} p_{(i)} \geq p$. Neste ponto, todos os tokens incluídos no processo de acumulação formam o "conjunto núcleo", e finalmente o conjunto núcleo é normalizado.

- Diferença e conexão com Top-k: Comparado ao Top-k com tamanho de truncamento fixo, Top-p pode dinamicamente se adaptar às características de "cauda longa" de diferentes distribuições, com melhor adaptabilidade a casos extremos de distribuição de probabilidade desigual.

Na geração de texto, quando Top-p, Top-k e coeficiente de temperatura são definidos simultaneamente, esses parâmetros funcionam juntos de uma maneira de filtragem em camadas, com ordem de prioridade: ajuste de temperatura → Top-k → Top-p. Temperatura ajusta a inclinação geral da distribuição, Top-k primeiro retém os k candidatos com maior probabilidade, depois Top-p seleciona o conjunto mínimo com probabilidade cumulativa ≥ p dos resultados Top-k como o conjunto candidato final. No entanto, geralmente escolher um de Top-k ou Top-p é suficiente; se ambos forem definidos, o conjunto candidato real é a interseção dos dois.
Note que se a temperatura for definida como 0, Top-k e Top-p se tornam irrelevantes porque o Token mais provável será o próximo Token previsto; se Top-k for definido como 1, temperatura e Top-p também se tornam irrelevantes porque apenas um Token passa no critério Top-k e será o próximo Token previsto.

**(2) Prompting Zero-shot, One-shot e Few-shot**

De acordo com o número de exemplos (Exemplars) que fornecemos ao modelo, prompts podem ser divididos em três tipos. Para melhor entendê-los, vamos usar uma tarefa de classificação de sentimento como exemplo, com o objetivo de fazer o modelo julgar o tom emocional de um texto (como positivo, negativo ou neutro).

**Zero-shot Prompting** Isto significa que não damos ao modelo nenhum exemplo e diretamente pedimos que complete a tarefa baseado em instruções. Isto beneficia da poderosa capacidade de generalização do modelo adquirida após pré-treinamento em dados massivos.

Caso: Damos diretamente ao modelo instruções, exigindo que complete a tarefa de classificação de sentimento.

```Python
Texto: O curso de Agente de IA da Datawhale é excelente!
Sentimento: Positivo
```

**One-shot Prompting** Fornecemos ao modelo um exemplo completo, mostrando-lhe o formato da tarefa e estilo de saída esperado.

Caso: Primeiro damos ao modelo um par completo de "pergunta-resposta" como demonstração, depois pomos nossa nova pergunta.

```Python
Texto: O serviço deste restaurante é muito lento.
Sentimento: Negativo

Texto: O curso de Agente de IA da Datawhale é excelente!
Sentimento:
```

O modelo imitará o formato do exemplo dado e completará "Positivo" para o segundo texto.

**Few-shot Prompting** Fornecemos múltiplos exemplos, o que permite que o modelo entenda mais precisamente os detalhes, limites e nuances da tarefa, alcançando assim melhor desempenho.

Caso: Fornecemos múltiplos exemplos cobrindo diferentes situações, permitindo que o modelo tenha uma compreensão mais abrangente da tarefa.

```Python
Texto: O serviço deste restaurante é muito lento.
Sentimento: Negativo

Texto: O enredo deste filme é muito insosso.
Sentimento: Neutro

Texto: O curso de Agente de IA da Datawhale é excelente!
Sentimento:
```

O modelo sintetizará todos os exemplos e classificará mais precisamente o sentimento da última sentença como "Positivo".

**(3) Impacto do Instruction Tuning**

Os primeiros modelos GPT (como GPT-3) eram principalmente modelos de "completação de texto"; eles eram bons em continuar texto baseado em texto precedente mas não necessariamente bons em entender e executar instruções humanas.

**Instruction Tuning** é uma técnica de ajuste fino que usa uma grande quantidade de dados no formato "instrução-resposta" para treinar ainda mais modelos pré-treinados. Após instruction tuning, modelos podem melhor entender e seguir instruções do usuário. Todos os modelos que usamos no trabalho e estudo diários hoje (como `ChatGPT`, `DeepSeek`, `Qwen`) são modelos ajustados por instruções em suas famílias de modelos.

- **Prompts para modelos de "completação de texto" (você precisa usar prompts few-shot para "ensinar" o modelo o que fazer):**

```Plain
Este é um programa que traduz inglês para chinês.
Inglês: Hello
Chinês: 你好
Inglês: How are you?
Chinês:
```

- **Prompts para modelos "ajustados por instruções" (você pode dar instruções diretamente):**

```Plain
Por favor traduza o seguinte inglês para chinês:
How are you?
```

A emergência do instruction tuning simplificou muito como interagimos com modelos, tornando instruções diretas e claras em linguagem natural possíveis.

**(4) Técnicas Básicas de Prompting**

**Role-playing (Interpretação de Papel)** Ao atribuir ao modelo um papel específico, podemos guiar seu estilo de resposta, tom e escopo de conhecimento, tornando sua saída mais adequada para necessidades de cenários específicos.

```Plain
# Caso
Você é agora um especialista sênior em programação Python. Por favor explique o que é GIL (Global Interpreter Lock) em Python de uma forma que até um iniciante possa entender.
```

**In-context Example (Exemplo em Contexto)** Isto é consistente com a ideia de prompting few-shot. Ao fornecer exemplos claros de entrada-saída no prompt, "ensinamos" o modelo como lidar com nossos pedidos, o que é especialmente eficaz ao lidar com formatos complexos ou tarefas de estilo específico.

```Plain
# Caso
Preciso que você extraia nomes de produtos e sentimento do usuário de avaliações de produtos. Por favor, produza estritamente no formato JSON abaixo.

Avaliação: O display da tela deste laptop "Stardust" é incrível, mas não gosto muito da sensação do teclado.
Saída: {"product_name": "Laptop Stardust", "sentiment": "Misto"}

Avaliação: Os fones de ouvido "SoundMove" que acabei de comprar têm ótima qualidade de som, e a duração da bateria excedeu minhas expectativas!
Saída:
```

**(5) Chain-of-Thought (Cadeia de Pensamento)**

Para problemas complexos requerendo raciocínio lógico, cálculo ou pensamento multi-passo, pedir diretamente ao modelo para dar uma resposta frequentemente leva a erros. **Chain-of-Thought (CoT)** é uma técnica de prompting poderosa que melhora a capacidade de raciocínio do modelo em tarefas complexas guiando-o a "pensar passo a passo".

A chave para implementar CoT é adicionar uma frase guia simples no prompt, como "por favor pense passo a passo" ou "Vamos pensar passo a passo."

```Plain
# Prompt Chain-of-Thought
Um time de basquete ganhou 60% de seus 80 jogos em uma temporada. Na próxima temporada, eles jogaram 15 jogos e ganharam 12. Qual é a porcentagem total de vitórias para ambas as temporadas?
Por favor pense passo a passo e resolva.

>>>
(O modelo pode produzir)
Ok, vamos calcular passo a passo.
Passo 1: Calcular o número de jogos ganhos na primeira temporada.
80 jogos * 60% = 48 jogos.
Passo 2: Calcular o número total de jogos e total de vitórias para ambas as temporadas.
Total de jogos = 80 + 15 = 95 jogos.
Total de vitórias = 48 + 12 = 60 jogos.
Passo 3: Calcular a porcentagem total de vitórias.
Porcentagem total de vitórias = (Total de vitórias / Total de jogos) * 100% = (60 / 95) * 100% ≈ 63.16%.
Então, a porcentagem total de vitórias para ambas as temporadas é aproximadamente 63.16%.
```

Ao mostrar explicitamente seu processo de raciocínio, o modelo não apenas chega mais facilmente à resposta correta mas também torna sua resposta mais crível e mais fácil para verificarmos e corrigirmos.

### 3.2.2 Tokenização de Texto

Sabemos que computadores essencialmente só podem entender números. Portanto, antes de alimentar texto em linguagem natural para modelos de linguagem de grande escala, ele deve primeiro ser convertido em um formato numérico que o modelo possa processar. Este processo de converter sequências de texto em sequências numéricas é chamado **Tokenização (Tokenization)**. O papel de um **Tokenizer** é definir um conjunto de regras para dividir texto bruto em unidades mínimas, que chamamos de **Tokens**.

**3.2.2.1 Por que a Tokenização é Necessária**

Tarefas iniciais de processamento de linguagem natural podem adotar estratégias de tokenização simples:

-   **Baseado em Palavras**: Divide diretamente sentenças em palavras usando espaços ou pontuação. Este método é intuitivo mas enfrenta desafios significativos:
    -   **Explosão de Vocabulário e OOV**: O vocabulário de uma língua é vasto. Se cada palavra for tratada como um token independente, o vocabulário se torna difícil de gerenciar. Pior, o modelo não pode lidar com qualquer palavra que não apareça em seu vocabulário (ex., "DatawhaleAgent"). Este fenômeno é conhecido como problema "Out-Of-Vocabulary" (OOV).
    -   **Falta de Associação Semântica**: O modelo tem dificuldade em capturar as relações semânticas entre palavras morfologicamente similares. Por exemplo, "look," "looks," e "looking" são tratadas como três tokens completamente diferentes, apesar de compartilharem um significado central comum. Similarmente, a semântica de palavras de baixa frequência nos dados de treinamento não pode ser totalmente aprendida devido às suas raras ocorrências.

-   **Baseado em Caracteres**: Divide texto em caracteres individuais. Este método tem um vocabulário muito pequeno (ex., letras inglesas, números e pontuação) e assim evita o problema OOV. No entanto, sua desvantagem é que caracteres individuais geralmente carecem de significado semântico independente. O modelo deve gastar mais esforço aprendendo a combinar caracteres em palavras significativas, levando a aprendizado ineficiente.

Para equilibrar tamanho de vocabulário e expressão semântica, modelos de linguagem de grande escala modernos adotam amplamente algoritmos de **Tokenização de Subpalavras (Subword Tokenization)**. A ideia central é manter palavras comuns (como "agent") como tokens únicos e completos enquanto divide palavras incomuns (como "Tokenization") em peças de subpalavras significativas (como "Token" e "ization"). Esta abordagem não apenas controla o tamanho do vocabulário mas também permite que o modelo entenda e gere novas palavras combinando subpalavras.

**3.2.2.2 Análise do Algoritmo Byte-Pair Encoding**

Byte-Pair Encoding (BPE) é um dos algoritmos de tokenização de subpalavras mais mainstream<sup>[6]</sup>, adotado pelos modelos da série GPT. Sua ideia central é muito concisa e pode ser entendida como um processo de mesclagem "guloso":

1. **Inicialização**: Inicializar o vocabulário para todos os caracteres básicos aparecendo no corpus.
2. **Mesclagem Iterativa**: No corpus, contar a frequência de todos os pares de tokens adjacentes, encontrar o par com maior frequência, mesclá-los em um novo token e adicioná-lo ao vocabulário.
3. **Repetir**: Repetir passo 2 até que o tamanho do vocabulário atinja um limiar predefinido.

**Demonstração de Caso:** Suponha que nosso mini corpus seja `{"hug": 1, "pug": 1, "pun": 1, "bun": 1}`, e queremos construir um vocabulário de tamanho 10. O processo de treinamento BPE pode ser representado pela Tabela 3.1:

<div align="center">
  <p>Tabela 3.1 Exemplo do Processo de Mesclagem do Algoritmo BPE</p>
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/3-figures/1757249275674-5.png" alt="Descrição da figura" width="90%"/>
</div>

Após o término do treinamento, quando o tamanho do vocabulário atinge 10, obtemos novas regras de tokenização. Agora, para uma palavra não vista "bug", o tokenizer primeiro verificará se "bug" está no vocabulário e encontrará que não está; depois verificará "bu" e encontrará que não está; finalmente verificará "b" e "ug", encontrará que ambos estão, e assim dividirá em `['b', 'ug']`.

Abaixo usamos um código Python simples para simular o processo acima:

```Python
import re, collections

def get_stats(vocab):
    """Contar frequências de pares de tokens"""
    pairs = collections.defaultdict(int)
    for word, freq in vocab.items():
        symbols = word.split()
        for i in range(len(symbols)-1):
            pairs[symbols[i],symbols[i+1]] += freq
    return pairs

def merge_vocab(pair, v_in):
    """Mesclar pares de tokens"""
    v_out = {}
    bigram = re.escape(' '.join(pair))
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word in v_in:
        w_out = p.sub(''.join(pair), word)
        v_out[w_out] = v_in[word]
    return v_out

# Preparar corpus, adicionar </w> no final de cada palavra para indicar término, e dividir caracteres
vocab = {'h u g </w>': 1, 'p u g </w>': 1, 'p u n </w>': 1, 'b u n </w>': 1}
num_merges = 4 # Definir número de mesclagens

for i in range(num_merges):
    pairs = get_stats(vocab)
    if not pairs:
        break
    best = max(pairs, key=pairs.get)
    vocab = merge_vocab(best, vocab)
    print(f"Mesclagem {i+1}: {best} -> {''.join(best)}")
    print(f"Novo vocabulário (parcial): {list(vocab.keys())}")
    print("-" * 20)

>>>
Mesclagem 1: ('u', 'g') -> ug
Novo vocabulário (parcial): ['h ug </w>', 'p ug </w>', 'p u n </w>', 'b u n </w>']
--------------------
Mesclagem 2: ('ug', '</w>') -> ug</w>
Novo vocabulário (parcial): ['h ug</w>', 'p ug</w>', 'p u n </w>', 'b u n </w>']
--------------------
Mesclagem 3: ('u', 'n') -> un
Novo vocabulário (parcial): ['h ug</w>', 'p ug</w>', 'p un </w>', 'b un </w>']
--------------------
Mesclagem 4: ('un', '</w>') -> un</w>
Novo vocabulário (parcial): ['h ug</w>', 'p ug</w>', 'p un</w>', 'b un</w>']
--------------------
```

Este código demonstra claramente como o algoritmo BPE gradualmente constrói e expande o vocabulário iterativamente mesclando os pares de tokens adjacentes de maior frequência.

Muitos algoritmos subsequentes são otimizações baseadas em BPE. Entre eles, WordPiece e SentencePiece desenvolvidos pelo Google são os dois mais influentes.

- **WordPiece**: O algoritmo adotado pelo modelo BERT do Google<sup>[7]</sup>. É muito similar ao BPE, mas o critério para mesclar tokens não é "maior frequência" mas "maximizar a melhoria da probabilidade do modelo de linguagem do corpus". Simplesmente colocado, prioriza mesclar pares de tokens que podem maximizar a melhoria da "fluência" de todo o corpus.
- **SentencePiece**: Uma ferramenta de tokenização de código aberto pelo Google<sup>[8]</sup>, adotada pelos modelos da série Llama. Sua maior característica é tratar espaços como caracteres comuns (geralmente representados por sublinhado `_`). Isto torna o processo de tokenização e decodificação completamente reversível e independente de línguas específicas (por exemplo, não precisa saber que chinês não usa espaços para segmentação de palavras).

**3.2.2.3 Significado dos Tokenizers para Desenvolvedores**

Entender os detalhes dos algoritmos de tokenização não é o objetivo, mas como desenvolvedor de agentes, entender o impacto real dos tokenizers é importante, pois se relaciona diretamente com desempenho, custo e estabilidade do agente:

- **Limitação de Janela de Contexto**: A janela de contexto do modelo (como 8K, 128K) é calculada em **contagem de Token**, não contagem de caracteres ou palavras. O mesmo texto pode ter contagens de Token vastamente diferentes em diferentes línguas (como chinês e inglês) ou com diferentes tokenizers. Gerenciar precisamente o comprimento de entrada e evitar exceder limites de contexto é a base para construir agentes de memória de longo prazo.
- **Custo de API**: A maioria das APIs de modelos cobra baseado em contagem de Token. Entender como seu texto será tokenizado é um passo chave em estimar e controlar custos de operação de agentes.
- **Anomalias de Desempenho do Modelo**: Às vezes comportamento estranho do modelo decorre da tokenização. Por exemplo, o modelo pode ser bom em calcular `2 + 2` mas pode cometer erros com `2+2` (sem espaços) porque o último pode ser tratado pelo tokenizer como um token independente e incomum. Similarmente, uma palavra com capitalização diferente da primeira letra pode ser dividida em sequências de Token completamente diferentes, afetando a compreensão do modelo. Considerar essas "armadilhas" ao projetar prompts e analisar saídas do modelo ajuda a melhorar a robustez do agente.

### 3.2.3 Chamando Modelos de Linguagem de Grande Escala de Código Aberto

No Capítulo 1 deste livro, interagimos com modelos de linguagem de grande escala através de APIs para conduzir nossos agentes. Este é um método rápido e conveniente, mas não o único. Para muitos cenários requerendo processamento de dados sensíveis, operação offline ou controle fino de custos, implantar modelos de linguagem de grande escala diretamente localmente se torna crucial.

**Hugging Face Transformers** é uma poderosa biblioteca de código aberto que fornece interfaces padronizadas para carregar e usar dezenas de milhares de modelos pré-treinados. Usaremos ela para completar esta prática.

**Configuração de Ambiente e Seleção de Modelo**: Para garantir que a maioria dos leitores possa executar suavemente em computadores pessoais, deliberadamente escolhemos um modelo de pequena escala mas poderoso: `Qwen/Qwen1.5-0.5B-Chat`. Este é um modelo de diálogo com cerca de 500 milhões de parâmetros de código aberto pela Alibaba DAMO Academy. É pequeno em tamanho, excelente em desempenho, e muito adequado para aprendizado introdutório e implantação local.

Primeiro, por favor garanta que você instalou as bibliotecas necessárias:

```Plain
pip install transformers torch
```

Na biblioteca `transformers`, tipicamente usamos as classes `AutoModelForCausalLM` e `AutoTokenizer` para carregar automaticamente pesos e tokenizers correspondentes ao modelo. O código seguinte automaticamente baixará arquivos de modelo necessários e configurações de tokenizer do Hugging Face Hub, o que pode levar algum tempo dependendo da velocidade da sua rede.

```Python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# Especificar ID do modelo
model_id = "Qwen/Qwen1.5-0.5B-Chat"

# Definir dispositivo, priorizar GPU
device = "cuda" if torch.cuda.is_available() else "cpu"
print(f"Usando dispositivo: {device}")

# Carregar tokenizer
tokenizer = AutoTokenizer.from_pretrained(model_id)

# Carregar modelo e movê-lo para dispositivo especificado
model = AutoModelForCausalLM.from_pretrained(model_id).to(device)

print("Modelo e tokenizer carregados!")
```

Vamos criar um prompt de diálogo. O modelo Qwen1.5-Chat segue um template de diálogo específico. Depois, podemos usar o `tokenizer` carregado no passo anterior para converter o prompt de texto em IDs numéricos (isto é, IDs de Token) que o modelo pode entender.

```Python
# Preparar entrada de diálogo
messages = [
    {"role": "system", "content": "Você é um assistente útil."},
    {"role": "user", "content": "Olá, por favor se apresente."}
]

# Usar template do tokenizer para formatar entrada
text = tokenizer.apply_chat_template(
    messages,
    tokenize=False,
    add_generation_prompt=True
)

# Codificar texto de entrada
model_inputs = tokenizer([text], return_tensors="pt").to(device)

print("Texto de entrada codificado:")
print(model_inputs)

>>>
{'input_ids': tensor([[151644, 8948, 198, 2610, 525, 264,  10950, 17847, 13,151645, 198, 151644, 872, 198, 108386, 37945, 100157, 107828,1773, 151645, 198, 151644, 77091, 198]], device='cuda:0'), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]],
       device='cuda:0')}
```

Agora podemos chamar o método `generate()` do modelo para gerar uma resposta. O modelo produzirá uma série de IDs de Token representando sua resposta.

Finalmente, precisamos usar o método `decode()` do tokenizer para traduzir esses IDs numéricos de volta em texto legível por humanos.

```Python
# Usar modelo para gerar resposta
# max_new_tokens controla o número máximo de novos Tokens que o modelo pode gerar
generated_ids = model.generate(
    model_inputs.input_ids,
    max_new_tokens=512
)

# Truncar a parte de entrada dos IDs de Token gerados
# Desta forma apenas decodificamos a parte recém-gerada pelo modelo
generated_ids = [
    output_ids[len(input_ids):] for input_ids, output_ids in zip(model_inputs.input_ids, generated_ids)
]

# Decodificar IDs de Token gerados
response = tokenizer.batch_decode(generated_ids, skip_special_tokens=True)[0]

print("\nResposta do modelo:")
print(response)

>>>
Meu nome é Tongyi Qianwen, um modelo de linguagem pré-treinado desenvolvido pela Alibaba Cloud. Posso responder perguntas, criar texto, expressar opiniões e escrever código. Minhas principais funções são fornecer ajuda em múltiplos campos, incluindo mas não limitado a: compreensão de linguagem, geração de texto, tradução automática, sistemas de resposta a perguntas, etc. Há algo em que posso ajudá-lo?
```

Após executar todo o código, você verá a introdução gerada pelo modelo sobre o modelo Qwen no seu computador local. Parabéns, você implantou e executou com sucesso um modelo de linguagem de grande escala de código aberto localmente!

### 3.2.4 Seleção de Modelo

Na seção anterior, executamos com sucesso um pequeno modelo de linguagem de código aberto localmente. Isto naturalmente levanta uma questão crucial para desenvolvedores de agentes: no contexto atual de centenas de modelos florescendo, como devemos escolher o modelo mais adequado para tarefas específicas?

Escolher um modelo de linguagem não é simplesmente buscar "o maior, o mais forte" mas um processo de tomada de decisão equilibrando desempenho, custo, velocidade e métodos de implantação. Esta seção primeiro organizará várias considerações chave para seleção de modelo, depois revisará modelos mainstream atuais de código fechado e aberto.

Como a tecnologia de modelos de linguagem de grande escala está em um estágio de desenvolvimento rápido, com novos modelos e versões surgindo constantemente e iteração extremamente rápida, esta seção se esforça para fornecer uma visão geral de modelos mainstream atuais e considerações de seleção quando escrita, mas leitores devem notar que versões específicas de modelos e dados de desempenho mencionados podem mudar ao longo do tempo, e apenas alguns trabalhos são listados, não abrangentemente. Focamos mais em introduzir características técnicas centrais, tendências de desenvolvimento e princípios gerais de seleção no desenvolvimento de agentes.

**3.2.4.1 Considerações Chave para Seleção de Modelo**

Ao escolher um modelo de linguagem de grande escala para seu agente, você pode avaliar abrangentemente a partir das seguintes dimensões:

- **Desempenho e Capacidade**: Esta é a consideração central. Diferentes modelos se destacam em diferentes tarefas; alguns são bons em raciocínio lógico e geração de código, enquanto outros são melhores em escrita criativa ou tradução multilíngue. Você pode se referir a alguns rankings de benchmark públicos (como LMSys Chatbot Arena Leaderboard) para avaliar capacidades abrangentes de modelos.
- **Custo**: Para modelos de código fechado, custo se manifesta principalmente em taxas de chamada de API, geralmente cobradas por contagem de Token. Para modelos de código aberto, custo se manifesta em hardware (GPU, memória) e operações necessárias para implantação local. Escolhas precisam ser feitas baseadas no uso esperado da aplicação e orçamento.
- **Velocidade (Latência)**: Para agentes requerendo interação em tempo real (como atendimento ao cliente, NPCs de jogo), velocidade de resposta do modelo é crucial. Alguns modelos leves ou otimizados (como GPT-3.5 Turbo, Claude 3.5 Sonnet) têm melhor desempenho em latência.
- **Janela de Contexto**: O limite superior de contagem de Token que o modelo pode processar de uma vez. Para agentes precisando entender documentos longos, analisar repositórios de código ou manter memória de conversação de longo prazo, escolher um modelo com uma janela de contexto maior (como 128K Tokens ou superior) é necessário.
- **Método de Implantação**: Usar APIs é mais simples e conveniente, mas dados precisam ser enviados a terceiros e estão sujeitos a termos do provedor de serviços. Implantação local pode garantir privacidade de dados e maior grau de autonomia, mas tem requisitos técnicos e de hardware mais altos.
- **Ecossistema e Cadeia de Ferramentas**: A popularidade de um modelo também determina a maturidade de seu ecossistema circundante. Modelos mainstream geralmente têm suporte de comunidade mais rico, tutoriais, modelos pré-treinados, ferramentas de ajuste fino e frameworks de desenvolvimento compatíveis (como LangChain, LlamaIndex, Hugging Face Transformers), o que pode acelerar muito o desenvolvimento e reduzir a dificuldade. Escolher um modelo com uma comunidade ativa e cadeia de ferramentas completa torna mais fácil encontrar soluções e recursos ao encontrar problemas.
- **Capacidade de Ajuste Fino e Personalização**: Para agentes precisando processar dados específicos de domínio ou realizar tarefas específicas, capacidade de ajuste fino do modelo é crucial. Alguns modelos fornecem interfaces e ferramentas de ajuste fino convenientes, permitindo que desenvolvedores treinem personalizadamente usando seus próprios conjuntos de dados, melhorando significativamente o desempenho e precisão do modelo em cenários específicos. Modelos de código aberto geralmente fornecem maior flexibilidade neste aspecto.
- **Segurança e Ética**: Com a aplicação ampla de modelos de linguagem de grande escala, seus riscos potenciais de segurança e questões éticas estão cada vez mais proeminentes. Ao escolher modelos, considere seu desempenho em viés, toxicidade, alucinação, etc., e investimento de provedores de serviços ou comunidades de código aberto em segurança de modelos e IA responsável. Para aplicações voltadas ao público ou envolvendo informações sensíveis, segurança de modelos e conformidade ética são considerações que não podem ser ignoradas.

**3.2.4.2 Visão Geral de Modelos de Código Fechado**

Modelos de código fechado geralmente representam a vanguarda da tecnologia de IA atual e fornecem serviços de API estáveis e fáceis de usar, tornando-os a primeira escolha para construir agentes de alto desempenho.

1. **Série GPT da OpenAI**: Do GPT-3 que abriu a era de modelos grandes, ao ChatGPT que introduziu RLHF (Reinforcement Learning from Human Feedback) e alcançou alinhamento com intenção humana, ao GPT-4 que abriu a era multimodal, a OpenAI continua a liderar o desenvolvimento da indústria. O mais recente GPT-5 eleva ainda mais capacidades multimodais e inteligência geral a novos patamares, processando perfeitamente entradas de texto, áudio e imagem e gerando saídas correspondentes, com velocidade de resposta e naturalidade significativamente melhoradas, especialmente se destacando em diálogo de voz em tempo real.
2. **Série Gemini do Google**: Os modelos da série Gemini do Google DeepMind são representantes de multimodalidade nativa, com a característica central de processamento unificado de múltiplas modalidades incluindo texto, código, áudio/vídeo e imagens, e vantagens em processamento de informações massivas com janelas de contexto ultra-longas. Gemini Ultra é seu modelo mais poderoso, adequado para tarefas altamente complexas; Gemini Pro é adequado para uma ampla gama de tarefas, fornecendo alto desempenho e eficiência; Gemini Nano é otimizado para implantação em dispositivo. Os mais recentes modelos da série Gemini 2.5, como Gemini 2.5 Pro e Gemini 2.5 Flash, melhoram ainda mais capacidades de raciocínio e janelas de contexto, especialmente Gemini 2.5 Flash com velocidade de inferência mais rápida e custo-benefício, adequado para cenários requerendo respostas rápidas.
3. **Série Claude da Anthropic**: Anthropic é uma empresa focada em segurança de IA e IA responsável. Seus modelos da série Claude priorizaram segurança de IA desde o estágio de design, renomados pela confiabilidade em lidar com documentos longos, reduzir saídas prejudiciais e seguir instruções, profundamente favorecidos por aplicações empresariais. A série Claude 3 inclui Claude 3 Opus (mais inteligente, desempenho mais forte), Claude 3 Sonnet (escolha equilibrada de desempenho e velocidade), e Claude 3 Haiku (modelo mais rápido e compacto, adequado para interação quase em tempo real). Os mais recentes modelos da série Claude 4, como Claude 4 Opus, fizeram progresso significativo em inteligência geral, raciocínio complexo e geração de código, melhorando ainda mais capacidades em lidar com contextos longos e tarefas multimodais.
4. **Modelos Mainstream Domésticos**: A China emergiu com muitos modelos competitivos de código fechado no campo de modelos de linguagem de grande escala, representados por Baidu ERNIE Bot, Tencent Hunyuan, Huawei Pangu-α, iFlytek SparkDesk e Moonshot AI. Esses modelos domésticos têm vantagens naturais em processamento de chinês e capacitam profundamente indústrias locais.

**3.2.4.3 Visão Geral de Modelos de Código Aberto**

Modelos de código aberto fornecem aos desenvolvedores o mais alto grau de flexibilidade, transparência e autonomia, catalisando um ecossistema de comunidade próspero. Eles permitem que desenvolvedores implantem localmente, realizem ajuste fino personalizado e tenham controle completo do modelo.

- **Série Llama da Meta**: A série Llama da Meta é um marco importante em modelos de linguagem de grande escala de código aberto. A série se tornou a base para muitos projetos derivados e pesquisas com excelente desempenho abrangente, acordos de licenciamento abertos e forte suporte da comunidade. A série Llama 4 foi lançada em abril de 2025, os primeiros modelos da Meta adotando arquitetura Mixture of Experts (MoE), o que melhora significativamente a eficiência computacional ativando apenas partes do modelo necessárias para processar tarefas específicas. A série inclui três modelos distintamente posicionados: Llama 4 Scout suporta uma janela de contexto de 10 milhões de tokens projetada para análise de documentos longos e implantação móvel. Llama 4 Maverick foca em capacidades multimodais, se destacando em codificação, raciocínio complexo e suporte multilíngue. Llama 4 Behemoth supera concorrentes em múltiplos benchmarks STEM e é o modelo mais poderoso da Meta atualmente.
- **Série Mistral AI**: Mistral AI da França é renomada por seu design de modelo "pequeno tamanho, alto desempenho". Seu mais recente modelo Mistral Medium 3.1 foi lançado em agosto de 2025, com precisão e velocidade de resposta significativamente melhoradas em tarefas como geração de código, raciocínio STEM e Q&A entre domínios, com desempenho de benchmark superior ao Claude Sonnet 3.7 e Llama 4 Maverick e outros modelos similares. Tem capacidades multimodais nativas, pode simultaneamente processar entradas mistas de imagem e texto, e tem uma "camada de adaptação de tom" integrada para ajudar empresas a alcançar mais facilmente saídas alinhadas com a marca.
- **Forças de Código Aberto Domésticas**: Fabricantes domésticos e instituições de pesquisa também estão ativamente abraçando código aberto, como a série **Qwen (Tongyi Qianwen)** da Alibaba e a série **ChatGLM** da colaboração da Universidade Tsinghua com Zhipu AI. Eles fornecem poderosas capacidades em chinês e construíram comunidades ativas ao seu redor.

Para desenvolvedores de agentes, modelos de código fechado fornecem conveniência "pronta para uso", enquanto modelos de código aberto nos concedem "liberdade de personalização". Entender as características e modelos representativos desses dois campos é o primeiro passo para fazer seleções técnicas sábias para nossos projetos de agentes.

## 3.3 Leis de Escala e Limitações de Modelos de Linguagem de Grande Escala

Modelos de Linguagem de Grande Escala (LLMs) fizeram progresso notável nos últimos anos, com limites de capacidade continuamente em expansão e cenários de aplicação cada vez mais ricos. No entanto, por trás dessas conquistas está uma compreensão profunda da relação entre escala do modelo, volume de dados e recursos computacionais, nomeadamente **Leis de Escala (Scaling Laws)**. Enquanto isso, como uma tecnologia emergente, LLMs também enfrentam muitos desafios e limitações. Esta seção explorará profundamente esses conceitos centrais, visando ajudar leitores a compreender abrangentemente os limites de capacidade dos LLMs, aproveitando assim pontos fortes e evitando pontos fracos ao construir agentes.

### 3.3.1 Leis de Escala

**Leis de Escala (Scaling Laws)** são uma das descobertas mais importantes no campo de modelos de linguagem de grande escala nos últimos anos. Elas revelam que há relações de lei de potência previsíveis entre desempenho do modelo e contagem de parâmetros do modelo, volume de dados de treinamento e recursos computacionais. Esta descoberta fornece orientação teórica para o desenvolvimento contínuo de modelos de linguagem de grande escala, esclarecendo a lógica subjacente de que aumentar investimento de recursos pode sistematicamente melhorar o desempenho do modelo.

Pesquisas descobriram que em sistemas de coordenadas log-log, desempenho do modelo (geralmente medido por Loss) mostra relações suaves de lei de potência com todos os três fatores: contagem de parâmetros, volume de dados e computação<sup>[9]</sup>. Simplesmente colocado, contanto que continuemos e proporcionalmente aumentemos esses três elementos, o desempenho do modelo melhorará previsivelmente e suavemente sem gargalos óbvios. Esta descoberta fornece orientação clara para design e treinamento de modelos grandes: dentro de restrições de recursos, maximize escala do modelo e volume de dados de treinamento o máximo possível.

Pesquisas iniciais focaram mais em aumentar contagem de parâmetros do modelo, mas a "Lei Chinchilla" do DeepMind proposta em 2022 fez correções importantes<sup>[10]</sup>. Esta lei aponta que sob um orçamento computacional dado, para alcançar desempenho ótimo, **há uma proporção ótima entre contagem de parâmetros do modelo e volume de dados de treinamento**. Especificamente, modelos ótimos devem ser menores do que anteriormente comumente acreditado mas precisam ser treinados com muito mais dados. Por exemplo, um modelo Chinchilla de 70 bilhões de parâmetros, porque foi treinado com 4 vezes mais dados que GPT-3 (175 bilhões de parâmetros), na verdade supera o último. Esta descoberta corrigiu a percepção unilateral de "maior é melhor", enfatizou a importância da eficiência de dados e guiou o design de muitos modelos grandes eficientes subsequentes (como a série Llama).

O produto mais surpreendente das leis de escala é "emergência de capacidade". A chamada emergência de capacidade se refere a quando escala do modelo atinge um certo limiar, ele subitamente exibe capacidades completamente novas que não existem ou têm desempenho pobre em modelos de pequena escala. Por exemplo, **Chain-of-Thought**, **Instruction Following**, raciocínio multi-passo, geração de código e outras capacidades todas apareceram significativamente apenas após contagens de parâmetros do modelo atingirem dezenas ou até centenas de bilhões. Este fenômeno indica que modelos de linguagem de grande escala não estão simplesmente memorizando e recitando; eles podem ter formado algum nível mais profundo de abstração e capacidades de raciocínio durante o aprendizado. Para desenvolvedores de agentes, emergência de capacidade significa que escolher um modelo de escala suficientemente grande é um pré-requisito para alcançar tomada de decisão autônoma complexa e capacidades de planejamento.

### 3.3.2 Alucinação de Modelo

**Alucinação de Modelo (Model Hallucination)** geralmente se refere a conteúdo gerado por modelos de linguagem de grande escala que contradiz fatos objetivos, entrada do usuário ou informações contextuais, ou gera fatos, entidades ou eventos não existentes. A essência da alucinação é que modelos "fabricam" informações com excesso de confiança durante a geração em vez de recuperar ou raciocinar com precisão. De acordo com formas de manifestação, alucinações podem ser divididas em múltiplos tipos<sup>[11]</sup>, tais como:

- **Alucinações Factuais**: Modelos geram informações inconsistentes com fatos do mundo real.
- **Alucinações de Fidelidade**: Em tarefas como resumo de texto e tradução, conteúdo gerado falha em refletir fielmente o significado do texto fonte.
- **Alucinações Intrínsecas**: Conteúdo gerado pelo modelo contradiz diretamente informações de entrada.

A produção de alucinação resulta de múltiplos fatores trabalhando juntos. Primeiro, dados de treinamento podem conter informações errôneas ou contraditórias. Segundo, o mecanismo de geração autoregressiva do modelo determina que ele apenas prevê o próximo token mais provável sem um módulo de verificação de fatos integrado. Finalmente, ao enfrentar tarefas requerendo raciocínio complexo, modelos podem cometer erros em cadeias lógicas, fabricando assim conclusões erradas. Por exemplo: um Agente de planejamento de viagem pode recomendar um local turístico não existente ou reservar um bilhete com um número de voo incorreto.

Adicionalmente, modelos de linguagem de grande escala enfrentam desafios como oportunidade de conhecimento insuficiente e vieses em dados de treinamento. Capacidades de modelos de linguagem de grande escala vêm de seus dados de treinamento. Isto significa que o conhecimento que o modelo possui é o material mais recente quando seus dados de treinamento foram coletados. Para eventos ocorrendo após esta data, conceitos recém-emergidos ou fatos mais recentes, o modelo será incapaz de perceber ou responder corretamente. Enquanto isso, dados de treinamento frequentemente contêm vários vieses e estereótipos da sociedade humana. Quando modelos aprendem sobre esses dados, inevitavelmente absorvem e refletem esses vieses<sup>[12]</sup>.

Para melhorar a confiabilidade de modelos de linguagem de grande escala, pesquisadores e desenvolvedores estão ativamente explorando múltiplos métodos para detectar e mitigar alucinações:

1. **Nível de Dados**: Reduzir alucinações da fonte através de limpeza de dados de alta qualidade, introdução de conhecimento factual e Reinforcement Learning from Human Feedback (RLHF)<sup>[13]</sup>.
2. **Nível de Modelo**: Explorar novas arquiteturas de modelo ou permitir que modelos expressem incerteza sobre conteúdo gerado.
3. **Nível de Inferência e Geração**:
   1. **Geração Aumentada por Recuperação (RAG)**<sup>[14]</sup>: Este é atualmente um dos métodos eficazes para mitigar alucinações. Sistemas RAG recuperam informações relevantes de bases de conhecimento externas (como bancos de dados de documentos, páginas web) antes da geração, depois usam informações recuperadas como contexto para guiar modelos a gerar respostas baseadas em fatos.
   2. **Raciocínio Multi-passo e Verificação**: Guiar modelos a realizar raciocínio multi-passo e conduzir auto-verificação ou verificação externa em cada passo.
   3. **Introdução de Ferramentas Externas**: Permitir que modelos chamem ferramentas externas (como motores de busca, calculadoras, interpretadores de código) para obter informações em tempo real ou realizar cálculos precisos.

Embora problemas de alucinação sejam difíceis de eliminar completamente no curto prazo, através das estratégias acima, sua frequência de ocorrência e impacto podem ser significativamente reduzidos, melhorando a confiabilidade e praticidade de modelos de linguagem de grande escala em aplicações reais.

## 3.4 Resumo do Capítulo

Este capítulo introduziu conhecimento fundamental necessário para construir agentes, focando em modelos de linguagem de grande escala (LLMs) como seu componente central. O conteúdo começou do desenvolvimento inicial de modelos de linguagem, detalhou a arquitetura Transformer e introduziu métodos para interagir com LLMs. Finalmente, este capítulo organizou ecossistemas de modelos mainstream atuais, padrões de desenvolvimento e suas limitações inerentes.

**Revisão de Conhecimento Central:**

- **Evolução de Modelo e Arquitetura Central**: Este capítulo rastreou desde modelos de linguagem estatísticos (N-gram) até modelos de redes neurais (RNN, LSTM), até a arquitetura Transformer que estabeleceu a base para LLMs modernos. Através de implementação de código "de cima para baixo", este capítulo dissecou componentes centrais do Transformer e explicou o papel chave do mecanismo de auto-atenção em computação paralela e captura de dependências de longa distância.
- **Métodos de Interação com Modelos**: Este capítulo introduziu dois aspectos centrais de interação com LLMs: Engenharia de Prompt e Tokenização. O primeiro guia comportamento do modelo, o último é a base para entender processamento de entrada do modelo. Através da prática de implantar e executar modelos de código aberto localmente, conhecimento teórico foi aplicado a operações reais.
- **Ecossistema de Modelo e Seleção**: Este capítulo organizou sistematicamente fatores chave a pesar ao escolher modelos para agentes e revisou características e posicionamento de modelos de código fechado representados por OpenAI GPT e Google Gemini e modelos de código aberto representados por Llama e Mistral.
- **Leis e Limitações**: Este capítulo explorou leis de escala impulsionando melhoria de capacidade de LLM e explicou princípios subjacentes. Enquanto isso, este capítulo também analisou limitações inerentes de modelos como alucinações factuais e conhecimento desatualizado, o que é crucial para construir agentes confiáveis e robustos.

**De Fundamentos de LLM para Construção de Agentes:**

Os fundamentos de LLM deste capítulo ajudam principalmente todos a entender melhor o processo de nascimento e desenvolvimento de modelos grandes, o que também contém algum pensamento sobre design de agentes. Por exemplo, como projetar prompts eficazes para guiar planejamento e tomada de decisão de Agente, como escolher modelos apropriados baseados em requisitos de tarefas, e como adicionar mecanismos de verificação em fluxos de trabalho de Agente para evitar alucinações de modelo—soluções para esses problemas são todas construídas sobre a base deste capítulo. Estamos agora prontos para transitar da teoria para a prática. No próximo capítulo, começaremos a explorar construção de paradigma de agente clássico, aplicando conhecimento aprendido neste capítulo ao design real de agentes.

## Exercícios

1. Em processamento de linguagem natural, modelos de linguagem evoluíram de estatísticos para modelos de redes neurais.

   - Por favor use o mini corpus fornecido neste capítulo (`datawhale agent learns`, `datawhale agent works`) para calcular a probabilidade da sentença `agent works` sob o modelo Bigram
   - A suposição central de modelos N-gram é a suposição de Markov. Por favor explique o significado desta suposição e quais limitações fundamentais modelos N-gram têm?
   - Como modelos de linguagem de redes neurais (RNN/LSTM) e Transformer superam limitações de modelos N-gram respectivamente? Quais são suas respectivas vantagens?

2. A arquitetura Transformer<sup>[4]</sup> é a base de modelos de linguagem de grande escala modernos. Entre eles:

   > **Dica**: Pode combinar implementação de código na Seção 3.1.2 deste capítulo para ajudar na compreensão

   - Qual é a ideia central do mecanismo de Auto-Atenção (Self-Attention)?
   - Por que o Transformer pode processar sequências em paralelo enquanto RNN deve processar serialmente? Que papel a Codificação Posicional (Positional Encoding) desempenha?
   - Qual é a diferença entre arquitetura Decoder-Only e arquitetura completa Encoder-Decoder? Por que modelos de linguagem de grande escala mainstream atuais todos adotam arquitetura Decoder-Only?

3. Algoritmos de tokenização de subpalavras de texto são uma tecnologia chave para modelos de linguagem de grande escala, responsáveis por converter texto em sequências de token que o modelo pode processar. Por que não podemos usar diretamente "caracteres" ou "palavras" como unidades de entrada do modelo? Que problema o algoritmo BPE (Byte Pair Encoding) resolve?

4. Seção 3.2.3 deste capítulo introduziu como implantar modelos de linguagem de grande escala de código aberto localmente. Por favor complete a seguinte prática e análise:

   > **Dica**: Esta é uma questão de prática hands-on; operação real é recomendada

   - Seguindo a orientação deste capítulo, implante um modelo de código aberto leve localmente (recomendo [Qwen3-0.6B](https://modelscope.cn/models/Qwen/Qwen3-0.6B)), tente ajustar parâmetros de amostragem e observe seu impacto na saída
   - Escolha uma tarefa específica (como classificação de texto, extração de informações, geração de código, etc.), projete e compare diferentes estratégias de prompt (como Zero-shot, Few-shot, Chain-of-Thought) e suas diferenças de efeito em resultados de saída
   - Compare modelos de código fechado e modelos de código aberto de dimensões de desempenho, custo, controlabilidade, privacidade, etc.
   - Se você quer construir um agente de atendimento ao cliente de nível empresarial, que tipo de modelo você escolheria? Que fatores precisam ser considerados?

5. Alucinação de Modelo<sup>[11]</sup> é uma das limitações chave de modelos de linguagem de grande escala atuais. Este capítulo introduziu métodos para mitigar alucinações (como geração aumentada por recuperação, raciocínio multi-passo, invocação de ferramenta externa)

   - Por favor escolha um e explique seu princípio de funcionamento e cenários aplicáveis
   - Pesquise estudos e artigos de vanguarda—há outros métodos para mitigar alucinações de modelo, e quais melhorias e vantagens eles têm?

6. Suponha que você queira projetar um agente assistente de leitura de artigos que possa ajudar pesquisadores a ler rapidamente e entender artigos acadêmicos, incluindo: resumir conteúdo central de pesquisa de artigo, responder perguntas sobre artigos, extrair informações chave, comparar pontos de vista de diferentes artigos, etc. Por favor responda:

   - Qual modelo você escolheria como modelo base ao projetar o agente? Que fatores precisam ser considerados ao escolher?
   - Como projetar prompts para guiar o modelo a entender melhor artigos acadêmicos? Artigos acadêmicos são geralmente muito longos e podem exceder o limite de janela de contexto do modelo—como você resolveria este problema?
   - Pesquisa acadêmica é rigorosa, significando que precisamos garantir que informações geradas pelo agente sejam precisas, objetivas e fiéis ao texto original. Que designs você acha que deveriam ser adicionados ao sistema para melhor alcançar este requisito?

## Referências

[1] Bengio, Y., Ducharme, R., Vincent, P., & Jauvin, C. (2003). A neural probabilistic language model. *Journal of Machine Learning Research*, 3, 1137-1155.

[2] Elman, J. L. (1990). Finding structure in time. *Cognitive Science*, 14(2), 179-211.

[3] Hochreiter, S., & Schmidhuber, J. (1997). Long short-term memory. *Neural Computation*, 9(8), 1735-1780.

[4] Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., ... & Polosukhin, I. (2017). Attention is all you need. In *Advances in neural information processing systems* (pp. 5998-6008).

[5] Radford, A., Narasimhan, K., Salimans, T., & Sutskever, I. (2018). Improving language understanding by generative pre-training. OpenAI.

[6] Gage, P. (1994). A new algorithm for data compression. *C Users Journal*, *12*(2), 23-38.

[7] Schuster, M., & Nakajima, K. (2012, March). Japanese and korean voice search. In *2012 IEEE international conference on acoustics, speech and signal processing (ICASSP)* (pp. 5149-5152). IEEE.

[8] Kudo, T., & Richardson, J. (2018). SentencePiece: A simple and language independent subword tokenizer and detokenizer for neural text processing. *arXiv preprint arXiv:1808.06226*.

[9] Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., ... & Amodei, D. (2020). Scaling Laws for Neural Language Models. arXiv preprint arXiv:2001.08361.

[10] Hoffmann, J., Borgeaud, E., Mensch, A., Buchatskaya, E., Cai, T., Rutherford, R., ... & Sifre, L. (2022). Training Compute-Optimal Large Language Models. arXiv preprint arXiv:2203.07678.

[11] Ji, Z., Lee, N., Fries, R., Yu, T., & Su, D. (2023). Survey of Hallucination in Large Language Models.

[12] Bender, E. M., Gebru, T., McMillan-Major, A., & Mitchell, M. (2021). On the Dangers of Stochastic Parrots: Can Language Models Be Too Big? .

[13] Christiano, P., Leike, J., Brown, T. B., Martic, M., Legg, S., & Amodei, D. (2017). Deep reinforcement learning from human preferences. *arXiv preprint arXiv:1706.03741*.

[14] Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goswami, N., ... & Kiela, D. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. In *Advances in neural information processing systems* (pp. 9459-9474).
