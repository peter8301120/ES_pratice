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
| histogram | 可以根據自訂的欄位和區間來分桶 | ex.3, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_building_bar_charts.html) | 
| | field: 要分桶的欄位 | |
| | interval: 每個桶的區間 | |
| date_histogram | 與histogram類似，針對時間來分桶 | ex.4, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_returning_empty_buckets.html) | 
| | field: 要分桶的欄位 | |
| | interval: 每個桶的區間，以日曆術語表示，例:month | |
| | format: 返回的桶中日期的格式，方便閱讀，例:yyyy-MM-dd | |
| | min_doc_count: 如果設為0的話即使是空的桶也會返回結果，避免有些桶的結果沒返回 | |
| | extended_bounds: 返回邊界中所有桶的值，避免有時候文檔時間不夠導致有缺少的區間，例:少了9月份的文檔如果沒設這個就會沒返回9月的桶 | min, max |
##### ex.1 #####
```js
GET /cars/transactions/_search
{
    "aggs" : {
       "actors" : {
         "terms" : {
            "field" :        "actors",
            "size" :         10,
            "collect_mode" : "breadth_first" 
         }
    }
}
```
##### ex.2 #####
```js
GET /cars/transactions/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": { 
            "avg_price": { 
               "avg": {
                  "field": "price" 
               }
            }
         }
      }
   }
}
```
##### ex.3 #####
```js
GET /cars/transactions/_search
{
   "size" : 0,
   "aggs":{
      "price":{
         "histogram":{ 
            "field": "price",
            "interval": 20000
         },
         "aggs":{
            "revenue": {
               "sum": { 
                 "field" : "price"
               }
             }
         }
      }
   }
}
```
##### ex.4 #####
```js
GET /cars/transactions/_search
{
   "size" : 0,
   "aggs": {
      "sales": {
         "date_histogram": {
            "field": "sold",
            "interval": "month",
            "format": "yyyy-MM-dd",
            "min_doc_count" : 0, 
            "extended_bounds" : { 
                "min" : "2014-01-01",
                "max" : "2014-12-31"
            }
         }
      }
   }
}
```  
## Query-part2 ##  

| Item | Description | Remark |
| :--: | :---------- | :----: |
| global | 全局桶，不理會查詢結果將所有文檔加入桶中 | ex.5 |
| filter | 過濾桶，符合條件的文檔才加入桶中 | ex.6 |
|	cardinality | 近似聚合，計算某個欄位不同值的數量 | ex.7, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/cardinality.html) |
|  | field: 要計算的欄位 |  |
|  | precision_threshold: 0~40000，代表真正個數在幾以下的可以保持將近100%準確度，例100可保證返回值在100以下的正確率將近100%，但在100情況下即使維一值有百萬誤差也在5%之內 |  |
| percentiles | 對某欄位計算其百分位數 | ex.8, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_building_bar_charts.html) |
|  | field: 要計算的欄位 |  |
|  | percents: 要返回的百分位數，預設為[1, 5, 25, 50, 75, 95, 99] |  |
|  | values: 返回輸入的值為第幾百分位 |  |
| significant_terms | 針對某欄位找出異常指標 | ex.9, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_significant_terms_demo.htm) |
|  | field: 要找的欄位 |  |
|  | size: 返回的文檔個數 |  |

  
##### ex.5 #####
```js
GET /cars/transactions/_search
{
    "size" : 0,
    "query" : {
        "match" : {
            "make" : "ford"
        }
    },
    "aggs" : {
        "single_avg_price": {
            "avg" : { "field" : "price" } 
        },
        "all": {
            "global" : {}, 
            "aggs" : {
                "avg_price": {
                    "avg" : { "field" : "price" } 
                }

            }
        }
    }
}
```  
##### ex.6 #####
```js
GET /cars/transactions/_search
{
   "size" : 0,
   "query":{
      "match": {
         "make": "ford"
      }
   },
   "aggs":{
      "recent_sales": {
         "filter": { 
            "range": {
               "sold": {
                  "from": "now-1M"
               }
            }
         },
         "aggs": {
            "average_price":{
               "avg": {
                  "field": "price" 
               }
            }
         }
      }
   }
}
```  
##### ex.7 #####
```js
GET /cars/transactions/_search
{
    "size" : 0,
    "aggs" : {
        "distinct_colors" : {
            "cardinality" : {
              "field" : "color",
              "precision_threshold" : 100 
            }
        }
    }
}
```  
## Query-part3 ##  

| Item | Description | Remark |
| :--: | :---------- | :----: |
| percentiles | 對某欄位計算其百分位數 | ex.8, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/percentiles.html) |
|  | field: 要計算的欄位 |  |
|  | percents: 要返回的百分位數，預設為[1, 5, 25, 50, 75, 95, 99] |  |
|  | values: 返回輸入的值為第幾百分位 |  |
| significant_terms | 針對某欄位找出異常指標 | ex.9, [Example](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_significant_terms_demo.htm) |
|  | field: 要找的欄位 |  |
|  | size: 返回的文檔個數 |  |

  
##### ex.8-1 #####
```js
GET /website/logs/_search
{
    "size" : 0,
    "aggs" : {
        "zones" : {
            "terms" : {
                "field" : "zone" 
            },
            "aggs" : {
                "load_times" : {
                    "percentiles" : { 
                      "field" : "latency",
                      "percents" : [50, 95.0, 99.0] 
                    }
                },
                "load_avg" : {
                    "avg" : {
                        "field" : "latency"
                    }
                }
            }
        }
    }
}
```  
##### ex.8-2 #####
```js
GET /website/logs/_search
{
    "size" : 0,
    "aggs" : {
        "zones" : {
            "terms" : {
                "field" : "zone"
            },
            "aggs" : {
                "load_times" : {
                    "percentile_ranks" : {
                      "field" : "latency",
                      "values" : [210, 800] 
                    }
                }
            }
        }
    }
}
```  
##### ex.9 #####
```js
GET mlratings/_search
{
  "size" : 0,
  "query": {
    "filtered": {
      "filter": {
        "term": {
          "movie": 46970
        }
      }
    }
  },
  "aggs": {
    "most_sig": {
      "significant_terms": { 
        "field": "movie",
        "size": 6
      }
    }
  }
}
```  
