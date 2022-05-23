## Prometheus & Grafana

#### Referências
##### [Grafana](https://grafana.com/docs/grafana/latest/installation/docker/)
##### [Métricas do Spring boot](https://grafana.com/blog/2022/05/04/how-to-capture-spring-boot-metrics-with-the-opentelemetry-java-instrumentation-agent/)

##### [Todos Dashboards disponíveis](https://grafana.com/grafana/dashboards/)

Utilizaremos as seguintes tecnologias:

- Docker
- Grafana
- Prometheus
- Aplicação Spring Boot

1. Criar um arquivo com o seguinte conteúdo:

```sh
global:
  scrape_interval: 10s
scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
        - 
  - job_name: 'spring-boot-letscode'
    scrape_interval: 5s
    metrics_path: /actuator/prometheus
    static_configs:
      - targets: [ 'localhost:8081' ]
```

2. Instalar o prometheus conforme o comando abaixo:
```sh
docker run -d --name=prometheus -p 9090:9090 --network host -v <DIRETORIO_ARQUIVO>:/etc/prometheus prom/prometheus --config.file=/etc/prometheus/prometheus.yml
```

3. Instalar o grafana conforme o comando abaixo:
```sh
docker run -d --name=grafana \
-p 3000:3000 \
-e "GF_SERVER_PROTOCOL=http" \
-e "GF_SERVER_HTTP_PORT=3000" \
--network host \
grafana/grafana
```

**Obs:** O usuário e senha padrão do grafana é: ``admin``

4. No projeto Java que será monitorado, é necessário adicionar as seguintes dependências no ``pom.xml``

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
  <scope>runtime</scope>
</dependency>
```

5. No application.properties do projeto, é necessário adicionar o conteúdo abaixo:

```yml
management.endpoints.web.exposure.include=*
```

6. Acessando a interface ``http://localhost:9090`` você pode utilizar as consultas ``http://localhost:8080/actuator/prometheus`` com os nomes que estão pré-definidos.

[Imagem de Exemplo](https://i.imgur.com/hsQzz7G.png)

7. Acessando a interface ``http://localhost:3000`` crie um novo Datasource apontando para o Prometheus.

8. Crie seu Dashboard personalizado.

[Personalize suas métricas](https://www.metricfire.com/blog/using-grafana-and-graphite-to-monitor-server-load/#bDisk-IOb)

9. Adicione o seguinte [Dashboard](https://grafana.com/grafana/dashboards/4701) importando ele no grafana.