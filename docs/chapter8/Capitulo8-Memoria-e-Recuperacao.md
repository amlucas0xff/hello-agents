# Capítulo 8 Memória e Recuperação

<div align="right">
  <a href="./Chapter8-Memory-and-Retrieval.md">English</a> | <a href="./第八章%20内存与检索.md">中文</a> | Português
</div>

Este capítulo apresentará sistemas de memória e mecanismos de recuperação de agentes em profundidade. Abordaremos os desafios inerentes aos agentes sem estado, exploraremos como construir sistemas de memória de múltiplas camadas (working memory, episodic memory, semantic memory) e entenderemos como os agentes podem adquirir capacidades de raciocínio de longo prazo através de RAG (Retrieval-Augmented Generation).

Para permitir que os leitores experimentem rapidamente a funcionalidade completa deste capítulo, fornecemos um pacote Python diretamente instalável. Você pode instalar a versão correspondente a este capítulo com o seguinte comando:

```bash
pip install "hello-agents[all]==0.2.7"
```

Este capítulo adicionará principalmente os seguintes componentes ao framework HelloAgents:

- **MemoryTool** (`hello_agents/tools/builtin/memory_tool.py`): Ferramenta de gerenciamento de memória de múltiplas camadas
- **RAGTool** (`hello_agents/tools/builtin/rag_tool.py`): Ferramenta de recuperação aumentada
- **VectorStore** (`hello_agents/rag/vector_store.py`): Sistema de banco de vetores
- **Embedder** (`hello_agents/rag/embedder.py`): Módulo de geração de embeddings

Além de instalar o framework, você também precisa configurar a API LLM no arquivo `.env`. Os exemplos deste capítulo utilizam principalmente grandes modelos de linguagem para recuperação e gerenciamento de memória.

Após concluir a configuração, você pode começar a jornada de aprendizagem deste capítulo!

## 8.1 Por Que os Agentes Precisam de Memória

### 8.1.1 Limitações dos Agentes Sem Estado

No capítulo anterior, construímos agentes básicos como SimpleAgent e ReActAgent. Embora eles possam concluir tarefas únicas de forma eficaz, existe uma limitação fundamental: **eles são sem estado**. Agentes sem estado não podem lembrar informações de interações passadas e não podem aprender e evoluir através da acumulação de experiência.

Considere o seguinte cenário de conversação:

```python
agent = SimpleAgent(name="Assistente", llm=llm)

# Rodada 1
response1 = agent.run("Meu nome é Alice")
# Agent: "Olá Alice, como posso ajudá-la?"

# Rodada 2
response2 = agent.run("Qual é o meu nome?")
# Agent: "Desculpe, não sei o seu nome."
```

Este problema surge porque cada vez que o método `run` é executado, o agente processa apenas a entrada atual, sem informações de contexto sobre interações anteriores. Este é o dilema básico dos agentes sem estado.

### 8.1.2 Arquitetura de Sistema de Memória de Múltiplas Camadas

Para permitir que os agentes tenham capacidades de "memória" similares às humanas, precisamos projetar um sistema de memória em camadas. Com base em pesquisas da ciência cognitiva, dividimos a memória do agente em três níveis:

1. **Working Memory (Memória de Trabalho)**: Memória de curto prazo que armazena informações da conversa atual, similar à memória de trabalho humana
2. **Episodic Memory (Memória Episódica)**: Registra eventos históricos específicos e cenas de interação, similar à memória episódica humana
3. **Semantic Memory (Memória Semântica)**: Extrai e armazena conceitos, regras e conhecimento de longo prazo, similar à memória semântica humana

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/8-figures/8-1.png" alt="" width="85%"/>
  <p>Figura 8.1 Arquitetura de sistema de memória de múltiplas camadas</p>
</div>

## 8.2 Implementando MemoryTool

O MemoryTool é a implementação central do sistema de memória em HelloAgents. Ele integra três tipos de memória e fornece uma interface unificada para gerenciamento de memória.

### 8.2.1 Definição da Estrutura de Dados de Memória

Primeiro, definimos uma classe `Memory` para representar uma única unidade de memória:

```python
from dataclasses import dataclass
from datetime import datetime
from typing import Optional, Dict, Any, Literal

MemoryType = Literal["working", "episodic", "semantic"]

@dataclass
class Memory:
    """Estrutura de dados de memória"""
    content: str
    memory_type: MemoryType
    timestamp: datetime
    importance: float = 0.5
    metadata: Optional[Dict[str, Any]] = None

    def to_dict(self) -> Dict[str, Any]:
        """Converter para dicionário"""
        return {
            "content": self.content,
            "memory_type": self.memory_type,
            "timestamp": self.timestamp.isoformat(),
            "importance": self.importance,
            "metadata": self.metadata or {}
        }
```

Esta estrutura de dados contém vários campos chave:
- `content`: Conteúdo real da memória
- `memory_type`: Tipo de memória (working/episodic/semantic)
- `timestamp`: Timestamp de criação
- `importance`: Score de importância (0-1), usado para estratégia de esquecimento
- `metadata`: Metadados adicionais para armazenamento de informação estendida

### 8.2.2 Implementação Central do MemoryTool

```python
from hello_agents.tools.base import BaseTool
from typing import List, Dict, Any
import json

class MemoryTool(BaseTool):
    """Ferramenta de gerenciamento de memória de múltiplas camadas"""

    def __init__(self, user_id: str, max_working_memory: int = 10):
        super().__init__(
            name="memory",
            description="Gerenciar e recuperar memórias do agente (working, episodic, semantic)"
        )
        self.user_id = user_id
        self.max_working_memory = max_working_memory

        # Armazenamento de memória de múltiplas camadas
        self.working_memory: List[Memory] = []
        self.episodic_memory: List[Memory] = []
        self.semantic_memory: List[Memory] = []

    def execute(self, action: str, **kwargs) -> Any:
        """
        Executar operação de memória

        Args:
            action: Tipo de operação (add, search, get, clear)
            **kwargs: Parâmetros de operação
        """
        if action == "add":
            return self._add_memory(**kwargs)
        elif action == "search":
            return self._search_memory(**kwargs)
        elif action == "get":
            return self._get_memories(**kwargs)
        elif action == "clear":
            return self._clear_memory(**kwargs)
        else:
            raise ValueError(f"Operação não suportada: {action}")

    def _add_memory(
        self,
        content: str,
        memory_type: MemoryType = "working",
        importance: float = 0.5,
        metadata: Optional[Dict] = None
    ) -> str:
        """Adicionar memória"""
        memory = Memory(
            content=content,
            memory_type=memory_type,
            timestamp=datetime.now(),
            importance=importance,
            metadata=metadata
        )

        if memory_type == "working":
            self.working_memory.append(memory)
            # Estratégia FIFO para working memory
            if len(self.working_memory) > self.max_working_memory:
                # Promover memórias importantes para episódica
                old_memory = self.working_memory.pop(0)
                if old_memory.importance > 0.7:
                    self.episodic_memory.append(old_memory)

        elif memory_type == "episodic":
            self.episodic_memory.append(memory)

        elif memory_type == "semantic":
            self.semantic_memory.append(memory)

        return f"Memória adicionada: {memory_type}"

    def _search_memory(
        self,
        query: str,
        memory_type: Optional[MemoryType] = None,
        limit: int = 5,
        min_importance: float = 0.0
    ) -> List[Dict[str, Any]]:
        """
        Buscar memórias relevantes

        Args:
            query: Consulta de busca
            memory_type: Tipo de memória para buscar (None = buscar todas)
            limit: Número máximo de resultados
            min_importance: Score mínimo de importância
        """
        # Determinar pool de busca
        if memory_type:
            search_pool = self._get_memory_pool(memory_type)
        else:
            search_pool = (
                self.working_memory +
                self.episodic_memory +
                self.semantic_memory
            )

        # Filtrar por importância
        filtered = [
            m for m in search_pool
            if m.importance >= min_importance
        ]

        # Busca simples baseada em palavras-chave (pode ser estendida para busca vetorial)
        results = []
        query_lower = query.lower()

        for memory in filtered:
            if query_lower in memory.content.lower():
                results.append(memory.to_dict())

        # Ordenar por importância e recenticidade
        results.sort(
            key=lambda x: (x['importance'], x['timestamp']),
            reverse=True
        )

        return results[:limit]
```

### 8.2.3 Caso de Uso: Agente com Memória

Agora vamos criar um agente com capacidades de memória:

```python
from hello_agents import SimpleAgent, HelloAgentsLLM
from hello_agents.tools import MemoryTool
from dotenv import load_dotenv

load_dotenv()

# Criar ferramenta de memória
memory_tool = MemoryTool(user_id="alice")

# Criar agente
agent = SimpleAgent(
    name="Assistente com Memória",
    llm=HelloAgentsLLM(),
    system_prompt="""Você é um assistente útil com memória.
    Use a ferramenta de memória para lembrar informações importantes do usuário."""
)

# Adicionar ferramenta de memória
agent.add_tool(memory_tool)

# Rodada 1: Usuário compartilha informação
response1 = agent.run("Meu nome é Alice e eu gosto de programação Python")
# Agent automaticamente salva na memória através da ferramenta

# Rodada 2: Recuperar informação
response2 = agent.run("Qual é o meu nome e o que eu gosto?")
# Agent: "Seu nome é Alice e você gosta de programação Python."
```

## 8.3 Implementando RAGTool

RAG (Retrieval-Augmented Generation) é uma técnica que aprimora a geração de LLM através de recuperação de conhecimento externo. É especialmente adequado para cenários que requerem conhecimento profissional de domínio ou dados atualizados em tempo real.

### 8.3.1 Princípios Básicos de RAG

O fluxo de trabalho de RAG consiste em três etapas principais:

1. **Embedding (Vetorização)**: Converter documentos de texto em vetores densos de alta dimensão
2. **Indexing (Indexação)**: Armazenar vetores em um banco de vetores para busca eficiente
3. **Retrieval (Recuperação)**: Quando há uma consulta, encontrar os K documentos mais similares através de busca de similaridade vetorial

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/8-figures/8-2.png" alt="" width="85%"/>
  <p>Figura 8.2 Fluxo de trabalho RAG</p>
</div>

### 8.3.2 Implementando VectorStore

Primeiro implementamos um banco de vetores simples baseado em busca linear:

```python
import numpy as np
from typing import List, Tuple, Dict, Any
import json
import os

class VectorStore:
    """Banco de vetores simples"""

    def __init__(self, dimension: int = 768):
        self.dimension = dimension
        self.vectors: np.ndarray = np.empty((0, dimension))
        self.metadata: List[Dict[str, Any]] = []

    def add(
        self,
        vectors: np.ndarray,
        metadata: List[Dict[str, Any]]
    ):
        """Adicionar vetores ao banco"""
        if vectors.shape[0] != len(metadata):
            raise ValueError("Número de vetores deve corresponder aos metadados")

        self.vectors = np.vstack([self.vectors, vectors])
        self.metadata.extend(metadata)

    def search(
        self,
        query_vector: np.ndarray,
        k: int = 5,
        min_score: float = 0.0
    ) -> List[Tuple[Dict[str, Any], float]]:
        """
        Buscar top-k vetores mais similares

        Args:
            query_vector: Vetor de consulta
            k: Número de resultados
            min_score: Score mínimo de similaridade

        Returns:
            Lista de (metadata, score de similaridade)
        """
        if len(self.vectors) == 0:
            return []

        # Calcular similaridade de cosseno
        similarities = self._cosine_similarity(
            query_vector.reshape(1, -1),
            self.vectors
        )[0]

        # Obter top-k índices
        top_k_indices = np.argsort(similarities)[-k:][::-1]

        # Filtrar por score mínimo
        results = []
        for idx in top_k_indices:
            score = float(similarities[idx])
            if score >= min_score:
                results.append((self.metadata[idx], score))

        return results

    @staticmethod
    def _cosine_similarity(a: np.ndarray, b: np.ndarray) -> np.ndarray:
        """Calcular similaridade de cosseno"""
        a_norm = a / (np.linalg.norm(a, axis=1, keepdims=True) + 1e-10)
        b_norm = b / (np.linalg.norm(b, axis=1, keepdims=True) + 1e-10)
        return np.dot(a_norm, b_norm.T)

    def save(self, path: str):
        """Salvar banco de vetores"""
        np.save(f"{path}_vectors.npy", self.vectors)
        with open(f"{path}_metadata.json", 'w') as f:
            json.dump(self.metadata, f)

    @classmethod
    def load(cls, path: str) -> "VectorStore":
        """Carregar banco de vetores"""
        vectors = np.load(f"{path}_vectors.npy")
        with open(f"{path}_metadata.json", 'r') as f:
            metadata = json.load(f)

        store = cls(dimension=vectors.shape[1])
        store.vectors = vectors
        store.metadata = metadata
        return store
```

### 8.3.3 Implementação do RAGTool

```python
from hello_agents.tools.base import BaseTool
from hello_agents.rag import VectorStore, Embedder
from typing import List, Dict, Any

class RAGTool(BaseTool):
    """Ferramenta de recuperação aumentada"""

    def __init__(
        self,
        knowledge_base_path: str,
        embedder: Optional[Embedder] = None
    ):
        super().__init__(
            name="rag",
            description="Recuperar conhecimento relevante da base de conhecimento"
        )
        self.knowledge_base_path = knowledge_base_path
        self.embedder = embedder or Embedder()

        # Carregar ou criar banco de vetores
        if os.path.exists(f"{knowledge_base_path}_vectors.npy"):
            self.vector_store = VectorStore.load(knowledge_base_path)
        else:
            self.vector_store = VectorStore()

    def execute(self, action: str, **kwargs) -> Any:
        """Executar operação RAG"""
        if action == "add":
            return self._add_documents(**kwargs)
        elif action == "search":
            return self._search(**kwargs)
        elif action == "save":
            return self._save()
        else:
            raise ValueError(f"Operação não suportada: {action}")

    def _add_documents(
        self,
        documents: List[str],
        metadata: Optional[List[Dict]] = None
    ) -> str:
        """Adicionar documentos à base de conhecimento"""
        if metadata is None:
            metadata = [{"text": doc} for doc in documents]

        # Gerar embeddings
        embeddings = self.embedder.embed(documents)

        # Adicionar ao banco de vetores
        self.vector_store.add(embeddings, metadata)

        return f"Adicionados {len(documents)} documentos"

    def _search(
        self,
        query: str,
        k: int = 3,
        min_score: float = 0.5
    ) -> List[Dict[str, Any]]:
        """Buscar documentos relevantes"""
        # Gerar vetor de consulta
        query_vector = self.embedder.embed([query])[0]

        # Buscar no banco de vetores
        results = self.vector_store.search(
            query_vector,
            k=k,
            min_score=min_score
        )

        return [
            {
                "content": meta.get("text", ""),
                "score": score,
                "metadata": meta
            }
            for meta, score in results
        ]

    def _save(self) -> str:
        """Salvar base de conhecimento"""
        self.vector_store.save(self.knowledge_base_path)
        return "Base de conhecimento salva"
```

### 8.3.4 Caso de Uso: Agente Aprimorado com RAG

```python
from hello_agents import SimpleAgent, HelloAgentsLLM
from hello_agents.tools import RAGTool
from dotenv import load_dotenv

load_dotenv()

# Criar ferramenta RAG
rag_tool = RAGTool(knowledge_base_path="./knowledge_base")

# Adicionar documentos de conhecimento
rag_tool.execute(
    "add",
    documents=[
        "Python é uma linguagem de programação de alto nível interpretada.",
        "Machine Learning é um subcampo da inteligência artificial.",
        "RAG significa Retrieval-Augmented Generation."
    ]
)

# Salvar base de conhecimento
rag_tool.execute("save")

# Criar agente
agent = SimpleAgent(
    name="Assistente de Conhecimento",
    llm=HelloAgentsLLM(),
    system_prompt="""Você é um assistente de conhecimento.
    Use a ferramenta RAG para recuperar informação relevante antes de responder."""
)

agent.add_tool(rag_tool)

# Consulta
response = agent.run("O que é RAG?")
# Agent recuperará "RAG significa Retrieval-Augmented Generation" e fornecerá uma resposta contextualizada
```

## 8.4 Resumo do Capítulo

Neste capítulo, exploramos sistemas de memória e mecanismos de recuperação para agentes:

### Principais Conquistas

1. **Sistema de Memória de Múltiplas Camadas**: Implementamos MemoryTool suportando três tipos de memória (working, episodic, semantic)
2. **Capacidades RAG**: Implementamos RAGTool para recuperação de conhecimento baseada em vetores
3. **Integração com Framework**: Ferramentas integradas perfeitamente com HelloAgents através da interface BaseTool

### Conceitos Centrais

- **Working Memory**: Memória de curto prazo para contexto de conversação atual
- **Episodic Memory**: Memória de longo prazo para eventos históricos específicos
- **Semantic Memory**: Base de conhecimento para conceitos e regras gerais
- **RAG**: Técnica de recuperação aumentada combinando busca de conhecimento com geração

### Próximos Passos

No próximo capítulo, exploraremos **Engenharia de Contexto**, onde aprenderemos como otimizar e gerenciar contexto do agente para melhorar desempenho e eficiência em tarefas de longo horizonte.

## Exercícios

1. Estenda MemoryTool para implementar uma estratégia de "esquecimento" baseada em importância e tempo
2. Implemente busca de similaridade vetorial usando FAISS ou outra biblioteca de banco de vetores
3. Crie um agente que combine MemoryTool e RAGTool para construir um assistente de aprendizagem personalizado
4. Projete uma estratégia de promoção de memória que converta automaticamente working memory importante em semantic memory

## Referências

[1] Weng, L. (2023). LLM Powered Autonomous Agents. Lil'Log. https://lilianweng.github.io/posts/2023-06-23-agent/

[2] Lewis, P., et al. (2020). Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks. NeurIPS.

[3] Significant Gravitas. AutoGPT Memory System. https://github.com/Significant-Gravitas/AutoGPT
