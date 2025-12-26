<div align="right">
  <a href="./README.md">中文</a> | Português
</div>

# Agente de RPG Imersivo (Versão Python)

Este é um agente de RPG (Role-Playing Game) imersivo implementado em Python, permitindo aos usuários conversar com personagens personalizados. Suporta vários modelos compatíveis com o formato de API OpenAI.

## Características

- 🎭 Configuração de personagens altamente personalizável
- 🧠 Suporta vários modelos de IA (compatíveis com formato de API OpenAI)
- 📝 Suporta configuração detalhada de nome de personagem, obra de origem, traços de personalidade, etc.
- 💬 Experiência de diálogo imersiva
- 🔁 Suporta alternância entre múltiplos personagens

## Requisitos de Ambiente

- Python 3.8 ou superior
- Chave de API de serviço de modelo que suporta formato de API OpenAI

## Etapas de Instalação

1. Clone ou baixe este projeto localmente

2. Instale os pacotes de dependência:
   ```bash
   pip install -r requirements.txt
   ```

3. Configure informações da API:
   - Crie arquivo `.env` no diretório raiz do projeto
   - Adicione o seguinte conteúdo:
   ```
   LLM_API_KEY=sua_chave_api_real
   LLM_MODEL_ID=ID_do_modelo (exemplo: gpt-3.5-turbo, claude-3-opus, etc.)
   LLM_BASE_URL=URL_base_da_API (opcional, não necessário se for OpenAI padrão)
   ```

## Modo de Uso

1. Execute o programa principal:
   ```bash
   python roleplay_agent.py
   ```

2. Digite informações do personagem conforme solicitado:
   - Nome do personagem
   - Obra de origem
   - Personalidade e características
   - Fala de abertura (opcional)

3. Comece a conversar com o personagem:
   - Digite mensagens diretamente para interagir com o personagem
   - Digite `quit` ou `exit` para sair do programa
   - Digite `new` para começar novo personagem
   - Digite `reset` para redefinir a conversa atual

## Serviços de Modelo Suportados

Esta aplicação é compatível com todos os serviços de modelo que suportam formato de API OpenAI, por exemplo:
- Série OpenAI GPT
- Azure OpenAI
- Anthropic Claude (através de camada de compatibilidade)
- Modelos auto-hospedados (como Ollama, LocalAI, etc.)
- E outros serviços de modelo compatíveis com formato de API OpenAI

## Exemplo de Diálogo

```
🎭 Bem-vindo ao Agente de RPG Imersivo!
Primeiro vamos configurar um personagem...

Digite o nome do personagem (exemplo: Sun Wukong): Sun Wukong
Digite a obra de origem do personagem (exemplo: Jornada ao Oeste): Jornada ao Oeste
Digite a personalidade e características do personagem (exemplo: indomável, inteligente e corajoso, odeia o mal...): Grande Sábio Igualador do Céu, indomável, inteligente e corajoso, odeia o mal. Gosta de falar "eu, o Velho Sun", temperamento impaciente mas valoriza sentimentos e justiça. Possui olhos de fogo dourado, despreza as formalidades dos mortais.
Digite a fala de abertura (opcional, pressione Enter para usar o padrão): Ei! De onde vem este pequeno demônio, ouse dar seu nome ao encontrar o Velho Sun?

✅ Personagem inicializado com sucesso: Sun Wukong (de Jornada ao Oeste)
💡 Sun Wukong: Ei! De onde vem este pequeno demônio, ouse dar seu nome ao encontrar o Velho Sun?

==================================================
Comece a conversar! Digite 'quit' ou 'exit' para sair, digite 'new' para começar novo personagem.
==================================================

Você: Olá, Grande Sábio!
Sun Wukong: *Balançou seu cajado dourado, estreitou os olhos de fogo dourado examinando você* Hmm! Vejo que tem certa coragem ao cumprimentar o Velho Sun. Diga! Quem é você? O que está fazendo aqui na Montanha das Flores e Frutas? Eu, o Velho Sun, estava procurando alguém para praticar artes marciais comigo!
```

## Explicação de Configuração

- **LLM_API_KEY**: Sua chave de API do serviço de modelo de IA
- **LLM_MODEL_ID**: O ID do modelo a ser usado (exemplo: gpt-4, claude-3-opus, etc.)
- **LLM_BASE_URL**: URL base do serviço de API (necessário se usar serviço não-padrão OpenAI)

## Observações

- Certifique-se de que sua chave de API é válida e tem permissões de uso correspondentes
- O conteúdo gerado por IA pode conter informações fictícias, trate racionalmente
- Use a API de forma razoável, atenção aos limites de cota
- De acordo com o serviço de modelo escolhido, pode ser necessário ajustar parâmetros como `temperature` para obter melhores resultados

## Stack Tecnológica

- Python 3.8+
- OpenAI Python SDK
- python-dotenv (gerenciamento de variáveis de ambiente)

## Licença

Este projeto é apenas para fins de aprendizado e pesquisa.
