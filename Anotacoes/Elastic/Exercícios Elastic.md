16/06/2022-17:01
**Status:** #🌲 
**Tags:** [[Elasticsearch]]


# GPU Dataset

## 1 - Sort Aggs

#### Question
Create a query that returns the 3 categories with the highest average TDP. Filter <span style="color:red">out</span> the "Unknown" category.

#### Hint
Aggregations can be sorted via the "bucket_sort" aggs

#### Answer
~~~JSON
GET gpu_benchmark/_search
{
  "size": 0, 
  "aggs": {
    "category": {
      "terms": {
        "field": "category.keyword",
        "size": 3
      },
      "aggs": {
        "tdp": {
          "avg": {
            "field": "tdp"
          }
        },
        "sorted": {
          "bucket_sort": {
            "sort": [
              {
                "tdp": {
                  "order": "desc"
                }
              }
            ]
          }
        }
      }
    }
  },
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "category.keyword": "Unknown"
          }
        }
      ]
    }
  }
}
~~~


## 3 - Ingest Pipeline / Reindex

#### Question
Use reindex and ingest pipelines to add the field "family" to all docs.
This field should contain the first word of the "gpu_name" field.
Also apply this as the default pipeline for this index.

#### Hint
Use the "\s" regex to match all spaces.

#### Answer
##### Creating the pipeline
~~~JSON
PUT _ingest/pipeline/add_gpu_family
{
  "description": "Adds family field",
  "processors": [
    {
      "split": {
        "field": "gpu_name",
        "separator": "\\s",
        "target_field": "family_non_split"
      }
    },
    {
      "set": {
        "field": "family",
        "value": "{{{family_non_split.0}}}"
      }
    },
    {
      "remove": {
        "field": "family_non_split"
      }
    }
  ]
}
~~~

##### Reindex to a backup index
~~~JSON
POST _reindex
{
  "dest": {
    "index": "gpu_benchmark-bkp",
    "pipeline": "add_gpu_family"
  },
  "source": {
    "index": "gpu_benchmark"
  }
}
~~~

##### Reindex back to original index
~~~JSON
POST _reindex
{
  "dest": {
    "index": "gpu_benchmark",
    "pipeline": "add_gpu_family"
  },
  "source": {
    "index": "gpu_benchmark-bkp"
  }
}
~~~

##### Add default pipeline
~~~JSON
PUT gpu_benchmark/_settings
{
  "default_pipeline": "add_gpu_family"
}
~~~

##### Remove backup index
~~~JSON
DELETE gpu_benchmark-bkp
~~~


## 4 - Aggs

#### Question
For each gpu family, return the amount of categories.
Filter <span style="color:red">out</span> the "Unknown" category

#### Hint
Use the "cardinality" aggregator to return the amount of unique values

#### Answer
~~~JSON
GET gpu_benchmark/_search
{
  "aggs": {
    "family": {
      "terms": {
        "field": "family.keyword",
        "size": 100
      },
      "aggs": {
        "quantidade": {
          "cardinality": {
            "field": "category"
          }
        }
      }
    }
  },
  "size": 0,
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "category": "Unknown"
          }
        }
      ]
    }
  }
}
~~~



# [Olympic Events](https://georgebridgeman.com/exercises)

## [Part 01](https://georgebridgeman.com/exercises/olympic-data/olympics-01/)

### Exercise 09

#### Question
The `Height` and `Weight` fields suffer from the same problem as the `Age` field. Later exercises will require numeric-type queries for these fields so we want to exclude any document we can’t use in our analyses. In a single request, delete all documents from the `olympic-events` index that have a value of `NA` for either the `Age`, `Height` or `Weight` field.

#### Answer
~~~JSON
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
~~~


### Exercise 10

#### Question
Notice how the `Games` field contains both the Olympic year and season. Create an ingest pipeline called `split_games` that will split this field into two new fields - `year` and `season` - and remove the original `Games` field.

#### Hint
- Use the "Ingest Node Pipelines" option inside "Stack Management" to create the pipeline via interface
- The magic keyword "{{\_\_source.FIELD}" can be used to access document fields

#### Answer
~~~JSON
PUT _ingest/pipeline/split_games
{
  "description": "Splits the games field into year and season fields",
  "processors": [
    {
      "split": {
        "field": "Games",
        "separator": "\\s"
      }
    },
    {
      "set": {
        "field": "year",
        "value": "{{_source.Games.0}}"
      }
    },
    {
      "set": {
        "field": "season",
        "value": "{{_source.Games.1}}"
      }
    },
    {
      "remove": {
        "field": "Games"
      }
    }
  ]
}
~~~

### Exercise 11

#### Question
Ensure your new pipeline is working correctly by simulating it with these values:

-   1998 Summer
-   2014 Winter

#### Answer
~~~JSON
POST test/_doc/1?pipeline=split_games
{
  "Games": "1998 Summer"
}

POST test/_doc/1?pipeline=split_games
{
  "Games": "2014 Winter"
}
~~~

#### Correct way to simulate pipelines
~~~JSON
POST /_ingest/pipeline/my-pipeline-id/_simulate
{
  "docs": [
    {
      "_index": "index",
      "_id": "id",
      "_source": {
        "foo": "bar"
      }
    },
    {
      "_index": "index",
      "_id": "id",
      "_source": {
        "foo": "rab"
      }
    }
  ]
}
~~~


### Exercise 12

#### Answer
~~~JSON
PUT olympic-events-fixed
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "properties": {
      "athleteId": {
        "type": "integer"
      },
      "age": {
        "type": "short"
      },
      "height": {
        "type": "short"
      },
      "weight": {
        "type": "short"
      },
      "athleteName": {
        "type": "text", 
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      },
      "gender": {
        "type": "keyword"
      },
      "team": {
        "type": "keyword"
      },
      "noc": {
        "type": "keyword"
      },
      "year": {
        "type": "short"
      },
      "season": {
        "type": "keyword"
      },
      "city": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      },
      "sport": {
        "type": "keyword"
      },
      "event": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      },
      "medal": {
        "type": "keyword"
      }
    }
  }
}
~~~


## [Part 02](https://georgebridgeman.com/exercises/olympic-data/olympics-02/)

### Exercise 13

#### Question
Reindex the data in the `olympic-events` index into the new `olympic-events-fixed` index created in exercise 12 using the `split_games` pipeline created in exercise 10.

#### Answer
~~~JSON
POST _reindex
{
  "source": {
    "index": "olympic-events"
  },
  "dest": {
    "index": "olympic-events-fixed",
    "pipeline": "split_games"
  }
}
~~~

### Exercise 14

#### Question
Look at the mapping for the `olympic-events-fixed` index. Notice how Elasticsearch has created new fields. We created the mapping for this index with the same field names as before but we put all the field names in lowercase. Field names are case sensitive, so `Age` and `age` are different, distinct fields to Elasticsearch.

Also notice that the new mapping uses `athleteId` instead of `ID`, `athleteName` instead of `Name` and `gender` instead of `Sex`.

We’ll need to correct this by tearing down the new index and reindexing with an additional pipeline to use the correct field names. To save us constantly having to recreate the index with the right mappings, we can leverage index templates.

Create an index template called `olympic-events` for new indices with a name beginning with `olympic-events-`. Use the mapping and settings we defined in exercise 12 and configure the mapping so Elasticsearch will throw an exception if a document contains a field not defined in the mapping.

#### Answer
~~~JSON
{
  "template": {
    "settings": {
      "index": {
        "number_of_shards": "1",
        "number_of_replicas": "0"
      }
    },
    "mappings": {
      "dynamic": "strict",
      "dynamic_templates": [],
      "properties": {
        "age": {
          "type": "short"
        },
        "athleteId": {
          "type": "integer"
        },
        "athleteName": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        },
        "city": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        },
        "event": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        },
        "gender": {
          "type": "keyword"
        },
        "height": {
          "type": "short"
        },
        "medal": {
          "type": "keyword"
        },
        "noc": {
          "type": "keyword"
        },
        "season": {
          "type": "keyword"
        },
        "sport": {
          "type": "keyword"
        },
        "team": {
          "type": "keyword"
        },
        "weight": {
          "type": "short"
        },
        "year": {
          "type": "short"
        }
      }
    },
    "aliases": {}
  }
}
~~~

### Exercise 15

#### Question
Create a new ingest pipeline called `reconcile_fields` to replace all fields with their correct field names (except for the `Games` field), then also execute the `split_games` pipeline.


#### Answer
~~~JSON
PUT _ingest/pipeline/reconcile_fields
{
  "description": "Changes field names for the olympic_events import",
  "processors": [
    {
      "rename": {
        "field": "Age",
        "target_field": "age"
      }
    },
    {
      "rename": {
        "field": "ID",
        "target_field": "athleteId"
      }
    },
    {
      "rename": {
        "field": "Name",
        "target_field": "athleteName"
      }
    },
    {
      "rename": {
        "field": "Sex",
        "target_field": "gender"
      }
    },
    {
      "rename": {
        "field": "Height",
        "target_field": "height"
      }
    },
    {
      "rename": {
        "field": "Weight",
        "target_field": "weight"
      }
    },
    {
      "rename": {
        "field": "City",
        "target_field": "city"
      }
    },
    {
      "rename": {
        "field": "Medal",
        "target_field": "medal"
      }
    },
    {
      "rename": {
        "field": "NOC",
        "target_field": "noc"
      }
    },
    {
      "rename": {
        "field": "Sport",
        "target_field": "sport"
      }
    },
    {
      "rename": {
        "field": "Team",
        "target_field": "team"
      }
    },
    {
      "pipeline": {
        "name": "split_games"
      }
    }
  ]
}
~~~

### Exercise 16

#### Question
Test your new pipeline with the following document:
~~~JSON
{
  "NOC": "ARG",
  "Sex": "M",
  "City": "Los Angeles",
  "Weight": "98",
  "Name": "Ernesto Arturo Alas",
  "Sport": "Shooting",
  "Games": "1984 Summer",
  "Event": "Shooting Men's Free Pistol, 50 metres",
  "Height": "186",
  "Team": "Argentina",
  "ID": 2224,
  "Medal": "NA",
  "Age": "54"
}
~~~

~~~JSON
POST /_ingest/pipeline/my-pipeline-id/_simulate
{
  "docs": [
    {
      "_index": "index",
      "_id": "id",
      "_source": {
        "foo": "bar"
      }
    },
    {
      "_index": "index",
      "_id": "id",
      "_source": {
        "foo": "rab"
      }
    }
  ]
}
~~~


#### Answer
~~~JSON
POST teste/_doc/1?pipeline=reconcile_fields
{
  "NOC": "ARG",
  "Sex": "M",
  "City": "Los Angeles",
  "Weight": "98",
  "Name": "Ernesto Arturo Alas",
  "Sport": "Shooting",
  "Games": "1984 Summer",
  "Event": "Shooting Men's Free Pistol, 50 metres",
  "Height": "186",
  "Team": "Argentina",
  "ID": 2224,
  "Medal": "NA",
  "Age": "54"
}
~~~

### Exercise 17

#### Question
Delete the `olympic-events-fixed` index.

#### Answer
~~~JSON
DELETE olympic-events-fixed
~~~


### Exercise 18

#### Question
Reindex the data in the `olympic-events` index into a new `olympic-events-fixed` index using the `reconcile_fields` pipeline. If Elasticsearch throws any exceptions, you may have missed a field in your pipeline.

#### Answer
~~~JSON
POST _reindex
{
  "source": {
    "index": "olympic-events"
  },
  "dest": {
    "index": "olympic-events-fixed",
    "pipeline": "reconcile_fields"
  }
}
~~~


## [Part 03](https://georgebridgeman.com/exercises/olympic-data/olympics-03/)

### Exercise 19

#### Question
Write a single query to find the name of all Gymnastics events. There are less than 100 Gymnastics event types.

#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "aggs": {
    "events": {
      "terms": {
        "field": "event.keyword",
        "size": 10000
      }
    }
  }, 
  "query": {
    "match": {
      "sport": "Gymnastics"
    }
  },
  "size": 0
}
~~~

### Exercise 20

#### Question
Write a single query to find the average weight for male and female competitors in Gymnastics events.

#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "aggs": {
    "gender": {
      "terms": {
        "field": "gender",
        "size": 20
      },
      "aggs": {
        "avg_weight": {
          "avg": {
            "field": "weight"
          }
        }
      }
    }
  }, 
  "query": {
    "match": {
      "sport": "Gymnastics"
    }
  },
  "size": 0
}
~~~

### Exercise 21

#### Question
Write a single query to find the year that each of the 590 unique events first appeared in the Olympic Games, and which events were introduced most recently.

#### Hint
Use [bucket sort](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/search-aggregations-pipeline-bucket-sort-aggregation.html) to sort aggs by year

#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "aggs": {
    "event": {
      "terms": {
        "field": "event.keyword",
        "size": 1000
      },
      "aggs": {
        "first_year": {
          "min": {
            "field": "year"
          }
        },
        "sorted": {
          "bucket_sort": {
            "sort": [
              {
                "first_year": { "order": "desc" }
              }
            ]
          }
        }
      }
    }
  }, 
  "size": 0
}
~~~


### Exercise 22

#### Question
Write a query to return only the following fields for the 50 tallest athletes in the 2016 Rio de Janeiro Games:

-   athleteName
-   team
-   sport
-   age
-   height
-   weight
-   gender

#### Hint
Use the "\_source" key to define which values should be returned

#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "_source": ["athleteName", "team", "sport", "age", "height", "weight", "gender"], 
  "size": 50,
  "query": {
    "match": {
      "city.keyword": "Rio de Janeiro"
    }
  },
  "sort": [
    {
      "height": {
        "order": "desc"
      }
    }
  ]
}
~~~


### Exercise 23

#### Question
The weight and height fields are in metric. Weight is in `kg` and height is in `cm`. Add a scripted field called `weightLbs` to the previous query to return the weight in lbs. The formula for this is: `Weight * 2.2`


#### Hint
- The "script_fields" key can be used to add fields to each document on runtime
- With painless query scripting, use the "doc\['FIELD'\].value" to access values

#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "_source": ["athleteName", "team", "sport", "age", "height", "weight", "gender"], 
  "size": 50,
  "query": {
    "match": {
      "city.keyword": "Rio de Janeiro"
    }
  },
  "sort": [
    {
      "height": {
        "order": "desc"
      }
    }
  ],
  "script_fields": {
    "weightLbs": {
      "script": {
        "lang": "painless",
        "source": "doc['weight'].value * 2.2"
      }
    }
  }
}
~~~


### Exercise 24

#### Question
Add a scripted field called `bmi` to the previous query to return the BMI for each athlete, calculated using the following formula: `Weight / (Height in m squared)`

#### Hint
Parentheses can be used to execute math in the correct order

#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "_source": ["athleteName", "team", "sport", "age", "height", "weight", "gender"], 
  "size": 50,
  "query": {
    "match": {
      "city.keyword": "Rio de Janeiro"
    }
  },
  "sort": [
    {
      "height": {
        "order": "desc"
      }
    }
  ],
  "script_fields": {
    "weightLbs": {
      "script": {
        "lang": "painless",
        "source": "doc['weight'].value * 2.2"
      }
    },
    "bmi": {
      "script": {
        "lang": "painless",
        "source": "doc['weight'].value / ((doc['height'].value / 100) * (doc['height'].value / 100))"
      }
    }
  }
}
~~~


## [Part 04](https://georgebridgeman.com/exercises/olympic-data/olympics-04/)

### Exercise 25

#### Question
Write a query to return the first 50 documents for gold medal athletics events, in descending age order.


#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "query": {
    "match": {
      "medal": "Gold"
    }
  },
  "size": 50,
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}
~~~

### Exercise 26

#### Question
Write a query to match swimming events where either:

-   The athlete’s weight was between 60kg and 70kg
-   The athlete’s age was less than 20

Enhance the query so the results identify whether the weight, age, or both matched the search criteria.

#### Hint
[Como dar enhance??](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/search-request-body.html#request-body-search-highlighting)


#### Answer
~~~JSON
GET olympic-events-fixed/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "range": {
            "age": {
              "lt": 20
            }
          }
        },
        {
          "range": {
            "weight": {
              "gte": 60,
              "lt": 70
            }
          }
        }
      ],
      "minimum_should_match": 1
    }
  }
}
~~~

### Exercise 28

#### Question
Change the number of replica shards for the `olympic-noc-regions` index to be 0. The index should then be green and contain 230 documents.

#### Answer
~~~JSON
PUT olympic-noc-regions/_settings
{
  "number_of_replicas": 0
}
~~~

### Exercise 29

#### Question
Create an enrich policy and ingest pipeline that uses the `enrich` processor to add details of the National Olympic Committee to each document in the `olympic-events-fixed` index. Call the policy `olympic-noc-append` and the pipeline `enrich-noc`. Add details of the matching NOC entry to a new field called `nocDetails`, matching on the `noc` field.

#### Hint
If your pipeline does not find the enrich policy, maybe you forgot to execute such policy.

#### Answer
##### Enrich Policy
~~~JSON
PUT /_enrich/policy/olympic-noc-append
{
  "match": {
    "indices": "olympic-noc-regions",
    "match_field": "noc",
    "enrich_fields": ["notes", "region"]
  }
}

POST _enrich/policy/olympic-noc-append/_execute
~~~

##### Pipeline
~~~JSON
PUT _ingest/pipeline/enrich-noc
{
  "processors": [
    {
      "enrich": {
        "field": "noc",
        "policy_name": "olympic-noc-append",
        "target_field": "nocDetails"
      }
    }
  ]
}
~~~

### Exercise 30

#### Question
Create a new index called `olympic-events-enriched`, into which we can reindex the Olympic events but with some enriched fields. Change the mapping settings for the new index so we can add fields dynamically.

#### Answer
~~~JSON
PUT olympic-events-enriched
{
  "mappings": {
    "dynamic": true,
  }
}
~~~


### Exercise 31

#### Question
Reindex the `olympic-events-fixed` index into `olympic-events-enriched`, running it through the `enrich-noc` ingest pipeline. Once complete, verify the new field was added to the `olympic-events-fixed` index, and populated with details of the associated NOC.

#### Answer
~~~JSON
POST _reindex
{
  "source": {
    "index": "olympic-events-fixed"
  },
  "dest": {
    "index": "olympic-events-enriched",
    "pipeline": "enrich-noc"
  }
}
~~~






# Exam
### Task 2
~~~JSON
GET blogs/_search
{
  "aggs": {
    "calendar_year": {
      "date_histogram": {
        "field": "publish_date",
        "calendar_interval": "year"
      },
      "aggs": {
        "nr_authors": {
          "cardinality": {
            "field": "authors.uid.keyword"
          }
        }
      }
    }
  },
  "size": 0,
  "query": {
    "match_phrase": {
      "content": "open source"
    }
  }
}
~~~

### Task 3

#### Answer

##### Atualiza mapping
~~~JSON
PUT task3
{
  "settings": {
    "number_of_replicas": 0,
    "number_of_shards": 1,
    "analysis": {
      "analyzer": {
        "content_analyzer": {
          "filter": "lowercase",
          "char_filter": "html_strip",
          "tokenizer": "standard"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "authors": {
        "properties": {
          "company": {
            "type": "keyword"
          },
          "first_name": {
            "type": "keyword"
          },
          "full_name": {
            "type": "text",
            "analyzer": "standard"
          },
          "job_title": {
            "type": "keyword"
          },
          "last_name": {
            "type": "keyword"
          },
          "uid": {
            "type": "keyword"
          }
        }
      },
      "category": {
        "type": "keyword"
      },
      "content": {
        "type": "text",
        "fields": {
          "eng": {
            "type": "text",
            "analyzer": "english"
          }
        }
      },
      "locale": {
        "type": "keyword"
      },
      "publish_date": {
        "type": "date",
        "format": "iso8601"
      },
      "tags": {
        "properties": {
          "elastic_stack": {
            "type": "keyword"
          },
          "level": {
            "type": "keyword"
          },
          "product": {
            "type": "keyword"
          },
          "topic": {
            "type": "keyword",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "use_case": {
            "type": "keyword"
          },
          "use_cases": {
            "type": "keyword"
          }
        }
      },
      "title": {
        "type": "text",
        "analyzer": "standard",
        "fields": {
          "search": {
            "type": "search_as_you_type"
          }
        }
      },
      "url": {
        "type": "keyword"
      }
    }
  }
}
~~~

##### Reindex
~~~JSON
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "task3"
  }
}
~~~

### Task 4
#### Answer

##### Atualiza Mapping
~~~JSON
PUT task4
{
  "settings": {
    "number_of_shards": 6,
    "number_of_replicas": 0
  },
  "mappings": {
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "bytes_sent": {
        "type": "long"
      },
      "content_type": {
        "type": "keyword"
      },
      "geo.location": {
        "type": "geo_point"
      },
      "request": {
        "type": "keyword"
      },
      "response": {
        "type": "keyword"
      },
      "runtime_ms": {
        "type": "long"
      },
      "verb": {
        "type": "keyword"
      }
    }
  }
}
~~~

##### Pipeline
~~~JSON
[
      {
        "remove" : {
          "field" : "is_https"
        }
      },
      {
        "rename" : {
          "field" : "geoip_location_lat",
          "target_field" : "geo.location.lat"
        }
      },
      {
        "rename" : {
          "field" : "geoip_location_lon",
          "target_field" : "geo.location.lon"
        }
      },
      {
        "user_agent" : {
          "field" : "user_Agent"
        }
      },
      {
        "remove" : {
          "field" : "user_Agent"
        }
      }
    ]
~~~

##### Reindex
~~~JSON
POST _reindex
{
  "source": {
    "index": "web_traffic"
  },
  "dest": {
    "index": "task4",
    "pipeline": "task4"
  }
}
~~~


### Task 5

#### Answer
##### Atualiza Mapping
~~~JSON
PUT task5
{
  "settings": {
    "number_of_replicas": 0,
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "authors": {
        "type": "nested"
      },
      "category": {
        "type": "keyword"
      },
      "content": {
        "type": "text"
      },
      "locale": {
        "type": "keyword"
      },
      "publish_date": {
        "type": "date",
        "format": "iso8601"
      },
      "tags": {
        "properties": {
          "elastic_stack": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "level": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "product": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "topic": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "use_case": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "use_cases": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          }
        }
      },
      "title": {
        "type": "text"
      },
      "url": {
        "type": "keyword"
      }
    }
  }
}
~~~

##### Reindex
~~~JSON
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "task5"
  }
}
~~~

##### Search
~~~JSON
GET task5/_search
{
  "query": {
    "nested": {
      "path": "authors",
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "authors.first_name": "david"
              }
            },
            {
              "match": {
                "authors.job_title": "engineer"
              }
            }
          ]
        }
      }
    }
  }
}
~~~


### Task 6

#### Answer

##### Enrich Policy
~~~JSON
PUT _enrich/policy/enrich_blogs
{
  "match": {
    "indices": "categories",
    "match_field": "uid",
    "enrich_fields": ["title"]
  }
}
POST /_enrich/policy/enrich_blogs/_execute
~~~


##### Pipeline
~~~JSON
PUT _ingest/pipeline/task6
[
      {
        "enrich" : {
          "field" : "category",
          "policy_name" : "enrich_blogs",
          "target_field" : "categories"
        }
      },
      {
        "remove" : {
          "field" : "category"
        }
      }
    ]
~~~

##### Reindex
~~~JSON
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "task6",
    "pipeline": "task6"
  }
}
~~~


### Task 8

#### Answer

##### Lifecycle policy
~~~JSON
PUT _ilm/policy/my.metrics-system-dev-policy
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_age": "2m"
          },
          "set_priority": {
            "priority": 100
          }
        }
      },
      "warm": {
        "min_age": "2m",
        "actions": {
          "readonly": {},
          "set_priority": {
            "priority": 50
          }
        }
      },
      "cold": {
        "min_age": "5m",
        "actions": {
          "set_priority": {
            "priority": 0
          }
        }
      }
    }
  }
}
~~~


##### Index Template
~~~JSON
PUT _index_template/my.metrics-system-dev
{
  "template": {
    "settings": {
      "index": {
        "lifecycle": {
          "name": "my.metrics-system-dev-policy"
        },
        "number_of_shards": "1",
        "number_of_replicas": "0"
      }
    }
  },
  "index_patterns": [
    "my.metrics-system-dev*"
  ],
  "data_stream": {
    "hidden": false
  },
  "composed_of": [
    "metrics-mappings"
  ]
}
~~~

##### Start the stream
~~~JSON
PUT _data_stream/my.metrics-system-dev
~~~

#### Extra
##### Force data stream rollover
~~~JSON
POST /my.metrics-system-dev/_rollover/
~~~

##### Get data stream stats
~~~JSON
GET /_data_stream/my.metrics-system-dev/_stats?human=true
~~~

### Task 9

#### Answer
##### Create Snap Repo
~~~JSON
PUT /_snapshot/snap-repo
{
  "type": "fs",
  "settings": {
    "location": "/var/tmp/repo/search"
  }
}
~~~


### Task 10
#### Hint
When the word "boost" is mentioned, look for "highlight" on the docs.

#### Answer
Not working
~~~JSON
GET blogs/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "publish_date": {
              "gte": "now-2y/y"
            }
          }
        }
      ]
    },
    "boosting": {
      "negative_boost": 0.2, 
      "positive": {
        "match_phrase": {
          "content": "certified engineer"
        }
      },
      "negative": {
        "match": {
          "content": "certified engineer"
        }
      }
    }
  },
  "sort": [
    {
      "_score": {
        "order": "desc"
      }
    },
    {
      "_doc": {
        "order": "asc"
      }
    }
  ]
}
~~~

# Material

## Lab 2.2: Searching with the Query DSL

### Task 8
~~~JSON
GET blogs/_search
{
  "query": {
    "multi_match": {
      "query": "certified engineer",
      "fields": ["title", "content"]
    }
  },
  "highlight": {
    "fields": {
      "content": {}
    }
  }
}
~~~

### Task 9
~~~JSON
GET blogs/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match_phrase": {
            "title": "certified engineer"
          }
        },
        {
          "match_phrase": {
            "title": "certified engineer"
          }
        }
      ] 
    }
  }
}
~~~

### Task 10
~~~JSON
GET blogs/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "fuzzy": {
            "content": {
              "value": "certified engineer",
              "fuzziness": "5"
            }
          }
        },
        {
          "fuzzy": {
            "title": {
              "value": "certified engineer",
              "fuzziness": "5"
            }
          }
        }
      ]
    }
  }
}
~~~

### Task 11
~~~JSON
GET blogs/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "title": "meetups"
          }
        },
        {
          "match": {
            "content": "meetups"
          }
        }
      ],
      "minimum_should_match": 1,
      "filter": [
        {
          "range": {
            "FIELD": {
              "lte": "now-1y/y"
            }
          }
        }
      ]
    }
  }
}
~~~

## Lab 2.3: Aggregations

### Task 12
~~~JSON
GET blogs/_search
{
  "size": 0,
  "aggs": {
    "qtd": {
      "cardinality": {
        "field": "authors.uid.keyword"
      }
    }
  }
}
~~~

### Task 13
~~~JSON
GET blogs/_search
{
  "aggs": {
    "use_case": {
      "terms": {
        "field": "tags.use_case.keyword",
        "size": 1000
      }
    }
  },
  "query": {
    "bool": {
      "must": {
        "multi_match": {
          "query": "open source",
          "fields": [
            "title^2",
            "content"
          ],
          "type": "phrase"
        }
      }
    }
  }
}
~~~

### Task 14
~~~JSON
GET blogs/_search
{
  "aggs": {
    "authors": {
      "terms": {
        "field": "authors.uid.keyword",
        "size": 10
      }
    }
  }, 
  "size": 0
}
~~~

### Task 15
~~~JSON
GET blogs/_search
{
  "aggs": {
    "calendar_year": {
      "date_histogram": {
        "field": "publish_date",
        "interval": "year"
      },
      "aggs": {
        "use_case": {
          "terms": {
            "field": "tags.use_case.keyword",
            "size": 100
          },
          "aggs": {
            "author": {
              "terms": {
                "field": "authors.full_name.keyword",
                "size": 1
              }
            }
          }
        }
      }
    }
  }, 
  "size": 0
}
~~~


## Lab 3.1: Overview of Mappings

### Task 8

##### Update mapping
~~~JSON
PUT blogs_fixed/
{
  "mappings": {
    "properties": {
      "authors": {
        "properties": {
          "first_name": {
            "type": "keyword"
          },
          "full_name": {
            "type": "text"
          }
        }
      },
      "tags": {
        "properties": {
          "elastic_stack": {
            "type": "keyword"
          },
          "industry": {
            "type": "keyword"
          },
          "level": {
            "type": "keyword"
          },
          "product": {
            "type": "keyword"
          },
          "tags": {
            "type": "keyword"
          },
          "topic": {
            "type": "keyword"
          },
          "use_case": {
            "type": "keyword"
          },
          "use_cases": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
~~~

##### Update \_meta field
~~~JSON
PUT blogs_fixed/_mapping
{
  "_meta": {
    "created_by": "Lucas Eduardo Hoeltgebaum"
  }
}
~~~

### Task 10
~~~JSON
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fixed"
  }
}

~~~



# To Study
Paginação
From
Highlight

## [Analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/analysis-analyzers.html)
Placa
[HTML Strip](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/analysis-htmlstrip-charfilter.html)