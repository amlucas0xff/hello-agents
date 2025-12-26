<div align="right">
  <a href="./README.md">中文</a> | <a href="./README_EN.md">English</a> | Português
</div>

# InnoCore AI - Núcleo de Pesquisa e Inovação

<div align="center">

**Assistente Inteligente de Inovação em Pesquisa | Intelligent Research Innovation Assistant**

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-green.svg)](https://fastapi.tiangolo.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

*Sistema de Automação de Fluxo Completo de Pesquisa Baseado em Colaboração Multi-Agente*

*Construído no framework HelloAgent, suporta alternância flexível de LLM*

</div>

---

## 📖 Visão Geral do Projeto

InnoCore AI (Núcleo de Pesquisa e Inovação) é um sistema assistente inteligente de inovação em pesquisa construído no framework HelloAgent. Através da colaboração multi-agente, automatiza todo o fluxo de pesquisa desde busca de artigos, análise aprofundada, assistência de escrita até verificação de citações.

### Características Principais

- 🤖 **Colaboração Multi-Agente**: quatro agentes principais (Hunter/Miner/Coach/Validator) trabalham em conjunto
- 🔄 **Suporte Dual-Mode**: modo individual (controle refinado) + modo coordenado (conclusão com um clique)
- 📚 **Análise Inteligente de Artigos**: analisa automaticamente PDFs, extrai informações-chave, gera relatórios de análise aprofundada
- ✍️ **Assistente de Escrita IA**: polimento acadêmico, conversão de estilo, sugestões de escrita em tempo real
- 🔍 **Verificação Inteligente de Citações**: identifica automaticamente DOI/ArXiv ID, gera citações em múltiplos formatos
- 🎯 **Automação de Fluxo de Trabalho**: conclusão com um clique do fluxo completo de busca → análise → citação → relatório

### Destaques Técnicos

- **Análise Aprofundada de PDF**: suporta extração estruturada de artigos acadêmicos (título, autor, resumo, texto completo)
- **Recuperação Híbrida**: recuperação vetorial + correspondência de palavras-chave, melhora precisão de recuperação
- **Saída em Streaming**: transmissão em tempo real via WebSocket, proporciona experiência de interação fluida
- **Arquitetura Assíncrona**: baseada no framework assíncrono FastAPI, processamento concorrente de alto desempenho
- **Design Modular**: arquitetura em camadas clara, fácil de expandir e manter

## 🎯 Cenários de Aplicação

### Para Quem é Adequado?

- 📖 **Estudantes de Pós-graduação/Doutorado**: compreender rapidamente áreas de pesquisa, auxiliar na escrita de artigos
- 👨‍🏫 **Professores Universitários**: acompanhar os mais recentes avanços em pesquisa, auxiliar na solicitação de projetos
- 🔬 **Pessoal de P&D Empresarial**: pesquisa tecnológica, análise de patentes, pesquisa competitiva
- 📝 **Escritores Acadêmicos**: polimento de artigos, gerenciamento de citações, verificação de formatação

### Cenários de Uso Típicos

1. **Revisão de Literatura**: busca automática de artigos relacionados → análise em lote → geração de relatório de revisão
2. **Escrita de Artigos**: sugestões de polimento em tempo real → geração automática de citações → verificação de formatação
3. **Pesquisa Investigativa**: rastreamento de tópicos específicos → mineração de pontos inovadores → sugestões de direções de pesquisa
4. **Tradução Acadêmica**: tradução chinês-inglês → otimização de expressões acadêmicas → padronização de terminologia

## 🏗️ Arquitetura do Sistema

### Arquitetura Geral

```
┌─────────────────────────────────────────────────────────┐
│                    Camada de Interface Frontend          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
│  │ Busca    │  │ Análise  │  │ Assistente│  │ Gerenciam│ │
│  │ Artigos  │  │Aprofundada│  │ Escrita  │  │ Citações │ │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                   Camada de API                          │
│  FastAPI + WebSocket + RESTful API                      │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                 Camada de Orquestração de Agentes        │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
│  │ 🕵️Hunter │  │ 🧠 Miner│  │ ✍️ Coach│  │ 🔎 Validator│ │
│  │ Busca    │  │ Análise  │  │ Assistente│  │ Verificação│ │
│  │ Artigos  │  │Aprofundada│  │ Escrita  │  │ Citações  │ │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                   Camada de Serviços Core                │
│  Análise PDF | Recuperação Vetorial | Chamadas LLM | Fila de Tarefas │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                   Camada de Persistência de Dados        │
│  PostgreSQL | Qdrant | Redis | Armazenamento de Arquivos│
└─────────────────────────────────────────────────────────┘
```

### Quatro Agentes Principais

| Agente | Responsabilidade | Capacidades Principais |
|--------|------|----------|
| 🕵️ **Hunter** | Busca e Monitoramento de Artigos | Busca em tempo real ArXiv/IEEE, filtragem inteligente, download automático |
| 🧠 **Miner** | Análise Aprofundada e Mineração | Análise PDF, extração de pontos inovadores, análise comparativa, geração de relatórios |
| ✍️ **Coach** | Assistência e Polimento de Escrita | Polimento acadêmico, conversão de estilo, sugestões em tempo real, otimização de terminologia |
| 🔎 **Validator** | Verificação e Formatação de Citações | Validação DOI, geração em múltiplos formatos, verificação de metadados, padronização |

## Início Rápido

### 1. Instalação

```bash
# Instalar dependências principais
python install.py

# Ou instalar manualmente
pip install fastapi uvicorn python-multipart python-dotenv pydantic httpx requests
```

### 2. Configuração

Criar arquivo `.env`:
```bash
cp .env.example .env
# Editar arquivo .env e adicionar sua chave API OpenAI
```

### 3. Executar Aplicação

```bash
python run.py
```

### 4. Acessar

- Aplicação Principal: http://localhost:8000
- Documentação API: http://localhost:8000/docs
- Verificação de Saúde: http://localhost:8000/health

## Funcionalidades

### Modos de Trabalho

- **Modo Individual**: usar cada agente independentemente para tarefas específicas
- **Modo Workflow** ⭐: fluxo de trabalho completo automatizado coordenando todos os agentes

### Agentes

- 🕵️ **Hunter Agent**: busca e monitoramento de literatura
- 🧠 **Miner Agent**: análise aprofundada de artigos e extração de insights
- ✍️ **Coach Agent**: assistência de escrita e melhoria de estilo
- 🔎 **Validator Agent**: verificação e formatação de citações

### Automação de Fluxo de Trabalho

Fluxo de trabalho de pesquisa completo em um clique:
1. Buscar artigos (Hunter)
2. Analisar conteúdo (Miner)
3. Gerar citações (Validator)
4. Criar relatório (Coach)

## Estrutura do Projeto

```
innocore_ai/
├── agents/          # Agentes IA
├── api/            # Rotas da API REST
├── core/           # Funcionalidade principal
├── models/         # Modelos de dados
├── services/       # Lógica de negócio
├── utils/          # Utilitários
├── frontend/       # Interface Web
├── main.py         # Entrada principal da aplicação
├── run.py          # Script de execução simples
├── install.py      # Script de instalação
└── requirements-core.txt  # Dependências principais
```

## Requisitos

- Python 3.8+
- Chave API OpenAI
- Redis (opcional, para cache)

## Desenvolvimento

```bash
# Instalar dependências de desenvolvimento
pip install -r requirements.txt

# Executar com auto-reload
python run.py
```

## Efeitos de Demonstração

### Interface Principal - Alternância Dual-Mode
![Interface Principal](docs/screenshots/01-主界面.png)

### Funcionalidade de Busca de Artigos
![Busca de Artigos](docs/screenshots/02-论文搜索.png)

### Funcionalidade de Análise Aprofundada
![Análise de Artigos](docs/screenshots/03-论文分析.png)

## 📊 Métricas de Desempenho

- **Busca de Artigos**: ~5 segundos (tempo de resposta API ArXiv)
- **Análise PDF**: ~3 segundos/artigo (artigo acadêmico padrão)
- **Análise Aprofundada**: ~20 segundos/artigo (incluindo inferência IA)
- **Polimento de Escrita**: ~2 segundos para primeira palavra gerada (saída em streaming)
- **Verificação de Citações**: ~3 segundos/citação (incluindo validação API externa)
- **Fluxo de Trabalho Completo**: ~70 segundos (buscar 3 artigos + análise + citação + relatório)

## 🛣️ Roadmap de Desenvolvimento

### v1.0 (Versão Atual) ✅
- [x] Funcionalidades básicas dos quatro agentes
- [x] Análise aprofundada de PDF
- [x] Fluxo de trabalho dual-mode
- [x] Interface Web
- [x] Documentação API

### v1.1 (Planejado)
- [ ] Integração de banco de dados vetorial (Qdrant)
- [ ] Sistema de usuários e gerenciamento de permissões
- [ ] Histórico e funcionalidade de favoritos
- [ ] Otimização de processamento em lote

### v2.0 (Futuro)
- [ ] Base de conhecimento em duas camadas (L1 pré-definida + L2 privada)
- [ ] Aprendizado de estilo de escrita personalizado
- [ ] Suporte multilíngue
- [ ] Adaptação mobile

## 🤝 Guia de Contribuição

Bem-vindo a contribuir com código, reportar problemas ou fazer sugestões!

1. Fazer Fork deste repositório
2. Criar branch de funcionalidade (`git checkout -b feature/AmazingFeature`)
3. Fazer commit das alterações (`git commit -m 'Add some AmazingFeature'`)
4. Fazer push para o branch (`git push origin feature/AmazingFeature`)
5. Abrir Pull Request

## 📄 Licença

Este projeto adota a licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes

## 🙏 Agradecimentos

- [HelloAgent](https://github.com/datawhalechina/hello-agents) - Framework multi-agente
- [FastAPI](https://fastapi.tiangolo.com/) - Framework Web moderno
- [ArXiv API](https://arxiv.org/help/api) - Fonte de dados de artigos acadêmicos

## 📮 Informações de Contato

- Página do Projeto: [GitHub](https://github.com/A-pricity/innocore-ai)
- Feedback de Problemas: [Issues](https://github.com/A-pricity/innocore-ai/issues)
- E-mail: 2827867731@qq.com

---

<div align="center">

**Se este projeto foi útil para você, por favor dê uma ⭐️ Star!**

Feito com dedicação pela Equipe InnoCore AI

</div>
