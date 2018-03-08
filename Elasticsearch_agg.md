# Elasticsearch #  

## Aggregations-part1 ##  

### aggs底下需自訂義每個桶的名稱 ### 
| Item | Description | Remark |
| :--: | :---------- | :----: |
| terms | 根據某欄位分桶 | ex.1 |
| | field: 要分桶的欄位 | |
| | size: 返回的文檔個數 | |
| | collect_mode: 預設為深度優先，改為breadth_first則為廣度優先，某些聚合廣度優先較快且省記憶體 | [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_preventing_combinatorial_explosions.html) | 
| min, max, avg, sum | 針對桶計算最大, 最小, 平均, 總值 | ex.2 | 
| | field: 要計算的欄位 | |
| histogram | 可以根據自訂的欄位和區間來分桶 | ex.3 | 
| | field: 要分桶的欄位 | |
| | interval: 每個桶的區間 | |
| date_histogram | 與histogram類似，針對時間來分桶 | ex.4 | 
| | field: 要分桶的欄位 | |
| | interval: 每個桶的區間，以日曆術語表示，例:month | |
| | format: 返回的桶中日期的格式，方便閱讀，例:yyyy-MM-dd | |
| | min_doc_count: 如果設為0的話即使是空的桶也會返回結果，避免有些桶的結果沒返回 | |
| | extended_bounds: 返回邊界中所有桶的值，避免有時候文檔時間不夠導致有缺少的區間，例:少了9月份的文檔如果沒設這個就會沒返回9月的桶 | min, max |
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
