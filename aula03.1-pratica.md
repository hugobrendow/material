## Projeto Reativo

1. Primeiro passo é criar a estrutura do projeto no Spring Initializr, com as seguintes dependências:
    - Spring Reactive Web
    - Spring Data Reactive MongoDB
    - Lombok
    - Embedded MongoDB Database

2. Importar o projeto inicial na sua IDE preferida.

3. Criar uma entidade de usuário no pacote `entity` do seu projeto:

```sh

import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document
@Data
@NoArgsConstructor
public class Usuario {

    @Id
    private String id;

    private String nome;
    private String email;
    private String telefone;
    private String empresa;
}

```

4. Criar uma interface UsuarioRepository no pacote `repository`

```sh
import br.com.letscode.webflux.entity.Usuario;
import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface UsuarioRepository extends ReactiveCrudRepository<Usuario, String> {
}

```

5. Criar uma classe de serviço UsuarioService no pacote `service`

```sh

import br.com.letscode.webflux.entity.Usuario;
import br.com.letscode.webflux.repository.UsuarioRepository;
import org.springframework.stereotype.Service;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@Service
public class UsuarioService {
    private final UsuarioRepository usuarioRepository;

    public UsuarioService(UsuarioRepository usuarioRepository) {
        this.usuarioRepository = usuarioRepository;
    }

    public Flux<Usuario> getAllUsuarios() {
        return usuarioRepository.findAll();
    }

    public Mono<Usuario> getUsuarioById(String id) {
        return usuarioRepository.findById(id);
    }

    public Mono<Void> deleteUsuarioById(String id) {
        return usuarioRepository.deleteById(id);
    }

    public Mono<Void> deleteAll() {
        return usuarioRepository.deleteAll();
    }

    public Mono<Usuario> createUsuario(Usuario usuario) {
        return usuarioRepository.save(usuario);
    }
}


```

6. No controlador, nós podemos ter 2 formas de fazer a mesma coisa, a primeira é utilizando as tradicionais annotations utilizadas no SpringMVC, ou utilizando RouterFunction, para endpoints funcionais.

```sh

import br.com.letscode.webflux.entity.Usuario;
import br.com.letscode.webflux.service.UsuarioService;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/v1/usuarios")
public class UsuarioControlerMVC {
    private final UsuarioService usuarioService;

    public UsuarioControlerMVC(UsuarioService usuarioService) {
        this.usuarioService = usuarioService;
    }

    @GetMapping
    public Flux<Usuario> getAllUsuarios() {
        return usuarioService.getAllUsuarios();
    }

    @GetMapping("/{id}")
    public Mono<Usuario> getUsuarioById(@PathVariable String id) {
        return usuarioService.getUsuarioById(id);
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Usuario> createUsuario(@RequestBody Usuario usuario) {
        return usuarioService.createUsuario(usuario);
    }

    @DeleteMapping("/{id}")
    public Mono<Void> deleteUsuarioById(@PathVariable String id) {
        return usuarioService.deleteUsuarioById(id);
    }
}


```

7. A segunda opção para utilização da controller, segue o modelo abaixo:


```sh

import br.com.letscode.webflux.entity.Usuario;
import br.com.letscode.webflux.service.UsuarioService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpStatus;
import org.springframework.web.reactive.config.EnableWebFlux;
import org.springframework.web.reactive.function.server.RouterFunction;
import org.springframework.web.reactive.function.server.RouterFunctions;
import org.springframework.web.reactive.function.server.ServerResponse;

@Configuration
@EnableWebFlux
public class UsuarioController {
    private static String BASE_URL = "/v1/usuarios";
    private final UsuarioService usuarioService;

    public UsuarioController(UsuarioService usuarioService) {
        this.usuarioService = usuarioService;
    }

    @Bean
    public RouterFunction<ServerResponse> getAllUsuario() {
        return RouterFunctions.route().GET(BASE_URL, request -> ServerResponse.ok().body(usuarioService.getAllUsuarios(), Usuario.class)).build();
    }

    @Bean
    public RouterFunction<ServerResponse> getUsuarioById() {
        return RouterFunctions.route()
                .GET(BASE_URL.concat("/{id}"), request -> {
                    String id = request.pathVariable("id");
                    return ServerResponse.ok().body(usuarioService.getUsuarioById(id), Usuario.class);
                }).build();
    }

    @Bean
    public RouterFunction<ServerResponse> createUsuario() {
        return RouterFunctions.route()
                .POST(BASE_URL.concat("/{id}"), request -> request.bodyToMono(Usuario.class).flatMap(usuarioService::createUsuario)
                        .flatMap(usuario -> ServerResponse.status(HttpStatus.CREATED).body(usuario, Usuario.class))).build();
    }

    @Bean
    public RouterFunction<ServerResponse> deleteById() {
        return RouterFunctions.route()
                .DELETE(BASE_URL.concat("/{id}"), request -> {
                    String id = request.pathVariable("id");
                    return ServerResponse.ok().body(usuarioService.deleteUsuarioById(id), Usuario.class);
                }).build();
    }

}
```

8. Adicionar o conteúdo abaixo no arquivo `applications.properties`

```sh
spring.data.mongodb.uri=mongodb://localhost:27017/usuarios
```

9. Criar o arquivo MongoConfig.java no diretório `config`

```sh

import com.mongodb.reactivestreams.client.MongoClient;
import com.mongodb.reactivestreams.client.MongoClients;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.data.mongodb.config.AbstractReactiveMongoConfiguration;
import org.springframework.data.mongodb.repository.config.EnableReactiveMongoRepositories;

@EnableReactiveMongoRepositories
public class MongoConfig extends AbstractReactiveMongoConfiguration {

    @Value("${mongo.uri}")
    String mongoUri;

    @Override
    public MongoClient reactiveMongoClient() {
        return MongoClients.create(mongoUri);
    }

    @Override
    protected String getDatabaseName() {
        return "usuarios";
    }
}

```

10. Criar o arquivo `docker-compose.yml` e executar o comando abaixo:

```sh
version: '3.8'
services:
  mongodb:
    image: mongo
    volumes:
      - mongodata:/data/db
    ports:
      - "27017:27017"

volumes:
  mongodata:
```

Após criado, executar o seguinte comando:

```sh
docker-compose up -d
```

11. Executar o run da sua aplicação e testar os endpoints criados.