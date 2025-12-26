<div align="right">
  <a href="./NODEJS_INSTALL_GUIDE.md">中文</a> | Português
</div>

# Tutorial de Instalação do Node.js e npx

## 📋 Índice

- [Por que é necessário instalar o Node.js?](#por-que-é-necessário-instalar-o-nodejs)
- [Tutorial de Instalação no Windows](#tutorial-de-instalação-no-windows)
- [Tutorial de Instalação no macOS](#tutorial-de-instalação-no-macos)
- [Tutorial de Instalação no Linux](#tutorial-de-instalação-no-linux)
- [Verificação da Instalação](#verificação-da-instalação)
- [Problemas Comuns](#problemas-comuns)

---

## Por que é necessário instalar o Node.js?

No estudo do protocolo MCP no capítulo dez, precisamos usar servidores MCP fornecidos pela comunidade, e a maioria desses servidores é escrita em JavaScript/TypeScript, exigindo o ambiente de execução Node.js.

**Após instalar o Node.js, você terá**:
- ✅ **node**: Ambiente de execução JavaScript
- ✅ **npm**: Gerenciador de pacotes Node (Node Package Manager)
- ✅ **npx**: Executor de pacotes npm (baixa e executa pacotes npm automaticamente)

**Função do npx**:
```bash
# Método tradicional: precisa instalar primeiro e depois executar
npm install -g @modelcontextprotocol/server-filesystem
server-filesystem

# Usando npx: baixa e executa automaticamente (recomendado)
npx @modelcontextprotocol/server-filesystem
```

---

## Tutorial de Instalação no Windows

### Método 1: Pacote de Instalação Oficial (Recomendado)

#### Passo 1: Baixar o Pacote de Instalação

Acesse o site oficial do Node.js: https://nodejs.org/

Você verá duas versões:
- **LTS (Suporte de Longo Prazo)**: Recomendado para a maioria dos usuários ✅
- **Current (Versão Mais Recente)**: Contém os recursos mais recentes

**Recomenda-se baixar a versão LTS** (por exemplo: 20.x.x LTS)

#### Passo 2: Executar o Programa de Instalação

1. Clique duas vezes no arquivo `.msi` baixado
2. Clique em "Next" para iniciar a instalação
3. Aceite o acordo de licença
4. Selecione o caminho de instalação (o padrão está correto)
5. **Importante**: Certifique-se de marcar as seguintes opções:
   - ✅ Node.js runtime
   - ✅ npm package manager
   - ✅ Add to PATH (adicionar automaticamente às variáveis de ambiente)
6. Clique em "Install" para iniciar a instalação
7. Aguarde a conclusão da instalação e clique em "Finish"

#### Passo 3: Verificar a Instalação

Abra o **PowerShell** ou **Prompt de Comando** (CMD) e digite:

```powershell
# Verificar versão do Node.js
node -v
# Deve exibir: v20.x.x

# Verificar versão do npm
npm -v
# Deve exibir: 10.x.x

# Verificar versão do npx
npx -v
# Deve exibir: 10.x.x
```

Se todos exibirem os números de versão normalmente, a instalação foi bem-sucedida! ✅

---

## Tutorial de Instalação no macOS

### Método 1: Pacote de Instalação Oficial

#### Passo 1: Baixar o Pacote de Instalação

Acesse: https://nodejs.org/

Baixe o arquivo `.pkg` da **versão LTS**

#### Passo 2: Instalação

1. Clique duas vezes no arquivo `.pkg`
2. Siga as instruções do assistente de instalação
3. Digite a senha de administrador
4. Conclua a instalação

#### Passo 3: Verificar a Instalação

Abra o **Terminal** e digite:

```bash
node -v
npm -v
npx -v
```

---

## Tutorial de Instalação no Linux

### Ubuntu/Debian

#### Método 1: Usando o Repositório NodeSource (Recomendado)

```bash
# Atualizar lista de pacotes
sudo apt update

# Instalar curl (se ainda não tiver)
sudo apt install -y curl

# Adicionar repositório NodeSource (Node.js 20.x LTS)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Instalar Node.js e npm
sudo apt install -y nodejs

# Verificar instalação
node -v
npm -v
npx -v
```

#### Método 2: Usando apt (versão pode ser mais antiga)

```bash
sudo apt update
sudo apt install -y nodejs npm
```

---

### CentOS/RHEL/Fedora

```bash
# Adicionar repositório NodeSource
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -

# Instalar Node.js
sudo yum install -y nodejs

# Verificar instalação
node -v
npm -v
npx -v
```

---

### Arch Linux

```bash
# Instalar usando pacman
sudo pacman -S nodejs npm

# Verificar instalação
node -v
npm -v
npx -v
```

---

## Verificação da Instalação

### Etapas de Verificação Completa

Após a conclusão da instalação, execute os seguintes comandos para verificação completa:

```bash
# 1. Verificar versão
node -v
npm -v
npx -v

# 2. Testar Node.js
node -e "console.log('Node.js funcionando normalmente!')"

# 3. Testar npm
npm --version

# 4. Testar npx (executar um pacote simples)
npx cowsay "Hello MCP!"
```

### Saída Esperada

```
v20.11.0
10.2.4
10.2.4
Node.js funcionando normalmente!
10.2.4
 _____________
< Hello MCP! >
 -------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

---

## Testar Conexão com Servidor MCP

Após a instalação, teste a conexão com servidores MCP da comunidade:

### Testar Servidor de Sistema de Arquivos

```bash
# Usar npx para executar o servidor MCP de sistema de arquivos
npx -y @modelcontextprotocol/server-filesystem .
```

Se você ver informações de inicialização do servidor, está tudo normal!

### Testar em Python

Crie o script de teste `test_mcp.py`:

```python
import asyncio
from hello_agents.protocols import MCPClient

async def test():
    client = MCPClient([
        "npx", "-y",
        "@modelcontextprotocol/server-filesystem",
        "."
    ])

    async with client:
        tools = await client.list_tools()
        print(f"✅ Conexão bem-sucedida! Ferramentas disponíveis: {[t['name'] for t in tools]}")

asyncio.run(test())
```

Execute:

```bash
python test_mcp.py
```

---

## Problemas Comuns

### P1: Comando não encontrado após instalação

**Windows**:
```powershell
# Verificar variáveis de ambiente
echo $env:PATH

# Adicionar Node.js manualmente ao PATH
# 1. Clique com botão direito em "Este Computador" -> "Propriedades"
# 2. "Configurações avançadas do sistema" -> "Variáveis de ambiente"
# 3. Nas "Variáveis do sistema", encontre "Path"
# 4. Adicione: C:\Program Files\nodejs\
```

**macOS/Linux**:
```bash
# Verificar variáveis de ambiente
echo $PATH

# Adicionar a ~/.bashrc ou ~/.zshrc
export PATH="/usr/local/bin:$PATH"
source ~/.bashrc  # ou source ~/.zshrc
```

---

### P2: npm muito lento

Use fonte de espelho nacional (espelho Taobao):

```bash
# Uso temporário
npm install --registry=https://registry.npmmirror.com

# Configuração permanente
npm config set registry https://registry.npmmirror.com

# Verificar
npm config get registry
```

---

### P3: Erro de permissão do npx

**Windows**:
```powershell
# Executar PowerShell como administrador
```

**macOS/Linux**:
```bash
# Não use sudo para executar npx
# Se encontrar problemas de permissão, corrija as permissões do diretório global do npm
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---

### P4: Conflito de versão

Se você precisar gerenciar várias versões do Node.js, recomenda-se usar ferramentas de gerenciamento de versão:

**Windows**: [nvm-windows](https://github.com/coreybutler/nvm-windows)

```powershell
# Após instalar nvm-windows
nvm install 20.11.0
nvm use 20.11.0
```

**macOS/Linux**: [nvm](https://github.com/nvm-sh/nvm)

```bash
# Instalar nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Instalar Node.js
nvm install 20
nvm use 20
```

---

### P5: npx baixa pacotes muito lentamente

```bash
# Método 1: Usar espelho nacional
npx --registry=https://registry.npmmirror.com @modelcontextprotocol/server-filesystem

# Método 2: Instalar globalmente primeiro, depois usar
npm install -g @modelcontextprotocol/server-filesystem
server-filesystem
```

---

## Próximos Passos

Após a conclusão da instalação, você pode:

1. ✅ Executar `code/02_Connect2MCP.py` para testar conexão com cliente MCP
2. ✅ Explorar servidores MCP da comunidade: https://github.com/modelcontextprotocol/servers
3. ✅ Continuar estudando o restante do conteúdo do capítulo dez

---

## Recursos de Referência

- **Site oficial do Node.js**: https://nodejs.org/
- **Documentação do npm**: https://docs.npmjs.com/
- **Documentação do npx**: https://docs.npmjs.com/cli/v10/commands/npx
- **Lista de servidores MCP**: https://github.com/modelcontextprotocol/servers
- **Espelho npm Taobao**: https://npmmirror.com/

---

**Bons estudos!** 🎉

Se tiver dúvidas, consulte a seção de problemas comuns ou a documentação oficial.
