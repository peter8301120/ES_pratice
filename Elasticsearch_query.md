# Elasticsearch #  

## Query-part1 ##

| Item | Description | Remark |
| :--: | :---------- | :----: |
| from | 從第幾個匹配文檔返回查詢 | ex.1 |
| size | 一次返回多少個文檔 | ex.1 |
| match_all(空查詢) | 找出所有文檔 | ex.2 |
| match | 根據條件查詢類似filter但有對文檔評分，較花時間 | ex.3 | 
| multi_match | 可匹配多個條件的查詢 | ex.4 | 

##### ex.1 #####
```js
GET /_search
{
  "from": 30,
  "size": 10
}
```
##### ex.2 #####
```js
GET /_search
{
    "query": {
        "match_all": {}
    }
}
```
##### ex.3 #####
```js
GET /_search
{
    "query": {
        "match": {
            "tweet": "elasticsearch"
        }
    }
}
```
##### ex.4 #####
```js
GET /_search
{
    "query": {
        "multi_match": {
            "query":    "full text search",
            "fields":   [ "title", "body" ]
        }
    }
}
```  
## Query-part2 ##  

| Item | Description | Remark |
| :--: | :---------- | :----: |
| range | 找出符合範圍文檔的查詢gt, gte, lt, lte | ex.5 |
| bool | 複合查詢，可含多個查詢語句 | ex.6 |
|  | must, must_not(必定需要符合的條件) |  |
|  | should, should_not(不一定要符合但符合時得分增加) |  |
|  | filter(根據條件查詢，不計算文檔得分速度較快) |  |
| term, terms | 精確值查詢類似filter, 但是會評分 | ex.7 |
| exists, missing | 查詢某些欄位是否有值 | ex.8 |
| constant_score | 不評分的查詢可取代只有filter的bool查詢 | ex.9 | 
  
##### ex.5 #####
```js
GET /_search
{
    "query": {
        "range": {
            "age": {
                "gte":  20,
                "lt":   30
            }
        }
    }
}
```  
##### ex.6 #####
```js
GET /_search
{
    "query": {
        "bool": {
            "must":     { "match": { "title": "how to make millions" }},
            "must_not": { "match": { "tag":   "spam" }},
            "should": [
                { "match": { "tag": "starred" }},
                { "range": { "date": { "gte": "2014-01-01" }}}
            ]
        }
    }
}
```  
##### ex.7 #####
```js
GET /_search
{
    "query": {
        "term" : {
            "price" : 20
        }
    }
}
```  
##### ex.8 #####
```js
GET /_search
{
    "query": {
        "exists":   {
            "field":    "title"
        }
    }
}
```  
##### ex.9 #####
```js
GET /_search
{
    "query": {
        "constant_score":   {
            "filter": {
                "term": { "category": "ebooks" } 
            }
        }
    }
}
```  
