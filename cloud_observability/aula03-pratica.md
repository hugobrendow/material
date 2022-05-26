## Spring Cloud Netflix

#### Referências

[Spring.io/service-registration](https://spring.io/guides/gs/service-registration-and-discovery/)


### Configurando Eureka - Service Registration and Discovery

1. Utilizando [Spring Starter](https://start.spring.io/), deve ser criado o servidor responsável pelo serviço. Composto pela seguinte dependência:

- Eureka Server

2. Após criar o projeto e abrir na sua IDE preferida. Deve ser adicionado as seguintes anotações na sua classe Principal do projeto:

```java
@SpringBootApplication
@EnableEurekaServer
public class Application {
 
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
 
}
```
3. Adicionar as seguintes configurações no `application.properties`

```properties
server.port=8761
 
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

#### Configurando Eureka Client

1. Adicionar a seguinte dependência no projeto para registro no Eureka:

```sh
Eureka Discovery Client
```

2. Adicionar a seguinte anotação na sua classe principal da aplicação:

```java
@EnableDiscoveryClient
```

3. Adicionar as seguintes configurações no `application.properties`:

```properties
server.port=8080
 
spring.application.name=[nome do projeto]
eureka.instance.hostname=localhost
```

4. Após validar, acessar a URL para verificar o Eureka cadastrado:

```url
http://localhost:8761/eureka/apps
```

### Configurando Feign

1. O primeiro passo para utilização é adicionar nas dependências:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>3.1.2</version>
</dependency>
```

2. Adicionar anotação em cima da classe principal da aplicação:

```java
@EnableFeignClients
```

3. Após habilitar, é necessário a criação de uma interface que irá representar a chamada para o serviço do Feign, exemplo:

```java
@FeignClient(url = "http://localhost:8085/usuarios", name = "usuario")
public interface UsuarioClient {

    @GetMapping
    List<Usuario> buscarTodos();
}
```

4. Para utilização do Ribbon, você pode criar uma classe no Eureka Server:

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    @LoadBalanced
    public RestTemplate init() {
        return new RestTemplate();
    }
}
```

5. Após a criação desta classe, nosso Feign Client pode ser acessível pela seguinte rota, pois o nome usuarios é convertido para o host onde está o serviço.

```java
@FeignClient(name = "usuarios")
public interface UsuarioClient {

    @GetMapping("/usuarios")
    List<Usuario> buscarTodos();
}
```

### Configurando Resilience4J para circuit breaker

1. Adicionar dependência no projeto onde será consumido outra API:

```properties
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot2</artifactId>
</dependency>
```

2. Adicionar conteúdo no arquivo `application.yml`:

```yml
resilience4j:
  circuitbreaker:
    configs:
      default:
        registerHealthIndicator: true
        slidingWindowSize: 10
        minimumNumberOfCalls: 3 # mínimo para mudar para o estado ABERTO
        permittedNumberOfCallsInHalfOpenState: 3
        automaticTransitionFromOpenToHalfOpenEnabled: true
        waitDurationInOpenState: 10s # Tempo para aguardar para HALF-OPEN
        failureRateThreshold: 50 # Quando 50% retornar falha, o estado muda para ABERTO
  instances:
    servico:
      baseConfig: default

```

3. Adicionar a anotation em cima do método que será visualizado para atribuição do Circuit Break:

```java
    @CircuitBreaker(name = "servico")
```