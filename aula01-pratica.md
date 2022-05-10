## Docker prática

### Nosso primeiro container

```sh
docker run ubuntu /bin/echo Hello, docker!
```

##### O que este comando fez
Primeiro o docker baixou a imagem do ubuntu (veremos mais na frente de onde vem essa imagem), utilizou o comando echo do container para exibir a mensagem Hello, Docker!

Este container ele cria, exibe a mensagem e morre. Porque ele não tem nenhum processo em execução, para o container permanecer no status de running, ele precisa ter um processo executando.


##### Criando container com processo ativo

```sh
docker run -d -p 8080:80 nginx
```

Com este comando nós baixamos a imagem do Nginx, utilizamos -d para executar em background (não exibir o bash), o -p iremos ver um pouco mais na frente.

### Comandos Docker

Este comando nós conseguimos ver as opções para controlar no Docker:
```sh
docker run --help
```

Este comando verifica quais são os containers que estão em execução
```sh
docker ps
```

Este outro comando exibe todos os containers que foram criados
```sh
docker ps -a
```

Este comando exibe todos os IDs dos containers
```sh
docker ps -aq
```

Com este comando nós estamos pausando a execução container, ou seja, não conseguiremos mais acessar a página web.

```sh
docker pause ID/nome
```

Com este comando nós estamos retomando o container para o status de running.

```sh
docker unpause ID/nome
```

Com este comando nós estamos parando o container deixando no status de exited.

```sh
docker stop ID/nome
```

Com este comando nós estamos removendo o container, com isso ele remove tudo que foi alocado após criar a imagem

```sh
docker rm ID/nome
```

Com este comando conseguimos visualizar todas as imagens que estão no nosso repositório local.

```sh
docker images
```


### Portas

Com o comando abaixo, nós criamos um container com o apontamento de portas dinâmicas.

```sh
docker run -d -P nginx
```

Com o comando abaixo, nós conseguimos listar todas as portas apontadas para o container

```sh
docker port -P
```

Ou podemos definir qual a porta que será apontada do container para o Host, como fizemos no começo

```sh
docker run -d -p 8180:80 nginx
```

### Imagens

Com o comando abaixo, nós conseguimos identificar quais imagens estão no nosso repositório local:

```sh
docker images
```

Com o comando abaixo, nós conseguimos identificar as camadas e seus respectivos tamanhos na criação da imagem:

```sh
docker history name/id
```

### Criando imagens

#### Criando imagens através do commit

Nós podemos criar uma imagem de um container em execução. Por exemplo, vamos adicionar um arquivo, e salvar a imagem e criar um novo container.



```sh
docker exec -it NAME/ID bash
ls -l # visualiza os arquivos antes da criação
touch arquivo-teste.txt # cria um arquivo de teste
ls -l # visualiza o diretório com o novo arquivo criado
exit

docker stop ID/NAME # para o container
docker commit NAME/ID meunginx:nginx # cria uma imagem a partir do container
docker images # lista as imagens do repositório local
docker run -d -p 8220:80 meunginx:nginx # cria um novo container com a imagem nova

docker exec -it NAME/ID bash
ls -l # O arquivo estará lá
```

Com o comando -it nós estamos avisando que iremos interagir com o bash, ou seja, só será fechado após escrevermos exit ou Ctrl + c


#### Criando imagem através do arquivo .Dockerfile

Criamos um arquivo Dockerfile e um arquivo chamado "arquivo_teste.txt" no diretório com o seguinte conteúdo:

```
FROM nginx
COPY arquivo_teste.txt /tmp/arquivo_teste.txt
```

Utilizamos o seguinte comando para criação da imagem:

```sh
docker image build -t novaimagem:nginx . # -t para criar o nome da imagem
```

Agora criamos um novo container com a imagem criada, e visualizamos para ver se o arquivo está lá:

```sh
docker run -d -p 8880:80 novaimagem:nginx # cria um novo container com a imagem nova

docker exec -it NAME/ID bash
cd /tmp
ls -l # para visualizar o arquivo_teste.txt
```

E verificar os steps da imagem:

```sh
docker history novaimagem:nginx
```

### Docker Hub

Contextualizar o que é o docker hub.