# Link para os desafios
## [Exercícios Elastic](https://georgebridgeman.com/exercises/olympic-data/olympics-01/)

## Desafio 01 e 02(Configurar o Elastic e Kibana):
### Instalação Docker:
#### Precisamos de um arquivo "docker-compose.yml" com a seguinte configuração:
``` python
version: '3'
services:
    elastic:
        image: elasticsearch:7.17.3
        volumes:
            - ./elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
        ports:
            - "9200:9200"
    kibana:
        image: kibana:7.17.3
        volumes:
            - ./kibana.yml:/usr/share/kibana/config/kibana.yml
        ports:
            - "5601:5601"
```
#### Logo após precisamos de um arquivo "elasticsearch.yml" com a seguinte configuração: 
``` python
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: Cluster-Firmo
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: Master-Node
#node.master: true
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
# path.data: /var/lib/elasticsearch
#
# Path to log files:
#
# path.logs: /var/log/elasticsearch
# path.repo: /var/tmp/repo
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
# bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
network.host: 0.0.0.0
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
discovery.type: single-node
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.seed_hosts: ["10.0.3.2:9300", "10.0.3.2:9201"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["Master-Node"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true
#
# ---------------------------------- Security ----------------------------------
#
#                                 *** WARNING ***
#
# Elasticsearch security features are not enabled by default.
# These features are free, but require configuration changes to enable them.
# This means that users don’t have to provide credentials and can get full access
# to the cluster. Network connections are also not encrypted.
#
# To protect your data, we strongly encourage you to enable the Elasticsearch security features. 
# Refer to the following documentation for instructions.
#
# https://www.elastic.co/guide/en/elasticsearch/reference/7.16/configuring-stack-security.html
# xpack.security.enabled: true
# xpack.security.enrollment.enabled: true
# xpack.security.authc.api_key.enabled: true
# xpack.security.transport.ssl.enabled: true
```

#### Logo após precisamos de um arquivo "kibana.yml" com a seguinte configuração: 
``` python
# For more configuration options see the configuration guide for Kibana in
# https://www.elastic.co/guide/index.html

# =================== System: Kibana Server ===================
# Kibana is served by a back end server. This setting specifies the port to use.
server.port: 5601

# Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
# The default is 'localhost', which usually means remote machines will not be able to connect.
# To allow connections from remote users, set this parameter to a non-loopback address.
server.host: "0.0.0.0"

# Enables you to specify a path to mount Kibana at if you are running behind a proxy.
# Use the `server.rewriteBasePath` setting to tell Kibana if it should remove the basePath
# from requests it receives, and to prevent a deprecation warning at startup.
# This setting cannot end in a slash.
#server.basePath: ""

# Specifies whether Kibana should rewrite requests that are prefixed with
# `server.basePath` or require that they are rewritten by your reverse proxy.
# Defaults to `false`.
#server.rewriteBasePath: false

# Specifies the public URL at which Kibana is available for end users. If
# `server.basePath` is configured this URL should end with the same basePath.
# server.publicBaseUrl: "https://kibana2.hoeltgebaum.com"

# The maximum payload size in bytes for incoming server requests.
#server.maxPayload: 1048576

# The Kibana server's name. This is used for display purposes.
server.name: "Escolha o nome"

# =================== System: Kibana Server (Optional) ===================
# Enables SSL and paths to the PEM-format SSL certificate and SSL key files, respectively.
# These settings enable SSL for outgoing requests from the Kibana server to the browser.
# server.ssl.enabled: true
# server.ssl.certificate: /etc/certificates/cert.crt
# server.ssl.key: /etc/certificates/cert.key

# =================== System: Elasticsearch ===================
# The URLs of the Elasticsearch instances to use for all your queries.
elasticsearch.hosts: ["http://elastic:9200"]

# If your Elasticsearch is protected with basic authentication, these settings provide
# the username and password that the Kibana server uses to perform maintenance on the Kibana
# index at startup. Your Kibana users still need to authenticate with Elasticsearch, which
# is proxied through the Kibana server.
# elasticsearch.username: "kibana_system"
# elasticsearch.password: "9sDAD5T9q8bU36H2jdaP"


# xpack.security.enabled: true
# xpack.security.encryptionKey: "tG0HPMzDMdwXRbhP4i1r7k9vvaqfMLAR"

# Kibana can also authenticate to Elasticsearch via "service account tokens".
# Service account tokens are Bearer style tokens that replace the traditional username/password based configuration.
# Use this token instead of a username/password.
# elasticsearch.serviceAccountToken: "my_token"

# Time in milliseconds to wait for Elasticsearch to respond to pings. Defaults to the value of
# the elasticsearch.requestTimeout setting.
#elasticsearch.pingTimeout: 1500

# Time in milliseconds to wait for responses from the back end or Elasticsearch. This value
# must be a positive integer.
#elasticsearch.requestTimeout: 30000

# The maximum number of sockets that can be used for communications with elasticsearch.
# Defaults to `Infinity`.
#elasticsearch.maxSockets: 1024

# Specifies whether Kibana should use compression for communications with elasticsearch
# Defaults to `false`.
#elasticsearch.compression: false

# List of Kibana client-side headers to send to Elasticsearch. To send *no* client-side
# headers, set this value to [] (an empty list).
#elasticsearch.requestHeadersWhitelist: [ authorization ]

# Header names and values that are sent to Elasticsearch. Any custom headers cannot be overwritten
# by client-side headers, regardless of the elasticsearch.requestHeadersWhitelist configuration.
#elasticsearch.customHeaders: {}

# Time in milliseconds for Elasticsearch to wait for responses from shards. Set to 0 to disable.
#elasticsearch.shardTimeout: 30000

# =================== System: Elasticsearch (Optional) ===================
# These files are used to verify the identity of Kibana to Elasticsearch and are required when
# xpack.security.http.ssl.client_authentication in Elasticsearch is set to required.
#elasticsearch.ssl.certificate: /path/to/your/client.crt
#elasticsearch.ssl.key: /path/to/your/client.key

# Enables you to specify a path to the PEM file for the certificate
# authority for your Elasticsearch instance.
#elasticsearch.ssl.certificateAuthorities: [ "/path/to/your/CA.pem" ]
```

#### Com isso, é só usar o comando "docker-compose up" e acessar o [http://localhost:5601/](http://localhost:5601/) ou a porta escolhida na configuração.

## Desafio 03 (Adicionar dados)
### Download the dataset [from here](https://s3.amazonaws.com/elasticsearch-exercises.whatgeorgemade/events.ndjson.gz) and use Kibana’s Data Visualizer to upload the file into a new index called olympec-events.
#### Esse desafio consiste em fazer o dowload no site a cima e seguir os seguintes passos:
##### 1.  Ao fazer o dowload o arquivo vai estar compactado, ou seja, devemos descompactar o arquivo.
##### 2. Ao descompactar o arquivo precisamos adicionar ele no Data Visualizer do Kibana do seguinte modo:
###### Entre na barra de pesquisa do elastic e digite "Data Visualizer" e clique no "Machine Learning / Data Visualizer": ![[Pasted image 20221017110337.png]]
###### Ao entrar na tela você irá importar os dados clicando no "Select File" do "Import data":
![[Pasted image 20221017110257.png]]

###### Logo após você irá importar o arquivo desejado, arrastando ou selecionando ele:
![[Pasted image 20221017110529.png]]
###### Após carregar, é só clicar no "Import" e aguardar.
![[Pasted image 20221017110640.png]]
###### Ao finalizar devemos colocar o index name, nesse caso será "index_name" e depois é só clicar para importar:
![[Pasted image 20221017110747.png]]
###### E pronto, o arquivo foi importado:
![[Pasted image 20221017110850.png]]

## Desafio 06(Alterar o status do cluster para green)
### Change the cluster or index settings as required to get the cluster to a green status.
#### Para resolver este problema é bem simples. Como temos apenas 1 node, não adianta nada termos réplicas do mesmo, já que ele irá "salvar" nele mesmo. Nesse caso não precisamos ter nenhuma réplica, para fazermos isso precisamos de um simples POST:
``` JSON
PUT _settings
{
    "index" : {
        "number_of_replicas" : 0
    }
}
```
#### Com isso esse desafio é resolvido e o status do cluster se torna green.

## Desafio 07
### Look at how Elasticsearch has applied very general-purpose mappings to the data. Why has it chosen to use a `keyword` type for the `Age` field? Find **all** unique values for the `Age` field; there are less than 100 unique values for the `Age` field.
#### Suponha que você tem mais de 100 nomes cadastrado no seu Elastic, inclusive nomes repetidos diversas vezes, com isso você quer pegar todos os nomes e a quantidade deles. Por exemplo: existem 30 Lucas no banco mas e quero que retorne:
``` JSON
{
 "name": "Lucas"
 "doc_count": 30 
}
```
#### Que seria o tanto de documentos que tem o nome Lucas.
#### Para retornar isso precisamos de uma query bem simples. Como ele pede a Idade o nosso campo para pesquisa será o AGE e a forma que iremos utilizar, será com o terms do agregattions que agrega todos os diferentes campos pelo valor deles.
``` JSON
GET olympic-events/_search
{
  "size": 0,
  "aggs": {
    "Age": {
      "terms": {
        "field": "Age",
        "size": 100
      }
    }
  }
}
```

## Desafio 08 (Reindex)
###  We will be deleting data in the next exercise; making a backup is always prudent. Without making any changes to the data, reindex the `olympic-events` index into a new index called `olympic-events-backup`.
#### Para fazermos um reeindex é simples, primeiro precisamos passar qual é o index que queremos fazer o backup e para onde ele irá. No nosso caso o index será o olympic-events e o index para backup será  olympic-events-backup. Para fazermos isso precisamos da seguinte query:
``` JSON
POST _reindex
{
  "source": {
    "index": "olympic-events"
  },
  "dest": {
    "index": "olympic-events-backup"
  }
}
```

## Desafio 09
### The `Height` and `Weight` fields suffer from the same problem as the `Age` field. Later exercises will require numeric-type queries for these fields so we want to exclude any document we can’t use in our analyses. In a single request, delete all documents from the `olympic-events` index that have a value of `NA` for either the `Age`, `Height` or `Weight` field.
#### Este desafio pede para que a gente exclua todos os "fields" que tenha valores = "NA". Para isso precisamos fazer um delete_by_query :
``` JSON
POST olympic-events/_delete_by_query
{
  "query": {
    "bool": {
      "must": [
        {
          "multi_match": {
            "query": "NA",
            "fields": ["Age.keyword", "Height.keyword", "Weight.keyword"]
          }
        }
      ]
    }
  }
}
```

## Desafio 10 (Pipeline)
### Notice how the `Games` field contains both the Olympic year and season. Create an ingest pipeline called `split_games` that will split this field into two new fields - `year` and `season` - and remove the original `Games` field.
#### Para criar um pipeline devemos pesquisar por: ![[Pasted image 20221017163210.png]]
#### Logo após devemos criar um novo pipeline:
![[Pasted image 20221017163304.png]]
#### Neste momento devemos colocar o nome e a descrição do pipeline:
![[Pasted image 20221017163509.png]]
#### Com isso vamos iniciar a "lógica" por trás do pipeline. Clique no "Add a processor":
![[Pasted image 20221017163838.png]]
#### Para esse exercício, iremos usar o split já que ele pede para que a gente separe o ano e a estação em fields diferentes. Para isso precisamos criar uma lista onde o split será no espaço, assim como no python:
``` PYTHON
texto = "1984 Primavera"
texto.split(" ")
# ['1984', 'Primavera']
```
#### Então devemos selecionar o "Processor" split:
![[Pasted image 20221017164707.png]]
#### Ao seleciona-lo teremos diversas opções, iremos marcar apenas o "Field" e o "Separator". No Field iremos colocar o nome do field que queremos alterar, no caso desse exercício devemos colocar "Games" e o separador será pelo espaço " ", porém se você tentar deixar apenas espaço irá dar erro, para isso devemos usar o "\\s+" sem as aspas. Após preencher é só clicar em add/update:
![[Pasted image 20221017165808.png]]

#### Após isso iremos para outro processo que é o de settar os novos fields. Para isso devemos clicar em "Add a processor":
![[Pasted image 20221017170031.png]]
#### Após isso devemos selecionar o processor "Set". Com isso irá aparecer novamente os campos "Field" e  "Value". No campo Field iremos escolher um novo nome, nesse caso será "Year" pois é o que o exercicio pede. No campo "Value" iremos colocar a seguinte expressão "{{Games.0}}" sem as aspas logo após é só clicar em add/update. A configuração irá ficar assim:
![[Pasted image 20221017170749.png]]
#### Para criar o field "Season" é só repetir o processo porém alterar a chave de acesso da lista(No value) e o nome do Field. Logo após é só clicar em add/update. Irá ficar assim:
![[Pasted image 20221017170803.png]]
#### O próximo passo do exercício é excluir o field "Games". Para isso iremos repetir o processo de adicionar um processor e a opção dele será Remove. Neste caso só iremos marcar o field que queremos remover("Games").  Logo após é só clicar em add/update. Irá ficar assim:
![[Pasted image 20221017171126.png]]
#### Pronto, agora que finalizamos o pipeline vamos retornar ao Dev Tools. Para rodar esse pipeline é bem simples precisamos apenas colocar a seguinte query:
``` JSON
POST olympic-events/_update_by_query?pipeline=nome_do_pipeline
```
#### E pronto, finalizamos o desafio.
