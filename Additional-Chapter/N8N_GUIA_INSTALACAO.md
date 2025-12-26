<div align="right">
  <a href="./N8N_INSTALL_GUIDE.md">中文</a> | Português
</div>

Aqui apresentamos o método de instalação local usando Docker no projeto, pois esta é a forma mais estável e mais adequada para explorar continuamente o uso do n8n.

Primeiro, acesse o site oficial do Docker: [Docker: Accelerated Container Application Development](https://www.docker.com/)

Selecione seu dispositivo terminal para download. Aqui usamos Windows como demonstração.

![image-20250912025341155](./N8N_INSTALL_GUIDE/image-20250912025341272.png)

Após o download, você pode alterar o caminho de armazenamento no disco, pois as imagens geralmente são grandes, tente não armazenar no disco C.

![image-20250912032540657](./N8N_INSTALL_GUIDE/image-20250912032540657.png)

Em seguida, abra sua linha de comando e digite os seguintes comandos para baixar o n8n:

```
docker volume create n8n_data
docker run -d --restart unless-stopped --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n n8nio/n8n
```

Agora podemos ver o n8n em execução no Docker!

![image-20250912033251997](./N8N_INSTALL_GUIDE/image-20250912033251997.png)

Clique em 5678:5678 para acessar a interface de inicialização do n8n.

![image-20250912033341666](./N8N_INSTALL_GUIDE/image-20250912033341666.png)

Após entrar na página, você pode ver o botão para abrir um novo projeto.

![image-20250912034040656](./N8N_INSTALL_GUIDE/image-20250912034040656.png)

Existem três funcionalidades principais:

![image-20250912234709064](./N8N_INSTALL_GUIDE/image-20250912234709064.png)

Após abrir o botão de adicionar novo nó, você pode pesquisar nós ou selecionar os nós que você precisa para adicionar!

![image-20250912234748845](./N8N_INSTALL_GUIDE/image-20250912234748845.png)
