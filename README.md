
# Descrição das atividades solicitadas.

Uma documetação sobre a sprint 6.


# Configurando o Elasticsearch e Kibana no Docker

Com o docker instalado e iniciado, faremos o pull das imagens do Elasticsearch e Kibana. Neste exemplo utilizaremos a versão 7.7.0. Você também pode conferir as imagens atuais no link: https://www.docker.elastic.co

```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.7.0
```

```bash
docker pull docker.elastic.co/kibana/kibana:7.7.0
```
Com as imagens baixadas, agora é hora que iniciar cada uma, primeiro o Elasticsearch:
```bash
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.7.0

```
Em seguida, é preciso executar o comando docker ps para listar o container em que o Elasticsearch está em execução. Será preciso copiar o container ID do elastic para configurarmos o link, conforme comando abaixo:
```bash
docker run - link {Container ID do Elasticsearch}:elasticsearch -p 5601:5601 docker.elastic.co/kibana/kibana:7.7.0

```

Com as duas imagens iniciadas, é hora conferir. Primeiro o Elasticsearch acessando o link http://localhost:9200

E agora, conferindo o Kibana acessando o link http://localhost:5601

Pronto, tudo certo!

Uma outra forma de subir as aplicações no docker é utilizando o Docker Compose, que é uma ferramenta para definir e executar aplicações complexas com docker, possibilitando o gerenciamento da sua aplicação como uma única entidade ao invés de trabalhar com containers individuais.

Com o compose, você define uma aplicação multi-container em um único arquivo YAML, dentro deste arquivo cada container é definido como um serviço e então usando um simples comando você inicia todos os serviços em um host.

```bash
version: '3.7'
services:

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.7.0
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      discovery.type: "single-node"
      ES_JAVA_OPTS: "-Xms2g -Xmx2g"
      xpack.monitoring.enabled: "true"
    volumes:
      - ./esdata:/usr/share/elasticsearch/data
      
  kibana:
    image: docker.elastic.co/kibana/kibana:7.7.0
    ports:
      - "5601:5601"
    environment:
      ELASTICSEARCH_URL: http://elasticsearch:9200
    depends_on:
      - elasticsearch
      
volumes:
  esdata:
    driver: local
```

image — Existem várias imagens do Docker no Elasticsearch, mas as mantidas pelo elastic.co são as melhores

ports — Para o Elasticsearch, a configuração mapeará a porta 9200 do seu contêiner para a porta 9200 do host. No Kibana, a configuração mapeará a porta 5601 do contêiner para a porta 5601 do host.

environment — Existem três variáveis de ambiente:

discovery.type — Para informar que será executado em apenas um nó
ES_JAVA_OPTS — Definimos o tamanho mínimo e máximo do heap para que o Elasticsearch tenha memória suficiente para executar, leia mais aqui.
xpack.monitoring.enable — Habilitamos o monitoramento X-Pack para verificação e comportamento do Elasticsearch.
Para testar, basta salvar o código acima em um arquivo com o nome docker-compose.yml e executar o comando:

docker-compose up