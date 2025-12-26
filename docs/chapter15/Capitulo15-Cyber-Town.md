# Capítulo 15: Construindo Cyber Town

<div align="right">
  <a href="./Chapter15-Building-Cyber-Town.md">English</a> | <a href="./第十五章%20构建赛博小镇.md">中文</a> | Português
</div>

Neste capítulo, exploraremos uma direção totalmente nova: **combinar tecnologia de agentes com motores de jogos para construir uma cidade de IA cheia de vitalidade**.

Você se lembra daqueles NPCs realistas em "The Sims" ou "Animal Crossing"? Eles têm suas próprias personalidades, memórias e relacionamentos sociais. A Cyber Town neste capítulo será um projeto similar, mas diferente dos jogos tradicionais, nossos NPCs têm "inteligência" real - eles podem entender conversas dos jogadores, lembrar de interações passadas e reagir de forma diferente com base nos níveis de afeição. A Cyber Town neste capítulo inclui as seguintes funcionalidades principais:

**(1) Sistema de Diálogo Inteligente para NPCs**: Os jogadores podem ter conversas em linguagem natural com NPCs, e os NPCs responderão com base em suas configurações de função e memórias.

**(2) Sistema de Memória**: NPCs têm memória de curto e longo prazo, capazes de lembrar histórico de interações com jogadores.

**(3) Sistema de Afeição**: As atitudes dos NPCs em relação aos jogadores mudam com as interações, de estranho a familiar, de amigável a íntimo.

**(4) Interação Gamificada**: Os jogadores podem se mover livremente em uma cena de escritório estilo pixel 2D e interagir com diferentes NPCs.

**(5) Sistema de Registro em Tempo Real**: Todas as conversas e interações são registradas para facilitar depuração e análise.

## 15.1 Visão Geral do Projeto e Design da Arquitetura

### 15.1.1 Por Que Construir uma Cidade de IA

NPCs em jogos tradicionais geralmente só podem dizer frases fixas ou ter interações limitadas através de árvores de diálogo predefinidas. Mesmo nos jogos de RPG mais complexos, os diálogos dos NPCs são pré-escritos por roteiristas. Esta abordagem é controlável, mas carece de "inteligência" e "vitalidade" reais.

Imagine se os NPCs em jogos pudessem entender qualquer coisa que você diz, não mais limitados a opções predefinidas. Você pode se comunicar com NPCs em linguagem natural. Os NPCs se lembrarão do que você disse da última vez, seu relacionamento e até suas preferências. Cada NPC tem sua própria profissão, personalidade e estilo de fala. As atitudes dos NPCs em relação a você mudam com as interações, de estranhos a amigos, até amigos próximos.

Esta é a nova possibilidade que a tecnologia de IA traz aos jogos. Ao combinar modelos de linguagem de grande escala com motores de jogos, podemos criar NPCs que estão verdadeiramente "vivos". Isto não é apenas uma demonstração técnica, mas uma exploração de formas futuras de jogos. Em jogos educacionais, NPCs podem interpretar figuras históricas e cientistas, conduzindo ensino interativo com estudantes. Em escritórios virtuais, NPCs podem interpretar colegas e mentores, fornecendo ajuda e conselhos. NPCs também podem servir como companheiros, conduzindo comunicação emocional com usuários, aplicados em campos de saúde mental. Claro, a aplicação mais direta é adicionar NPCs de IA a jogos tradicionais para melhorar a experiência do jogador.

### 15.1.2 Visão Geral da Arquitetura Técnica

Cyber Town adota uma arquitetura de separação de **motor de jogo + serviço back-end**, dividida em quatro camadas, como mostrado na Figura 15.1.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-1.png" alt="" width="85%"/>
  <p>Figura 15.1 Arquitetura Técnica da Cyber Town</p>
</div>

A camada front-end usa o motor de jogo Godot 4.5, responsável pela renderização do jogo, controle do jogador, exibição de NPCs e interface de diálogo. Godot é um motor de jogo 2D/3D de código aberto, muito adequado para desenvolver rapidamente jogos estilo pixel. A camada back-end usa o framework FastAPI, responsável pelo roteamento de API, gerenciamento de estado de NPCs, processamento de diálogos e registro. FastAPI é um framework web Python moderno com excelente desempenho e fácil desenvolvimento. A camada de agente usa nosso próprio framework HelloAgents, responsável pela inteligência dos NPCs, gerenciamento de memória e cálculo de afeição. Cada NPC é uma instância SimpleAgent com memória e estado independentes. A camada de serviços externos fornece capacidades de LLM, armazenamento vetorial e persistência de dados, incluindo API de LLM, banco de dados vetorial Qdrant e banco de dados relacional SQLite.

O processo de fluxo de dados é mostrado na Figura 15.2:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-2.png" alt="" width="85%"/>
  <p>Figura 15.2 Processo de Fluxo de Dados</p>
</div>

Os jogadores pressionam a tecla E no Godot para interagir com NPCs, e o Godot envia solicitações de diálogo ao back-end FastAPI via API HTTP. O back-end chama o SimpleAgent do HelloAgents para processar o diálogo, o Agente recupera histórico relevante do sistema de memória e, em seguida, chama o LLM para gerar uma resposta. O back-end atualiza o estado do NPC e a afeição, registra logs no console e arquivo e, finalmente, retorna a resposta ao front-end Godot. O Godot exibe a resposta do NPC e atualiza a interface, completando um loop de interação completo.

A estrutura do projeto é a seguinte, facilitando a localização do código-fonte:

```
Helloagents-AI-Town/
├── helloagents-ai-town/           # Projeto de jogo Godot
│   ├── project.godot              # Configuração do projeto Godot
│   ├── scenes/                    # Cenas do jogo
│   │   ├── main.tscn              # Cena principal (escritório)
│   │   ├── player.tscn            # Personagem do jogador
│   │   ├── npc.tscn               # Personagem NPC
│   │   └── dialogue_ui.tscn       # Interface de diálogo
│   ├── scripts/                   # Scripts GDScript
│   │   ├── main.gd                # Lógica da cena principal
│   │   ├── player.gd              # Controle do jogador
│   │   ├── npc.gd                 # Comportamento do NPC
│   │   ├── dialogue_ui.gd         # Lógica da interface de diálogo
│   │   ├── api_client.gd          # Cliente de API
│   │   └── config.gd              # Gerenciamento de configuração
│   └── assets/                    # Recursos do jogo
│       ├── characters/            # Sprites de personagens
│       ├── interiors/             # Cenas internas
│       ├── ui/                    # Materiais de interface
│       └── audio/                 # Efeitos sonoros e música
│
└── backend/                       # Back-end Python
    ├── main.py                    # Programa principal FastAPI
    ├── agents.py                  # Sistema de Agente NPC
    ├── relationship_manager.py    # Gerenciamento de afeição
    ├── state_manager.py           # Gerenciamento de estado
    ├── logger.py                  # Sistema de registro
    ├── config.py                  # Gerenciamento de configuração
    ├── models.py                  # Modelos de dados
    ├── requirements.txt           # Dependências Python
    └── .env.example               # Exemplo de variáveis de ambiente
```

O design detalhado da arquitetura e o fluxo de dados serão apresentados nas seções subsequentes.

### 15.1.3 Experiência Rápida: Execute o Projeto em 5 Minutos

Antes de mergulhar nos detalhes de implementação, vamos primeiro executar o projeto para ver o resultado final. Desta forma, você terá uma compreensão intuitiva de todo o sistema.

**Requisitos de Ambiente:**

- Godot 4.2 ou superior
- Python 3.10 ou superior
- Chave de API LLM (OpenAI, DeepSeek, Zhipu, etc.)

**Obter o Projeto:**

Você pode verificar `code/chapter15/Helloagents-AI-Town`, ou clonar o repositório completo hello-agents do GitHub.

**Iniciar o Back-End:**

```bash
# 1. Entrar no diretório backend
cd Helloagents-AI-Town/backend

# 2. Instalar dependências
pip install -r requirements.txt

# 3. Configurar variáveis de ambiente
cp .env.example .env
# Editar arquivo .env, preencher sua chave de API

# 4. Iniciar serviço back-end
python main.py
```

Após inicialização bem-sucedida, você verá a seguinte saída:

```
============================================================
🎮 Serviço back-end Cyber Town iniciando...
============================================================
✅ Todos os serviços iniciados!
📡 Endereço da API: http://0.0.0.0:8000
📚 Documentação da API: http://0.0.0.0:8000/docs
============================================================
```

**Iniciar Godot:**

A instalação do Godot é muito simples. O Windows fornece um arquivo `.exe` direto, e o Mac também fornece um arquivo `.dmg`. Você pode baixar diretamente do site oficial ([Windows](https://godotengine.org/download/windows/) / [Mac](https://godotengine.org/download/macos/))

Abra o motor Godot, clique no botão "Import", navegue até `Helloagents-AI-Town/helloagents-ai-town/scenes/main.tscn` e clique em "Import and Edit". Depois que o Godot importar os recursos, pressione `F5` ou clique no botão "Run" para iniciar o jogo.

**Experimente as Funcionalidades Principais:**

Depois que o jogo iniciar, você verá uma cena de escritório Datawhale estilo pixel, como mostrado na Figura 15.3.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-3.png" alt="" width="85%"/>
  <p>Figura 15.3 Cena de Jogo Cyber Town</p>
</div>

Use as teclas WASD para mover o personagem do jogador. Quando você anda perto de um NPC, a tela exibirá um prompt "Pressione E para interagir". Depois de pressionar a tecla E, uma caixa de diálogo aparecerá, e você pode digitar qualquer coisa que queira dizer, como mostrado na Figura 15.4.

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-4.png" alt="" width="85%"/>
  <p>Figura 15.4 Interface de Diálogo com NPC</p>
</div>

Os NPCs responderão com base em suas configurações de função (engenheiro Python, gerente de produto, designer de UI) e seu histórico de interações. À medida que a conversa progride, a afeição do NPC em relação a você aumentará gradualmente, de "estranho" a "familiar", depois para "amigável", "íntimo" e até "amigo próximo".

**O sistema de afeição é implementado no back-end**. Cada conversa ajusta o valor de afeição com base no conteúdo da mensagem do jogador e análise de sentimento. Embora o valor de afeição não seja exibido diretamente na interface do jogo front-end, todas as mudanças de afeição são registradas em detalhes nos logs do back-end. Você pode visualizar as mudanças de afeição para cada conversa no arquivo `backend/logs/dialogue_YYYY-MM-DD.log`. O arquivo de log registra informações detalhadas para cada conversa, incluindo: valor de afeição atual, memórias relevantes recuperadas, resposta do NPC, quantidade de mudança de afeição (+2.0, +3.0, etc.), motivo da mudança (saudação amigável, comunicação normal, etc.) e resultados de análise de sentimento (positivo, neutro, etc.). Este design permite aos desenvolvedores rastrear claramente o desenvolvimento do relacionamento entre NPCs e jogadores, e também fornece uma base de dados para adicionar interface de afeição ao front-end posteriormente.

Todas as conversas são registradas nos arquivos de log do back-end. Você pode visualizá-las em tempo real com o seguinte comando:

```bash
# No diretório backend
python view_logs.py
```

Esta experiência simples demonstra as funcionalidades principais da AI Town. A seguir, vamos mergulhar em como implementar essas funcionalidades.

## 15.2 Sistema de Agente NPC

### 15.2.1 SimpleAgent Baseado em HelloAgents

Na Cyber Town, cada NPC é um agente independente. Usamos o SimpleAgent do framework HelloAgents para implementar a inteligência do NPC. SimpleAgent é uma implementação de agente leve que encapsula funções principais como chamadas de LLM, gerenciamento de mensagens e chamadas de ferramentas.

Recorde o SimpleAgent que aprendemos no Capítulo 7. Seu núcleo é um loop de diálogo simples: receber mensagem do usuário, chamar LLM para gerar resposta, retornar resultado. Na Cyber Town, precisamos criar uma instância SimpleAgent para cada NPC e configurar prompts de sistema únicos para eles, dando a cada NPC personalidades e configurações de função diferentes.

Vamos ver como criar um Agente NPC. Primeiro, precisamos definir as informações básicas do NPC, incluindo ID, nome, profissão e personalidade. Em seguida, construímos prompts de sistema com base nessas informações, permitindo que o LLM interprete o papel deste NPC. Finalmente, criamos uma instância SimpleAgent e configuramos o sistema de memória.

```python
from hello_agents import SimpleAgent, HelloAgentsLLM
from hello_agents.memory import MemoryManager, WorkingMemory, EpisodicMemory

def create_npc_agent(npc_id: str, name: str, role: str, personality: str):
    """Criar Agente NPC"""
    # Construir prompt de sistema
    system_prompt = f"""Você é {name}, um {role}.
Suas características de personalidade: {personality}

Você trabalha no escritório Datawhale, trabalhando com colegas para promover o desenvolvimento da comunidade de código aberto.
Por favor, tenha conversas naturais com jogadores com base em sua função e personalidade.
Lembre-se de suas conversas anteriores para manter a coerência do diálogo.
"""

    # Criar instância LLM
    llm = HelloAgentsLLM()

    # Criar gerenciador de memória
    memory_manager = MemoryManager(
        working_memory=WorkingMemory(capacity=10, ttl_minutes=120),
        episodic_memory=EpisodicMemory(
            db_path=f"memory_data/{npc_id}_episodic.db",
            collection_name=f"{npc_id}_memories"
        )
    )

    # Criar Agente
    agent = SimpleAgent(
        name=name,
        llm=llm,
        system_prompt=system_prompt,
        memory_manager=memory_manager
    )

    return agent
```

Este código demonstra como criar um Agente NPC. O prompt de sistema define a identidade e personalidade do NPC, e o gerenciador de memória permite que o NPC lembre do histórico de conversas com jogadores. WorkingMemory é memória de curto prazo com capacidade de 10 mensagens e tempo de retenção de 120 minutos. EpisodicMemory é memória de longo prazo, usando banco de dados SQLite e banco de dados vetorial Qdrant para armazenamento, e pode recuperar conversas históricas relevantes.

O fluxo de trabalho do Agente NPC é mostrado na Figura 15.5:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-5.png" alt="" width="85%"/>
  <p>Figura 15.5 Fluxo de Trabalho do Agente NPC</p>
</div>

### 15.2.2 Configurações de Função dos NPCs e Design de Prompt

Um bom NPC precisa de personalidade e configurações de função distintas. Na Cyber Town, projetamos três NPCs representando diferentes profissões e personalidades.

**Zhang San - Engenheiro Python**

Zhang San é um engenheiro Python sênior responsável pelo desenvolvimento principal do framework HelloAgents. Ele tem uma personalidade rigorosa, fala diretamente e gosta de usar termos técnicos. Ele tem altos requisitos para qualidade de código e frequentemente compartilha dicas de programação e melhores práticas.

```python
npc_zhang = {
    "npc_id": "zhang_san",
    "name": "Zhang San",
    "role": "Engenheiro Python",
    "personality": "Rigoroso, profissional, gosta de compartilhar conhecimento técnico. Fala diretamente, foca em qualidade de código."
}
```

**Li Si - Gerente de Produto**

Li Si é um gerente de produto experiente responsável pelo planejamento de produto e design de experiência do usuário do framework HelloAgents. Ele tem uma personalidade extrovertida, é bom em comunicação e sempre pode pensar da perspectiva do usuário. Ele gosta de discutir design de produto e necessidades do usuário, e frequentemente pergunta "por quê".

```python
npc_li = {
    "npc_id": "li_si",
    "name": "Li Si",
    "role": "Gerente de Produto",
    "personality": "Extrovertido, bom em comunicação, foca em experiência do usuário. Gosta de pensar da perspectiva do usuário."
}
```

**Wang Wu - Designer de UI**

Wang Wu é um designer de UI criativo responsável pelo design de interface e apresentação visual do framework HelloAgents. Ele tem uma personalidade gentil, estética única e percepção aguçada de cor e layout. Ele gosta de discutir conceitos de design e estética, e frequentemente compartilha inspiração de design.

```python
npc_wang = {
    "npc_id": "wang_wu",
    "name": "Wang Wu",
    "role": "Designer de UI",
    "personality": "Gentil, criativo, estética única. Foca em apresentação visual e experiência do usuário."
}
```

Esses três NPCs têm características distintas. Os jogadores podem escolher interagir com diferentes NPCs com base em seus interesses. Zhang San pode ensinar habilidades de programação, Li Si pode discutir design de produto com você, e Wang Wu pode compartilhar inspiração de design.

### 15.2.3 Integração do Sistema de Memória

O sistema de memória é a chave para a inteligência do NPC. Um NPC que pode lembrar de conversas passadas fará os jogadores se sentirem mais realistas e interessantes. Usamos `WorkingMemory` e `EpisodicMemory` do HelloAgents para construir memória de curto e longo prazo.

A memória de curto prazo armazena conteúdo de conversa recente com capacidade limitada e limpeza automática ao longo do tempo. Seu papel é manter a coerência do diálogo, permitindo que os NPCs entendam o contexto. Por exemplo, quando um jogador diz "De que cor é?", o NPC precisa encontrar na memória de curto prazo a que "isso" se refere.

A memória de longo prazo armazena todo o histórico de conversas, usando bancos de dados vetoriais para recuperação semântica. Quando um jogador menciona um tópico, o NPC pode recuperar conversas históricas relevantes da memória de longo prazo, relembrando conteúdo discutido anteriormente. Por exemplo, quando um jogador diz "Você se lembra do projeto que discutimos da última vez?", o NPC pode encontrar registros de conversas relevantes da memória de longo prazo.

A arquitetura do sistema de memória é mostrada na Figura 15.6:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-6.png" alt="" width="85%"/>
  <p>Figura 15.6 Arquitetura do Sistema de Memória</p>
</div>

No uso real, o Agente primeiro obtém conversas recentes da memória de curto prazo, depois recupera conversas históricas relevantes da memória de longo prazo, envia essas informações juntas ao LLM e gera respostas mais precisas e personalizadas.

```python
# Fluxo de processamento de diálogo do Agente
def process_dialogue(agent, player_message):
    # 1. Obter conversas recentes da memória de curto prazo
    recent_messages = agent.memory_manager.working_memory.get_recent_messages(5)

    # 2. Recuperar histórico relevante da memória de longo prazo
    relevant_memories = agent.memory_manager.episodic_memory.search(
        query=player_message,
        top_k=3
    )

    # 3. Construir contexto
    context = {
        "recent": recent_messages,
        "relevant": relevant_memories
    }

    # 4. Chamar Agente para gerar resposta
    reply = agent.run(player_message, context=context)

    # 5. Salvar no sistema de memória
    agent.memory_manager.add_interaction(player_message, reply)

    return reply
```

Este processo garante que os NPCs possam lembrar do histórico de interações com jogadores e refletir isso nas conversas.

### 15.2.4 Geração de Diálogo em Lote: Modo de Carga Leve

Na operação real, um problema foi rapidamente descoberto: quando múltiplos jogadores conversam simultaneamente com diferentes NPCs, o back-end precisa processar simultaneamente múltiplas solicitações de LLM. Cada solicitação precisa chamar a API, o que não apenas aumenta os custos, mas também pode causar falhas ou atrasos na solicitação devido a limites de simultaneidade.

Para resolver este problema, projetamos um **sistema de geração de diálogo em lote**. A ideia principal é: mesclar múltiplas solicitações de diálogo de NPC em uma chamada de LLM, deixando o LLM gerar todas as respostas de NPC de uma vez. Isto é como "pratos pré-fabricados" de um restaurante - preparados em lote com antecedência, usados diretamente quando necessário, reduzindo muito os custos e a latência.

O fluxo de trabalho da geração em lote é mostrado na Figura 15.7:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-7.png" alt="" width="85%"/>
  <p>Figura 15.7 Geração em Lote vs Modo Tradicional</p>
</div>

A implementação do gerador em lote é muito inteligente. Construímos um prompt especial exigindo que o LLM gere todos os diálogos de NPC de uma vez e os retorne em formato JSON. Desta forma, uma chamada de API pode obter todas as respostas de NPC, reduzindo custos para 1/3 do original e reduzindo significativamente a latência.

```python
class NPCBatchGenerator:
    """Gerador para geração em lote de diálogos de NPC"""

    def __init__(self):
        self.llm = HelloAgentsLLM()
        self.npc_configs = NPC_ROLES  # Todas as configurações de NPC

    def generate_batch_dialogues(self, context: Optional[str] = None) -> Dict[str, str]:
        """Gerar diálogos em lote para todos os NPCs

        Args:
            context: Contexto da cena (como "horário de trabalho da manhã", "hora do almoço", etc.)

        Returns:
            Dict[str, str]: Mapeamento de nomes de NPC para conteúdo de diálogo
        """
        # Construir prompt de geração em lote
        prompt = self._build_batch_prompt(context)

        # Uma chamada LLM gera todos os diálogos
        response = self.llm.invoke([
            {"role": "system", "content": "Você é um gerador de diálogo de NPC de jogo, habilidoso em criar diálogos de escritório naturais e realistas."},
            {"role": "user", "content": prompt}
        ])

        # Analisar resposta JSON
        dialogues = json.loads(response)
        # Formato de retorno: {"Zhang San": "...", "Li Si": "...", "Wang Wu": "..."}

        return dialogues

    def _build_batch_prompt(self, context: Optional[str] = None) -> str:
        """Construir prompt de geração em lote"""
        # Inferir cena automaticamente com base no tempo
        if context is None:
            context = self._get_current_context()

        # Construir descrições de NPC
        npc_descriptions = []
        for name, cfg in self.npc_configs.items():
            desc = f"- {name}({cfg['title']}): {cfg['activity']} em {cfg['location']}, personalidade {cfg['personality']}"
            npc_descriptions.append(desc)

        npc_desc_text = "\n".join(npc_descriptions)

        prompt = f"""Por favor, gere diálogos ou descrições de comportamento atuais para 3 NPCs no escritório Datawhale.

【Cena】{context}

【Informações do NPC】
{npc_desc_text}

【Requisitos de Geração】
1. Gere 1 frase para cada NPC (20-40 caracteres)
2. O conteúdo deve corresponder às configurações de função, atividades atuais e atmosfera da cena
3. Pode ser solilóquio, descrição de status de trabalho ou pensamentos simples
4. Deve ser natural e realista, como colegas de escritório reais
5. **Deve retornar estritamente em formato JSON**

【Formato de Saída】(siga estritamente)
{{"Zhang San": "...", "Li Si": "...", "Wang Wu": "..."}}

【Exemplo de Saída】
{{"Zhang San": "Este bug é realmente irritante, depurando há duas horas...", "Li Si": "Hmm, a prioridade desta funcionalidade precisa ser reavaliada.", "Wang Wu": "A arte do latte neste café é realmente bonita, a inspiração está vindo!"}}

Por favor, gere (retorne apenas JSON, sem outro conteúdo):
"""
        return prompt
```

A chave deste design é a construção do prompt. Exigimos explicitamente que o LLM retorne formato JSON e fornecemos saída de exemplo. O LLM gerará respostas estritamente de acordo com este formato, e só precisamos analisar o JSON para obter todos os diálogos de NPC.

A geração em lote tem um benefício adicional: todos os diálogos de NPC são gerados no mesmo contexto, então eles têm um certo grau de correlação. Por exemplo, se Zhang San está depurando um bug, Li Si pode mencionar ajudar a dar uma olhada; se Wang Wu está projetando uma interface, Zhang San pode dizer que verificará o rascunho do design mais tarde. Isto torna a atmosfera de todo o escritório mais realista e coerente.

Claro, a geração em lote também tem algumas limitações. É mais adequada para gerar "diálogos de fundo" de NPC ou "solilóquios" em vez de interações diretas com jogadores. Para conversas iniciadas por jogadores, ainda usamos Agentes individuais para processá-las para garantir respostas personalizadas e precisas. A geração em lote é usada principalmente nos seguintes cenários:

1. **Diálogos de fundo de NPC**: O que os NPCs estão fazendo e dizendo quando os jogadores entram na cena
2. **Atualizações programadas**: Atualizar status e diálogos de NPC em intervalos regulares
3. **Atmosfera da cena**: Gerar diferentes diálogos com base no tempo (manhã, meio-dia, noite)
4. **Redução de custos**: Usar geração em lote para reduzir frequência de chamadas de API em cenários de alta simultaneidade

**Modo Híbrido: Geração em Lote + Resposta Instantânea**

Na implementação real, adotamos um modo híbrido que combina geração em lote e resposta instantânea. Este design é muito inteligente, garantindo tanto eficiência quanto qualidade de interação.

Especificamente, o sistema executa periodicamente geração em lote em segundo plano, gerando "diálogos de fundo" para todos os NPCs na cena atual. Esses diálogos são armazenados em cache, e quando os jogadores se aproximam dos NPCs mas ainda não iniciaram interação, os NPCs exibem esses diálogos de fundo, como "Depurando código...", "Lendo documentação de produto...", etc. Isto faz os NPCs parecerem "vivos" em vez de modelos estáticos.

No entanto, quando um jogador pressiona a tecla E para iniciar interação, o sistema imediatamente muda para o modo de resposta instantânea. Neste ponto, o back-end chama o Agente dedicado do NPC, gerando respostas personalizadas com base na mensagem específica do jogador, memória histórica e nível de afeição. Este processo é em tempo real, garantindo que as respostas do NPC sejam altamente relevantes para a entrada do jogador.

```python
# Implementação do modo híbrido em main.py
@app.post("/dialogue")
async def dialogue(request: DialogueRequest):
    """Lidar com diálogo jogador-NPC (modo de resposta instantânea)"""
    npc_id = request.npc_id
    player_message = request.player_message
    player_name = request.player_name

    # Obter Agente NPC (cada NPC tem um Agente independente)
    agent = npc_agents.get(npc_id)
    if not agent:
        raise HTTPException(status_code=404, detail="NPC não encontrado")

    # Gerar instantaneamente resposta personalizada
    # Aqui não usamos geração em lote, mas chamamos o método run do Agente
    reply = agent.run(player_message)

    # Atualizar afeição
    affinity_change = relationship_manager.update_affinity(
        npc_id, player_name, player_message, reply
    )

    return {
        "npc_reply": reply,
        "affinity_score": affinity_change["score"],
        "affinity_level": affinity_change["level"]
    }

# Tarefa em segundo plano: gerar periodicamente diálogos de fundo em lote
async def background_dialogue_update():
    """Tarefa em segundo plano: atualizar diálogos de fundo de NPC a cada 5 minutos"""
    while True:
        try:
            # Usar gerador em lote para gerar diálogos de fundo para todos os NPCs
            batch_generator = get_batch_generator()
            dialogues = batch_generator.generate_batch_dialogues()

            # Atualizar para gerenciador de estado
            for npc_name, dialogue in dialogues.items():
                state_manager.update_npc_background_dialogue(npc_name, dialogue)

            print(f"✅ Atualização de diálogo de fundo completa: {len(dialogues)} NPCs")
        except Exception as e:
            print(f"❌ Falha na atualização de diálogo de fundo: {e}")

        # Esperar 5 minutos
        await asyncio.sleep(300)
```

As vantagens deste modo híbrido são muito óbvias:

1. **Redução de custos**: Diálogos de fundo usam geração em lote, uma chamada gera todos os diálogos de NPC, baixo custo
2. **Garantia de qualidade**: Interações de jogadores usam resposta instantânea, cada resposta é personalizada, alta qualidade
3. **Experiência aprimorada**: NPCs sempre têm "diálogos de fundo", parecendo muito vivos; interações de jogadores têm respostas precisas, boa experiência
4. **Ajuste flexível**: Pode ajustar dinamicamente a frequência de geração em lote com base na carga do servidor

Através da combinação de geração em lote e resposta instantânea, implementamos um sistema de NPC que é tanto eficiente quanto inteligente. Em circunstâncias normais, os jogadores não sentem nenhuma diferença, mas os custos e o desempenho do back-end são significativamente otimizados. Esta abordagem de design também pode ser aplicada a outros cenários que requerem um grande número de chamadas de IA.

## 15.3 Design do Sistema de Afeição

### 15.3.1 Classificação de Níveis de Afeição

Na Cyber Town, as atitudes dos NPCs em relação aos jogadores mudam com as interações. Projetamos um sistema de afeição de cinco níveis, de estranho a amigo próximo, com cada nível tendo diferentes faixas de pontuação e desempenhos comportamentais correspondentes.

A ideia central do sistema de afeição é: ao quantificar o relacionamento entre NPCs e jogadores, tornar as respostas dos NPCs mais realistas e em camadas. Quando os jogadores entram pela primeira vez no jogo, todos os NPCs têm uma atitude de estranho em relação aos jogadores, com respostas sendo educadas mas distantes. À medida que as conversas progridem, se os jogadores se comportam de forma amigável, a afeição do NPC aumentará gradualmente, e as respostas se tornarão mais cordiais e detalhadas.

Dividimos a afeição em cinco níveis, cada um correspondendo a uma faixa de pontuação, como mostrado na Figura 15.8:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-8.png" alt="" width="85%"/>
  <p>Figura 15.8 Classificação de Níveis de Afeição</p>
</div>

- **Estranho (0-20 pontos)**: O NPC acabou de conhecer o jogador, a atitude é educada mas mantém distância. As respostas são breves, não compartilharão ativamente informações pessoais.

- **Familiar (21-40 pontos)**: O NPC começa a lembrar do jogador, disposto a ter trocas simples. As respostas se tornam mais naturais, ocasionalmente compartilhando algumas informações relacionadas ao trabalho.

- **Amigável (41-60 pontos)**: O NPC trata o jogador como amigo, disposto a compartilhar mais informações. As respostas são mais detalhadas, perguntará ativamente sobre a situação do jogador.

- **Íntimo (61-80 pontos)**: O NPC confia muito no jogador, disposto a compartilhar tópicos privados. As respostas são cheias de entusiasmo, fornecerá ajuda e conselhos ao jogador.

- **Amigo Próximo (81-100 pontos)**: O NPC trata o jogador como o melhor amigo, fala sobre tudo. As respostas são muito cordiais, compartilhará pensamentos e sentimentos internos.

Este design permite aos jogadores sentirem claramente a mudança em seu relacionamento com os NPCs, e também fornece uma base para jogabilidade subsequente. Por exemplo, somente após atingir um certo nível de afeição os NPCs compartilharão certas informações especiais ou fornecerão tarefas especiais.

### 15.3.2 Lógica de Cálculo de Afeição

O cálculo de afeição precisa considerar múltiplos fatores. Não podemos simplesmente adicionar uma pontuação fixa para cada conversa, o que tornaria o sistema mecânico e irreal. Um bom sistema de afeição deve ser capaz de identificar a atitude do jogador e ajustar dinamicamente as pontuações com base no conteúdo da conversa.

Na Cyber Town, usamos LLM para analisar o conteúdo da conversa, julgando se a atitude do jogador é amigável, neutra ou não amigável. Em seguida, ajustamos a pontuação de afeição com base no resultado do julgamento. Este processo é automático, os jogadores não precisam escolher deliberadamente opções, tornando as interações mais naturais.

O processo de cálculo de afeição é mostrado na Figura 15.9:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-9.png" alt="" width="85%"/>
  <p>Figura 15.9 Processo de Cálculo de Afeição</p>
</div>

```python
class RelationshipManager:
    """Gerenciador de afeição"""

    def __init__(self):
        self.affinity_data = {}  # Armazenar dados de afeição
        self.llm = HelloAgentsLLM()  # Para analisar conversas

    def analyze_sentiment(self, player_message: str, npc_reply: str) -> int:
        """Analisar sentimento da conversa, retornar valor de mudança de afeição"""
        prompt = f"""Analise a atitude do jogador na seguinte conversa:
Jogador: {player_message}
NPC: {npc_reply}

Por favor, julgue se a atitude do jogador é:
1. Amigável (+5 pontos): Educado, entusiasta, expressando agradecimento ou concordância
2. Neutra (+2 pontos): Pergunta ou declaração normal
3. Não amigável (-3 pontos): Rude, indiferente, crítico ou negativo

Retorne apenas o número, sem outro conteúdo."""

        response = self.llm.think([{"role": "user", "content": prompt}])
        try:
            score_change = int(response.strip())
            return max(-3, min(5, score_change))  # Limitar entre -3 e 5
        except:
            return 2  # Padrão neutro

    def update_affinity(self, npc_id: str, player_name: str,
                       player_message: str, npc_reply: str) -> dict:
        """Atualizar afeição"""
        key = f"{npc_id}_{player_name}"

        # Obter afeição atual
        if key not in self.affinity_data:
            self.affinity_data[key] = {
                "score": 0,
                "level": "Estranho",
                "interaction_count": 0
            }

        # Analisar sentimento da conversa
        score_change = self.analyze_sentiment(player_message, npc_reply)

        # Atualizar pontuação
        current_score = self.affinity_data[key]["score"]
        new_score = max(0, min(100, current_score + score_change))

        # Atualizar nível
        level = self.get_affinity_level(new_score)

        # Atualizar dados
        self.affinity_data[key].update({
            "score": new_score,
            "level": level,
            "interaction_count": self.affinity_data[key]["interaction_count"] + 1
        })

        return self.affinity_data[key]

    def get_affinity_level(self, score: int) -> str:
        """Obter nível de afeição com base na pontuação"""
        if score <= 20:
            return "Estranho"
        elif score <= 40:
            return "Familiar"
        elif score <= 60:
            return "Amigável"
        elif score <= 80:
            return "Íntimo"
        else:
            return "Amigo Próximo"
```

Esta implementação usa LLM para analisar o conteúdo da conversa, julgando automaticamente a atitude do jogador e ajustando a afeição. Este design torna o sistema de afeição mais inteligente e natural, os jogadores não precisam deliberadamente agradar NPCs, apenas se comunicam normalmente.

### 15.3.3 Afeição Afeta o Diálogo

A afeição não é apenas um número, ela deve realmente afetar o comportamento do NPC. Na Cyber Town, modificamos os prompts de sistema do NPC para deixar os NPCs ajustarem estilos de resposta com base nos níveis de afeição atuais.

Quando a afeição está baixa, os NPCs mantêm uma atitude educada mas distante. Quando a afeição aumenta, os NPCs se tornam mais entusiastas e faladores. Esta mudança é alcançada ajustando dinamicamente os prompts de sistema.

```python
def create_npc_agent_with_affinity(npc_id: str, name: str, role: str,
                                   personality: str, affinity_level: str):
    """Criar Agente NPC com afeição"""

    # Ajustar prompts com base no nível de afeição
    affinity_prompts = {
        "Estranho": "Você acabou de conhecer este jogador, seja educado mas não excessivamente entusiasta. Mantenha respostas breves e profissionais.",
        "Familiar": "Você já conhece este jogador, pode ter trocas normais. As respostas devem ser naturais e amigáveis.",
        "Amigável": "Você trata este jogador como amigo, disposto a compartilhar mais informações. As respostas devem ser detalhadas e entusiastas.",
        "Íntimo": "Você confia muito neste jogador, pode compartilhar tópicos privados. As respostas devem ser cheias de cuidado.",
        "Amigo Próximo": "Você trata este jogador como seu melhor amigo, fala sobre tudo. As respostas devem ser cordiais e sinceras."
    }

    system_prompt = f"""Você é {name}, um {role}.
Suas características de personalidade: {personality}

Relacionamento atual com o jogador: {affinity_level}
{affinity_prompts.get(affinity_level, affinity_prompts["Estranho"])}

Você trabalha no escritório Datawhale, trabalhando com colegas para promover o desenvolvimento da comunidade de código aberto.
Por favor, responda naturalmente com base em sua função, personalidade e relacionamento com o jogador.
"""

    # Criar Agente
    llm = HelloAgentsLLM()
    agent = SimpleAgent(
        name=name,
        llm=llm,
        system_prompt=system_prompt
    )

    return agent
```

Este design faz o comportamento do NPC mudar dinamicamente com a afeição. Os jogadores podem sentir claramente que à medida que as interações aumentam, as atitudes dos NPCs em relação a eles estão mudando gradualmente, melhorando muito a imersão e a diversão do jogo.

## 15.4 Implementação do Serviço Back-End

### 15.4.1 Estrutura da Aplicação FastAPI

O back-end da Cyber Town é construído usando o framework FastAPI, responsável por lidar com solicitações do front-end Godot, chamar Agentes NPC do HelloAgents, gerenciar estado de NPC e afeição, e registrar logs. Uma estrutura de aplicação clara torna o código mais fácil de manter e estender.

Nossa aplicação FastAPI adota um design modular, separando diferentes funções em diferentes arquivos, como mostrado na Figura 15.10:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-10.png" alt="" width="85%"/>
  <p>Figura 15.10 Estrutura da Aplicação Back-End</p>
</div>

Vamos começar com `main.py`, o arquivo de entrada para a aplicação FastAPI:

```python
from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field
from typing import Optional
import uvicorn

from agents import NPCAgentManager
from relationship_manager import RelationshipManager
from state_manager import StateManager
from logger import DialogueLogger
from config import settings

# Criar aplicação FastAPI
app = FastAPI(
    title="Serviço Back-End Cyber Town",
    description="Sistema de diálogo AI NPC baseado em HelloAgents",
    version="1.0.0"
)

# Configurar CORS, permitir acesso do front-end Godot
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Ambiente de produção deve limitar domínios específicos
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Inicializar gerenciadores
agent_manager = NPCAgentManager()
relationship_manager = RelationshipManager()
state_manager = StateManager()
dialogue_logger = DialogueLogger()

@app.on_event("startup")
async def startup_event():
    """Inicialização na inicialização da aplicação"""
    print("=" * 60)
    print("🎮 Serviço back-end Cyber Town iniciando...")
    print("=" * 60)

    # Inicializar Agentes NPC
    agent_manager.initialize_npcs()
    print("✅ Agentes NPC inicializados")

    # Inicializar gerenciador de estado
    state_manager.initialize_npcs()
    print("✅ Gerenciador de estado inicializado")

@app.get("/")
async def root():
    """Verificação de saúde"""
    return {
        "status": "running",
        "message": "Serviço back-end Cyber Town está executando",
        "version": "1.0.0",
        "npcs": state_manager.get_npc_count()
    }

if __name__ == "__main__":
    uvicorn.run(
        app,
        host=settings.HOST,
        port=settings.PORT,
        log_level="info"
    )
```

Este arquivo de programa principal define a estrutura básica da aplicação FastAPI, configura middleware CORS para permitir solicitações de origem cruzada e inicializa gerenciadores na inicialização. A seguir, implementaremos rotas de API específicas.

### 15.4.2 Design de Rota de API

O back-end da Cyber Town precisa fornecer vários endpoints de API principais para lidar com solicitações do front-end Godot. Adicionamos essas rotas a `main.py`.

**Obter Status do NPC**

Esta API retorna o status atual de todos os NPCs, incluindo localização, se está ocupado, etc.:

```python
from models import NPCStatusResponse

@app.get("/npcs/status", response_model=NPCStatusResponse)
async def get_npc_status():
    """Obter status de todos os NPCs"""
    npcs = state_manager.get_all_npc_states()
    return {"npcs": npcs}

@app.get("/npcs/{npc_id}/status")
async def get_single_npc_status(npc_id: str):
    """Obter status de um único NPC"""
    npc = state_manager.get_npc_state(npc_id)
    if not npc:
        raise HTTPException(status_code=404, detail=f"NPC {npc_id} não existe")
    return npc
```

**Interface de Diálogo**

Esta é a API mais importante, lidando com conversas jogador-NPC:

```python
from models import DialogueRequest, DialogueResponse

@app.post("/dialogue", response_model=DialogueResponse)
async def dialogue(request: DialogueRequest):
    """Lidar com diálogo jogador-NPC"""
    # 1. Verificar se NPC existe
    if not agent_manager.has_npc(request.npc_id):
        raise HTTPException(status_code=404, detail=f"NPC {request.npc_id} não existe")

    # 2. Verificar se NPC está ocupado
    if state_manager.is_npc_busy(request.npc_id):
        raise HTTPException(status_code=409, detail=f"NPC {request.npc_id} está conversando com outro jogador")

    # 3. Marcar NPC como ocupado
    state_manager.set_npc_busy(request.npc_id, True)

    try:
        # 4. Obter afeição atual
        affinity_info = relationship_manager.get_affinity(
            request.npc_id,
            request.player_name
        )

        # 5. Chamar Agente para gerar resposta
        agent = agent_manager.get_agent(request.npc_id, affinity_info["level"])
        reply = agent.run(request.player_message)

        # 6. Atualizar afeição
        new_affinity = relationship_manager.update_affinity(
            request.npc_id,
            request.player_name,
            request.player_message,
            reply
        )

        # 7. Registrar log
        dialogue_logger.log_dialogue(
            npc_id=request.npc_id,
            player_name=request.player_name,
            player_message=request.player_message,
            npc_reply=reply,
            affinity_info=new_affinity
        )

        # 8. Retornar resposta
        return DialogueResponse(
            npc_reply=reply,
            affinity_level=new_affinity["level"],
            affinity_score=new_affinity["score"]
        )

    except Exception as e:
        dialogue_logger.log_error(f"Falha no processamento do diálogo: {str(e)}")
        raise HTTPException(status_code=500, detail=f"Falha no processamento do diálogo: {str(e)}")

    finally:
        # 9. Liberar status do NPC
        state_manager.set_npc_busy(request.npc_id, False)
```

**Consulta de Afeição**

Esta API permite consultar afeição jogador-NPC:

```python
from models import AffinityInfo

@app.get("/affinity/{npc_id}/{player_name}", response_model=AffinityInfo)
async def get_affinity(npc_id: str, player_name: str):
    """Obter afeição jogador-NPC"""
    if not agent_manager.has_npc(npc_id):
        raise HTTPException(status_code=404, detail=f"NPC {npc_id} não existe")

    affinity = relationship_manager.get_affinity(npc_id, player_name)
    return affinity
```

O fluxo de chamada de rota de API é mostrado na Figura 15.11:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-11.png" alt="" width="85%"/>
  <p>Figura 15.11 Fluxo de Chamada de API</p>
</div>

### 15.4.3 Gerenciamento de Estado e Sistema de Registro

**Gerenciador de Estado**

O gerenciador de estado é responsável por rastrear o estado atual de cada NPC, incluindo localização, se está ocupado, ação atual, etc. Isto é importante para prevenir problemas de simultaneidade, como evitar que um NPC converse com múltiplos jogadores simultaneamente.

```python
# state_manager.py
from typing import Dict, List, Optional
from datetime import datetime

class StateManager:
    """Gerenciador de estado NPC"""

    def __init__(self):
        self.npc_states: Dict[str, dict] = {}

    def initialize_npcs(self):
        """Inicializar estados de NPC"""
        npcs = [
            {
                "npc_id": "zhang_san",
                "name": "Zhang San",
                "role": "Engenheiro Python",
                "position": {"x": 300, "y": 200}
            },
            {
                "npc_id": "li_si",
                "name": "Li Si",
                "role": "Gerente de Produto",
                "position": {"x": 500, "y": 200}
            },
            {
                "npc_id": "wang_wu",
                "name": "Wang Wu",
                "role": "Designer de UI",
                "position": {"x": 700, "y": 200}
            }
        ]

        for npc in npcs:
            self.npc_states[npc["npc_id"]] = {
                **npc,
                "is_busy": False,
                "current_action": "idle",
                "last_interaction": None
            }

    def get_npc_state(self, npc_id: str) -> Optional[dict]:
        """Obter estado do NPC"""
        return self.npc_states.get(npc_id)

    def get_all_npc_states(self) -> List[dict]:
        """Obter todos os estados de NPC"""
        return list(self.npc_states.values())

    def is_npc_busy(self, npc_id: str) -> bool:
        """Verificar se NPC está ocupado"""
        npc = self.npc_states.get(npc_id)
        return npc["is_busy"] if npc else False

    def set_npc_busy(self, npc_id: str, busy: bool):
        """Definir status ocupado do NPC"""
        if npc_id in self.npc_states:
            self.npc_states[npc_id]["is_busy"] = busy
            if busy:
                self.npc_states[npc_id]["last_interaction"] = datetime.now().isoformat()

    def get_npc_count(self) -> int:
        """Obter contagem de NPC"""
        return len(self.npc_states)
```

**Sistema de Registro**

O sistema de registro implementa saída dupla: console e arquivo. Isto facilita visualização em tempo real e salvamento de registros históricos.

```python
# logger.py
import logging
from datetime import datetime
from pathlib import Path

class DialogueLogger:
    """Registrador de diálogo"""

    def __init__(self, log_dir: str = "logs"):
        self.log_dir = Path(log_dir)
        self.log_dir.mkdir(exist_ok=True)

        # Criar nome de arquivo de log (por data)
        today = datetime.now().strftime("%Y-%m-%d")
        log_file = self.log_dir / f"dialogue_{today}.log"

        # Configurar registro
        self.logger = logging.getLogger("DialogueLogger")
        self.logger.setLevel(logging.INFO)

        # Manipulador de console
        console_handler = logging.StreamHandler()
        console_handler.setLevel(logging.INFO)
        console_formatter = logging.Formatter(
            '%(asctime)s - %(levelname)s - %(message)s',
            datefmt='%H:%M:%S'
        )
        console_handler.setFormatter(console_formatter)

        # Manipulador de arquivo
        file_handler = logging.FileHandler(log_file, encoding='utf-8')
        file_handler.setLevel(logging.INFO)
        file_formatter = logging.Formatter(
            '%(asctime)s - %(levelname)s - %(message)s',
            datefmt='%Y-%m-%d %H:%M:%S'
        )
        file_handler.setFormatter(file_formatter)

        # Adicionar manipuladores
        self.logger.addHandler(console_handler)
        self.logger.addHandler(file_handler)

    def log_dialogue(self, npc_id: str, player_name: str,
                    player_message: str, npc_reply: str,
                    affinity_info: dict):
        """Registrar diálogo"""
        log_message = f"""
{'='*60}
NPC: {npc_id}
Jogador: {player_name}
Mensagem do jogador: {player_message}
Resposta do NPC: {npc_reply}
Afeição: {affinity_info['level']} ({affinity_info['score']}/100)
Contagem de interações: {affinity_info['interaction_count']}
{'='*60}
"""
        self.logger.info(log_message)

    def log_error(self, error_message: str):
        """Registrar erro"""
        self.logger.error(error_message)
```

Este sistema de registro exibe conteúdo de diálogo em tempo real no console enquanto o salva em arquivos. Os logs de cada dia são salvos em arquivos separados para facilitar análise subsequente.

### 15.4.4 Compreendendo o Sistema de Cena do Godot

Antes de começar a construir cenas de jogo, precisamos primeiro entender os conceitos principais do Godot - Cena e Nó. Esta é a maior diferença entre Godot e outros motores de jogos, e também uma de suas características mais poderosas.

**O Que é um Nó?**

Nós são os blocos de construção mais básicos no Godot. Você pode pensar nos nós como blocos de Lego, cada nó tem uma função específica. Por exemplo, nós Sprite2D são usados para exibir imagens, nós AudioStreamPlayer são usados para reproduzir áudio, e nós CharacterBody2D são usados para lidar com movimento físico de personagens. Godot fornece centenas de diferentes tipos de nós, cada um focado em fazer uma coisa bem.

Nós podem formar relacionamentos pai-filho, formando uma estrutura em árvore. Nós pai podem afetar nós filho, por exemplo, mover um nó pai moverá simultaneamente todos os nós filho, ocultar um nó pai ocultará simultaneamente todos os nós filho. Esta relação hierárquica nos permite organizar e gerenciar facilmente objetos de jogo complexos.

**O Que é uma Cena?**

Uma cena é uma coleção de nós, salva em um arquivo .tscn. Você pode pensar em uma cena como um "prefab". Por exemplo, podemos criar uma cena de "jogador" contendo todos os nós relacionados, como sprites de personagens, corpos de colisão, efeitos sonoros, etc. Em seguida, usar esta cena múltiplas vezes no jogo, cada uso criará uma instância independente.

O poder das cenas está em sua reutilização e modularidade. Podemos instanciar uma cena dentro de outra cena, formando estruturas aninhadas. Por exemplo, a cena principal pode conter cenas de jogador, múltiplas cenas de NPC e cenas de interface. Modificar a cena de NPC afetará automaticamente todas as instâncias de NPC, simplificando muito o desenvolvimento e a manutenção.

**Um Exemplo Simples**

Vamos usar um exemplo simples para entender cenas e nós. Suponha que queremos criar uma cena de "jogador":

```
Player (CharacterBody2D)  ← Nó raiz, responsável por movimento físico
├─ AnimatedSprite2D       ← Nó filho, exibe animação de personagem
├─ CollisionShape2D       ← Nó filho, define forma de colisão
└─ Camera2D               ← Nó filho, câmera segue jogador
```

Esta cena contém 4 nós formando uma estrutura em árvore. CharacterBody2D é o nó raiz, os outros três são seus nós filho. Podemos adicionar scripts a cada nó para controlar seu comportamento, ou adicionar um script ao nó raiz para coordenar todos os nós filho.

Quando instanciamos esta cena Player na cena principal, Godot cria uma cópia de toda esta árvore de nós. Podemos criar múltiplas instâncias de jogador, cada instância é independente com sua própria posição, estado e comportamento.

**Vantagens da Instanciação de Cena**

Na Cyber Town, temos três NPCs: Zhang San, Li Si e Wang Wu. Sem usar o sistema de cena, precisaríamos criar nós, definir propriedades e escrever scripts para cada NPC separadamente, levando a muito trabalho repetitivo. Usando o sistema de cena, só precisamos criar uma cena genérica de NPC, depois instanciá-la três vezes, definindo diferentes nomes e informações de função através de parâmetros de script.

O benefício deste design é: se quisermos adicionar uma nova funcionalidade a todos os NPCs (como exibir balões de diálogo acima de suas cabeças), só precisamos modificar a cena de NPC, e todas as instâncias obterão automaticamente esta funcionalidade.

## 15.5 Construção de Cena de Jogo Godot

**Por Que Escolher Godot como Motor de Jogo?**

Entre muitos motores de jogos, escolhemos Godot 4.5 como motor front-end, principalmente com base nas seguintes considerações:

(1) **Godot tem vantagens naturais no desenvolvimento de jogos 2D**. Cyber Town é um jogo 2D estilo pixel de cima para baixo. O motor 2D do Godot é muito maduro, fornecendo tipos de nós projetados especificamente para jogos 2D como TileMap, AnimatedSprite2D, CharacterBody2D, etc. A eficiência de desenvolvimento é muito maior do que motores como Unity. O Sistema de Cena do Godot nos permite encapsular elementos como jogadores, NPCs e interface em cenas independentes, depois instanciá-las na cena principal. Este design baseado em componentes é muito adequado para nossas necessidades.

(2) **Godot é completamente de código aberto e gratuito**. Godot usa a licença MIT, sem taxas de royalty ou compartilhamento de receita, o que é muito amigável para projetos de ensino e projetos de código aberto. Você pode modificar livremente o código-fonte do motor e comercializar jogos sem se preocupar com problemas de licenciamento. Em contraste, embora Unity seja poderoso, ele introduziu uma política de taxa de tempo de execução em 2024, causando controvérsia generalizada na comunidade de desenvolvedores.

(3) **Godot tem um custo de aprendizado extremamente baixo**. Godot usa GDScript como sua principal linguagem de script, uma linguagem tipada dinamicamente semelhante a Python com sintaxe concisa e fácil de entender e uma curva de aprendizado muito suave. Para leitores já familiarizados com Python, aprender GDScript não tem praticamente nenhuma barreira - declarações de variáveis, definições de funções, fluxo de controle e outras sintaxes são altamente semelhantes a Python. Você pode até começar a escrever scripts de jogos dentro de poucas horas. A estrutura de árvore de nós do Godot também é muito intuitiva, você pode ver visualmente os relacionamentos hierárquicos da cena no editor, o que é muito amigável para iniciantes.

(4) **Godot se integra muito simplesmente com back-ends Python**. Godot tem um nó HTTPRequest integrado que pode facilmente se comunicar com back-ends FastAPI via HTTP. Só precisamos criar um script de cliente de API encapsulando todas as chamadas de API para invocar capacidades de IA do back-end no jogo. Esta arquitetura de separação de front-end e back-end nos permite desenvolver e testar independentemente lógica de jogo e lógica de IA, melhorando muito a eficiência de desenvolvimento.

Claro, Godot também tem algumas limitações. Por exemplo, as capacidades 3D do Godot ainda ficam atrás do Unreal Engine e Unity. Se você quiser desenvolver jogos 3D em grande escala, pode precisar considerar outros motores. Mas para jogos 2D, jogos indie e projetos de ensino, Godot é uma excelente escolha.

### 15.5.1 Design de Cena e Organização de Recursos

Depois de entender o sistema de cena do Godot, vamos ver o design de cena da Cyber Town. O jogo inteiro consiste em quatro cenas principais: Main (cena principal), Player (jogador), NPC (personagem não jogador) e DialogueUI (interface de diálogo). Cada cena é um módulo independente que pode ser editado e testado separadamente, depois combinado para formar um jogo completo.

A organização de cena da Cyber Town adota um design modular. Primeiro criamos três cenas básicas: Player (jogador), NPC (personagem não jogador) e DialogueUI (interface de diálogo). Em seguida, em Main (cena principal), instanciamos e combinamos essas cenas. É particularmente digno de nota que os três NPCs (Zhang San, Li Si, Wang Wu) são todos instâncias da mesma cena de NPC, apenas com diferentes informações de função definidas através de parâmetros de script.

Vamos primeiro ver a estrutura das quatro cenas principais, como mostrado na Figura 15.12:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-12.png" alt="" width="85%"/>
  <p>Figura 15.12 Quatro Cenas Principais da Cyber Town</p>
</div>

Este diagrama mostra quatro cenas independentes e suas estruturas internas. **Cena 1 (Main)** é a cena principal, contendo imagem de fundo (Sprite2D), instância de jogador, nó de organização de NPCs (com três instâncias de NPC abaixo), instância de interface de diálogo, nó de organização de paredes e música de fundo. Note que Player, NPC_Zhang, NPC_Li, NPC_Wang e DialogueUI aqui são instâncias de cena, não nós comuns. **Cena 2 (Player)** define a estrutura do personagem do jogador, incluindo animação, colisão, câmera e dois nós de efeito sonoro. **Cena 3 (NPC)** é um modelo genérico - Zhang San, Li Si e Wang Wu são todos instâncias desta cena, contendo colisão, animação, área de interação e dois rótulos. **Cena 4 (DialogueUI)** é um nó CanvasLayer contendo Panel e vários elementos de interface.

O processo de instanciação de cena pode ser entendido assim: Criamos o arquivo de cena NPC.tscn no editor Godot, definindo a estrutura de nós do NPC. Em seguida, na cena Main, "instanciamos" esta cena de NPC três vezes, criando três cópias independentes nomeadas NPC_Zhang, NPC_Li e NPC_Wang respectivamente. Cada cópia tem sua própria posição e estado, mas elas compartilham a mesma estrutura de nós. Se modificarmos NPC.tscn, como adicionar um novo nó de efeito sonoro ao NPC, todas as três instâncias obterão automaticamente este efeito sonoro.

Os passos para criar essas cenas no Godot são os seguintes:

1. **Criar cena Player**: Criar nova cena, selecionar CharacterBody2D como nó raiz, adicionar nós filho AnimatedSprite2D, CollisionShape2D, Camera2D, InteractSound e RunningSound, salvar como Player.tscn.

2. **Criar cena NPC**: Criar nova cena, selecionar CharacterBody2D como nó raiz, adicionar nós filho CollisionShape2D, AnimatedSprite2D, InteractionArea (Area2D com CollisionShape2D abaixo), NameLabel e DialogueLabel, salvar como NPC.tscn.

3. **Criar cena DialogueUI**: Criar nova cena, selecionar CanvasLayer como nó raiz, adicionar nó filho Panel, sob Panel adicionar NPCName, NPCTitle, DialogueText (RichTextLabel), PlayerInput (LineEdit), SendButton e CloseButton, salvar como DialogueUI.tscn.

4. **Criar cena Main**: Criar nova cena, selecionar Node2D como nó raiz, adicionar Background (Sprite2D) como imagem de fundo, sob Background adicionar decoração de baleia, depois instanciar cena Player, criar nó NPCs e instanciar cena NPC três vezes abaixo dele, instanciar cena DialogueUI, criar nó Walls para organizar colisões de parede, finalmente adicionar AudioStreamPlayer para reproduzir música de fundo.

As vantagens deste método de organização de cena são: cada cena é independente e pode ser testada separadamente; NPCs usam instâncias da mesma cena, modificar uma vez afeta todos os NPCs; cenas se comunicam através de sinais com baixo acoplamento, fácil de manter e estender.

### 15.5.2 Implementação de Controle de Jogador

O personagem do jogador é um dos elementos mais importantes no jogo. Precisamos implementar controle de movimento WASD, troca de animação, detecção de colisão, interação com NPCs e sistema de efeitos sonoros.

A estrutura da cena do jogador inclui: um CharacterBody2D como nó raiz, responsável por movimento físico e colisão; um AnimatedSprite2D exibindo animação de personagem; um CollisionShape2D definindo forma de colisão; uma Camera2D seguindo o jogador; dois AudioStreamPlayers reproduzindo efeitos sonoros de interação e sons de caminhada respectivamente.

O script de controle de jogador `player.gd` implementa lógica de movimento, interação e efeito sonoro:

```python
extends CharacterBody2D

# Velocidade de movimento
@export var speed: float = 200.0

# NPC atualmente interagível
var nearby_npc: Node = null

# Estado de interação (desabilitar movimento durante interação)
var is_interacting: bool = false

# Referências de nó
@onready var animated_sprite: AnimatedSprite2D = $AnimatedSprite2D
@onready var camera: Camera2D = $Camera2D

# Referências de efeito sonoro
@onready var interact_sound: AudioStreamPlayer = null
@onready var running_sound: AudioStreamPlayer = null

# Estado de efeito sonoro de caminhada
var is_playing_running_sound: bool = false

func _ready():
    # Adicionar ao grupo de jogadores (importante! NPCs precisam deste grupo para identificar jogador)
    add_to_group("player")

    # Obter nós de efeito sonoro (opcional, não dará erro se não existir)
    interact_sound = get_node_or_null("InteractSound")
    running_sound = get_node_or_null("RunningSound")

    # Habilitar câmera
    camera.enabled = true

    # Reproduzir animação padrão
    if animated_sprite.sprite_frames != null and animated_sprite.sprite_frames.has_animation("idle"):
        animated_sprite.play("idle")

func _physics_process(_delta: float):
    # Se interagindo, desabilitar movimento
    if is_interacting:
        velocity = Vector2.ZERO
        move_and_slide()
        # Reproduzir animação parada
        if animated_sprite.sprite_frames != null and animated_sprite.sprite_frames.has_animation("idle"):
            animated_sprite.play("idle")
        # Parar efeito sonoro de caminhada
        stop_running_sound()
        return

    # Obter direção de entrada
    var input_direction = Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")

    # Definir velocidade
    velocity = input_direction * speed

    # Mover
    move_and_slide()

    # Atualizar animação e direção
    update_animation(input_direction)

    # Atualizar efeito sonoro de caminhada
    update_running_sound(input_direction)

func update_animation(direction: Vector2):
    """Atualizar animação de personagem (suporta 4 direções)"""
    if animated_sprite.sprite_frames == null:
        return

    # Reproduzir animação com base na direção de movimento
    if direction.length() > 0:
        # Movendo - determinar direção principal
        if abs(direction.x) > abs(direction.y):
            # Movimento esquerda-direita
            if direction.x > 0:
                # Direita
                if animated_sprite.sprite_frames.has_animation("walk_right"):
                    animated_sprite.play("walk_right")
                    animated_sprite.flip_h = false
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
                    animated_sprite.flip_h = false
            else:
                # Esquerda
                if animated_sprite.sprite_frames.has_animation("walk_left"):
                    animated_sprite.play("walk_left")
                    animated_sprite.flip_h = false
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
                    animated_sprite.flip_h = true
        else:
            # Movimento cima-baixo
            if direction.y > 0:
                # Baixo
                if animated_sprite.sprite_frames.has_animation("walk_down"):
                    animated_sprite.play("walk_down")
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
            else:
                # Cima
                if animated_sprite.sprite_frames.has_animation("walk_up"):
                    animated_sprite.play("walk_up")
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
    else:
        # Parado
        if animated_sprite.sprite_frames.has_animation("idle"):
            animated_sprite.play("idle")

func _input(event: InputEvent):
    # Pressionar tecla E para interagir com NPC
    if event is InputEventKey:
        if event.pressed and not event.echo:
            if event.keycode == KEY_E or event.keycode == KEY_ENTER:
                if nearby_npc != null:
                    interact_with_npc()

func interact_with_npc():
    """Interagir com NPC próximo"""
    if nearby_npc != null:
        # Reproduzir efeito sonoro de interação
        if interact_sound:
            interact_sound.play()

        # Enviar sinal ao sistema de diálogo
        get_tree().call_group("dialogue_system", "start_dialogue", nearby_npc.npc_name)

func set_nearby_npc(npc: Node):
    """Definir NPC próximo"""
    nearby_npc = npc

func set_interacting(interacting: bool):
    """Definir estado de interação"""
    is_interacting = interacting
    if interacting:
        # Parar efeito sonoro de caminhada
        stop_running_sound()

func update_running_sound(direction: Vector2):
    """Atualizar efeito sonoro de caminhada"""
    if running_sound == null:
        return

    # Se movendo
    if direction.length() > 0:
        # Se efeito sonoro ainda não está reproduzindo, começar a reproduzir
        if not is_playing_running_sound:
            running_sound.play()
            is_playing_running_sound = true
    else:
        # Se parou de mover, parar efeito sonoro
        stop_running_sound()

func stop_running_sound():
    """Parar efeito sonoro de caminhada"""
    if running_sound and is_playing_running_sound:
        running_sound.stop()
        is_playing_running_sound = false
```

Este script implementa controle completo do jogador. Os jogadores usam teclas WASD (ou teclas de seta) para mover, e o personagem reproduz animações de 4 direções correspondentes (walk_up/down/left/right) com base na direção de movimento. Quando o jogador se aproxima de um NPC, o NPC chama `set_nearby_npc()` para se definir como um objeto interagível, e o jogador pode pressionar a tecla E para acionar interação. Durante a interação, efeitos sonoros são reproduzidos, e `call_group()` notifica o sistema de diálogo para iniciar conversa. Durante o diálogo, `set_interacting(true)` desabilita o movimento do jogador, que é restaurado após o fim do diálogo. Efeitos sonoros de caminhada são reproduzidos automaticamente quando o jogador se move e param automaticamente quando parado.

### 15.5.3 Comportamento e Interação de NPC

NPCs precisam implementar três funções principais: patrulhar e vagar aleatoriamente na cena, responder a interações de jogadores e exibir balões de diálogo. Usamos Area2D para detectar se o jogador está perto do NPC. Quando o jogador entra na faixa de interação, o jogador é notificado, e pressionar a tecla E inicia a conversa.

A estrutura da cena de NPC inclui: CharacterBody2D como nó raiz; CollisionShape2D define forma de colisão do NPC; AnimatedSprite2D exibe animação do NPC; InteractionArea (Area2D) detecta jogador entrando na faixa de interação, com CollisionShape2D abaixo definindo faixa de interação; NameLabel exibe nome do NPC; DialogueLabel exibe balão de diálogo.

O script de NPC `npc.gd` implementa lógica de patrulha, interação e balão de diálogo:

```python
extends CharacterBody2D

# Informações do NPC
@export var npc_name: String = "Zhang San"
@export var npc_title: String = "Engenheiro Python"

# Configuração de aparência do NPC
@export var sprite_frames: SpriteFrames = null  # Recurso de quadro de sprite personalizado

# Configuração de movimento do NPC
@export var move_speed: float = 50.0  # Velocidade de movimento
@export var wander_enabled: bool = true  # Se habilitar patrulha
@export var wander_range: float = 200.0  # Faixa de patrulha
@export var wander_interval_min: float = 3.0  # Intervalo mínimo de patrulha (segundos)
@export var wander_interval_max: float = 8.0  # Intervalo máximo de patrulha (segundos)

# Conteúdo de diálogo atual (obtido do back-end)
var current_dialogue: String = ""

# Referências de nó
@onready var animated_sprite: AnimatedSprite2D = $AnimatedSprite2D
@onready var interaction_area: Area2D = $InteractionArea
@onready var name_label: Label = $NameLabel
@onready var dialogue_label: Label = $DialogueLabel

# Referência do jogador
var player: Node = null

# Variáveis relacionadas à patrulha
var wander_target: Vector2 = Vector2.ZERO  # Posição alvo de patrulha
var wander_timer: float = 0.0  # Temporizador de patrulha
var is_wandering: bool = false  # Se atualmente patrulhando
var is_interacting: bool = false  # Se atualmente interagindo com jogador
var spawn_position: Vector2 = Vector2.ZERO  # Posição de geração

func _ready():
    # Adicionar ao grupo npcs
    add_to_group("npcs")

    # Definir nome do NPC
    name_label.text = npc_name

    # Conectar sinais de área de interação
    interaction_area.body_entered.connect(_on_body_entered)
    interaction_area.body_exited.connect(_on_body_exited)

    # Inicializar rótulo de diálogo
    dialogue_label.text = ""
    dialogue_label.visible = false

    # Definir quadros de sprite personalizados (se houver)
    if sprite_frames != null:
        animated_sprite.sprite_frames = sprite_frames

    # Reproduzir animação padrão
    if animated_sprite.sprite_frames != null and animated_sprite.sprite_frames.has_animation("idle"):
        animated_sprite.play("idle")

    # Registrar posição de geração
    spawn_position = global_position

    # Inicializar temporizador de patrulha
    if wander_enabled:
        wander_timer = randf_range(wander_interval_min, wander_interval_max)
        choose_new_wander_target()

func _on_body_entered(body: Node2D):
    """Jogador entra na faixa de interação"""
    if body.is_in_group("player"):
        player = body

        if player.has_method("set_nearby_npc"):
            player.set_nearby_npc(self)

func _on_body_exited(body: Node2D):
    """Jogador sai da faixa de interação"""
    if body.is_in_group("player"):
        if player != null and player.has_method("set_nearby_npc"):
            player.set_nearby_npc(null)
        player = null

func update_dialogue(dialogue: String):
    """Atualizar conteúdo de diálogo do NPC"""
    current_dialogue = dialogue
    dialogue_label.text = dialogue
    dialogue_label.visible = true

    # Ocultar diálogo após 10 segundos
    await get_tree().create_timer(10.0).timeout
    dialogue_label.visible = false

func _physics_process(delta: float):
    """Atualização física - lidar com movimento"""
    # Se interagindo com jogador, parar movimento
    if is_interacting:
        velocity = Vector2.ZERO
        move_and_slide()
        # Reproduzir animação parada
        if animated_sprite.sprite_frames != null and animated_sprite.sprite_frames.has_animation("idle"):
            animated_sprite.play("idle")
        return

    # Se patrulha não habilitada, não mover
    if not wander_enabled:
        return

    # Atualizar temporizador de patrulha
    wander_timer -= delta

    # Se temporizador terminar, escolher novo alvo e começar a mover
    if wander_timer <= 0:
        choose_new_wander_target()
        wander_timer = randf_range(wander_interval_min, wander_interval_max)

    # Se patrulhando, mover para alvo
    if is_wandering:
        # Verificar se alcançou alvo
        if global_position.distance_to(wander_target) < 10:
            # Alcançou alvo, parar movimento
            is_wandering = false
            velocity = Vector2.ZERO
            move_and_slide()
            # Reproduzir animação parada
            if animated_sprite.sprite_frames != null and animated_sprite.sprite_frames.has_animation("idle"):
                animated_sprite.play("idle")
        else:
            # Continuar movendo para alvo
            var direction = (wander_target - global_position).normalized()
            velocity = direction * move_speed
            move_and_slide()
            # Atualizar animação
            update_animation(direction)
    else:
        # Parar movimento
        velocity = Vector2.ZERO
        move_and_slide()
        # Reproduzir animação parada
        if animated_sprite.sprite_frames != null and animated_sprite.sprite_frames.has_animation("idle"):
            animated_sprite.play("idle")

func choose_new_wander_target():
    """Escolher novo alvo de patrulha"""
    # Escolher aleatoriamente um ponto perto da posição de geração
    var offset = Vector2(
        randf_range(-wander_range, wander_range),
        randf_range(-wander_range, wander_range)
    )
    wander_target = spawn_position + offset
    is_wandering = true

func update_animation(direction: Vector2):
    """Atualizar animação"""
    if animated_sprite.sprite_frames == null:
        return

    if direction.length() > 0:
        # Animação de movimento
        if abs(direction.x) > abs(direction.y):
            # Movimento esquerda-direita
            if direction.x > 0:
                if animated_sprite.sprite_frames.has_animation("walk_right"):
                    animated_sprite.play("walk_right")
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
                    animated_sprite.flip_h = false
            else:
                if animated_sprite.sprite_frames.has_animation("walk_left"):
                    animated_sprite.play("walk_left")
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
                    animated_sprite.flip_h = true
        else:
            # Movimento cima-baixo
            if direction.y > 0:
                if animated_sprite.sprite_frames.has_animation("walk_down"):
                    animated_sprite.play("walk_down")
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
            else:
                if animated_sprite.sprite_frames.has_animation("walk_up"):
                    animated_sprite.play("walk_up")
                elif animated_sprite.sprite_frames.has_animation("walk"):
                    animated_sprite.play("walk")
    else:
        # Animação parada
        if animated_sprite.sprite_frames.has_animation("idle"):
            animated_sprite.play("idle")

func set_interacting(interacting: bool):
    """Definir estado de interação"""
    is_interacting = interacting
```

Este script implementa comportamento completo de NPC. NPCs patrulham aleatoriamente dentro do `wander_range` em torno de sua posição de geração, escolhendo um novo ponto alvo e movendo para lá a cada `wander_interval_min` a `wander_interval_max` segundos. Durante o movimento, animações de 4 direções (walk_up/down/left/right) são reproduzidas, e ao alcançar o alvo, eles param e reproduzem a animação parada. Quando um jogador entra na InteractionArea, o NPC chama o método `set_nearby_npc(self)` do jogador, definindo-se como um objeto interagível. Após o jogador pressionar a tecla E, o sistema de diálogo chama o método `set_interacting(true)` do NPC, e o NPC para de se mover. Após o fim do diálogo, `set_interacting(false)` é chamado, e o NPC retoma a patrulha. A cena principal chama periodicamente o método `update_dialogue()` para atualizar o balão de diálogo do NPC, exibindo conteúdo de diálogo autônomo entre NPCs.

## 15.6 Implementação de Comunicação Front-End e Back-End

### 15.6.1 Encapsulamento de Cliente de API

O front-end Godot precisa se comunicar com o back-end FastAPI via HTTP. Criamos um script de cliente de API `api_client.gd`, encapsulando todas as chamadas de API, e o definimos como um singleton AutoLoad (auto-carregamento) para que outros scripts possam usá-lo convenientemente.

O cliente de API usa o nó HTTPRequest do Godot para enviar solicitações HTTP. HTTPRequest é um nó assíncrono que não bloqueia o jogo após enviar solicitações, mas notifica a conclusão da solicitação através de sinais. Isto garante a fluidez do jogo - mesmo com alta latência de rede, não há travamento. Usamos o mecanismo de sinal para notificar outros scripts de respostas de API em vez de usar await, permitindo que múltiplos scripts escutem simultaneamente a mesma resposta de API.

```python
# api_client.gd
extends Node

# Definições de sinal
signal chat_response_received(npc_name: String, message: String)
signal chat_error(error_message: String)
signal npc_status_received(dialogues: Dictionary)
signal npc_list_received(npcs: Array)

# Nós de solicitação HTTP
var http_chat: HTTPRequest
var http_status: HTTPRequest
var http_npcs: HTTPRequest

func _ready():
    # Criar nós de solicitação HTTP
    http_chat = HTTPRequest.new()
    http_status = HTTPRequest.new()
    http_npcs = HTTPRequest.new()

    add_child(http_chat)
    add_child(http_status)
    add_child(http_npcs)

    # Conectar sinais
    http_chat.request_completed.connect(_on_chat_request_completed)
    http_status.request_completed.connect(_on_status_request_completed)
    http_npcs.request_completed.connect(_on_npcs_request_completed)

# ==================== API de Chat ====================
func send_chat(npc_name: String, message: String) -> void:
    """Enviar solicitação de chat"""
    var data = {
        "npc_name": npc_name,
        "message": message
    }

    var json_string = JSON.stringify(data)
    var headers = ["Content-Type: application/json"]

    var error = http_chat.request(
        Config.API_CHAT,
        headers,
        HTTPClient.METHOD_POST,
        json_string
    )

    if error != OK:
        print("[ERROR] Falha ao enviar solicitação de chat: ", error)
        chat_error.emit("Falha na solicitação de rede")

func _on_chat_request_completed(_result: int, response_code: int, _headers: PackedStringArray, body: PackedByteArray) -> void:
    """Lidar com resposta de chat"""
    if response_code != 200:
        print("[ERROR] Solicitação de chat falhou: HTTP ", response_code)
        chat_error.emit("Erro do servidor: " + str(response_code))
        return

    var json = JSON.new()
    var parse_result = json.parse(body.get_string_from_utf8())

    if parse_result != OK:
        print("[ERROR] Falha ao analisar resposta")
        chat_error.emit("Falha na análise de resposta")
        return

    var response = json.data

    if response.has("success") and response["success"]:
        var npc_name = response["npc_name"]
        var msg = response["message"]
        print("[INFO] Resposta de NPC recebida: ", npc_name, " -> ", msg)
        chat_response_received.emit(npc_name, msg)
    else:
        chat_error.emit("Falha no chat")

# ==================== API de Status de NPC ====================
func get_npc_status() -> void:
    """Obter status de NPC"""
    # Verificar se solicitação está sendo processada
    if http_status.get_http_client_status() != HTTPClient.STATUS_DISCONNECTED:
        print("[WARN] Solicitação de status de NPC está sendo processada, pulando esta solicitação")
        return

    var error = http_status.request(Config.API_NPC_STATUS)

    if error != OK:
        print("[ERROR] Falha ao obter status de NPC: ", error)

func _on_status_request_completed(_result: int, response_code: int, _headers: PackedStringArray, body: PackedByteArray) -> void:
    """Lidar com resposta de status de NPC"""
    if response_code != 200:
        print("[ERROR] Solicitação de status de NPC falhou: HTTP ", response_code)
        return

    var json = JSON.new()
    var parse_result = json.parse(body.get_string_from_utf8())

    if parse_result != OK:
        print("[ERROR] Falha ao analisar status de NPC")
        return

    var response = json.data

    if response.has("dialogues"):
        var dialogues = response["dialogues"]
        print("[INFO] Atualização de status de NPC recebida: ", dialogues.size(), " NPCs")
        npc_status_received.emit(dialogues)

# ==================== API de Lista de NPC ====================
func get_npc_list() -> void:
    """Obter lista de NPC"""
    var error = http_npcs.request(Config.API_NPCS)

    if error != OK:
        print("[ERROR] Falha ao obter lista de NPC: ", error)

func _on_npcs_request_completed(_result: int, response_code: int, _headers: PackedStringArray, body: PackedByteArray) -> void:
    """Lidar com resposta de lista de NPC"""
    if response_code != 200:
        print("[ERROR] Solicitação de lista de NPC falhou: HTTP ", response_code)
        return

    var json = JSON.new()
    var parse_result = json.parse(body.get_string_from_utf8())

    if parse_result != OK:
        print("[ERROR] Falha ao analisar lista de NPC")
        return

    var response = json.data

    if response.has("npcs"):
        var npcs = response["npcs"]
        print("[INFO] Lista de NPC recebida: ", npcs.size(), " NPCs")
        npc_list_received.emit(npcs)
```

Este cliente de API encapsula três funções principais: enviar solicitação de chat (`send_chat`), obter status de NPC (`get_npc_status`) e obter lista de NPC (`get_npc_list`). Todas as solicitações HTTP são assíncronas, notificando resultados de resposta através de sinais. Criamos nós HTTPRequest independentes para cada API, permitindo que múltiplas solicitações sejam enviadas simultaneamente sem interferirem umas com as outras. URLs de API são obtidas do singleton Config para gerenciamento unificado conveniente. O sistema de diálogo escuta o sinal `chat_response_received` para receber respostas de NPC, e a cena principal escuta o sinal `npc_status_received` para atualizar balões de diálogo de NPC.

### 15.6.2 Implementação de Interface de Diálogo

A interface de diálogo é a interface para interação jogador-NPC. Precisamos projetar uma caixa de diálogo simples e bonita contendo nome do NPC, título, exibição de conteúdo de diálogo, caixa de entrada e botões.

A estrutura da interface de diálogo é mostrada na Figura 15.13:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-13.png" alt="" width="85%"/>
  <p>Figura 15.13 Estrutura da Interface de Diálogo</p>
</div>

O design da interface de diálogo é muito simples. DialogueUI é um nó CanvasLayer, significando que sempre exibirá no topo da tela do jogo e não será obscurecido por outros objetos do jogo. Panel é o fundo da caixa de diálogo, ancorado na parte inferior da tela. Sob Panel estão 6 elementos de interface colocados diretamente: NPCName exibe o nome do NPC, NPCTitle exibe o título, DialogueText usa RichTextLabel para exibir conteúdo de diálogo (suporta formato de texto rico), PlayerInput é um LineEdit para entrada do jogador, e SendButton e CloseButton são usados para enviar mensagens e fechar a caixa de diálogo respectivamente.

O script de interface de diálogo `dialogue_ui.gd` implementa a lógica da interface de diálogo:

```python
# dialogue_ui.gd
extends CanvasLayer

# Referências de nó de interface
@onready var panel = $Panel
@onready var npc_name_label = $Panel/NPCName
@onready var npc_title_label = $Panel/NPCTitle
@onready var dialogue_text = $Panel/DialogueText
@onready var input_field = $Panel/PlayerInput
@onready var send_button = $Panel/SendButton
@onready var close_button = $Panel/CloseButton

# Cliente de API
var api_client: Node = null

# NPC atual em diálogo
var current_npc_name: String = ""

func _ready():
    # Ocultar caixa de diálogo na inicialização
    visible = false

    # Conectar sinais de botão
    send_button.pressed.connect(_on_send_button_pressed)
    close_button.pressed.connect(_on_close_button_pressed)
    input_field.text_submitted.connect(_on_text_submitted)

    # Obter cliente de API
    api_client = get_node_or_null("/root/APIClient")

func start_dialogue(npc_name: String):
    """Iniciar diálogo com NPC"""
    current_npc_name = npc_name

    # Definir informações do NPC
    npc_name_label.text = npc_name
    npc_title_label.text = get_npc_title(npc_name)

    # Limpar conteúdo de diálogo
    dialogue_text.clear()
    dialogue_text.append_text("[color=gray]Conversa com " + npc_name + " iniciada...[/color]\n")

    # Limpar campo de entrada
    input_field.text = ""

    # Mostrar caixa de diálogo
    show_dialogue()

    # Focar campo de entrada
    input_field.grab_focus()

func show_dialogue():
    """Mostrar caixa de diálogo"""
    visible = true

    # Notificar jogador para entrar em estado de interação (desabilitar movimento)
    var player = get_tree().get_first_node_in_group("player")
    if player and player.has_method("set_interacting"):
        player.set_interacting(true)

func hide_dialogue():
    """Ocultar caixa de diálogo"""
    visible = false
    current_npc_name = ""

    # Notificar jogador para sair de estado de interação (habilitar movimento)
    var player = get_tree().get_first_node_in_group("player")
    if player and player.has_method("set_interacting"):
        player.set_interacting(false)

func _on_send_button_pressed():
    """Botão enviar clicado"""
    send_message()

func _on_close_button_pressed():
    """Botão fechar clicado"""
    hide_dialogue()

func _on_text_submitted(_text: String):
    """Campo de entrada enter pressionado"""
    send_message()

func send_message():
    """Enviar mensagem"""
    var message = input_field.text.strip_edges()

    if message.is_empty():
        return

    if current_npc_name.is_empty():
        return

    # Exibir mensagem do jogador
    dialogue_text.append_text("\n[color=cyan]Jogador:[/color] " + message + "\n")

    # Limpar campo de entrada
    input_field.text = ""

    # Desabilitar entrada
    input_field.editable = false
    send_button.disabled = true

    # Enviar solicitação de API
    if api_client:
        api_client.send_chat_request(current_npc_name, message)

func on_chat_response_received(npc_name: String, response: String):
    """Resposta de NPC recebida"""
    if npc_name == current_npc_name:
        # Exibir resposta de NPC
        dialogue_text.append_text("[color=yellow]" + npc_name + ":[/color] " + response + "\n")

        # Habilitar entrada
        input_field.editable = true
        send_button.disabled = false
        input_field.grab_focus()

func get_npc_title(npc_name: String) -> String:
    """Obter título do NPC"""
    var titles = {
        "Zhang San": "Engenheiro Python",
        "Li Si": "Gerente de Produto",
        "Wang Wu": "Designer de UI"
    }
    return titles.get(npc_name, "")
```

Esta interface de diálogo implementa funcionalidade completa de diálogo. Os jogadores podem inserir e enviar mensagens, e a interface usa o método append_text do RichTextLabel para exibir conteúdo de diálogo, suportando formato de texto rico (cores, negrito, etc.). Todas as chamadas de API são assíncronas, desabilitando a caixa de entrada enquanto aguarda respostas para prevenir envios duplicados. Quando a caixa de diálogo é exibida, notifica o jogador para entrar em estado de interação, desabilitando movimento, e restaura movimento quando fechada.

### 15.6.3 Integração da Cena Principal

Finalmente, precisamos integrar todas as funções na cena principal: controle de jogador, interação de NPC, interface de diálogo e atualizações de status de NPC. O script da cena principal `main.gd` coordena esses componentes e obtém periodicamente status de NPC do back-end para atualizar balões de diálogo de NPC.

```python
# main.gd
extends Node2D

# Referências de nó de NPC
@onready var npc_zhang: Node2D = $NPCs/NPC_Zhang
@onready var npc_li: Node2D = $NPCs/NPC_Li
@onready var npc_wang: Node2D = $NPCs/NPC_Wang

# Cliente de API
var api_client: Node = null

# Temporizador de atualização de status de NPC
var status_update_timer: float = 0.0

func _ready():
    print("[INFO] Inicialização da cena principal")

    # Obter cliente de API
    api_client = get_node_or_null("/root/APIClient")
    if api_client:
        api_client.npc_status_received.connect(_on_npc_status_received)

        # Obter imediatamente status de NPC uma vez
        api_client.get_npc_status()
    else:
        print("[ERROR] Cliente de API não encontrado")

func _process(delta: float):
    # Atualizar periodicamente status de NPC
    status_update_timer += delta
    if status_update_timer >= Config.NPC_STATUS_UPDATE_INTERVAL:
        status_update_timer = 0.0
        if api_client:
            api_client.get_npc_status()

func _on_npc_status_received(dialogues: Dictionary):
    """Atualização de status de NPC recebida"""
    print("[INFO] Atualizar status de NPC: ", dialogues)

    # Atualizar diálogo de cada NPC
    for npc_name in dialogues:
        var dialogue = dialogues[npc_name]
        update_npc_dialogue(npc_name, dialogue)

func update_npc_dialogue(npc_name: String, dialogue: String):
    """Atualizar diálogo do NPC especificado"""
    var npc_node = get_npc_node(npc_name)
    if npc_node and npc_node.has_method("update_dialogue"):
        npc_node.update_dialogue(dialogue)

func get_npc_node(npc_name: String) -> Node2D:
    """Obter nó de NPC por nome"""
    match npc_name:
        "Zhang San":
            return npc_zhang
        "Li Si":
            return npc_li
        "Wang Wu":
            return npc_wang
        _:
            return null
```

A função principal do script da cena principal é obter periodicamente status de NPC do back-end. Em `_ready()`, obtemos uma referência ao singleton APIClient e conectamos o sinal `npc_status_received`. Em seguida, chamamos imediatamente `get_npc_status()` para obter status de NPC uma vez. Em `_process()`, usamos um temporizador para chamar `get_npc_status()` a cada `Config.NPC_STATUS_UPDATE_INTERVAL` segundos (padrão 30 segundos). Quando atualizações de status de NPC são recebidas, a função de callback `_on_npc_status_received()` percorre todos os NPCs e chama seu método `update_dialogue()` para atualizar balões de diálogo. Desta forma, mesmo se o jogador não interagir com NPCs, ainda pode ver diálogo autônomo entre NPCs.

O processo completo de comunicação front-end e back-end é mostrado na Figura 15.14:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-14.png" alt="" width="85%"/>
  <p>Figura 15.14 Processo Completo de Comunicação Front-End e Back-End</p>
</div>

Neste ponto, todas as funções de comunicação front-end e back-end foram implementadas. Os jogadores podem se mover livremente no jogo, interagir com NPCs e ter conversas em linguagem natural. Enquanto isso, a cena principal obtém periodicamente status de NPC do back-end, atualiza balões de diálogo de NPC e exibe diálogo autônomo entre NPCs. Todo o sistema usa um mecanismo de sinal para comunicação, com acoplamento solto entre componentes, facilitando manutenção e extensão.

## 15.7 Resumo e Perspectivas

### 15.7.1 Revisão do Capítulo

Neste capítulo, completamos um projeto completo de cidade de IA - Cyber Town. Este projeto combina o framework HelloAgents com o motor de jogo Godot para criar um mundo virtual cheio de vitalidade. Vamos revisar o conteúdo principal que aprendemos.

**Design de Arquitetura Técnica**

Adotamos uma arquitetura separada de motor de jogo + serviço back-end, separando renderização front-end, lógica back-end e inteligência de IA em diferentes camadas. Godot lida com gráficos de jogo e interação de jogador, FastAPI lida com serviços de API e gerenciamento de estado, e HelloAgents lida com inteligência de NPC e sistemas de memória. Este design em camadas permite que cada parte seja desenvolvida e testada independentemente, e também fornece uma boa base para expansão futura.

**Sistema de Agente NPC**

Usamos o SimpleAgent do HelloAgents para criar agentes independentes para cada NPC. Cada NPC tem sua própria configuração de função, traços de personalidade e sistema de memória. Através de prompts de sistema cuidadosamente projetados, fizemos Zhang San um engenheiro Python rigoroso, Li Si um gerente de produto bom em comunicação, e Wang Wu um designer de UI criativo. Esses NPCs podem não apenas entender diálogo de jogadores, mas também responder de acordo com suas características de função.

**Sistema de Memória e Afeição**

Implementamos um sistema de memória de duas camadas: memória de curto prazo mantém coerência de diálogo, e memória de longo prazo armazena todo o histórico de interações. Através de recuperação semântica em bancos de dados vetoriais, NPCs podem recordar tópicos discutidos anteriormente. O sistema de afeição permite que as atitudes dos NPCs em relação aos jogadores mudem com a interação, de estranho a amigo próximo, com expressões comportamentais diferentes em cada nível. Esses designs fazem os NPCs parecerem mais realistas e interessantes.

**Construção de Cena de Jogo**

Usamos Godot para criar uma cena de escritório estilo pixel, implementando controle de jogador, patrulha de NPC, detecção de interação e interface de diálogo. Através do design modular do sistema de cena, podemos facilmente adicionar novos NPCs, novas cenas e novas funções. A sintaxe concisa do GDScript torna a implementação de lógica de jogo intuitiva e eficiente.

**Comunicação Front-End e Back-End**

Usamos API REST HTTP para implementar comunicação entre front-end Godot e back-end FastAPI. Através de solicitações assíncronas e sistemas de sinal, garantimos a fluidez do jogo - mesmo com alta latência de rede, a experiência do jogador não é afetada. O encapsulamento do cliente de API permite que outros scripts chamem convenientemente serviços back-end, e a implementação da interface de diálogo permite que jogadores se comuniquem naturalmente com NPCs.

A pilha de tecnologia do projeto é mostrada na Figura 15.15:

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-15.png" alt="" width="85%"/>
  <p>Figura 15.15 Pilha de Tecnologia da Cyber Town</p>
</div>

### 15.7.2 Direções de Extensão

Cyber Town é apenas um ponto de partida - há muitas direções para extensão. Essas extensões podem não apenas melhorar a diversão do jogo, mas também explorar mais possibilidades para tecnologia de IA em jogos.

**(1) Suporte Multiplayer Online**

Atualmente, Cyber Town é um jogo single-player, mas podemos estendê-lo para um jogo multiplayer online. Múltiplos jogadores podem simultaneamente entrar no mesmo escritório e interagir com NPCs e outros jogadores. Isto requer introdução de WebSocket para comunicação em tempo real e bancos de dados para persistir dados de jogadores e estados de NPC. NPCs podem lembrar interações com diferentes jogadores e manter níveis de afeição independentes para cada jogador.

**(2) Sistema de Missões**

Podemos projetar um sistema de missões para NPCs. Quando a afeição de um jogador com um NPC atinge um certo nível, o NPC fornecerá missões especiais. Por exemplo, Zhang San pode pedir ao jogador para ajudar a depurar código, Li Si pode pedir ao jogador para coletar feedback do usuário, e Wang Wu pode pedir ao jogador para avaliar propostas de design. Completar missões pode ganhar recompensas e aumentar ainda mais a afeição.

**(3) Interação NPC-para-NPC**

Atualmente, NPCs apenas interagem com jogadores, mas podemos habilitar NPCs a interagirem uns com os outros. Zhang San pode discutir requisitos de produto com Li Si, Li Si pode discutir design de interface com Wang Wu, e Wang Wu pode discutir implementação técnica com Zhang San. Essas interações podem ocorrer automaticamente em segundo plano, e jogadores podem observar diálogo entre NPCs, tornando o mundo inteiro mais vivo.

**(4) Sistema de Emoção**

Além da afeição, podemos adicionar um sistema de emoção mais complexo para NPCs. NPCs podem ter diferentes estados emocionais como feliz, triste, irritado e animado, que afetam estilo de resposta e comportamento do NPC. Por exemplo, quando um NPC está de bom humor, estará mais disposto a compartilhar informações; quando de mau humor, pode ser bastante frio.

**(5) Sistema de Eventos Dinâmicos**

Podemos projetar eventos dinâmicos para tornar o mundo do jogo mais rico. Por exemplo, realizar regularmente reuniões de equipe onde todos os NPCs e jogadores se reúnem para discutir progresso do projeto; ou realizar festas de aniversário celebrando o aniversário de um NPC; ou tarefas de emergência requerendo colaboração de todos. Esses eventos podem aumentar a variedade e diversão do jogo.

**(6) Mundo Maior**

Atualmente, Cyber Town tem apenas uma cena de escritório, mas podemos expandir para um mundo maior. Podemos adicionar diferentes cenas como cafés, bibliotecas e parques, cada uma com diferentes NPCs e métodos de interação. Jogadores podem se mover entre diferentes cenas e explorar um mundo virtual mais amplo.

**(7) Aprendizado Personalizado**

NPCs podem aprender preferências e hábitos de cada jogador. Por exemplo, se um jogador discute frequentemente Python com Zhang San, o NPC lembrará que o jogador está interessado em programação e compartilhará proativamente conteúdo relacionado no futuro. Se um jogador gosta de jogar à noite, o NPC lembrará deste hábito de tempo e será mais ativo à noite.

### 15.7.3 Reflexão e Perspectivas

Cyber Town demonstra o enorme potencial da tecnologia de IA em jogos. NPCs em jogos tradicionais são limitados por árvores de diálogo e scripts predefinidos, enquanto NPCs de IA podem entender e gerar linguagem natural, tendo conversas reais com jogadores. Isto não apenas melhora a imersão do jogo, mas também traz novas possibilidades ao design de jogos.

No entanto, NPCs de IA também enfrentam alguns desafios. Primeiro está a questão de custo - cada conversa requer chamar a API LLM, o que incorre em certas taxas. Para grandes jogos multiplayer online, este custo poderia ser muito alto. Segundo está a questão de latência - a inferência de LLM leva tempo, e se a latência de rede for alta, jogadores podem precisar esperar vários segundos para ver respostas de NPC. Finalmente, há a questão de controle de conteúdo - conteúdo gerado por LLM pode não ser totalmente controlável, requerendo prompts bem projetados e mecanismos de filtragem de conteúdo.

Apesar desses desafios, o futuro dos NPCs de IA permanece cheio de promessas. À medida que a tecnologia LLM se desenvolve, a velocidade de inferência se tornará mais rápida e os custos se tornarão menores. LLMs pequenos localizados também estão se desenvolvendo rapidamente - no futuro, eles podem ser capazes de executar diretamente nos dispositivos dos jogadores, não requerendo solicitações de rede. A combinação de tecnologia de IA e jogos trará aos jogadores experiências sem precedentes.

No capítulo de projeto de graduação da Parte 5, aprenderemos como construir agentes gerais usando agentes únicos e multi-agentes - este será seu tempo criativo, então fique ligado!
