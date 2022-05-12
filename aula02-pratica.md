### Prática de Docker Compose

#### [Documentação](https://docs.docker.com/compose/gettingstarted/)

#### Primeiro Container

1. Criar um arquivo Dockerfile com o seguinte conteúdo:

```sh
FROM openjdk:11
VOLUME /tmp
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app.jar"]
```

**ENTRYPOINT**: Comando para executar o artefato adicionado dentro da imagem.

2. Realizar o build da Imagem

```sh
docker build -t coders/spring-boot-docker-app .
```

#### Criar projeto java

1. Podemos criar nosso projetinho no link: [Spring Initializr](https://start.spring.io/)

    Com os seguintes requisitos:
    - Spring Web
    - Spring Data JPA
    - PostgreSQL Driver

2. Importar o projeto na sua **IDE Favorita**
3. Criar uma classe **PrimeiraController.java**

```sh
@RestController
public class PrimeiraController {

    @GetMapping
    public String index() {
        return "Bem-Vindos";
    }
}
```

4. Adicionar o conteúdo abaixo no arquivo **application.properties**
```sh
spring.datasource.url=jdbc:postgresql://docker-postgres:5432/db
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.show-sql=false
spring.jpa.hibernate.ddl-auto=create
```

5. Build da aplicação java na pasta raiz do projeto
```sh
mvn package -Dmaven.test.skip=true
```

6. Mover o arquivo Dockerfile para a pasta raiz do projeto java

7. Criar um arquivo **docker-compose.yml** na raiz do projeto com o seguinte conteúdo:
```sh
version: '3'
services:
 docker-app:
    image: coders/spring-boot-docker-app
    ports:
      - "8080:8080"
    depends_on:
      - docker-postgres
 docker-postgres:
    image: postgres:10.4
    environment:
      - POSTGRES_DB=db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
```
