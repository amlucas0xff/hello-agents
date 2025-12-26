<div align="right">
  <a href="./Extra06-GUIAgent科普与实战.md">中文</a> | Português
</div>

# GUI Agent Teoria e Prática - Jornada de Exploração da Próxima Geração de Interação Humano-Computador

## Introdução: Quando IA Aprende a "Ver" a Tela

Imagine este cenário: você diz ao seu celular "Ajude-me a reservar uma passagem de trem de alta velocidade para Shanghai amanhã, segunda classe, saindo por volta das 10 da manhã", e então a IA abre automaticamente o APP 12306, preenche local de partida, destino e data, filtra trens que atendem aos critérios, completa a reserva e o pagamento - todo o processo sem você precisar operar manualmente, a IA age como um assistente real, "vendo" a tela, "entendendo" a interface, "clicando" em botões.

Isso não é ficção científica, mas a realidade que **GUI Agent (Agente de Interface Gráfica do Usuário)** está realizando.

Nas últimas duas décadas, a solução mainstream para automação empresarial tem sido **RPA (Automação Robótica de Processos)**. No entanto, RPA tem uma fraqueza fatal: depende de seletores fixos de elementos de UI, uma vez que a interface muda ligeiramente, os scripts falham. Esta fragilidade resulta em enormes custos de manutenção.

O surgimento do GUI Agent mudou completamente esta situação. Ele não simplesmente "reproduz" scripts predefinidos, mas como humanos, através de **percepção visual** entende o conteúdo da tela, através da **capacidade de raciocínio de modelos de linguagem grandes** planeja caminhos de operação, completando autonomamente tarefas em ambientes de software dinâmicos e desconhecidos.

Este capítulo levará você a conhecer profundamente os princípios técnicos do GUI Agent, e através de três casos práticos, permitirá que você realmente domine como usar e implantar esses sistemas de agentes de ponta.

## Parte Um: Conhecimento Técnico do GUI Agent

### 1.1 O que é GUI Agent?

**GUI Agent (Agente de Interface Gráfica do Usuário)** é uma classe de sistemas de IA capaz de entender e operar autonomamente interfaces gráficas. Diferente de chamadas tradicionais de API ou ferramentas de linha de comando, GUI Agent interage diretamente com interfaces gráficas usadas por humanos - sejam APPs de celular, software desktop ou aplicações web.

#### 1.1.1 Mudança de Paradigma de RPA para AI Agent

Vamos entender esta transformação através de uma comparação:

| Dimensão | RPA Tradicional | GUI Agent (AI Agent) |
| -------------- | ---------------------------------------- | ------------------------------------ |
| **Princípio de Funcionamento** | Reprodução de scripts baseada em seletores fixos (como XPath, ID) | Operação autônoma baseada em compreensão visual e raciocínio de modelo de linguagem |
| **Adaptabilidade** | Falha com mudanças de interface | Pode se adaptar a mudanças de interface, possui elasticidade semântica |
| **Planejamento de Tarefas** | Requer definição manual de cada etapa de operação | Decompõe tarefas autonomamente de acordo com instruções em linguagem natural |
| **Capacidade Multi-Plataforma** | Requer escrever scripts específicos para cada plataforma | Solução visual universal, naturalmente multi-plataforma |
| **Custo de Manutenção** | Extremamente alto (mudanças de UI requerem reescrita de scripts) | Baixo (modelo se adapta automaticamente) |

**Diferença Central**: RPA é "automação frágil", enquanto GUI Agent é "autonomia inteligente".

#### 1.1.2 Por que GUI Agent De Repente Ficou Popular?

A explosão do GUI Agent não foi acidental, mas resultado da maturação simultânea de múltiplos campos tecnológicos. Primeiro foi o avanço revolucionário em modelos multimodais grandes. A partir de GPT-4o, Claude 3.5 Sonnet, Qwen-VL, esses modelos não apenas entendem texto, mas podem "ver" imagens, fornecendo "olhos" poderosos para GUI Agent. Quando você alimenta uma captura de tela para esses modelos, eles podem identificar com precisão "este é um botão de login", "aqui há uma caixa de pesquisa", até entender layouts complexos de interface.

Mais crucial foi o avanço em capacidade de localização. Modelos visuais antigos eram como míopes - sabiam que havia um botão na tela, mas não conseguiam dizer exatamente onde estava. Enquanto modelos mais recentes (como GUI-Owl, Qwen-VL) passaram por treinamento especializado, podem gerar com precisão coordenadas de tela $(x, y)$ de elementos UI, permitindo que Agents não apenas "vejam", mas "cliquem com precisão".

Finalmente, foi a mudança qualitativa na capacidade de raciocínio. A capacidade de pensamento em cadeia (Chain of Thought) de modelos de linguagem grandes deu ao Agent um "cérebro". Ele pode decompor uma instrução vaga como "reservar uma passagem de trem de alta velocidade amanhã" em etapas específicas como "abrir APP → selecionar data → inserir local → filtrar trens → confirmar pagamento", e continuamente refletir e corrigir durante a execução.

### 1.2 Arquitetura Técnica Central do GUI Agent

Um sistema GUI Agent completo pode ser decomposto em três módulos centrais: **Percepção** → **Raciocínio** → **Execução**. Este é um sistema de tomada de decisão autônoma em loop fechado.

<div align="center">
  <img src="./images/Extra06-figures/image1.png" alt="Arquitetura de três camadas do GUI Agent" width="90%"/>
  <p>Figura 1 Loop fechado de percepção-raciocínio-execução do GUI Agent</p>
</div>


#### 1.2.1 Camada de Percepção: Como Máquinas "Veem" a Tela

A camada de percepção é responsável por converter informações da tela em dados compreensíveis pela máquina. Atualmente existem duas rotas técnicas principais, representando duas filosofias de design completamente diferentes.

A primeira rota é percepção estruturada baseada em DOM ou árvore de acessibilidade. Este método obtém a estrutura interna da aplicação através de APIs do sistema - como a árvore DOM HTML de páginas web, ou View Hierarchy de aplicações Android. É como fornecer ao Agent uma "planta arquitetônica", permitindo saber com precisão o tipo e posição de cada botão e caixa de texto. A vantagem deste método é precisão e eficiência, mas o problema também é óbvio: muitas aplicações modernas simplesmente não expõem essas informações estruturadas. Interfaces desenhadas em Canvas, jogos, software de desktop remoto, todos são "caixas pretas" para soluções baseadas em DOM. Além disso, este método perde informações de layout visual, é difícil entender relações espaciais entre elementos, e tem compatibilidade multi-plataforma ruim.

A segunda rota é percepção baseada em visão pura, que também é a direção mais avançada atualmente. Agent captura diretamente imagens da tela, usando modelo visual grande (VLM) para "ver" a tela como humanos. A universalidade deste método é extremamente forte - não importa com qual tecnologia sua interface é implementada, desde que possa ser exibida na tela, o Agent pode entender. Mais importante, possui "elasticidade semântica". Mesmo que um botão mude de azul para verde, ou a posição se mova ligeiramente, Agent baseado em visão ainda pode identificar através da semântica que "este é o botão de login". RPA tradicional falharia nesta situação, mas GUI Agent lida facilmente. Claro, soluções de visão pura também têm desafios, a maior dificuldade é precisão de localização - o modelo não apenas precisa identificar o que é o botão, mas também gerar suas coordenadas precisas de tela.

#### 1.2.2 Camada de Raciocínio: Processo de Tomada de Decisão do Cérebro

A camada de raciocínio é o "cérebro" do GUI Agent, responsável por converter instruções abstratas do usuário em sequências de operações específicas. Isso envolve várias capacidades chave.

Primeiro é capacidade de decomposição de tarefas. Quando você diz ao Agent "Ajude-me a reservar uma passagem de trem de alta velocidade para Shanghai amanhã, segunda classe, saindo por volta das 10 da manhã", ele precisa entender a lógica complexa por trás desta frase. O Agent automaticamente decompõe esta demanda vaga em uma série de etapas específicas: abrir APP 12306 → clicar em "reserva de passagem" → inserir partida "Beijing" → inserir destino "Shanghai" → selecionar data "amanhã" → clicar em consultar → filtrar trens (segunda classe + por volta das 10 da manhã) → selecionar trem que atende critérios → clicar em reservar → preencher informações de passageiro → confirmar pagamento. Este processo de decomposição depende da compreensão do modelo de linguagem grande sobre senso comum e processos de negócio.

Mais sutil é o mecanismo de cadeia de pensamento. Para melhorar taxa de sucesso em tarefas complexas, GUI Agents modernos geram "monólogo interno" antes de cada etapa de operação. Por exemplo, quando a tela atual é a página inicial do 12306, objetivo do usuário é reservar passagem de trem de alta velocidade, Agent primeiro analisa: "Vejo opções na tela como 'reserva de passagem', 'consulta de pedido', preciso clicar em 'reserva de passagem' para entrar no fluxo de compra." Então decide: "Clicar no botão 'reserva de passagem' nas coordenadas (540, 320)." Este processo de pensamento explícito não apenas torna o comportamento do Agent mais explicável, mas também pode reduzir significativamente acúmulo de erros em operações de múltiplas etapas.

Finalmente é capacidade de reflexão e correção de erros. Se Agent clicar no botão "consultar" e descobrir que não aparece a lista esperada de trens, mas surge prompt "selecione data de partida", ele imediatamente percebe: "Pulei a etapa de selecionar data." Então ajusta estratégia: "Primeiro clicar no seletor de data, escolher data de amanhã, depois consultar novamente." Esta capacidade de autocorreção permite que Agent lide com várias situações inesperadas no mundo real.

#### 1.2.3 Camada de Execução: De Decisão a Ação

A camada de execução são as "mãos" do GUI Agent, responsável por converter decisões do modelo em operações reais do sistema.

Diferente do espaço aberto de geração de texto, o espaço de ação de operações GUI é limitado e claro. Clicar, clicar duas vezes, pressionar longo, deslizar, inserir, rolar, arrastar - essas ações básicas constituem a base de todas as operações complexas. Cada ação tem seus parâmetros específicos, por exemplo, clicar precisa de coordenadas (x, y), deslizar precisa de ponto inicial e final (x1, y1, x2, y2), inserir precisa de conteúdo de texto.

Aqui há um detalhe técnico chave: conversão de sistema de coordenadas. Modelos visuais (como Qwen-VL) geralmente geram coordenadas normalizadas (0-1000), enquanto a resolução real de tela de celular ou computador pode ser 1920x1080. A camada de execução deve fazer mapeamento preciso de coordenadas, convertendo saída do modelo em coordenadas físicas. E dispositivos diferentes ainda têm diferentes DPI e proporções de escala do sistema, tudo isso precisa ser considerado. Uma função simples de mapeamento pode ser assim: primeiro dividir coordenadas normalizadas por 1000, depois multiplicar pela largura e altura reais da tela, finalmente arredondar para obter coordenadas físicas.

Mais complexo é adaptação multi-plataforma. No Android, todas as operações são realizadas enviando instruções via ADB (Android Debug Bridge), por exemplo `adb shell input tap 500 1000` executa clique, `adb shell input swipe 500 1000 500 500` executa deslize. No iOS, é necessário usar libimobiledevice ou WDA (WebDriverAgent) para implementar funcionalidade similar. E em ambientes desktop Windows, Mac, Linux, geralmente usam bibliotecas Python como pyautogui, pynput para controlar diretamente mouse e teclado. A mesma ação "clicar", método de implementação é completamente diferente em diferentes plataformas, camada de execução precisa fornecer interface abstrata unificada para cada plataforma.

### 1.3 Comparação Panorâmica de Frameworks Open-Source Mainstream

2024-2025 foi período de explosão do GUI Agent, grandes empresas de tecnologia e instituições de pesquisa lançaram seus frameworks. Vamos comparar sistematicamente alguns dos projetos mais representativos:

<div align="center">
  <img src="./images/Extra06-figures/image2.png" alt="Comparação de frameworks GUI Agent mainstream" width="90%"/>
  <p>Figura 2 Gráfico radar de comparação panorâmica de frameworks GUI Agent mainstream</p>
</div>


### 1.4 Cenários de Aplicação e Limitações Técnicas

#### 1.4.1 Cinco Cenários de Aplicação Típicos

O potencial de aplicação do GUI Agent supera em muito nossa imaginação. No campo de cabines inteligentes, demandas de interação por voz durante a condução estão explodindo. Imagine que ao dirigir você diz "Navegue até a cafeteria mais próxima e ajude-me a pedir um café latte 10 minutos antes de chegar", GUI Agent pode coordenar APP de navegação e APP de delivery entre aplicativos, entender lógica temporal complexa, e ainda se adaptar a diferenças de UI de diferentes marcas de sistemas de veículos. Isso é exatamente o que sistemas tradicionais de veículos têm dificuldade em fazer.

No campo de testes de software, GUI Agent trouxe mudanças revolucionárias. Testes automatizados tradicionais dependem de ferramentas como Selenium, cada vez que UI é atualizada é necessário atualizar scripts de teste, custo de manutenção é extremamente alto. E GUI Agent pode se auto-adaptar a mudanças de UI - mesmo que posição de botão seja ajustada, cor mude, Agent ainda consegue encontrar elemento correto através de identificação semântica. Ele ainda pode fazer testes de regressão visual, detectar automaticamente anomalias de UI, até realizar testes exploratórios ativamente, descobrindo casos extremos que engenheiros de teste humanos podem ignorar.

Cenários de RPA de nível empresarial são outro mercado enorme. RPA tradicional não consegue lidar com aqueles sistemas legados sem API, mas GUI Agent pode. Extrair dados do Excel, preencher sistema ERP, enviar email de notificação - todo o workflow entre sistemas pode ser completamente automatizado. Para aqueles sistemas legados que rodaram por vinte ou trinta anos, sem nenhuma interface moderna, GUI Agent finalmente fornece possibilidade de automação.

Na vida pessoal, GUI Agent pode se tornar verdadeiro assistente inteligente. Publicar conteúdo programado em múltiplas plataformas sociais, agregar automaticamente notícias, clima, agenda todas as manhãs, registrar dados de exercícios e hábitos alimentares - esses trabalhos digitais repetitivos podem ser entregues ao Agent para completar. E para usuários com deficiência visual, deficiência física, GUI Agent abre ainda mais as portas para um novo mundo. Controlar totalmente celular através de voz, leitura inteligente de conteúdo da tela, transformar operações complexas em instruções simples, essas funcionalidades estão fazendo tecnologia verdadeiramente beneficiar cada pessoa.

#### 1.4.2 Três Principais Limitações da Tecnologia Atual

Mas também devemos ter consciência clara de que tecnologia GUI Agent ainda está em estágio inicial de desenvolvimento, enfrentando alguns desafios substanciais.

O mais preocupante é segurança e risco de alucinação. O problema de alucinação de modelos de linguagem grandes pode levar a consequências graves em GUI Agent. Usuário solicita "limpar desktop", Agent pode entender erroneamente como deletar todos os arquivos; um erro de número em operação de transferência pode causar perda econômica. Soluções de mitigação atuais incluem: exigir confirmação humana obrigatória para operações de alto risco, registrar logs detalhados de operações e suportar rollback, e testar suficientemente em ambiente sandbox. Mas tudo isso são medidas paliativas, resolver fundamentalmente o problema de alucinação de modelos ainda requer tempo.

Problema de custo e eficiência também não pode ser ignorado. Cada etapa de operação precisa chamar modelo grande para raciocínio, se usar API em nuvem, custo crescerá linearmente com número de chamadas. Uma tarefa complexa pode requerer dezenas de iterações, tempo total consumido é longo. Implantação local de modelo pequeno pode reduzir custo, mas taxa de precisão diminuirá. Cache de operações, reconhecimento de padrões, arquitetura híbrida (tarefas simples usam RPA, tarefas complexas usam IA) são direções sendo exploradas atualmente, mas ainda não formaram melhores práticas maduras.

Finalmente é gargalo de taxa de precisão. Mesmo os melhores sistemas, em cenários reais, taxa de sucesso é apenas 40-50%. Localização de elementos em interfaces complexas, processamento de conteúdo dinâmico (anúncios, pop-ups), acúmulo de erros em tarefas de cadeia longa, todos são problemas técnicos reais. Direções de avanço incluem modelos visuais grandes mais fortes, otimizar estratégias de operação através de aprendizado por reforço, e design colaborativo "humano no loop" (Human-in-the-loop). Mas de 50% para 90% de nível comercialmente viável pode ainda levar algum tempo.

---

## Parte Dois: Tutorial Prático de GUI Agent

Após aprendizado teórico, vamos através de dois casos práticos com dificuldade crescente, realmente dominar o uso e implantação de GUI Agent.

### Prática Um: Experiência Online Mobile-Agent (Sem Barreira)

#### 2.1.1 Acessar Demo Online

Mobile-Agent-v3 não apenas suporta celulares, mas também pode operar computadores. Conforme mostrado na Figura 3, na página Demo do ModelScope, mudamos seleção de dispositivo no canto superior esquerdo para "Computador", podemos entrar no ambiente de experiência PC Agent.

**Opção Um: Demo ModelScope** (Recomendado)
Link: https://modelscope.cn/studios/wangjunyang/Mobile-Agent-v3

**Opção Dois: Bailian Aliyun**
Link: https://bailian.console.aliyun.com/next?tab=demohouse#/experience/adk-computer-use/pc

Ambas plataformas fornecem **ambiente de celular/computador em nuvem**, sem necessidade de implantação local, pode experimentar funcionalidades completas.

### 2.1.2 Guia de Funcionalidades da Interface

Após entrar na página, você verá a interface de operação mostrada na Figura 3. Para garantir experiência consistente, certifique-se de fazer as seguintes **configurações chave**:

1. **Seleção de Dispositivo**: No menu suspenso no canto superior esquerdo, confirme seleção de **"Computador"** (não celular).
2. **Visualização de Desktop**: Janela à direita mostra desktop Windows 10 em nuvem alocado para você, com Office, navegador e outros softwares básicos pré-instalados.
3. **Área de Interação**: Canto inferior esquerdo é área de entrada de instruções, processo de pensamento (Thinking Process) e etapas de operação do Agent serão exibidos na caixa de diálogo acima.

<div align="center">
  <img src="./images/Extra06-figures/image3.png" alt="Interface Demo ModelScope" width="90%"/>
  <p>Figura 3 Explicação da interface Demo online Mobile-Agent-v3</p>
</div>

Nesta interface, você pode comandar diretamente o Agent para realizar operações de escritório, mas atualmente tempo de uso é limitado.

### 2.1.3 Treinamento de Tarefas Típicas

De acordo com capacidades predefinidas fornecidas pela interface, recomenda-se iniciantes começarem com os seguintes dois tipos de tarefas:

- **Controle de Nível de Sistema**: Tente fazer Agent modificar configurações do sistema.
  - *Exemplo de instrução*: "Definir cor do sistema para **modo claro**."
  - *Ponto de observação*: Agent consegue abrir "Menu Iniciar -> Configurações -> Personalização" como humano.
- **Trabalho de Escritório Entre Aplicativos**: Tente fazer Agent vincular navegador e software de escritório.
  - *Exemplo de instrução*: "No navegador Edge, pesquise preço das ações da Alibaba, depois crie uma planilha no WPS e preencha nome da empresa e preço atual das ações."
  - *Ponto de observação*: Agent consegue processar corretamente mudança de contexto entre softwares de "pesquisar informações" para "inserir informações".

### 2.1.4 Engenharia de Prompt: Como Comandar PC Agent

Em cenários GUI, Prompt de alta qualidade é chave para o sucesso. Combinando cenários de escritório acima, resumimos três técnicas centrais:

1. **Limites Explícitos de Aplicativo (Explicit Context)**
   - Evite instruções genéricas, como "escrever uma introdução".
   - **Escrita recomendada**: "Escrever uma introdução no **documento WPS Office**..."
   - *Análise*: Especificar explicitamente nome do software (App Name), pode reduzir tempo do Agent buscando ferramentas.
2. **Decomposição em Cadeia de Etapas (Chain of Steps)**
   - Não tente incluir toda lógica complexa em uma frase.
   - **Escrita recomendada**: "Primeira etapa, abrir Edge para pesquisar...; Segunda etapa, confirmar que página web carregou completamente, depois capturar dados...; Terceira etapa, abrir Excel para colar."
   - *Análise*: Operações GUI têm sequencialidade estrita, instruções em etapas podem reduzir significativamente taxa de erro de execução.
3. **Descrição de Atributos Visuais (Visual Attributes)**
   - Agent "vê" tela para operar, usar descrição de características visuais é mais eficaz.
   - **Escrita recomendada**: "Clicar no **botão azul de salvar** no canto superior direito" ou "Mudar cor da fonte para **vermelho**".

#### 2.1.5 Valor e Limitações da Experiência Online

O maior valor do Demo online fornecido pelo ModelScope é **experiência sem barreira**. Você não precisa configurar nenhum ambiente, não precisa preparar celular, nem precisa baixar nenhum software, pode sentir diretamente a magia do GUI Agent. Isso é muito útil para validar rapidamente ideias e entender limites técnicos.

Mas ambiente online também tem suas limitações. Primeiro é **problema de privacidade**, todas as operações são feitas em máquina virtual em nuvem, você não pode acessar dados pessoais reais. Segundo é **restrição de funcionalidades**, ambiente virtual só tem parte de APPs comuns pré-instalados, não pode testar cenários de aplicações específicas. Finalmente é **diferença de desempenho**, latência de inferência em nuvem será ligeiramente maior que implantação local.

Portanto, experiência online é adequada como ponto de partida para aprendizado e exploração, mas se quiser aplicar GUI Agent em cenários reais, você precisa tentar implantação local. Mobile-Agent-v3 oficial forneceu um [tutorial](https://github.com/X-PLUG/MobileAgent/blob/main/Mobile-Agent-v3/README_zh.md), pode tentar por conta própria

A Prática Dois a seguir levará você usando AutoGLM open-source recentemente pela **Zhipu** para entrar neste mundo mais profundo.

---

### Prática Dois: Implantação Local AutoGLM e Prática em Celular

Experiência online nos fez sentir capacidade do GUI Agent, mas o verdadeiro poder está em implantar em seu próprio dispositivo, controlando aplicações reais. AutoGLM é um framework muito adequado para desenvolvedores individuais iniciarem, sua arquitetura é clara, documentação é completa, processo de implantação é relativamente simples.

O objetivo desta prática é implantar AutoGLM em seu computador, conectar seu celular Android, depois deixar IA ajudá-lo a completar algumas tarefas reais - como responder automaticamente mensagens WeChat, ou atualizar periodicamente algum APP para obter dados mais recentes.

#### 2.2.1 Preparação de Ambiente: O Que Você Precisa

A implantação do Open-AutoGLM precisa de dois dispositivos centrais: um computador capaz de rodar Python, e um celular Android. A configuração do computador não precisa ser muito alta, porque AutoGLM suporta chamar API em nuvem, não necessariamente precisa rodar modelo grande localmente. Se você planeja usar API em nuvem (como GLM-4V da Zhipu), um notebook comum é suficiente. Mas se quiser experimentar solução totalmente local, então uma GPU com pelo menos 8GB de memória tornará experiência muito melhor.

Quanto ao celular, Android 7.0 ou superior é adequado, não requer permissões Root. Usuários de iPhone temporariamente não podem usar, porque a natureza fechada do iOS faz com que solução de depuração ADB não possa ser aplicada diretamente.

Em termos de ambiente de software, você precisa instalar Python 3.10 ou superior, e ferramenta ADB (Android Debug Bridge). ADB é a ponte conectando computador e celular, todas as capturas de tela, cliques, operações de deslize precisam ser realizadas através dele.

**Instalar ferramenta ADB (macOS / Linux):** De acordo com seu sistema, execute o seguinte comando no terminal:

```bash
# macOS usando Homebrew
brew install android-platform-tools

# Linux (Ubuntu/Debian)
sudo apt install android-tools-adb
```

Usuários Windows geralmente podem baixar diretamente pacote compactado Platform Tools e configurar variáveis de ambiente. [Referência](https://blog.csdn.net/x2584179909/article/details/108319973)

#### 2.2.2 Primeira Etapa: Instalar Open-AutoGLM

Se você possui **Claude Code**, pode configurar [GLM Coding Plan](https://bigmodel.cn/glm-coding) depois, inserir o seguinte prompt para implantar rapidamente:

```
Acesse documentação, instale AutoGLM para mim
https://raw.githubusercontent.com/zai-org/Open-AutoGLM/refs/heads/main/README.md
```

Se não tiver CLI similar, siga estas etapas manuais:

Abra terminal de linha de comando, primeiro clone repositório de código Open-AutoGLM:

```bash
git clone https://github.com/zai-org/Open-AutoGLM.git
cd Open-AutoGLM
```

Em seguida, instale dependências. Além de pacotes básicos de dependência, **certifique-se de executar comando de instalação do projeto** para garantir que todos os módulos possam ser chamados corretamente:

```bash
# 1. Instalar dependências básicas
pip install -r requirements.txt

# 2. Instalar projeto em si em modo editável (etapa chave)
pip install -e .

# 3. (Opcional) Se você é desenvolvedor, precisa instalar dependências de desenvolvimento adicionais
pip install -e ".[dev]"
```

Este processo geralmente leva alguns minutos, dependendo da velocidade da sua rede. Após instalação completa, você precisa configurar chave API. Se usar API GLM-4V da Zhipu, primeiro vá à plataforma aberta Zhipu para registrar conta e obter API Key, depois crie arquivo `.env` no diretório raiz do projeto:

```bash
# Conteúdo do arquivo .env
GLM_API_KEY=your_api_key_here
```

[AutoGLM-Phone-9B · Biblioteca de Modelos](https://modelscope.cn/models/ZhipuAI/AutoGLM-Phone-9B)

#### 2.2.3 Segunda Etapa: Conectar Seu Celular Android

Agora chegou a etapa chave: fazer computador "ver" e "controlar" seu celular. Isso requer três pequenas etapas: ativar modo desenvolvedor, ativar depuração USB, e **instalar ADB Keyboard**.

**1. Ativar Modo Desenvolvedor e Depuração USB** No celular Android, entre em "Configurações" → "Sobre o telefone", encontre "Número de versão", **clique 7 vezes consecutivas** (ou até aparecer prompt), você verá prompt "Você entrou em modo desenvolvedor". Volte para interface principal de configurações, entre em "Opções do desenvolvedor", encontre "Depuração USB" e **ative**.

**2. Instalar ADB Keyboard (Obrigatório)** Para permitir que IA insira texto no celular, precisamos instalar teclado especializado ADB.

- Endereço de download: https://github.com/senzhk/ADBKeyBoard/raw/master/ADBKeyboard.apk

Após instalação, lembre-se de ativar e mudar para **ADB Keyboard** em "Método de entrada" nas configurações do celular.

**3. Verificar Conexão** Use cabo USB para conectar celular ao computador (quando caixa de autorização aparecer no celular, clique em "Permitir"). No terminal do computador, digite:

```bash
adb devices
```

Se tudo estiver normal, você verá número de série do dispositivo:

```
List of devices attached
ABC12345    device
```

Se exibir `device`, parabéns, conexão de hardware estabelecida! Se exibir `unauthorized`, verifique se tela do celular tem caixa de confirmação de autorização.

Para usuários Windows, pode ser necessário instalar driver do celular. Maioria das marcas de celular (como Xiaomi, Huawei, OPPO) instalarão driver automaticamente ao conectar ao computador, mas se encontrar problemas, pode ir ao site oficial para baixar driver USB correspondente.

<div align="center">
  <img src="./images/Extra06-figures/image4.png" alt="Etapas de configuração de conexão ADB do celular" width="90%"/>
  <p>Figura 4 Fluxo completo de configuração de conexão ADB do celular Android</p>
</div>


#### 2.2.4 Terceira Etapa: Executar Sua Primeira Tarefa

Após conexão bem-sucedida, vamos executar uma tarefa simples mas prática.

Existem duas formas de chamar API diretamente:

**1. Zhipu BigModel**

- Documentação: https://docs.bigmodel.cn/cn/api/introduction
- `--base-url`: `https://open.bigmodel.cn/api/paas/v4`
- `--model`: `autoglm-phone`
- `--apikey`: Solicite sua API Key na plataforma Zhipu

**2. ModelScope (Comunidade Mage)**

- Documentação: https://modelscope.cn/models/ZhipuAI/AutoGLM-Phone-9B
- `--base-url`: `https://api-inference.modelscope.cn/v1`
- `--model`: `ZhipuAI/AutoGLM-Phone-9B`
- `--apikey`: Solicite sua API Key na plataforma ModelScope

README oficial fornece interface de linha de comando, você pode inserir diretamente:

```bash
# Usando Zhipu BigModel
python main.py --base-url https://open.bigmodel.cn/api/paas/v4 --model "autoglm-phone" --apikey "your-bigmodel-api-key" "Abrir Meituan para pesquisar restaurantes de fondue próximos"

# Usando ModelScope
python main.py --base-url https://api-inference.modelscope.cn/v1 --model "ZhipuAI/AutoGLM-Phone-9B" --apikey "your-modelscope-api-key" "Abrir Meituan para pesquisar restaurantes de fondue próximos"
```

Após executar este comando, AutoGLM iniciará processo de inferência. Você verá saída de log em tempo real no terminal, ao mesmo tempo tela do celular começará a operar automaticamente. Todo o processo é aproximadamente assim:

Primeiro, AutoGLM captura screenshot da tela atual através de ADB, envia imagem para modelo analisar. Modelo identificará todos os ícones de APP na tela, e localizará em nível de pixel a posição do "Meituan". Então AutoGLM envia instrução de clique, através de `adb shell input tap x y` para abrir aplicativo.

Após aguardar inicialização do Meituan, AutoGLM captura tela novamente. Desta vez seu objetivo é encontrar "barra de pesquisa" na parte superior da página inicial. Após identificar e clicar na caixa de pesquisa, **ele chamará ADB Keyboard que instalamos na etapa de preparação de ambiente**, inserindo a string de caracteres "fondue próximo", finalmente clicando automaticamente no botão de pesquisa.

Todo o processo geralmente leva 15-20 segundos (tarefas de pesquisa têm passos um pouco mais), tempo específico depende da velocidade de inferência do modelo e latência da rede. Se você usar API em nuvem, tempo de "pensamento" de cada passo é cerca de 2-3 segundos. Se for modelo implantado localmente, GPU com boa configuração pode comprimir tempo de passo único para cerca de 1 segundo.

---

## Conclusão e Perspectivas

Através dessas duas práticas com níveis progressivos, experimentamos completamente o processo completo de GUI Agent desde demonstração online até implantação local. Demo online do Mobile-Agent nos fez entender rapidamente possibilidades da tecnologia, prática em celular do AutoGLM nos fez dominar habilidades de implantação real, enquanto solução de ponta GLM-ZERO mostra futuro de proteção de privacidade e aplicações offline.

Tecnologia GUI Agent ainda está evoluindo rapidamente. Sistemas atuais, embora já possam lidar com maioria das tarefas diárias, ainda têm grande espaço para melhoria em taxa de precisão, velocidade de inferência e controle de custos. Com o progresso contínuo de modelos visuais grandes e desenvolvimento de chips de inferência de ponta, temos razões para acreditar que GUI Agent se tornará um paradigma importante de interação humano-computador no futuro.

Talvez em breve futuro, cada pessoa terá um assistente digital verdadeiramente inteligente, que não apenas entenderá suas intenções, mas também atravessará diferentes aplicações e plataformas, ajudando você a completar vários trabalhos repetitivos. Naquela época, scripts de automação que hoje escrevemos com esforço se tornarão uma simples instrução em linguagem natural.

Este futuro, na verdade, já está a caminho.

---

## Materiais de Referência

1. Artigo Mobile-Agent-v3: https://arxiv.org/abs/2508.15144
2. GitHub Open-AutoGLM: https://github.com/zai-org/Open-AutoGLM
3. Projeto UI-TARS: https://github.com/bytedance/UI-TARS
