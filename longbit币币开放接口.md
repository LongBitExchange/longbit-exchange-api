## longbit币币开放接口

#### 接口签名

> 对所有API请求参数（加上access_secret，除去sign），根据参数名称的ASCII码表的顺序排序。如：foo:1, bar:2, foo_bar:3, secret:4排序后的顺序是bar:2, foo:1, foo_bar:3, secret:4。

> 将排序好的参数名和参数值拼装在一起，根据上面的示例得到的结果为：bar2foo1foo_bar3foobar4。

> 把拼装好的字符串采用utf-8编码，使用MD5算法md5(bar2foo1foo_bar3 secret4)；

###### 签名示例
设置参数值
```js
accessKey = qeqwe21312
accessSecret = nwekrk3h2ui3hri32hr
timestamp = 1557839220283
orderId = 23635556
```
按ASCII顺序排序
```js
accessKey = qeqwe21312
accessSecret = nwekrk3h2ui3hri32hr
orderId = 23635556
timestamp = 1557839220283
```
拼接参数名与参数值

```
accessKeyitqv7y3belss2efdaccessSecretoiutmajncmo757zehldotqiv7y3belssorderId23635556timestamp1557839220283
```

生成签名

```
sign = 6534a442b229f14c511d06cb3afb7932
```

java代码示例

```
引入依赖
<dependency>
    <groupId>commons-codec</groupId>
    <artifactId>commons-codec</artifactId>
    <version>1.10</version>
</dependency>


Map<String, String> param = new HashMap<>();
param.put("accessKey", "iv7y3b..");
param.put("accessSecret", "oajncmo757zehl...");
param.put("orderId", String.valueOf(23635556L));
param.put("timestamp", String.valueOf(1557839220283L));
String sign = sign(param);
        
        
public static String sign(Map<String, String> params) throws IOException {
    // 第一步：对参数进行ASCII排序
    String[] keys = params.keySet().toArray(new String[0]);
    Arrays.sort(keys);
    // 第二步：把所有参数名和参数值串在一起
    StringBuilder query = new StringBuilder();
    for (String key : keys) {
        String value = params.get(key);
        if (StringUtils.isBlank(value)) {
            continue;
        }
        query.append(key).append(value);
    }
    // 第三步：md5加密
    return DigestUtils.md5Hex(query.toString());
}
```
#### 域名
> https://api.longbit.io
>>  请科学上网

#### 接口api
##### 基本信息
##### 1.获取所有交易对
###### HTTP 请求
> GET /api/exchange/v1/common/symbols

###### 返回参数
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true | String | 交易对，GXC-USDT
baseCurrency| true| String| 基础币种,GXC
quoteCurrency| true| String| 报价币种,USDT
amountPrecision| true| Integer| 数量精度（小数点后位数）
pricePrecision| true | Integer| 报价精度（小数点后位数）

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/common/symbols'
```
###### 返回示例

```
{
  "message": null,
  "data": [
    {
      "symbol": "BTC-USDT",
      "baseCurrency": "BTC",
      "quoteCurrency": "USDT",
      "amountPrecision": 4,
      "pricePrecision": 2
    },
    {
      "symbol": "ETH-USDT",
      "baseCurrency": "ETH",
      "quoteCurrency": "USDT",
      "amountPrecision": 4,
      "pricePrecision": 2
    },
    {
      "symbol": "EOS-USDT",
      "baseCurrency": "EOS",
      "quoteCurrency": "USDT",
      "amountPrecision": 4,
      "pricePrecision": 4
    },
    {
      "symbol": "GXC-USDT",
      "baseCurrency": "GXC",
      "quoteCurrency": "USDT",
      "amountPrecision": 4,
      "pricePrecision": 4
    },
    {
      "symbol": "ETH-BTC",
      "baseCurrency": "ETH",
      "quoteCurrency": "BTC",
      "amountPrecision": 4,
      "pricePrecision": 6
    },
    {
      "symbol": "EOS-BTC",
      "baseCurrency": "EOS",
      "quoteCurrency": "BTC",
      "amountPrecision": 2,
      "pricePrecision": 8
    },
    {
      "symbol": "GXC-BTC",
      "baseCurrency": "GXC",
      "quoteCurrency": "BTC",
      "amountPrecision": 2,
      "pricePrecision": 8
    }
  ]
}
```

##### 账户相关
##### 1.资产余额
###### HTTP 请求
> GET /api/exchange/v1/account/balance

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
symbol | false| String| 币种 GXC；不传，则返回所有币种余额，
sign | true | String | 签名
###### 返回参数
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | true | Array | 资产列表

资产列表
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
coinId | true| String | 币种id
symbol | true | String | 币种标识
balance | true | number | 总余额
availableAmount |true| number | 可用余额
frozenAmount |true| number | 冻结余额

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/account/balance'
```
###### 返回示例

```
{
  "message": null,
  "data": [
    {
      "coinId": 1,
      "symbol": "GXC",
      "balance": 1967368.27148828,
      "frozenAmount": 1027858.43993281,
      "availableAmount": 6236.41122077
    }
  ]
}
```

##### 现货交易
##### 1.委托下单
###### HTTP 请求
> POST /api/exchange/v1/orders/order

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
symbol | true | String | 交易对，GXC-USDT
orderType | true | String | 委托单类型，limit 限价，market市价
side | true | String | 交易方向，buy，sell
unitPrice | false | number | 单价，订单类型为limit时非空
amount | true | number | 交易数量
sign | true | String | 签名

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | true | Long | 订单id

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/orders/order'
```
```
{
    "accessKey":"qwewqeq",
    "timestamp":1557839220283,
    "symbol":"GXC-USDT",
    "orderType":"limit",
    "side":"buy",
    "unitPrice":1.2,
    "amount":2,
    "sign":"fewfafdsfdsqeq"
}
```

###### 返回示例

```
{
    "message":null,
    "data":123123
}
```
#### 2.撤销委托单
###### HTTP 请求
> POST /api/exchange/v1/orders/cancel

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
orderId | true | Long | 委托单id
sign | true | String | 签名

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | String | 订单id

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/orders/cancel'
```
```
{
    "accessKey":"qwewqeq",
    "timestamp":1557839220283,
    "orderId":2123,
    "sign":ewqasdasdfsadf
}
```

###### 返回示例

```
{
    data:"2123",
    message:""
}
```

#### 3.批量撤销委托单
###### HTTP 请求
> POST /api/exchange/v1/orders/cancels

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
orderIds | true | String | 委托单id，中间用“,”分割，最大长度50,示例： 123,456,789
sign | true | String | 签名

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | String | 成功的订单id，中间用“,”分割， 示例： 123,456,789

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/orders/cancels'
```
```
{
	"accessKey": "lbBSlfTCSq8iMynI",
	"sign": "d7e23df2d344f5f36cd2bcf342a7b9d7",
	"orderIds": "9396947,9396948",
	"timestamp": "1599812206741"
}
```

###### 返回示例

```
{
	"message": null,
	"data": "9396947,9396948"
}
```

#### 4.查询当前未成交订单

###### HTTP 请求

> GET /api/exchange/v1/orders/opening

> 该接口有掉用频率限制 3秒钟一次 【新增】

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
sign | true | String | 签名
symbol | false | String | 交易对，GXC-USDT
side | false | String | 方向，buy或者sell
size | false | Integer | 数量，默认10，最大1000

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Array | 订单数据，时间倒序

订单列表
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
id | true | Long | 订单id
userId | true | Integer| 用户id
orderType | true | String| 订单类型，market市价交易，limit限价交易
side | true | String| 交易方向，buy，sell
symbol | true | String| 交易对
amount | true | number| 交易数量
unitPrice | false | number| 限价交易单价
completeAmount | true | number|订单中已成交的总数量
completePrice | true | number| 订单中已成交的总价格
completeFee | true | number| 已成交交易手续费总额
status | true | Integer | 订单状态：1 已提交，2 部分成交，3，部分成交撤销，4 完全成交，5 全部撤销
createTime|true|Long|提交订单时间
cancelTime|false|Long|撤销时间
finishedTime|false|Long|最后的成交时间

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/orders/opening?symbol=GXC-USDT&side=buy&size=2&accessKey=itqv7y3belss2efd&sign=da79801081827b885f0c926e241c2166&timestamp=1560258622838'
```

###### 返回示例
```
{
  "message": null,
  "data": [
    {
      "id": 80971619,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 13,
      "unitPrice": 1.6,
      "status": 1,
      "completeAmount": 0,
      "completePrice": 0,
      "completeFee": 0,
      "createTime": 1560258418000,
      "cancelTime": null,
      "finishedTime": null,
      "remainAmount": 13
    },
    {
      "id": 80971618,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 10,
      "unitPrice": 1.5,
      "status": 1,
      "completeAmount": 0,
      "completePrice": 0,
      "completeFee": 0,
      "createTime": 1560258410000,
      "cancelTime": null,
      "finishedTime": null,
      "remainAmount": 10
    }
  ]
}
```


#### 5.查询历史订单 
> GET /api/exchange/v1/orders/history

> 该接口有掉用频率限制 3秒钟一次 【新增】

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
sign | true | String | 签名
symbol | false | String | 交易对，GXC-USDT
side | false | String | 方向，buy或者sell
states| true |String|订单状态组合，','隔开，1 已提交，2 部分成交，3，部分成交撤销，4 完全成交，5 全部撤销
createTimeBegin|false|Long|开始日期(含)，范围[-180,createTimeEnd]，默认是today-180
createTimeEnd|false|Long|截止日期(含),范围[createTimeBegin,today],默认值today
size | false | Integer | 数量，默认100，最大1000

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Array | 订单数据，时间倒序

订单列表
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
id | true | Long | 订单id
userId | true | Integer| 用户id
orderType | true | String| 订单类型，market市价交易，limit限价交易
side | true | String| 交易方向，buy，sell
symbol | true | String| 交易对
amount | true | number| 交易数量
unitPrice | false | number| 限价交易单价
completeAmount | true | number|订单中已成交的总数量
completePrice | true | number| 订单中已成交的总价格
completeFee | true | number| 已成交交易手续费总额
status | true | Integer | 订单状态：1 已提交，2 部分成交，3，部分成交撤销，4 完全成交，5 全部撤销
createTime|true|Long|提交订单时间
cancelTime|false|Long|撤销时间
finishedTime|false|Long|最后的成交时间

###### 请求示例
```
    curl 'https://api.longbit.io/api/exchange/v1/orders/history?size=10&accessKey=itqv7y3belss2efd&createTimeEnd=1559577600000&sign=b58a5c73031fc752a0c4ac79105718c6&createTimeBegin=1559577600000&timestamp=1560261109490&states=3,4,5'
```

###### 返回示例
```
{
  "message": null,
  "data": [
    {
      "id": 60449306,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 10.000000000000000000,
      "unitPrice": 1.092200000000000000,
      "status": 4,
      "completeAmount": 10.000000000000000000,
      "completePrice": 10.913000000000000000,
      "completeFee": 0.020000000000000000,
      "createTime": 1559649361000,
      "cancelTime": null,
      "finishedTime": 1559649362000,
      "remainAmount": 0
    },
    {
      "id": 60446696,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 10.000000000000000000,
      "unitPrice": 1.092200000000000000,
      "status": 4,
      "completeAmount": 10.000000000000000000,
      "completePrice": 10.912000000000000000,
      "completeFee": 0.020000000000000000,
      "createTime": 1559649292000,
      "cancelTime": null,
      "finishedTime": 1559649292000,
      "remainAmount": 0
    },
    {
      "id": 60442419,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 1.000000000000000000,
      "unitPrice": 1.092200000000000000,
      "status": 4,
      "completeAmount": 1.000000000000000000,
      "completePrice": 1.092100000000000000,
      "completeFee": 0.002000000000000000,
      "createTime": 1559649187000,
      "cancelTime": null,
      "finishedTime": 1559649187000,
      "remainAmount": 0
    },
    {
      "id": 60441934,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 1.000000000000000000,
      "unitPrice": 1.092200000000000000,
      "status": 4,
      "completeAmount": 1.000000000000000000,
      "completePrice": 1.092100000000000000,
      "completeFee": 0.002000000000000000,
      "createTime": 1559649176000,
      "cancelTime": null,
      "finishedTime": 1559649176000,
      "remainAmount": 0
    },
    {
      "id": 60441275,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 1.000000000000000000,
      "unitPrice": 1.092200000000000000,
      "status": 4,
      "completeAmount": 1.000000000000000000,
      "completePrice": 1.092100000000000000,
      "completeFee": 0.002000000000000000,
      "createTime": 1559649159000,
      "cancelTime": null,
      "finishedTime": 1559649160000,
      "remainAmount": 0
    }
  ]
}
```
###### 6.查询订单详情 【新增】
> GET /api/exchange/v1/orders/getOrder

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
accessKey | true| String | 开放api请求key
timestamp | true | Long | 时间戳
sign | true | String | 签名
orderId | true | Long | 订单id

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Object | 订单数据

订单详情
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
id | true | Long | 订单id
userId | true | Integer| 用户id
orderType | true | String| 订单类型，market市价交易，limit限价交易
side | true | String| 交易方向，buy，sell
symbol | true | String| 交易对
amount | true | number| 交易数量
unitPrice | false | number| 限价交易单价
completeAmount | true | number|订单中已成交的总数量
completePrice | true | number| 订单中已成交的总价格
completeFee | true | number| 已成交交易手续费总额
status | true | Integer | 订单状态：1 已提交，2 部分成交，3，部分成交撤销，4 完全成交，5 全部撤销
createTime|true|Long|提交订单时间
cancelTime|false|Long|撤销时间
finishedTime|false|Long|最后的成交时间

###### 请求示例

```
    curl 'https://api.longbit.io/api/exchange/v1/orders/getOrder?accessKey=itqv7y3belss2efd&sign=b58a5c73031fc752a0c4ac79105718c6&timestamp=1560261109490&orderId=1234'
```

###### 返回示例
```
{
  "message": null,
  "data": {
      "id": 60449306,
      "userId": 1,
      "orderType": "limit",
      "side": "buy",
      "symbol": "GXC-USDT",
      "amount": 10.000000000000000000,
      "unitPrice": 1.092200000000000000,
      "status": 4,
      "completeAmount": 10.000000000000000000,
      "completePrice": 10.913000000000000000,
      "completeFee": 0.020000000000000000,
      "createTime": 1559649361000,
      "cancelTime": null,
      "finishedTime": 1559649362000,
      "remainAmount": 0
    }
}
````

#### 市场行情
##### 1.交易深度
###### HTTP 请求
> GET /api/exchange/market/depth

###### 请求参数

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true | String | 交易对
level | true | Integer | 价格聚合度，0 1 2 3 4
limit | true | Integer | 返回深度数量 [1,100]

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Object | 交易深度数据

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true| String | 交易对
level | true | Integer | 价格聚合度
asks | true | Array | 卖单
bids | true | Array | 买单

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
price | true| String | 价格
amount | true | String | 数量
volume | true | String | 报价

###### 请求示例

```
    curl 'https://api.longbit.io/api/exchange/market/depth?level=0&symbol=GXC-USDT&limit=1'
```

###### 返回示例

```
{
    message:"",
    data:{
        "level":0,
        "symbol":"GXC-BTC",
        "asks":[{
            "price":"0.00016500",
            "amount":"434.89",
            "volume":"0.07175685"
        }],
        "bids":[{
            "price":"0.00013638",
            "amount":"137.40",
            "volume":"0.01873862"
        }]
    }
    
}
```
##### 8.最新成交记录
###### HTTP 请求
> GET /api/exchange/market/trade

###### 请求参数
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true | String | 交易对
###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Object | 最新成交记录

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true| String | 交易对
unitPrice| true| String| 报价币种为单位的成交单价
amount| true| String| 基础币种为单位的成交总量
volume| true| String| 报价币种为单位的成交总量
side | true| String| 交易方向，buy或者sell
timestamp| true| long| 成交时间

###### 请求示例

```
    curl 'https://api.longbit.io/api/exchange/market/trade?symbol=GXC-USDT'
```

###### 返回示例

```
{
  "message": null,
  "data": [
    {
      "unitPrice": "2.1649",
      "amount": "70.9238",
      "timestamp": 1560246499000,
      "symbol":"GXC-USDT",
      "volume": "153.5430",
      "side": "sell"
    }
  ]
}
```
##### 9.近期成交记录
###### HTTP 请求
> GET /api/exchange/market/history/trade

###### 请求参数
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true | String | 交易对
size | false | Integer | 返回的数量，最大1000，默认50
###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Array | 成交记录

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true| String | 交易对
unitPrice| true| String| 报价币种为单位的成交单价
amount| true| String| 基础币种为单位的成交总量
volume| true| String| 报价币种为单位的成交总量
side | true| String| 交易方向，buy或者sell
timestamp| true| Long| 成交时间

###### 请求示例

```
    curl 'https://api.longbit.io/api/exchange/market/history/trade?symbol=GXC-USDT&size=10'
```

###### 返回示例

```
{
  "message": null,
  "data": [
    {
      "unitPrice": "2.1649",
      "amount": "70.9238",
      "timestamp": 1560246499000,
      "symbol": "GXC-USDT",
      "volume": "153.5430",
      "side": "sell"
    },
    {
      "unitPrice": "2.1649",
      "amount": "8.2195",
      "timestamp": 1560246498000,
      "symbol": "GXC-USDT",
      "volume": "17.7944",
      "side": "sell"
    },
    {
      "unitPrice": "2.1649",
      "amount": "9.2489",
      "timestamp": 1560246496000,
      "symbol": "GXC-USDT",
      "volume": "20.0230",
      "side": "buy"
    },
    {
      "unitPrice": "2.1603",
      "amount": "0.3906",
      "timestamp": 1560246070000,
      "symbol": "GXC-USDT",
      "volume": "0.8439",
      "side": "buy"
    },
    {
      "unitPrice": "2.1699",
      "amount": "0.3814",
      "timestamp": 1560245466000,
      "symbol": "GXC-USDT",
      "volume": "0.8276",
      "side": "buy"
    },
    {
      "unitPrice": "2.1500",
      "amount": "0.4081",
      "timestamp": 1560245058000,
      "symbol": "GXC-USDT",
      "volume": "0.8775",
      "side": "buy"
    },
    {
      "unitPrice": "2.1500",
      "amount": "7.8280",
      "timestamp": 1560245055000,
      "symbol": "GXC-USDT",
      "volume": "16.8302",
      "side": "sell"
    },
    {
      "unitPrice": "2.1670",
      "amount": "7.8114",
      "timestamp": 1560244381000,
      "symbol": "GXC-USDT",
      "volume": "16.9274",
      "side": "buy"
    },
    {
      "unitPrice": "2.1578",
      "amount": "0.1242",
      "timestamp": 1560244381000,
      "symbol": "GXC-USDT",
      "volume": "0.2680",
      "side": "buy"
    },
    {
      "unitPrice": "2.1500",
      "amount": "37.3561",
      "timestamp": 1560243829000,
      "symbol": "GXC-USDT",
      "volume": "80.3157",
      "side": "sell"
    }
  ]
}
```
##### 10.交易行情(24小时)
###### HTTP 请求
> GET /exchange/data/tick

###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Array | 交易行情

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true| String | 交易对
amount| true| String| 基础币种为单位的成交总量
volume| true| String| 报价币种为单位的成交总量
count | true| Long| 交易笔数
open| true| String| 开盘价
close| true| String| 收盘价
low| true| String| 最低价
high| true| String| 最高价
diff| true| String| 收盘开盘价格差值
diffRate| true| String| 涨幅比例

###### 请求示例

```
curl 'https://api.longbit.io/exchange/data/tick'
```

###### 返回示例

```
{
  "message": null,
  "data": [
    {
      "symbol": "LBP-USDT",
      "amount": "14016936.7094",
      "volume": "2159955.67269",
      "count": 5910,
      "open": "0.15759",
      "close": "0.15003",
      "low": "0.14692",
      "high": "0.15850",
      "diff": "-0.00756",
      "diffRate": "-4.80"
    },
    {
      "symbol": "BTC-USDT",
      "amount": "894.8757",
      "volume": "8123766.44",
      "count": 26862,
      "open": "9277.07",
      "close": "9157.10",
      "low": "8831.99",
      "high": "9303.23",
      "diff": "-119.97",
      "diffRate": "-1.30"
    },
    {
      "symbol": "ETH-USDT",
      "amount": "17981.1158",
      "volume": "4841472.73",
      "count": 22087,
      "open": "276.11",
      "close": "269.53",
      "low": "263.52",
      "high": "276.26",
      "diff": "-6.58",
      "diffRate": "-2.39"
    }
  ]
}
```
##### 交易对限额
###### HTTP 请求
> GET /api/exchange/v1/common/symbol/limit

###### 请求参数
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true | String | 交易对
###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Object | 成交记录

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true| String | 交易对
amountLimit| true| number| 最低交易量
totalPriceLimit| true| number| 最低交易额
###### 请求示例

```
    curl 'https://api.longbit.io/api/exchange/v1/common/symbol/limit?symbol=LBP-USDT'
```

###### 返回示例

```
{
	"message": null,
	"data": {
		"symbol": "LBP-USDT",
		"amountLimit": 1.00000000,
		"totalPriceLimit": 0.10000000
	}
}
```
##### 获取K线信息
###### HTTP 请求
> GET /exchange/data/kline

###### 请求参数
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
symbol | true | String | 交易对
interval | true | String | 时间单位1m:1分钟,5m:5分钟,15m:15分钟,30m:30分钟,60m:1小时,4h:4小时,1d:1天,1w:1周,1mon:1个月 
startTime | true | String | 起始时间
endTime | true | String | 结束时间


###### 返回数据
参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
message | false| String | 返回代码
data | false | Object | 成交记录

参数名称 | 是否必须 | 类型 | 描述
---|---|---|---|
0 | true| String | 开始时间
1| true| number| 结束时间
2| true| number| 时间单位
3| true| number| 开盘价
4| true| number| 收盘价
5| true| number| 最高价
6| true| number| 最低价
7| true| number| 基础币种计量的交易量
8| true| number| 报价币种计量的交易量
###### 请求示例

```
主地址：
    curl 'https://api.longbit.io/exchange/data/kline?symbol=LBP-USDT&interval=1d&startTime=1543045629000&endTime=1574149689000'
```

###### 返回示例

```
{
	"message": null,
	"data": [
		[1573142400000, 1573228800000, "1D", 0.1186700000, 0.1164000000, 0.1187300000, 0.1148900000, 44578053.20208497608141, 5254481.81456309267507],
		[1573228800000, 1573315200000, "1D", 0.1163700000, 0.1080300000, 0.1173900000, 0.1042000000, 40913447.05675836550837, 4566734.10710963000000],
		[1573315200000, 1573401600000, "1D", 0.1080400000, 0.1076200000, 0.1088100000, 0.1060900000, 43956263.30326637223060, 4733479.10718785800000],
		[1573401600000, 1573488000000, "1D", 0.1076600000, 0.1071800000, 0.1082300000, 0.1060300000, 43013543.38850000000000, 4629025.25233846900000],
		[1573488000000, 1573574400000, "1D", 0.1072000000, 0.1070500000, 0.1079700000, 0.1066700000, 42156655.64660186480186, 4515864.83038056400000],
		[1573574400000, 1573660800000, "1D", 0.1070100000, 0.1069600000, 0.1073200000, 0.1044600000, 41889223.81970000000000, 4475148.62135865200000],
		[1573660800000, 1573747200000, "1D", 0.1069700000, 0.1063300000, 0.1070900000, 0.1050000000, 42068679.35070201699505, 4486327.53452276300000],
		[1573747200000, 1573833600000, "1D", 0.1063800000, 0.1058300000, 0.1065100000, 0.1051700000, 42856405.66230128844164, 4539832.94511928223192],
		[1573833600000, 1573920000000, "1D", 0.1058300000, 0.0999700000, 0.1058800000, 0.0996200000, 44173917.54850000000000, 4549541.14626054400000],
		[1573920000000, 1574006400000, "1D", 0.0999700000, 0.0970700000, 0.1008100000, 0.0966000000, 42649822.15694943663376, 4211178.38425866900000],
		[1574006400000, 1574092800000, "1D", 0.0971000000, 0.0936300000, 0.0971000000, 0.0920700000, 43608377.81840000000000, 4119747.18012610400000],
		[1574092800000, 1574179200000, "1D", 0.0936200000, 0.085170000000000000, 0.0938500000, 0.0838700000, 28129391.382122852963600000, 2569494.215637147000000000]
	]
}
```

#### 三、异常代码

http状态码 | 异常代码 | 描述
---|---|---
400 |error.exchange.symbol.unsupport | 不支持的交易对
400 |fault.balance.not_enough\|%s | [币种]余额不足
400 |fault.exchange.precision.unsupport\|%s | [币种]精度不支持
400 |error.timestamp.invalid | 时间戳无效
400 |error.access_key.invalid | accessKey无效
400 |error.sign.invalid | 签名无效


```
{
  "message": "error.sign.invalid",
  "data": null
}
```
