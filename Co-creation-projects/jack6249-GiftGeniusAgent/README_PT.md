<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# 🎁 GiftGenius: Assistente Inteligente de Presentes

Sistema de colaboração multi-agente baseado no framework HelloAgents, fornecendo recomendações de presentes precisas e atenciosas.

## 📝 Introdução ao Projeto

GiftGenius é um agente de recomendação de presentes inteligente, projetado para resolver o problema milenar de "que presente dar". Não é apenas uma ferramenta simples de busca por palavras-chave, mas um pipeline multi-agente (Multi-Agent Pipeline) que simula o processo de tomada de decisão humana.

Através da colaboração dividida em Estrategista (formulação de estratégia) -> Caçador (busca em toda a web) -> Editor (limpeza de dados e criação de conteúdo), ele pode buscar as informações mais recentes de produtos de toda a web com base no perfil personalizado do usuário (MBTI, signo do zodíaco, orçamento, etc.) e gerar um guia de presentes ilustrado com preços transparentes.

- Que problema resolve?

   Resolve a dificuldade de escolha ao dar presentes, e problemas como recomendações de produtos desatualizadas, preços acima do orçamento e textos entediantes.

- Quais recursos especiais possui?

   Suporta análise psicológica de MBTI/signo do zodíaco, comparação automática de preços e busca de alternativas, extração de dados anti-alucinação.

- Para quais cenários é adequado?

  Cenários que requerem recomendações personalizadas, como presentes de feriados, surpresas de aniversário, planejamento de datas comemorativas, etc.

## ✨ Recursos Principais

- [x] Análise precisa de perfil: Baseado em dimensões como personalidade MBTI, signo do zodíaco, idade, etc., analisa profundamente as preferências potenciais do destinatário e formula estratégias de busca personalizadas.

- [x] Controle inteligente de orçamento: Suporta faixas de orçamento personalizadas (como "500-1000 yuan"), e possui mecanismo de "guardião de preços", interceptando automaticamente produtos acima do orçamento e acionando busca degradada (encontrar alternativas).

- [x]  Busca online em tempo real: Utiliza o motor de busca Tavily para obter informações, preços e imagens de produtos mais recentes de 2025, recusando recomendações desatualizadas.

- [x] Relatório visualizado: Gera finalmente uma tabela Markdown contendo imagens de produtos, referências de preços e textos persuasivos, intuitiva e fácil de ler.

## 🛠️ Stack Tecnológica

- Framework: HelloAgents

- Paradigma de Agente: Usa SimpleAgent do framework HelloAgent

- Ferramentas e API:

  Tavily Search API (para busca online), Baidu Youxuan MCP (para busca online)

- Outras dependências: `mcp`, `nest_asyncio`, `python-dotenv`, `numpy`

## 🚀 Início Rápido

### Requisitos de Ambiente

Python 3.10+

Jupyter Notebook / Jupyter Lab

### Instalação de Dependências

```bash
pip install -r requirements.txt
```

### Configuração de Chaves de API

Copie o template do arquivo de configuração:

```bash
# Crie o arquivo .env
cp .env.example .env
# Edite o arquivo .env, preenchendo suas chaves de API
```



### Executar o Projeto

Modifique o arquivo user_profile.json, preenchendo as informações do destinatário do presente (como MBTI, orçamento, etc.).

Inicie o Jupyter Notebook:

```bash
jupyter notebook main.ipynb
```
O projeto usa por padrão o Baidu Youxuan MCP, pode ser modificado para Tavily Search API.
```py
# Configuração de fonte de busca
# Valores possíveis: "tavily" (geral/internacional) ou "baidu" (e-commerce/nacional)
os.environ["SEARCH_PROVIDER"] = "baidu"
```


Clique em "Run All" para executar todas as células, o resultado final será gerado em outputs/gift_plan_output.md.

## 📖 Exemplo de Uso

Configuração de entrada (user_profile.json):

```json
{
    "性别": "男",
    "年龄": "24岁",
    "MBTI": "ISTJ",
    "星座": "白羊座",
    "预算": "200-500",
    "节日": "生日",
    "自定义": "喜欢数码"
}
```

Resultado da execução (final_gift_plan.md):

![example](https://github.com/datawhalechina/hello-agents/blob/main/Co-creation-projects/jack6249-GiftGeniusAgent/example.png)

## 🎯 Destaques do Projeto

- Arquitetura Dual-Stream: Divide "busca de dados duros" (encontrar preços) e "geração de conteúdo suave" (encontrar pontos de venda) em dois pipelines paralelos, reduzindo drasticamente a interferência de contexto e melhorando a qualidade do conteúdo.

- Guardrails Baseados em Código (Code-based Guardrails): Não depende do LLM para gerar JSON diretamente, mas extrai forçadamente preços e imagens dos resultados de busca através de expressões regulares em Python, eliminando pela raiz a alucinação de "inventar preços".

- Correção Dinâmica de Estratégia (Feedback Loop): Implementa mecanismo de "guardião de preços". Se o preço médio dos produtos encontrados exceder o orçamento, reaciona o "estrategista" para formular estratégia de "alternativa" até encontrar produtos adequados.

- Suporte a múltiplas fontes de dados: Integra duas fontes de busca: Baidu Youxuan MCP e Tavily Search API

## 🔮 Planos Futuros

- [ ] Interação Frontend: Adicionar página frontend para melhor experiência de interação do usuário

- [ ] Integração Profunda de Fonte de Dados: Integração completa da interface de comparação de preços e histórico de preços do Baidu Youxuan MCP, obtendo informações de preços e estoque em tempo real mais precisas, realizando função de "comparação de preços em toda a web".

- [ ] Enriquecer Opções: Aumentar mais opções de preferências pessoais, como tipos de produtos favoritos, marcas, etc.


🤝 Guia de Contribuição

Issues e Pull Requests são bem-vindos! Se você tiver melhores técnicas de otimização de Prompt ou ideias de novos padrões de Agente, sinta-se à vontade para compartilhar.

📄 Licença

MIT License

👤 Autor

GitHub: [@jack6249](https://github.com/jack6249)

🙏 Agradecimentos

Agradecimentos à comunidade Datawhale e ao projeto Hello-Agents pelo excelente framework e suporte tutorial!
