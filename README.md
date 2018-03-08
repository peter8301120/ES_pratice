# Elasticsearch #  

## Query ##

| Item | Description | Remark |
| :--: | :---------- | :----: |
| from | 從第幾個匹配文檔返回查詢 | ex.1 |
| size | 一次返回多少個文檔 | ex.1 |
| match_all(空查詢) | 找出所有文檔 | ex.2 |
| match | 根據條件查詢類似filter但有對文檔評分，較花時間 | ex.1 | 
| multi_match | 可匹配多個條件的查詢 | ex.1 |
| range | 找出符合範圍文檔的查詢gt, gte, lt, lte | ex.1 |
| bool | 複合查詢，可含多個查詢語句 | ex.1 |
|  | must, must_not(必定需要符合的條件) | ex.1 |
|  | should, should_not(不一定要符合但符合時得分增加) | ex.1 |
|  | filter(根據條件查詢，不計算文檔得分速度較快) | ex.1 |
| term, terms | 精確值查詢類似filter, 但是會評分 | ex.1 |
| exists, missing | 查詢某些欄位是否有值 | ex.1 |
| constant_score | 不評分的查詢可取代只有filter的bool查詢 | ex.1 |
