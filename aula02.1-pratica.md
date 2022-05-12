## Aula prática

#### Subindo serviço do wordpress

##### Comandos do Docker Compose
- **docker-compose up** - cria os containers com base no arquivo
- **docker-compose build** - realiza apenas o build das imagens que vão ser utilizadas
- **docker-compose logs** - exibe logs dos containers que constam no arquivo
- **docker-compose restart** - reinicia todos os containers
- **docker-compose ps** - lista todos containers que foi criado com o arquivo
- **docker-compose start** - inicia todos os containers do arquivo
- **docker-compose stop** - paralisa todos os containers do arquivo
- **docker-compose down** - paralisa e remove todos os containers e componentes criados através do arquivo: `rede, imagem e volume`

Para criação de um serviço do wordpress, utilizaremos o arquivo abaixo:

```sh
version: '3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: rootwordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "80:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: 
```