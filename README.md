#RestFul API Reference

``` 请务必在header中设置user agent为 'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.71 Safari/537.36' ```

``` symbol 规则： 基础币种+计价币种。如BTC/USDT，symbol为btcusdt；ETH/BTC， symbol为ethbtc。以此类推```

## 接口列表
| 接口数据类型 | 请求方法 | 类型     | 描述  | 需要验签  |
| ------------ | ----- | ------ | ----- | ----- | 
| 系统信息       | [GET /v1/pub/timestamp](#查询当前系统时间)  | GET | 查询当前系统时间  |N | 1
| 交易品种信息       | [GET /v1/pub/symbols](#交易品种的计价货币及报价精度)  | GET | 交易品种的计价货币和报价精度  |N |1
| 交易品种信息       | [GET /v1/pub/currencies](#交易币种列表)  | GET | 交易币种列表  |N | 1
| 市场行情       | [GET /v1/mkt/trade](#单个交易对最新成交记录)  | GET | 单个symbol最新成交记录 | N |1
| 市场行情       | [GET /v1/mkt/tradeHistory](#单个交易对批量成交记录)  | GET | 单个symbol批量成交记录 | N |1
| 市场行情       | [GET /v1/mkt/kline](#获取K线数据)  | GET | 获取K线数据  | N | 1
| 市场行情       | [GET /v1/mkt/last24hr](#24小时交易聚合行情(单个交易对))  | GET | 24小时交易聚合行情(单个交易对)  |N |
| 市场行情       | [GET /v1/mkt/aggregated](#获取聚合行情)  | GET | 24小时交易最优报价聚合行情(单个交易对)  | N | 1
| 市场行情       | [GET /v1/mkt/tickers](#get-markettickers)  | GET | 全部交易对的交易行情 | N | 1
| 市场行情       | [GET /v1/mkt/depth](#市场深度行情MBP)  | GET | 市场深度行情MBP（单个交易对） | N | 1
|账户信息	|[GET /v1/priv/accounts](#查询用户的所有账户状态)|	GET| 查询用户的所有账户状态| Y|
|账户信息	|[GET /v1/priv/accounts/{accountId}/balance](#查询主站指定账户的余额)|GET|	查询指定账户余额	|Y|
|交易	|[POST /v1/priv/orders/place](#主站下单)|POST|	下单	|Y|
|交易	|[POST /v1/priv/orders/{orderId}/cancel](#申请撤销一个订单请求)|POST|	按order-id撤销一个订单	|Y|
|交易	|[POST /v1/priv/orders/batchCancel](#批量撤销订单)|POST|	按order_id, 批量撤销订单（最多50个)	|Y|
|交易	|[POST /v1/priv/orders/batchCancelEx](#批量撤销未成交的订单)|POST|	批量撤销未成交订单（最多100个) 	|Y|
|用户订单信息	|[GET /v1/priv/orders/{orderId}](#根据order-id查询订单详情)|GET|根据order-id查询订单详情|Y| 1
|用户订单信息	|[GET /v1/priv/orders/{orderId}/matchResults](#查询某个订单的成交明细)	|GET| 根据order-id查询订单的成交明细	|Y| 1
|用户订单信息	|[GET /v1/priv/orders](#查询当前委托或历史委托)	|GET|查询用户当前委托或历史委托订单 (最多100个)	|Y| 1
|用户订单信息	|[GET /v1/priv/matchResults](#查询当前成交历史成交)	|GET|查询用户当前成交或历史成交|Y| 1
|用户订单信息	|[GET /v1/priv/order/openOrders](#获取所有当前未成交订单)	|GET|查询用户当前未成交订单 (最多500个)|Y|


### 基本信息API

####  GET /v1/pub/timestamp <a name="查询当前系统时间">查询系统当前时间</a>

请求参数:

(无)

 响应数据:

```
系统时间戳
```

请求响应例子

```
/* GET /v1/priv/accounts */
{
  "status": "ok",
  "data": 1997900087028
}
```

####  GET /v1/pub/symbols <a name="交易品种的计价货币及报价精度">交易品种的计价货币和报价精度</a>

 请求参数:
(无)

响应数据:

| 参数名称    | 是否必须 | 数据类型   | 描述    | 取值范围 |
| -------------- | ---- | ------ | ----- | ---- |
| base-currency  | true | string | 基础币种  |      |
| quote-currency | true | string | 计价币种  |      |
| price-precision | true | string | 价格精度位数（0为个位） |      |
| amount-precision | true | string | 数量精度位数（0为个位，注意不是[交易限额](https://exshellglobal.zendesk.com/hc/zh-cn/articles/360000076392-%E4%BA%A4%E6%98%93%E9%99%90%E9%A2%9D)） |      |
| symbol-partition | true | string | 交易区 | ExShell 主区 |

 请求响应例子:

```
/* GET /v1/pub/symbols */
"data": [
    {
        "base-currency": "btc",
        "quote-currency": "usdt",
        "price-precision": 2,
        "amount-precision": 4,
        "symbol-partition": "main",
        "symbol": "btcusdt"
    }
    {
        "base-currency": "eth",
        "quote-currency": "usdt",
        "price-precision": 2,
        "amount-precision": 4,
        "symbol-partition": "main",
        "symbol": "ethusdt"
    }
  ]
```

####  GET /v1/pub/currencies <a name="交易币种列表">交易币种列表</a>

 请求参数:

(无)

 响应数据:

```
currency list
```

请求响应例子:

```
/* GET /v1/pub/currencies */
{
  "status": "ok",
  "data": [
    "usdt",
    "eth",
    "etc"
  ]
}
```

## 市场行情

在调用行情接口时，请添加get参数，key为AccessKeyId ，value为网页上申请的apikey的accesskey 。

例：

```
https://api.exshell.com/market/history/kline?period=1day&size=200&symbol=btcusdt&AccessKeyId=fff-xxx-ssss-kkk

```

#### GET /v1/mkt/kline <a name="获取K线数据">获取K线数据</a>

请求参数: 

| 参数名称 | 是否必须  | 类型     | 描述  | 默认值   | 取值范围  |
| ------------ | ----- | ------ | ----- | ----- | ------- |
| symbol       | true  | string | 交易对  |  | btcusdt, bchbtc...   |
| period       | true  | string | K线类型 |    | 1min, 5min, 15min, 30min, 60min, 1day, 1mon, 1week, 1year |
| size | false | integer | 获取数量 | 150 | [1,2000] |

响应数据: 

| 参数名称   | 是否必须 | 数据类型   | 描述   | 取值范围   |
| ------ | ---- | ------ | ----------- | ------ |
| status | true | string | 请求处理结果    | "ok" , "error" |
| ts     | true | number | 响应生成时间点，单位：毫秒  |    |
| tick   | true | object | KLine 数据   |      |
| ch     | true | string | 数据所属的 channel，格式： market.$symbol.kline.$period |    |

data 说明: 

```
  "data": [
{
    "id": K线id,
    "open": 开盘价,
    "close": 收盘价,当K线为最晚的一根时，是最新成交价
    "high": 最高价,
    "low": 最低价,
    "amount": 成交量,
    "count": 成交笔数,
    "vol": 成交额, 即 sum(每一笔成交价 * 该笔的成交量)
  }
]
```

请求响应示例: 

```
/* GET /v1/mkt/kline?period=1day&size=200&symbol=btcusdt */
{
  "status": "ok",
  "ch": "market.btcusdt.kline.1day",
  "ts": 1997223904689,
  "data": [
{
    "id": 1997184009,
    "open": 1739.2000,
    "close": 1873.0000,
    "high": 1959.0000,
    "low": 1447.0000,
    "amount": 17550.0264,
    "count": 0,
    "vol": 71091535.97866500
  },
// more data here
]
}

/* GET /v1/mkt/kline?period=not-exist&size=200&symbol=ethusdt */
{
  "ts": 1999758171270,
  "status": "error",
  "err-code": "invalid-parameter",
  "err-msg": "invalid period"
}

/* GET /v1/mkt/kline?period=1day&size=not-exist&symbol=ethusdt */
{
  "ts": 1999758221220,
  "status": "error",
  "err-code": "bad-request",
  "err-msg": "invalid size, valid range: [1,2000]"
}

/* GET /v1/mkt/kline?period=1day&size=200&symbol=not-exist */
{
  "ts": 1999758171270,
  "status": "error",
  "err-code": "invalid-parameter",
  "err-msg": "invalid symbol"
}
```

#### GET /v1/mkt/aggregated <a name="获取聚合行情">获取聚合行情(Ticker)</a>

请求参数: 

| 参数名称   | 是否必须  | 类型     | 描述  | 默认值   | 取值范围   |
| ------------ | ----- | ------ | -----  | ---  |  ----------- |
| symbol    | true  | string | 交易对   |   | btcusdt, bchbtc ...|

响应数据: 

| 参数名称   | 是否必须 | 数据类型   | 描述   | 取值范围   |
| ------ | ---- | ------ | -------  | ----  |
| status | true | string | 请求处理结果  | "ok" , "error" |
| ts     | true | number | 响应生成时间点，单位：毫秒    |     |
| tick   | true | object | K线数据    |      |
| ch     | true | string | 数据所属的 channel，格式： market.$symbol$.detail.merged |     |

tick 说明: 

```
  "tick": {
    "id": K线id,
    "open": 开盘价,
    "close": 收盘价,当K线为最晚的一根时，是最新成交价
    "high": 最高价,
    "low": 最低价,
    "amount": 成交量,
    "count": 成交笔数,
    "vol": 成交额, 即 sum(每一笔成交价 * 该笔的成交量)
    "bid": [买1价,买1量],
    "ask": [卖1价,卖1量]
  }

```

请求响应示例: 

```
/* GET /v1/mkt/aggregated?symbol=ethusdt */
{
"status":"ok",
"ch":"market.ethusdt.detail.merged",
"ts":1997225276959,
"tick":{
  "id":1997225270,
  "ts":1997225270000,
  "open":1469.0000,
  "close":1389.0000,
  "high":1985.0000,
  "low":1856.0000,
  "amount":81486.2926,
  "count":42122,
  "vol":157052744.85708200,
  "ask":[1885.0000,21.8804],
  "bid":[1884.0000,1.6702]
  }
}

/* GET /v1/mkt/aggregated?symbol=not-exist */
{
  "ts": 1490758171271,
  "status": "error",
  "err-code": "invalid-parameter",
  "err-msg": "invalid symbol"
}

```
#### GET /v1/mkt/tickers <a name="get-markettickers">全部symbol的交易行情</a>
```
{  
    "status":"ok",
    "ts":1019885463000,
    "data":[  
        {  
            "open":0.024297,      // 日K线 开盘价
            "close":0.022178,     // 日K线 收盘价
            "high":0.025255,      // 日K线 最高价
            "low":0.020110,       // 日K线 最低价
            "amount":23180.8510,  // 24小时成交量
            "count":82218,        // 24小时成交笔数
            "vol":983.0388715740, // 24小时成交额
            "symbol":"ethbtc"     // 交易对
        },
        {  
            "open":0.010345,
            "close":0.010656,
            "high":0.012368,
            "low":0.008088,
            "amount":78056.1860,
            "count":23077,
            "vol":971.7975953754,
            "symbol":"ltcbtc"
        }
    ]
}
```
注：当交易对尚未产生成交时，返回的数据里面 `open` `close` `high` `low` `amount` `count` `vol` 的值都为 `null`


#### GET /v1/mkt/depth <a name="市场深度行情MBP">获取 Market Depth 数据</a>

请求参数:

| 参数名称   | 是否必须  | 类型     | 描述    | 默认值   | 取值范围   |
| ------  | ----- | ------ | ------  | ----- | -------  |
| symbol     | true  | string | 交易对    |       | btcusdt, bchbtc... |
| type    | true  | string | Depth 类型     |       | step0, step1, step2, step3, step4, step5（合并深度0-5）；step0时，不合并深度 |

* 用户选择"合并深度"时，一定报价精度内的市场挂单将予以合并显示。合并深度仅改变显示方式，不改变实际成交价格。

响应数据:

| 参数名称   | 是否必须 | 数据类型   | 描述    | 取值范围    |
| ------ | ---- | ------ | -------  | ---  |
| status | true | string |       | "ok" 或者 "error" |
| ts     | true | number | 响应生成时间点，单位：毫秒    |     |
| tick   | true | object | Depth 数据    |     |
| ch     | true | string | 数据所属的 channel，格式： market.$symbol.depth.$type |  |

tick 说明:

```
  "tick": {
    "id": 消息id,
    "ts": 消息生成时间，单位：毫秒,
    "bids": 买盘,[price(成交价), amount(成交量)], 按price降序,
    "asks": 卖盘,[price(成交价), amount(成交量)], 按price升序
  }
```

请求响应示例:

```
/* GET /v1/mkt/depth?symbol=btcusdt&type=step1 */
{
  "status": "ok",
  "ch": "market.btcusdt.depth.step1",
  "ts": 1987472598811,
  "tick": {
    "id": 1987464585406,
    "ts": 1987464585406,
    "bids": [
      [6764, 2.1678], // [price, amount]
      [6763, 1.7162],
      [6761, 0.1],
      [6760, 12.8898],
      [6758, 1.2],
      [6755, 2.1009],
      [6754, 0.4708],
      [6753, 0.0564],
      [6751, 2.8031],
      [6750, 13.7785],
      [6749, 0.125],
      [6748, 4],
      [6742, 0.4337],
      [6740, 6.1612],
      [6736, 0.02],
      [6735, 1.3575],
      [6733, 2.002],
      [6732, 1.3449],
      [6730, 10.2974],
      [6729, 3.2226]
    ],
    "asks": [
      [6730, 1.2736],
      [6731, 0.0292],
      [6737, 2.5652],
      [6738, 4.2416],
      [6741, 1.9970],
      [6745, 1.188],
      [6746, 2.3412],
      [6751, 1.2609],
      [6759, 4.12],
      [6768, 3],
      [6770, 0.35],
      [6772, 2.61],
      [6774, 1.02],
      [6781, 7.1067],
      [6785, 2.198],
      [6786, 2.5206],
      [6788, 0.0166],
      [6791, 1.3218],
      [6792, 0.01],
      [6801, 0.034]
    ]
  }
}

/* GET /market/depth?symbol=ethusdt&type=not-exist */
{
  "ts": 1999759358098,
  "status": "error",
  "err-code": "invalid-parameter",
  "err-msg": "invalid type"
}
```


#### GET /v1/mkt/trade <a name="单个交易对最新成交记录">单个symbol最新成交记录</a>

请求参数:

| 参数名称   | 是否必须  | 类型  | 描述   | 默认值   | 取值范围  |
| -------  | ----- | ------ | ------ | ----- | ---- |
| symbol   | true  | string | 交易对   |   | btcusdt, bchbtc ... |

响应数据:

| 参数名称   | 是否必须 | 数据类型   | 描述   | 取值范围    |
| ------ | ---- | ------ | ----------| --------------- |
| status | true | string |    | "ok" 或者 "error" |
| ts     | true | number | 响应生成时间点，单位：毫秒    |      |
| tick   | true | object | Trade 数据      |     |
| ch     | true | string | 数据所属的 channel，格式： market.$symbol$.trade.detail |     |

tick 说明：

```
  "tick": {
    "id": 消息id,
    "ts": 最新成交时间,
    "data": [
      {
        "id": 成交id,
        "price": 成交价钱,
        "amount": 成交量,
        "direction": 主动成交方向,
        "ts": 成交时间
      }
    ]
  }
```

请求响应例子:

```
/* GET /v1/mkt/trade?symbol=ethusdt */
{
  "status": "ok",
  "ch": "market.btcusdt.trade.detail",
  "ts": 1987473346904,
  "tick": {
    "id": 650548679,
    "ts": 1987464451000,
    "data": [
      {
        "id": 650548679,
        "price": 6962.62,
        "amount": 0.312,
        "direction": "buy",
        "ts": 1987464451000
      }
    ]
  }
}

/* GET /v1/mkt/trade?symbol=not-exist */
{
  "ts": 1999759506428,
  "status": "error",
  "err-code": "invalid-parameter",
  "err-msg": "invalid symbol"
}
```
#### GET /v1/mkt/tradeHistory <a name="单个交易对批量成交记录">单个symbol批量成交记录</a>

请求参数:

| 参数名称   | 是否必须  | 类型   | 描述   | 默认值   | 取值范围    |
| ------- | ----- | ------ | ---- | ----- | ---  |
| symbol   | true  | string | 交易对   |       | btcusdt, bchbtc... |
| size  | false  |integer| 获取交易记录的数量    |  1   | [1, 2000]    |

响应数据:

| 参数名称   | 是否必须  | 类型  | 描述  | 默认值   | 取值范围   |
| -------- | ----- | ------ | --------  | ----- | ----  |
| status   | true  | string |    |    | ok, error   |
| ch     | true | string | 数据所属的 channel，格式： market.$symbol$.trade.detail  |    |
| ts    | true  |integer| 发送时间  |    |      |
| data  | true  | object | 成交记录    |    |    |

data 说明：

```
  "data": {
    "id": 消息id,
    "ts": 最新成交时间,
    "data": [
      {
        "id": 成交id,
        "price": 成交价,
        "amount": 成交量,
        "direction": 主动成交方向,
        "ts": 成交时间
      }
    ]
  }
```

请求响应例子:

```
/* GET  /v1/mkt/tradeHistory?symbol=btcusdt */
{
    "status": "ok",
    "ch": "market.btcusdt.trade.detail",
    "ts": 1903448925215,
    "data": [
        {
            "id": 36499997,
            "ts": 1903448920105,
            "data": [
                {
                    "id": 12572256642622,
                    "amount": 0.04,
                    "price": 6997,
                    "direction": "buy",
                    "ts": 1903448920105
                }
            ]
        }
    ]
}
```

#### GET /v1/mkt/last24hr <a name="24小时交易聚合行情(单个交易对)">获取24小时聚合成交量数据</a>

 请求参数:

| 参数名称    | 是否必须  | 类型  | 描述    | 默认值   | 取值范围  |
| ------- | ----- | ------ | ----- | ----- | ----  |
| symbol    | true  | string | 交易对   |    | btcusdt, bchbtc...|

 响应数据:

| 参数名称   | 是否必须 | 数据类型   | 描述     | 取值范围     |
| ------ | ---- | ------ | ------- | ------  |
| status | true | string |     | "ok" 或者 "error" |
| ts     | true | number | 响应生成时间点，单位：毫秒     |      |
| tick   | true | object | Detail 数据     |    |
| ch     | true | string | 数据所属的 channel，格式： market.$symbol.depth.$type |    |

tick 说明:

```
  "tick": {
    "id": 消息id,
    "ts": 24小时统计时间,
    "open": 前推24小时成交价,
    "close": 当前成交价,
    "high": 近24小时最高价,
    "low": 近24小时最低价,
    "amount": 24小时成交量,
    "count": 近24小时累积成交数,
    "vol": 近24小时累积成交额, 即 sum(每一笔成交价 * 该笔的成交量)
  }
```


请求响应例子

```
/* GET /v1/mkt/last24hr?symbol=btcusdt */
{
  "status": "ok",
  "ch": "market.btcusdt.detail",
  "ts": 1984473538995,
  "tick": {
    "ts": 1987464451000,
    "id": 1987464451,
    "open": 6870.54,
    "close": 6662.62,
    "high": 7019.00,
    "low": 5875.00,
    "amount": 9316.4346,
    "count": 1995,
    "vol": 21497376.905760
  }
}

/* GET  /v1/mkt/last24hr?symbol=not-exists */
{
  "ts": 1999759594751,
  "status": "error",
  "err-code": "invalid-parameter",
  "err-msg": "invalid symbol"
}
```

### 用户资产API

####  GET /v1/priv/accounts 
#### <a name="查询用户的所有账户状态">查询当前用户的所有账户(即account-ix)</a>

请求参数:

无

响应数据:

| 参数名称  | 是否必须 | 数据类型 | 描述 | 取值范围 |
| ----- | ---- | ------ | ----- | ----  |
| id    | true | long   | account-id |    |
| state | true | string | 账户状态  | working：正常, lock：账户被锁定 |
| type  | true | string | 账户类型  | spot：现货账户， margin：杠杆账户，otc：OTC账户  |

请求响应例子:

```
/* GET /v1/priv/accounts */
{
  "status": "ok",
  "data": [
    {
      "id": 100009,
      "type": "spot",
      "state": "working",
      "user-id": 1000
    }
  ]
}
```
####  GET /v1/priv/accounts/{account-id}/balance <a name="查询主站指定账户的余额">查询指定账户的余额</a>

请求参数

| 参数名称   | 是否必须 | 类型   | 描述   | 默认值  | 取值范围 |
| ---------- | ---- | ------ | --------------- | ---- | ---- |
| account-id | true | string | account-id，填在 path 中，可用 GET /v1/priv/accounts 获取 |      |      |

* 如果不知道自己的账户ID，请使用 ```GET /v1/priv/accounts``` 查询

响应数据:

| 参数名称  | 是否必须  | 数据类型   | 描述    | 取值范围   |
| ----- | ----- | ------ | ----- | ----- |
| id    | true  | long   | 账户 ID |      |
| state | true  | string | 账户状态  | working：正常  lock：账户被锁定 |
| type  | true  | string | 账户类型  | spot：现货账户， margin：杠杆账户，otc：OTC账户|
| list  | false | Array  | 子账户数组 |     |

list字段说明

| 参数名称   | 是否必须 | 数据类型   | 描述   | 取值范围    |
| -------- | ---- | ------ | ---- |  ------ |
| balance  | true | string | 余额   |    |
| currency | true | string | 币种   |    |
| type     | true | string | 类型   | trade: 交易余额，frozen: 冻结余额 |

请求响应例子:

```json
/* GET /v1/priv/accounts/'account-id'/balance */
{
  "status": "ok",
  "data": {
    "id": 100009,
    "type": "spot",
    "state": "working",
    "list": [
      {
        "currency": "usdt",
        "type": "trade",
        "balance": "600002115917.1362872650"
      },
      {
        "currency": "usdt",
        "type": "frozen",
        "balance": "378948.1199920000"
      },
     {
        "currency": "etc",
        "type": "trade",
        "balance": "499999894616.1302471000"
      },
      {
        "currency": "etc",
        "type": "frozen",
        "balance": "9281.6783000000"
      }
     {
        "currency": "eth",
        "type": "trade",
        "balance": "499999894616.1302471000"
      },
      {
        "currency": "eth",
        "type": "frozen",
        "balance": "9281.6783000000"
      }
    ],
    "user-id": 1000
  }
}
```

## 交易API

#### POST /v1/priv/orders/place <a name="主站下单">下单</a>

#### 请求参数

| 参数名称   | 是否必须  | 类型     | 描述    | 默认值  | 取值范围    |
| ----- | ----- | ------ | --------  | ---- | -------  |
| account-id | true  | string | 账户 ID，使用accounts方法获得。币币交易使用‘spot’账户的accountid；借贷资产交易，请使用‘margin’账户的accountid |      |     |
| amount     | true  | string | 限价单表示下单数量，市价买单时表示买多少钱，市价卖单时表示卖多少币 |   |   |
| price      | false | string | 下单价格，市价单不传该参数   |      |       |
| source     | false | string | 订单来源    | api，如果使用借贷资产交易，请填写‘margin-api’ |    |
| symbol     | true  | string | 交易对    |      | btcusdt, bchbtc...   |
| type       | true  | string | 订单类型    |    | buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单, buy-limit-maker, sell-limit-maker(详细说明见下)|

**buy-limit-maker**

当"下单价格">="市场最低卖出价"，订单提交后，系统将拒绝接受此订单；

当"下单价格"<"市场最低卖出价"，提交成功后，此订单将被系统接受。

**sell-limit-maker**

当"下单价格"<="市场最高买入价"，订单提交后，系统将拒绝接受此订单；

当"下单价格">"市场最高买入价"，提交成功后，此订单将被系统接受。


#### 响应数据:

| 参数名称 | 是否必须 | 数据类型 | 描述   | 取值范围 |
| ---- | ---- | ---- | ---- | ---- |
| data | false | string | 订单ID  |      |

#### 请求响应例子:

```json
/* POST /v1/priv/orders/place {
   "account-id": "100009",
   "amount": "10.1",
   "price": "100.1",
   "source": "api",
   "symbol": "ethusdt",
   "type": "buy-limit"
 } */
{
  "status": "ok",
  "data": "59378"
}
```

####  GET /v1/priv/order/openOrders <a name="获取所有当前未成交订单">查询用户当前未成交订单</a>

####  请求参数: 

`"account-id" 和 "symbol" 需同时指定或者二者都不指定。如果二者都不指定，返回最多500条尚未成交订单，按订单号降序排列。`

| 参数名称     | 是否必须 | 类型     | 描述           | 默认值  | 取值范围 |
| -------- | ---- | ------ | ------------ | ---- | ---- |
| account-id | true | string | 账号ID |      |      |
| symbol | true | string | 交易对 |      |   单个交易对字符串，缺省将返回所有符合条件尚未成交订单   |
| side | false | string | 主动交易方向 |      |   "buy"或者"sell"，缺省将返回所有符合条件尚未成交订单   |
| size | false | int | 所需返回记录数 |   10   |    [0,500]  |

####  响应数据: 

| 参数名称 | 是否必须 | 数据类型   | 描述    | 取值范围 |
| ---- | ---- | ------ | ----- | ---- |
| id | true | long | 订单号 |  |
| symbol| true | string | 交易对 |     |
| price | true | string | 下单价格 |    |
| created-at | true | int | 下单时间（毫秒） |  Unix时间戳  |
| type | true | string | 订单类型 |  buy-market, sell-market, buy-limit, sell-limit, buy-ioc, sell-ioc  |
| filled-amount | true | string | 下单时间（毫秒） |  对于非"部分成交"订单，此字段为 0  |
| filled-cash-amount | true | string | 已成交部分的订单价格(=已成交单量x下单价格) |  对于非"部分成交"订单，此字段为 0  |
| filled-fees | true | string | 已成交部分所收取手续费 |  对于非"部分成交"订单，此字段为 0  |
| source | true | string | 订单来源 |  sys, web, api, app  |
| state | true | string | 此订单状态 |  submitted（已提交）, partial-filled（部分成交）, cancelling（正在取消）  |

####  响应示例:3

```json
/* GET /v1/priv/order/openOrders */
{
  "status": "ok",
  "data": [
    {
      "id": 1957936,
      "symbol": "ethusdt",
      "account-id": 30925,
      "amount": "1.000000000000000000",
      "price": "0.553000000000000000",
      "created-at": 1939604762276,
      "type": "sell-limit",
      "filled-amount": "0.0",
      "filled-cash-amount": "0.0",
      "filled-fees": "0.0",
      "source": "web",
      "state": "submitted"
    }
  ]
}
```

####  POST /v1/priv/orders/{orderId}/cancel  <a name="申请撤销一个订单请求">按order-id撤销一个订单请求</a>

请求参数: 

| 参数名称     | 是否必须 | 类型     | 描述           | 默认值  | 取值范围 |
| -------- | ---- | ------ | ------------ | ---- | ---- |
| order-id | true | string | 订单ID，填在path中 |      |      |

响应数据: 

| 参数名称 | 是否必须 | 数据类型   | 描述    | 取值范围 |
| ---- | ---- | ------ | ----- | ---- |
| data | true | string | 订单 ID |      |

请求响应例子:

```
/* POST /v1/priv/orders/59378/cancel */
{
  "status": "ok",//注意，返回OK表示撤单请求成功。订单是否撤销成功请调用订单查询接口查询该订单状态
  "data": "59378"
}
```

#### POST /v1/priv/orders/batchCancel <a name="批量撤销订单">批量撤销订单</a>

请求参数:

| 参数名称  | 是否必须 | 类型   | 描述   | 默认值  | 取值范围 |
| ---- | ---- | ---- | ----  | ---- | ---- |
| order-ids | true | list | 撤销订单ID列表 |  |单次不超过50个订单id|

响应数据:

| 参数名称 | 是否必须  | 数据类型 | 描述    | 取值范围 |
| ---- | ----- | ---- | ----- | ---- |
| data | false | map | 撤单结果 |      |

请求响应例子:

```/v1/priv/orders/batchCancel
/* POST /v1/priv/orders/batchCancel */
{
  "order-ids": [
    "1", "2", "3"
  ]
}

-----

{
  "status": "ok",
  "data": {
    "success": [
      "1",
      "3"
    ],
    "failed": [
      {
        "err-msg": "记录无效",
        "order-id": "2",
        "err-code": "base-record-invalid"
      }
    ]
  }
}
```

####  POST  /v1/priv/orders/batchCancelEx  <a name="批量撤销未成交的订单">批量撤销未成交的订单</a>

请求参数: 

| 参数名称     | 是否必须 | 类型     | 描述           | 默认值  | 取值范围 |
| -------- | ---- | ------ | ------------ | ---- | ---- |
| account-id | true  | string | 账户ID     |     |      |
| symbol     | false | string | 交易对     |      |   单个交易对字符串，缺省将返回所有符合条件尚未成交订单  |
| side | false | string | 主动交易方向 |      |   "buy"或"sell"，缺省将返回所有符合条件尚未成交订单   |
| size | false | int | 所需返回记录数  |  100 |   [0,100]   |

响应数据: 

| 参数名称 | 是否必须 | 数据类型   | 描述    | 取值范围 |
| ---- | ---- | ------ | ----- | ---- |
| success-count | true | int | 成功取消的订单数 |     |
| failed-count | true | int | 取消失败的订单数 |     |
| next-id | true | long | 下一个符合取消条件的订单号 |    |

####  响应示例:

```json
/* POST /v1/priv/orders/batchCancelEx */
{
  "status": "ok",
  "data": {
    "success-count": 2,
    "failed-count": 0,
    "next-id": 695600
  }
}
```

####  GET/v1/priv/orders/{orderId} <a name="根据order-id查询订单详情">查询某个订单详情</a>

请求参数: 

| 参数名称     | 是否必须 | 类型  | 描述   | 默认值  | 取值范围 |
| -------- | ---- | ------ | -----  | ---- | ---- |
| order-id | true | string | 订单ID，填在path中 |      |      |

响应数据: 

| 参数名称     | 是否必须  | 数据类型   | 描述   | 取值范围     |
| ----------------- | ----- | ------ | -------  | ----  |
| account-id        | true  | long   | 账户 ID    |       |
| amount            | true  | string | 订单数量              |    |
| canceled-at       | false | long   | 订单撤销时间    |     |
| created-at        | true  | long   | 订单创建时间    |   |
| filled-amount     | true  | string | 已成交数量    |     |
| filled-cash-amount| true  | string | 已成交总金额     |      |
| filled-fees       | true  | string | 已成交手续费（买入为数字货币，卖出为法币） |     |
| finished-at       | false | long   | 订单变为终结态的时间，不是成交时间，包含"已撤单"状态    |     |
| id                | true  | long   | 订单ID    |     |
| price             | true  | string | 订单价格       |     |
| source            | true  | string | 订单来源   | api |
| state             | true  | string | 订单状态   | submitting , submitted 已提交, partial-filled 部分成交, partial-canceled 部分成交撤销, filled 完全成交, canceled 已撤销 |
| symbol            | true  | string | 交易对   | btcusdt, bchbtc ... |
| type              | true  | string | 订单类型   | buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单 |


请求响应例子:

```
/* GET/v1/priv/orders/{orderId} */
{
  "status": "ok",
  "data": {
    "id": 59378,
    "symbol": "ethusdt",
    "account-id": 100009,
    "amount": "10.1000000000",
    "price": "100.1000000000",
    "created-at": 1997901162594,
    "type": "buy-limit",
    "filled-amount": "10.1000000000",
    "filled-cash-amount": "1011.0100000000",
    "filled-fees": "0.0202000000",
    "finished-at": 6927901400467,
    "user-id": 1000,
    "source": "api",
    "state": "filled",
    "canceled-at": 0,
    "exchange": "exshell",
    "batch": ""
  }
}
```

####  GET/v1/priv/orders/{orderId}/matchresults  <a name="查询某个订单的成交明细">查询某个订单的成交明细</a>

请求参数:

| 参数名称  | 是否必须 | 类型  | 描述  | 默认值  | 取值范围 |
| -------- | ---- | ------ | -----  | ---- | ---- |
| order-id | true | string | 订单ID，填在path中 |      |      |

响应数据:

| 参数名称    | 是否必须 | 数据类型   | 描述   | 取值范围     |
| ------------- | ---- | ------ | -------- | -------- |
| created-at    | true | long   | 成交时间     |    |
| filled-amount | true | string | 成交数量     |    |
| filled-fees   | true | string | 成交手续费    |     |
| id            | true | long   | 订单成交记录ID |     |
| match-id      | true | long   | 撮合ID     |     |
| order-id      | true | long   | 订单 ID    |      |
| price         | true | string | 成交价格  |    |
| source        | true | string | 订单来源  | api      |
| symbol        | true | string | 交易对   | btcusdt, bchbtc...  |
| type          | true | string | 订单类型   | buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单 |

请求响应例子:

```
/* GET/v1/priv/orders/59378/matchresults */
{
  "status": "ok",
  "data": [
    {
      "id": 29553,
      "order-id": 59378,
      "match-id": 59335,
      "symbol": "ethusdt",
      "type": "buy-limit",
      "source": "api",
      "price": "100.1000000000",
      "filled-amount": "9.1155000000",
      "filled-fees": "0.0182310000",
      "created-at": 6927901400434
    }
  ]
}
```

####  GET /v1/priv/orders <a name="查询当前委托或历史委托">查询当前委托、历史委托</a>

请求参数:

| 参数名称   | 是否必须  | 类型     | 描述   | 默认值  | 取值范围   |
| ---------- | ----- | ------ | ------  | ---- | ----  |
| symbol     | true  | string | 交易对      |      |btcusdt, bchbtc ...  |
| types      | false | string | 查询的订单类型组合，使用','分割  |      | buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单 |
| start-date | false | string | 查询开始日期, 日期格式yyyy-mm-dd |      |      |
| end-date   | false | string | 查询结束日期, 日期格式yyyy-mm-dd |      |    |
| states     | true  | string | 查询的订单状态组合，使用','分割  |      | submitted 已提交, partial-filled 部分成交, partial-canceled 部分成交撤销, filled 完全成交, canceled 已撤销 |
| from       | false | string | 查询起始 ID   |      |    |
| direct     | false | string | 查询方向   |      | prev 向前，next 向后    |
| size       | false | string | 查询记录大小      |      |         |

响应数据: 

| 参数名称    | 是否必须  | 数据类型   | 描述   | 取值范围   |
| ----------------- | ----- | ------ | ----------------- | ----  |
| account-id        | true  | long   | 账户 ID    |     |
| amount            | true  | string | 订单数量    |   |
| canceled-at       | false | long   | 接到撤单申请的时间   |    |
| created-at        | true  | long   | 订单创建时间   |    |
| filled-amount      | true  | string | 已成交数量   |    |
| filled-cash-amount | true  | string | 已成交总金额    |    |
| filled-fees        | true  | string | 已成交手续费（买入为币，卖出为钱） |       |
| finished-at       | false | long   | 最后成交时间    |   |
| id                | true  | long   | 订单ID    |    |
| price             | true  | string | 订单价格  |    |
| source            | true  | string | 订单来源   | api  |
| state             | true  | string | 订单状态    | submitting , submitted 已提交, partial-filled 部分成交, partial-canceled 部分成交撤销, filled 完全成交, canceled 已撤销 |
| symbol            | true  | string | 交易对    | btcusdt, bchbtc, rcneth ... |
| type              | true  | string | 订单类型  | submit-cancel：已提交撤单申请  ,buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单 |


请求响应例子:

```
/* GET /v1/priv/orders */
{
  "status": "ok",
  "data": [
    {
      "id": 59378,
      "symbol": "ethusdt",
      "account-id": 100009,
      "amount": "10.1000000000",
      "price": "100.1000000000",
      "created-at": 1494901162595,
      "type": "buy-limit",
      "filled-amount": "10.1000000000",
      "filled-cash-amount": "1011.0100000000",
      "filled-fees": "0.0202000000",
      "finished-at": 6927901400467,
      "user-id": 1000,
      "source": "api",
      "state": "filled",
      "canceled-at": 0,
      "exchange": "exshell",
      "batch": ""
    }
  ]
}
```

####  GET /v1/priv/matchResults <a name="查询当前成交历史成交">查询当前成交、历史成交</a>

请求参数:

| 参数名称   | 是否必须  | 类型  | 描述   | 默认值  | 取值范围    |
| ---------- | ----- | ------ | ------ | ---- | ----------- |
| symbol     | true  | string | 交易对   | btcusdt, bchbtc ... |    |
| types      | false | string | 查询的订单类型组合，使用','分割   |      | buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单 |
| start-date | false | string | 查询开始日期, 日期格式yyyy-mm-dd | -61 days     | [-61day, now] |
| end-date   | false | string | 查询结束日期, 日期格式yyyy-mm-dd |   Now   |  [start-date, now]  |
| from       | false | string | 查询起始 ID    |   订单成交记录ID（最大值）   |     |
| direct     | false | string | 查询方向    |   默认next， 成交记录ID由大到小排序   | prev 向前，next 向后   |
| size       | false | string | 查询记录大小    |   100   | <=100  |

响应数据: 

| 参数名称   | 是否必须 | 数据类型   | 描述   | 取值范围   |
| ------------- | ---- | ------ | -------- | ------- |
| created-at    | true | long   | 成交时间     |    |
| filled-amount | true | string | 成交数量     |    |
| filled-fees   | true | string | 成交手续费    |    |
| id            | true | long   | 订单成交记录ID |    |
| match-id      | true | long   | 撮合ID     |    |
| order-id      | true | long   | 订单 ID    |    |
| price         | true | string | 成交价格     |    |
| source        | true | string | 订单来源     | api   |
| symbol        | true | string | 交易对      | btcusdt, bchbtc ...  |
| type          | true | string | 订单类型     | buy-market：市价买, sell-market：市价卖, buy-limit：限价买, sell-limit：限价卖, buy-ioc：IOC买单, sell-ioc：IOC卖单 |

请求响应例子:

```
/* GET /v1/priv/matchResults */
{
  "status": "ok",
  "data": [
    {
      "id": 29555,
      "order-id": 59378,
      "match-id": 59335,
      "symbol": "ethusdt",
      "type": "buy-limit",
      "source": "api",
      "price": "100.1000000000",
      "filled-amount": "0.9845000000",
      "filled-fees": "0.0019690000",
      "created-at": 1997901400467
    }
  ]
}

```
