* [接入说明](#1)
* [现货](#2)
  * [基础数据](#2.1)
    * [币对列表](#2.1.1)
    * [K线数据](#2.1.2)
    * [市场深度](#2.1.3)
    * [最新成交](#2.1.4)
  * [账户相关](#2.2)
    * [账户资产](#2.2.1)
    * [当前委托](#2.2.2)
    * [历史委托](#2.2.3)
    * [成交详情](#2.2.4)
  * [交易相关](#2.3)
    * [批量买入](#2.3.1)
    * [批量卖出](#2.3.2)
    * [订单取消](#2.3.3)
    * [批量取消](#2.3.4)
    
  * [推送](#2.4)
    * [公共推送](#2.4.1)
    * [交易相关](#2.4.2)


<h1 id="1">1 接入说明</h1>
<h2 id="1.1">1.1 接入URL</h2>

#### RESTAPI : https://truedex.io
#### Websocket : wss://push.truedex.io/socket.io

<h2 id="1.2">1.2 风控规则</h2>

#### 1)、基础数据无需签名

<h2 id="1.3">1.3 签名认证</h2>

#### 除公共接口（基础数据，行情数据）外的账户、交易相关的接口均必须使用您的 apisecret 做签名认证

<h3 id="1.3.1">1.3.1 请求头设置</h3>

#### 1)、请求头信息中 contentType 需要统一设置为: 'application/json'
#### 2)、请求头信息中 添加 apikey

<h3 id="1.3.2">1.3.2 请求参数</h3>

#### 1)、请求参数统一格式如下
```
{
  "sign":""         //签名信息
  "body":{}         //接口请求参数，参见下方接口文档
  "ts":  1683561600 //秒级时间戳
}
```

#### 2)、签名规则
#### 2.1)、每个API 中的请求参数将签名的内容为 : body+ts (注意: body中待签名字符串要求按照参数名字母排序方式转成JSON格式字符串, 最后再拼接上时间戳字段. body 为空时直接签名时间戳)
#### 2.2)、签名方式采用HmacSHA256方式，签名密钥为apikey 对应的secret
#### 2.3)、签名验证demo
```
apikey : ed54dfebb393ac90b9c891c2debb6b67
apisecret : 4598092df55fe84a4166d6828043621f
签名原串 : {"delegated":false,"max_count":10,"param":[{"base_quant":"0.20000000 ETH","ext_id":"2","price":"40.000000 USDT"}],"tpcode":"eth.usdt"}1683561600

请求参数：
{
  "sign":"9cfbb14834bf80e3cf1303505cccd6cbe78d47c6c3d187ce7f5a92ffdb00fc63"         //签名信息
  "body":{
    "delegated": false,                  
    "tpcode": "eth.usdt",                
    "max_count": 10,                     
    "param": [
        {
          "ext_id": 2,                     
          "base_quant": "0.20000000 ETH",  
          "price": "40.000000 USDT"        
        }
    ]  
  },
  "ts":  1683561600 
}
```

<h3 id="1.3.3">1.3.3 响应参数</h3>

#### 响应参数格式固定如下，具体见各个接口返回内容
```
{
  "code":200,                           // 200: 提交成功，非200表示提交异常，异常信息参考msg 内容
  "msg":"success",
  "data":{}                             //详情参考对应接口返回报文
}
```

<h1 id="2">2 现货</h1>
<h2 id="2.1">2.1 基础数据</h2>
<h3 id="2.1.1">2.1.1 币对列表</h3>

#### 请求地址: POST /api/dex/tradePair
#### 请求参数：
```
{
    "quoteSymbol": "USDT,6"
}
```
#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "list":[
                {
                    "t":"eth.usdt",  // 币对
                    "qs":"USDT,8",   // 行情币
                    "bs":"ETH,8",    // 基础币
                    "ldp":"0.4",     // 最新成交价 
                    "ldqv":"null",   // 最新成交量     
                    "ldps":"USDT,6", // 最新成交价符号      
                    "h":"0.02",      // 最高价
                    "l":"0.03",      // 最低价
                    "c":"0.01",      // 收盘价（最新成交价）
                    "o":"0.03",      // 开盘价
                    "dc":"1",        // 成交数量
                    "cr":"+0.1",     // 涨跌幅
                    "fbq":"0.03",    // 成交额  
                    "fqq":"0.2",     // 成交量
                    "tbq":"0.01",    // 总挂单量
                    "tqq":"0.2"      // 总挂单额
                ]
            },
        "qs":"USDT,6",
        "rt":1682408615
    }
}
```
<h3 id="2.1.2">2.1.2 K线数据</h3>

#### 请求地址: POST api/dex/kline
#### 请求参数
```
{
    "periodCode": "1min",
    "tradeCode": "eth.usdt",
    "start": 1682325720,
    "end": 2682325720
}
```
#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "list":[
                [
                "3913258",      //记录id
                "1685881920",   //生效时间
                "btc.usdt",     //币对
                "47",           //成交笔数
                "603647.86",    //成交额
                "47",           //成交笔数
                "22.22",        //成交量
                "1min",         //period
                "27166.84",     //收盘价
                "27167.09",     //开盘价
                "27167.08",     //最高价
                "27166.83",     //最低价
                "1685882003",   //创建时间
                ]
            ],        
        "start": 1682325720,
        "end": 1682325720,
        "returnTime": 1682389759,
        "periodCode": "1min",
    }
}
```

<h3 id="2.1.3">2.1.3 市场深度</h3>

#### 请求地址: POST /api/dex/depth 
#### 请求参数
```
{
    "tradeCode": "eth.usdt"
    "size":100   //最大100
}
```

#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "tradeCode": "eth.usdt",
        "returnTime": 1682404625,
        "lastUpdateId": null,
        "sellList": [
            [
                "1500",   // 价格
                "0.5"     // 数量
            ]
        ],
        "buyList": [
            [
                "1000",   // 价格
                "0.2"     // 数量
            ]
        ]
    }
}
```

<h3 id="2.1.4">2.1.4 最新成交</h3>

#### 请求地址 : POST /api/dex/dealHistory
#### 请求参数
```
{
    "size": 50,  // 最大100
    "tradeCode": "eth.usdt"
}
```

#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "returnTime": 1682392521,
        "tradeCode": "eth.usdt",
        "list": [
            {
                "id":"1",          //成交id
                "dpv":"100",       //成交价
                "dps":"USDT,6",    //成交价符号
                "dt":"1681871184", //成交时间
                "dv":"0.01",       //成交量     
                "ds":"ETH,8",      //基础币符号
                "os":"sell"        //maker方向
            }
        ]
    }
}
```
<h2 id="2.2">2.2 账户相关</h2>
<h3 id="2.2.1">2.2.1 账户资产</h2>

#### 请求地址 : POST /mm/dex/user/balance
#### 请求参数
```
无
```

#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "account": "aabbccddeeff",
        "wallet": [
            {                          
              "code":"amax.mtoken",         //合约名
              "balance":1000.5,             //余额
              "coin":"MUSDT"                //币符号(在Truedex合约中，币符号为USDT)
            }
        ],
        "contract": [
            {                          
              "coin":"USDT",                //币符号
              "availableQuant":1000.5,      //可用余额
              "frozenQuant":"MUSDT"         //冻结余额
            }
        ]
    }
}
```


<h3 id="2.2.2">2.2.2 当前委托</h3>

#### 请求地址 : POST /mm/dex/user/openOrders
#### 请求参数
```
{
    "tradeCode": "eth.usdt",
    "size": 100,       //最大100
    "offset": 1300     //已经获取获取到的最大订单id
}
```

#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "list": [
            {                          
            "id":"130008003",           //记录ID
            "oi":"103250553",           //订单ID
            "ei":"20230604010898690",   //extid
            "t":"eth.usdt",             //币对
            "th":"681CBA1128F713FA6AFDB89626656DE869E34DFB85CECE7B828DC5BD5E679746",   //交易hash
            "bn":3492093",              //区块编号
            "on":"dex.user1",           //用户名
            "pv":"1906.51",             //挂单价格
            "ps":"USDT,6",              //基础币
            "lbq":"0.29",               //挂单数量
            "lbs":"ETH,8",              //挂单币
            "dqq":"0",                  //成交量
            "dqs":"USDT,6",             //成交货币
            "mqq":"0",                  //已成交额
            "mqs":"ETH,8",              //已成交币
            "mbq":"0",                  //已成交量
            "apv":"0",                  //平均成交价 
            "mbs":"ETH,8",              //  
            "s":"sell",                 //方向   
            "ot":"limit",               //订单类型
            "ss":null,                  //订单状态(pending(已挂单)、partial(部分成交)、cancel(已取消: 清注意如果mqq(已成交大于0, 则订单为部分成交取消))、success(完全成交))
            "ca":"1685884122"           //创建时间 
            }
        ]
    }
}
```

<h3 id="2.2.3">2.2.3 历史委托</h3>

#### 请求地址 : POST /mm/dex/user/dealHistory
#### 请求参数
```
{
    "tradeCode": "eth.usdt",
    "size": 100,
    "offset": 1300     //已经获取获取到的最大订单id
}
```

#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "list": [
            {                          
            "id":"130008003",           //记录ID  
            "oi":"103250553",           //订单ID  
            "ei":"20230604010898690",   //extId
            "t":"eth.usdt",             //币对   
            "th":"681CBA1128F713FA6AFDB89626656DE869E34DFB85CECE7B828DC5BD5E679746",   //交易hash
            "bn":3492093",              //区块编号
            "on":"dex.user1",           //用户名
            "pv":"1906.51",             //挂单价格
            "ps":"USDT,6",              //基础币
            "lbq":"0.29",               //挂单数量
            "lbs":"ETH,8",              //挂单币
            "dqq":"0",                  //成交量
            "dqs":"USDT,6",             //成交货币
            "mqq":"0",                  //已成交
            "mqs":"ETH,8",              //已成交币
            "mbq":"0",                  //已成交量
            "apv":"0",                  //平均成交价
            "mbs":"ETH,8",              //币对信息
            "s":"sell",                 //方向(buy/sell)
            "ot":"limit",               //订单类型(limit/market)
            "ss": cancel,               //订单状态(pending(已挂单)、partial(部分成交)、cancel(已取消: 清注意如果mqq(已成交大于0, 则订单为部分成交取消))、success(完全成交))
            "ca":"1685884122"           //创建时间
            }
        ]
    }
}
```

<h3 id="2.2.4">2.2.4 成交明细</h3>

#### 请求地址 : POST /mm/dex/user/orderDetail
#### 请求参数
```
{
    "orderSide":"buy",   // buy、sell
    "orderId": "105971590"
}
```
#### 响应参数
```
{
    "code": 200,
    "msg": "success",
    "data": {
        "list": [
            {
                "id": 1917678,
                "boi": 105971590,      //buyOrderId
                "soi": 105971161,      //sellOrderId
                "t": "eth.usdt",       //币对
                "th": "1A84959E7529E9F88FBAE7AD879A61AD22FC5F7B920FBEF91A9CF02ADF2EC160",  //txHash ：交易hash
                "bn": 4651547,         //区块高度
                "be": "v1x.sell1",     //buyer
                "se": "v1x.sell1",     //seller
                "pv": "0.589841",      //成交价
                "ps": "USDT,6",        //价格币
                "mqq": "105.394521",   //成交额
                "mqs": "USDT,6",       //成交额币符号
                "mbq": "178.682935",   //成交量
                "mbs": "FFT,8",        //成交量币符号
                "ts": "buy",           //taker 方向
                "ca": 1687084838,      //创建时间
                "sfq": "1.053945",     //卖方手续费
                "sfs": "USDT,6",       //卖方手续费币符号
                "bfq": "1.786829",     //买方手续费
                "bfs": "FFT,8"         //买方手续费币符号
            }
        ]
    }
}
```
<h2 id="2.3">2.3 交易相关</h2>

<h3 id="2.3.1">2.3.1 批量买入</h3>

#### 请求地址 : POST /mm/dex/user/limitbuyn
#### 请求参数
```
{
    "account": "truedex.spot",           // 合约
    "submitter": "v1x.buy1",             // 交易提交人(主账户或者代理账户)
    "owner": "v1x.buy1",                 // 主账户
    "delegated": false,                  // 是否代理交易
    "tpcode": "eth.usdt",                // 币对
    "max_count": 10,                     // 最大数量
    "param": [
        {
          "ext_id": 2,                     // 自定义id
          "base_quant": "0.20000000 ETH",  // 购买数量(需要按照币对精度来设置, 币的精度可以参考币对列表接口)
          "price": "40.000000 USDT"        // 购买价格(需要按照币对精度来设置, USDT 需要设置成6位小数)
        }
    ]  
}
```
#### 响应参数
```
{
    "code":200,        // 200: 提交成功，非200表示提交异常，异常信息参考msg 内容
    "msg":"success",
    "data":"20830b1f62104c2255cb829a5c64a05f864a10847684794ba69240ad56df889a"
}
```

<h3 id="2.3.2">2.3.2 批量卖出</h3>

#### 请求地址 : POST /mm/dex/user/limitselln
#### 请求参数
```
{
    "delegated": false,                  // 是否代理交易
    "tpcode": "eth.usdt",                // 币对
    "max_count": 10,                     // 最大数量
    "param": [
        {
          "ext_id": 2,                     // 自定义id
          "base_quant": "0.20000000 ETH",  // 购买数量(需要按照币对精度来设置, 币的精度可以参考币对列表接口)
          "price": "40.000000 USDT"        // 购买价格(需要按照币对精度来设置, USDT 需要设置成6位小数)
        }
    ]  
}
```
#### 响应参数
```
{
    "code":200,                           // 200: 提交成功，非200表示提交异常，异常信息参考msg 内容
    "msg":"success",
    "data":"20830b1f62104c2255cb829a5c64a05f864a10847684794ba69240ad56df889a"
}
```

<h3 id="2.3.3">2.3.3 按价格或者区间取消</h3>

#### 请求地址 : POST /mm/dex/user/cancelalln
#### 请求参数
```
{
    "delegated": false,                  // 是否代理交易
    "tpcode": "eth.usdt",                // 币对
    "order_side":"buy",                  // 买卖方向(buy、sell)
    "lower_price":"0.000000 USDT",       // 最低价格(需要按照币对精度来设置, USDT 需要设置成6位小数)
    "upper_price":"100.000000 USDT",     // 最高价格(需要按照币对精度来设置, USDT 需要设置成6位小数)
    "count": 30              
}
```
#### 响应参数
```
{
    "code":200,                           // 200: 提交成功，非200表示提交异常，异常信息参考msg 内容
    "msg":"success",
    "data":"20830b1f62104c2255cb829a5c64a05f864a10847684794ba69240ad56df889a"
}
```

<h3 id="2.3.4">2.3.4 批量取消</h3>

#### 请求地址 : POST /mm/dex/user/perp_batchcancel
#### 请求参数
```
{
    "delegated": false,                  // 是否代理交易
    "tpcode": "eth.usdt",                // 币对
    "type":"limit",                      // 市场类型(limit、market)
    "side":"buy",                        // 买卖方向(buy、sell)
    "ids": [1234567,456780],             // 要取消的订单的id
    "memo": ""                           // 毫秒级时间戳(避免每次提交交易参数一致)
}
```
#### 响应参数
```
{
    "code":200,                           // 200: 提交成功，非200表示提交异常，异常信息参考msg 内容
    "msg":"success",
    "data":"20830b1f62104c2255cb829a5c64a05f864a10847684794ba69240ad56df889a"
}
```

<h2 id="2.4">2.4 推送</h2>
<h3 id="2.4.1">2.4.1 公共推送</h3>

<h3 id="2.4.1.1">2.4.1.1 连接钱包</h3>

#### 请求
```
{
    "type": "connectWallet",
    "account": "merchantxpro"
}
```

<h3 id="2.4.1.2">2.4.1.2 切换币对</h3>  

### 切换币对 : 服务端默认会订阅币对的 ticker、depth、kline 消息
#### 请求参数
```
{
    "type": "switchTradeCode",
    "tradeCode": "eth.usdt", 
    "periodCode": "1min" //  "1min","5min","15min","1hour","1day","1week","1mon"
}
```
#### 响应结果：
```
每间隔1秒响应ticker和 depth 消息（消息格式参考ticker 和 depth 消息）
```

<h3 id="2.4.1.3">2.4.1.3 订阅币对</h3>

#### 请求参数
```
{
  "type": "subscribe",
  "sympairList": [
    "eth.usdt@ticker",
    "eth.usdt@depth"
  ]
}
```
#### 响应结果: 每间隔1秒响应ticker和 depth 消息（消息格式参考ticker 和 depth 消息）
### 最新成交数据(ticker)
```
{ 
    "type":"ticker",          //类型消息
    "body":{
        "t":"fft.usdt",       //币对
        "ldp24":"0.585951",   //24小时前成交价
        "ldi":1952238,        //最新成交dealId
        "ldp":"0.610405",     //最新成交价
        "ldq":"210.92024",    //最新成交量
        "o":"0.585951",       //开盘价
        "h":"0.633815",       //开盘价
        "l":"0.570395",       //开盘价
        "tc24":"0",           //成交笔数
        "dc":36270,         
        "ts":1687146412,      //时间戳
        "tbq":"299732.7847",  //成交量
        "tqq":"185696.582146",//成交额
        "tss":"buy",          //成交方向
        "cr":"3.4600"         //涨跌幅
    }
}
```

### 深度数据推送(depth)
```
{
    "type":"depth",   //Depth 类型消息
    "body":{
        "tradeCode":"btc.usdt",
        "lastUpdateId":null,        
        "sellList": [
            [
                "1500", // 价格
                "0.5"   // 数量
            ]
        ],
        "buyList": [
            [
                "1000", // 价格
                "0.2"   // 数量
            ]
        ],
         "timestamp":1685628487
    }
}
```

### K线数据推送(kline)
```
{
    "type":"kline",
    "body":[        
        "3913258",      //记录id
        "1685881920",   //生效时间 
        "btc.usdt",     //币对 
        "47",           //成交笔数 
        "603647.86",    //成交额 
        "47",           //成交笔数 
        "22.22",        //成交量 
        "1min",         //period 
        "27166.84",     //收盘价 
        "27167.09",     //开盘价 
        "27167.08",     //最高价 
        "27166.83",     //最低价 
        "1685882003",   //创建时间 
     ]
}
```
<h3 id="2.4.1.4">2.4.1.4 取消订阅</h3>

#### 请求参数：
```
{
  "type": "unsubscribe",
  "sympairList": [
    "eth.usdt@ticker",
    "eth.usdt@depth"
  ]
}
```
#### 响应参数
```
取消订阅后将不会再收到申请订阅的消息
```