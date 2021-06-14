# elasticsearch

elasticsearch를 사용하면서 주로 사용하게 되는 명령어들을 정리하려고 만들었습니다

참고하시면 좋을 것 같네요 ㅎㅎ

elasticsearch는 kibana외에 Linux curl 을 이용하여 조회할 수도 있습니다

    curl -XGET http://[호스트]/[directory]/_search -H 'Content-Type: application/json' -d'
    {
      "query":{
        "match": {
          "FIELD": "TEXT"
        }
      }
    }'


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
