11/03/2022-09:30
**Status**: #🌲
**Tags**: [[Elastic Stack]], [[Database Não Relacional]]



# Resumo
- Fácil configuração através de arquivos [[YAML]]
- Facilmente escalável através de [[Cluster]]s
- Suporte de um grande leque de **Data Types**

# Conceitos

### Node
[[Instance]] do Elasticsearch
Pode ser de diferentes [roles](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#node-roles)
```ad-danger
title: Configuração mínima
1 node master
1 node data
## OR
1 node que realiza *todos* os trabalhos
```

### Índice
Nome que mapeia em qual [[#Shard]] devem ser realizadas [[#Requisições]]

### Shard
Separação dos dados em diferentes espaços, de forma que [[Parallel Processing]] seja mais fácil.
```ad-info
title: Clusterization
Em clusters com 2+ nodes, shards de 1 mesmo índice podem ser criadas em diferentes nodes.
```

### Réplica
Cópia dos dados de uma [[#Shard]]
Uma réplica nunca irá existir na mesma shard original, visto que isso seria redundante, como guardar um **Backup** no mesmo disco que seus dados.

### Mapping
Mapeamento de chaves para um [[#Índice]] específico
[Documentação de tipos](https://www.elastic.co/guide/en/elasticsearch/reference/8.3/mapping-types.html)

# Utilização

## Search Request (GET)

### Simples
~~~JSON
GET INDICE/_search
{
  "query": {
    "bool": {
      "must": [
          {"match": {CAMPO: VALOR}}
      ],
      "must_not": [
	      {"match": {CAMPO: VALOR}}
      ],
      "filter": [
	      {"match": {CAMPO: VALOR}}
      ]
    }
  }
}
~~~
A chave *must* define as <span style="color:green"> inclusões </span>
A chave *must_not* define as <span style="color:red"> exclusões </span>
A chave filter deve ser usada apenas para campo do tipo keyword, visto que essa faz com que o campo/valor específicado não altere o score dos documentos (melhor performance)

### Range
~~~JSON
GET INDICE/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            CAMPO: {
              "gte": DATA_INICIAL,
              "lte": DATA_FINAL
            }
          }
        }
      ]
    }
  }
}
~~~

```ad-info
Neste caso, ambos os valores são <span style="color:green">INCLUSOS</span>, para remover a inclusão, mudar "gte" para "gt" e "lte" para "lt"
```

### Múltiplos campos
Pesquisa por X em 1 ou mais campos do documento
~~~JSON
GET INDICE/_search
{
  "query": {
    "multi_match": {
      "query": VALOR,
      "fields": [CAMPO_1, CAMPO_2]
    }
  }
}
~~~

### Aggregation
[Documentação aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/8.3/search-aggregations.html)

#### Cardinality
~~~JSON
"aggs": {
  "authors": {
    "cardinality": {
      "field": "authors.uid.keyword"
    }
  }
}
~~~
Usado para contabilizar a quantidade de valores únicos em certo campo, só pode ser aplicado em campos do tipo ***keyword***

#### Terms
~~~JSON

~~~

### Scripted field (runtime)
~~~JSON

~~~

## Creation Request (POST)

### [[#Índice]]
~~~JSON
POST INDICE
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "id": {"type": "keyword"},
      "name": {"type": "text"},
      "age": {"type": "integer"},
      "height": {"type": "float"},
      "location": {"type": "geo_point"},
      "address": {
	      "type": "nested",
	      "properties": {
		      "country": {"type": "keyword"},
		      "region": {"type": "keyword"},
		      "number": {"type": "integer"}
	      }
      }
    }
  }
}
~~~

### Alias
~~~JSON
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "DADOS-*",
        "alias": "alias1",
        "is_write_index": true
      }
    }
  ]
}
~~~
Irá criar um alias de forma que, ao pesquisa por alias1, a requisição será executada em todos os índices que dão match em "DADOS-*"

#### [[Reindex]]
Deve ser realizado no servidor *DESTINO*, que irá então puxar dados do servidor remoto
~~~JSON
POST _reindex
{
  "max_docs": 1,
  "source": {
    "remote": {
      "host": "http://otherhost:9200",
      "username": "user",
      "password": "password"
    },
    "index": ["index1", "index2"],
    "size": 100
  },
  "dest": {
    "index": "destino",
    "pipeline": "meu_pipeline",
  }
}
~~~
**max_docs**: quantos registros devem ser transferidos
**size**: De quantos em quantos registros irá puxar
**pipeline** *opcional*: Pipeline para aplicar

## Update Request (PUT)

### Query Template
~~~JSON
PUT _scripts/my_query_template
{
  "script": {
    "lang": "mustache",
    "source": {
      "from": "{{from}}",
      "size": "{{size}}",
      "query": {
        "bool": {
          "must": [
            {"match": {"CAMPO": "{{VALOR}}"}}  
          ]
        }
      }
    }
  }
}
~~~

# Configuração
Deve ser configurado em arquivo YAML, como no exemplo a seguir
## Example Yaml
~~~YAML
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
cluster.name: Cluster-Hoeltgebaum
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
path.repo: /var/tmp/repo
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
xpack.security.enabled: true
xpack.security.enrollment.enabled: true
# xpack.security.transport.ssl.enabled: true
~~~

## Parâmetros
### server.host
**IP** onde o Elasticsearch será hosteado 
### cluster.name
Nome do [[Cluster]] 
### discovery.type
Forma de descoberta usada para se conectar a um [[Cluster]]
**single-node**: apenas 1 node
**multi-node**: 2+ nodes
```ad-info
title: multi-node
Para configurar clusters com mais de 1 node, outras configurações devem ser alteradas:
discovery.seed_hosts
cluster.initial_master_nodes
```
### bootstrap.memory_lock
Loca a memória do node - <span style="color: green">true</span> | <span style="color: red">false</span>
Se o config do java estiver 8GB, o node nunca deixará de usar 8GB.
### xpack.security.enabled
Abilita autenticação no node - <span style="color: green">true</span> | <span style="color: red">false</span>

### discovery.seed_hosts
Lista de endereços IP dos nodes que compõem o cluster

### cluster.initial_master_nodes

# Referências
[Documentação](https://www.elastic.co/elasticsearch/)
[Shards e Réplicas](https://stackoverflow.com/questions/15694724/shards-and-replicas-in-elasticsearch/15705989#15705989)
[Prova certificação](https://www.elastic.co/pt/training/elastic-certified-engineer-exam)
[Certificação](https://certified.elastic.co/c10d8a67-c822-4ace-88a1-acf8dd983884)
