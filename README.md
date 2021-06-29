# elasticsearch

elasticsearch를 사용하면서 주로 사용하게 되는 명령어들을 정리하려고 만들었습니다

참고하시면 좋을 것 같네요 ㅎㅎ


kibana console에서 my_index 내에 모든 데이터를 조회하는 쿼리문입니다

    GET [my_index]/_search
    {
      "query": {
        "match_all": {}
      }
    }


kibana console에서 my_index 내에 조건에 맞는 데이터를 조회하는 쿼리문입니다  
regexp match 등 여러 조건의 쿼리문이 있습니

    GET [my_index]/_search
    {
      "query":{
        "match": {
          "FIELD": "TEXT"
        }
      }
    }

elasticsearch는 kibana외에 Linux curl 을 이용하여 조회할 수도 있습니다

    curl -XGET http://[호스트]/[directory]/_search -H 'Content-Type: application/json' -d'
    {
      "query":{
        "match": {
          "FIELD": "TEXT"
        }
      }
    }'


일정 조건을 만들어 해당 조건을 반복검색하게 할 수 있습니다

    POST _scripts/[template_id]
    {
      "script": {
        "lang": "mustache",
        "source": {
          "from": "{{from}}{{^from}}0{{/from}}",
          "size": "{{size}}{{^size}}10{{/size}}",
          "query": {
            "match": {
              "FIELD": "{{qeury}}"
            }
          }
        }
      }
    }
    
추가된 스크립트는 다음과 같이 조회할 수 있습니다

    GET _scripts/[template_id]
    
사용법은 다음과 같습니다
    
    GET _search/template
    {
      "id": "[template_id]",
      "params": {
        "from": 0,
        "size": 1,
        "query" : "검색어"
      }
    }

[아래는 query.json 스크립트 예시입니다](https://github.com/bigstones/elasticsearch/tree/main/code)

    {
        "script": {
            "lang": "mustache",
            "source": {
                "from": "{{from}}",
                "size": "{{size}}",
                "sort": [{"timestamp" : "desc"},
                    "_score"
                ],
                "query": {
                    "bool": {
                        "must": [
                            {
                                "multi_match": {
                                    "query": "{{query}}",
                                    "type": "cross_fields",
                                    "fields": [
                                        "brand",
                                        "item.*",
                                    ]
                                }
                            }
                        ],
                        "filter": [{
                            "term": {
                                "state1": true
                            }
                        },{
                            "term": {
                                "state2": false
                            }
                        }]
                    }
                }
            }
        }
    }


## 수정

일괄 데이터 수정 시 다음과 같은 쿼리문을 사용하면 됩니다

    POST images/_update_by_query
    {
      "script":{
        "source":"ctx._source.Position = params.location",
        "params":{
          "location" : "지역"
        }
      },
      "query":{
        "term":{
          "Position" : ""
        }
      }
    }
