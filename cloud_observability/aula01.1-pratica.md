## Spring boot Actuator

#### Reference:
[Spring Boot Admin - Codecentric](https://codecentric.github.io/spring-boot-admin/current/)

#### Adicionando Actuator no projeto

1. Primeiro passo a ser realizado é a inclusão da dependência no `pom.xml` do projeto a ser monitorado.

```sh
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

2. Para adicionar as informações relacionadas ao projeto (informações buscadas no arquivo `pom.xml`) nos endpoints disponíveis do Actuator, podemos adicionar no `application.properties` as seguintes configurações:

```sh

info.app.name=@project.name@
info.app.description=@project.description@
info.app.version=@project.version@
info.app.java.version=@java.version@
```

3. Após configurado, podemos utilizar o endpoint do nosso projeto para verificarmos o funcionamento do Actuator.

Podemos realizar uma requisição `GET` para o endpoint: `http://localhost:PORTA/actuator`

4. Liberando recursos do actuator para visualização:

```sh
management.endpoint.info.enabled=true
management.endpoints.web.exposure.include=*
management.endpoints.web.exposure.exclude=env,beans
```


#### Criando projeto [Spring boot Admin](https://github.com/codecentric/spring-boot-admin)

1. Criar projeto utilizando o link [Spring Initializr](https://start.spring.io/) com as seguintes dependências:

- Spring Web
- Codecentric's Spring Boot Admin (Server)

**Obs:** Para utilização do Spring Boot Admin, até o momento deste arquivo, será possível apenas para versões menores do que **2.7**.

2. Importar o projeto na sua IDE preferida.

3. Substituir a annotation na classe `{NomeProjeto}Application.java` para o seguinte conteúdo:

```sh
@Configuration
@EnableAutoConfiguration
@EnableAdminServer
public class {NomeProjeto}Application {

    public static void main(String[] args) {
        SpringApplication.run(ObservabilityApplication.class, args);
    }

}

```

4. Após substituir, no aquivo `application.properties` é necessário a alteração da porta para não ocorrer conflitos com outros projetos:

```sh
server.port=8095
```

#### Configurando Plugin nos projetos para monitoramento

1. No projeto escolhido para ser monitorado, deverá ser adicionado a dependência do client do Spring Boot Admin:

```sh
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.6.7</version>
</dependency>
```

2. Após adicionar dependência e baixá-la no projeto, é necessário a inclusão da configuração para o projeto admin server, para transferência das informações, no arquivo `application.properties` deve ser adicionado:

```sh
spring.boot.admin.client.url=http://localhost:8095
```
3. Após adicionar e configurar no cliente, podemos acessar nossa página web do projeto Admin e coletar as informações de nossas aplicações.

4. Para coleta de mais informações, podemos adicionar no nosso client o seguinte conteúdo no arquivo `application.properties`:

```sh
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=always
```

#### Notificações por e-mail

1. Para notificação de métricas e informações da saúde dos serviços, podemos configurar um serviço de e-mail adicionando no `pom.xml` do servidor do Spring Admin:

```sh
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
    <version>2.6.7</version>
</dependency>
```

2. Após adicionar o serviço de e-mail, é necessário a configuração no arquivo `application.properties` o seguinte conteúdo, substindo para informações válidas:

```sh
spring.boot.admin.notify.mail.enabled=true
spring.mail.host=smtp.example.com
spring.mail.username=smtp_user
spring.mail.password=smtp_password
spring.boot.admin.notify.mail.to=admin@example.com
```