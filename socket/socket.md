### Socket接入说明

1. 接入地址
  
   ```47.56.161.239:36666```

  

1. 请求数据格式

   | 参数       | 类型   | 说明                                       | **示例**                                 |
   | ---------- | ------ | ------------------------------------------ | ---------------------------------------- |
   | length     | int    | 26 + body.length                           | 26                                       |
   | sequenceId | long   | 流水ID。预留，传0即可                      | 0                                        |
   | cmd        | short  | 指令。订阅:20001,取消订阅:20002            | 20001                                    |
   | version    | int    | 版本号。传1即可                            | 1                                        |
   | terminal   | String | 来源。安卓:1001,苹果:1002,WEB:1003,PC:1004 | 1003                                     |
   | requestid  | int    | 请求ID。预留，传0即可                      | 0                                        |
   | body       | String | 请求数据。JSON格式                         | {<br/>"channelId":"30001",<br/>...<br/>} |

   连接上socket后直接将以上数据写入输出流中

   

1. 响应数据格式

   | 参数         | 类型   | 说明                                                         | **示例**                                                     |
   | ------------ | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | length       | int    | 26 + body.length                                             | 26                                                           |
   | sequenceId   | long   | 流水ID。预留，传0即可                                        | 0                                                            |
   | cmd          | short  | 响应指令。<br/>推送交易对概览信息:30001,<br/>推送K线消息:30002,<br/>推送实时成交数据:30003,<br/>推送盘口数据:30004,<br/>推送全部盘口数据:30005,<br/>推送新订单数据:30101,<br/>推送订单成交数据:30102,<br/>推送订单完成数据:30103,<br/>推送订单撤销数据:30104,<br/>推送订单变化数据:30105,<br/>推送用户资产变化数据:30201 | 30001                                                        |
   | responseCode | int    | 响应码。200                                                  | 200                                                          |
   | requestid    | int    | 请求ID                                                       | 0                                                            |
   | body         | String | 请求数据。JSON格式                                           | {<br/>"amount":"1",<br>"currency":"USDT",<br/>"time":1565400666028,<br/>"userId":"XXX"<br/>} |
   
1. 签名说明

   * API KEY包含以下两部分

     * API KEY api访问密钥
     * Secret Key 签名认证加密所使用的密钥（仅申请时可见）

   * 签名步骤

     使用HmacSHA512算法对签名内容进行签名

   * 签名内容

     签名内容为apiKey=xxx&timestamp=xxx

   * 签名代码示例(Java)

     ```java
     String timestamp = "1566322200000";
     String secretKey = "XXXXXXXXXXXXXXXXXXXXXXX";
     Mac hmacSha512 = Mac.getInstance("HmacSHA512");
     SecretKeySpec secKey = new SecretKeySpec(secretKey.getBytes(StandardCharsets.UTF_8), "HmacSHA512");
     hmacSha512.init(secKey);
     String paramStr = "apiKey=4565B83XXXXXXXXXXXXF123&timestamp=1566322200000";
     byte[] hash = hmacSha512.doFinal(paramStr.getBytes(StandardCharsets.UTF_8));
     String sign = Hex.encodeHexString(hash);
     ```

     

1. 接口列表


| 说明                                    | body格式要求(必要字段)                                       | 备注                                                         |
| --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [订阅币种概览](#订阅币种概览)           | {<br/>"channelId":"30001",<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>} |                                                              |
| [订阅K线数据](#订阅k线数据)             | {<br/>"channelId":"30002",<br/>"symbol":**"BTC/USDT"**,<br/>"interval":**"1"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>} | interval可选值：<br>1分钟:1<br>15分钟:15<br>30分钟:30<br/>1小时:60<br/>4小时:240<br/>1天:D<br/>1周:W<br/>1月:M<br/> |
| [订阅实时成交](#订阅实时成交)           | {<br/>"channelId":"30003",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>} |                                                              |
| [订阅盘口数据(最多24条)](#订阅盘口数据) | {<br/>"channelId":"30004",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>} |                                                              |
| [订阅盘口数据](#订阅盘口数据)           | {<br/>"channelId":"30005",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>} |                                                              |
| [订阅新订单通知](#订阅新订单通知)       | {<br/>"channelId":"30101",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>"sign":**"XXX"**<br/>} |                                                              |
| [订阅订单成交通知](#订阅订单成交通知)   | {<br/>"channelId":"30102",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>"sign":**"XXX"**<br/>} |                                                              |
| [订阅订单完成通知](#订阅订单完成通知)   | {<br/>"channelId":"30103",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>"sign":**"XXX"**<br/>} |                                                              |
| [订阅订单撤销通知](#订阅订单撤销通知)   | {<br/>"channelId":"30104",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>"sign":**"XXX"**<br/>} |                                                              |
| [订阅订单变化通知](#订阅订单变化通知)   | {<br/>"channelId":"30105",<br/>"symbol":**"BTC/USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>"sign":**"XXX"**<br/>} |                                                              |
| [订阅用户资产变化](#订阅用户资产变化)   | {<br/>"channelId":"30201",<br/>"symbol":**"USDT"**,<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>"sign":**"XXX"**<br/>} |                                                              |
| [心跳](#心跳)                           | {<br/>"channelId":"11004",<br/>"apiKey":"XXX",<br/>"timestamp":"XXX"<br/>} |                                                              |
| [完整代码示例](#完整代码示例)| | 目前仅提供Java版本 |

##### 订阅币种概览

  * 请求body：

    ```json
    {
        "channelId":"30001",//订阅类型，固定值
        "apiKey":"XXX",
        "timestamp":"XXX"//时间戳，毫秒
    }
    ```

  * 响应body：

    ```json
    {
        "symbol": "BTC/USDT",//交易对
        "baseSymbol": "USDT",//基础币种
        "coinSymbol": "BTC",//交易币种
        "open": 0,//开盘价
        "high": 0,//最高价
        "low": 0,//最低价
        "close": 0,//收盘价
        "chg": 0,//涨跌幅比率
        "change": 0,//涨跌幅
        "volume": 0,//成交量
        "turnover": 0,//成交额
        "lastDayClose": 0,//昨日收盘价
        "usdRate": 0,//交易币对usd汇率
        "baseUsdRate": 0//基币对usd的汇率
    }
    ```

##### 订阅K线数据

  * 订阅body：

    ```json
    {
        "channelId":"30002",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "interval":"1",//K线周期，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX"//时间戳，毫秒
    }
    ```

  * 响应body：

    ```json
    {
        "openPrice": 0,//开盘价
        "highestPrice": 0,//最高价
        "lowestPrice": 0,//最低价
        "closePrice": 0,//收盘价
        "time": 1564450357000,//时间
        "period": "1",//周期
        "count": 0,//成交笔数
        "volume": 0,//成交量
        "turnover": 0//成交额
    }
    ```


##### 订阅实时成交

  * 订阅body：

    ```json
    {
        "channelId":"30003",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX"//时间戳，毫秒
    }
    ```

  * 响应body：

    ```json
    {
        "id": "123456",//成交ID
        "symbol": 0,//交易对
        "price": 0,//价格
        "amount": 0,//数量
        "buyTurnover": 0,//买成交额
        "sellTurnover": 0,//卖成交额
        "direction": "BUY",//方向：BUY买SELL卖
        "buyOrderId": "123456",//买方订单号
        "sellOrderId": "456789",//卖方订单号
        "time": 1564450357000//时间戳
    }
    ```


##### 订阅盘口数据

  * 订阅body：

    ```json
    {
        "channelId":"30004",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX"//时间戳，毫秒
    }
    ```

  * 响应body：

    ```json
    {    
        "maxDepth": 100,//最大深度
        "direction": "BUY",//方向：BUY买SELL卖
        "symbol": "BTC/USDT",//交易对
        "items": [
            {
                "price": 0,//价格
                "amount": 0//数量
            },
            ...
        ]
    }
    ```


##### 订阅盘口数据

  * 订阅body：

    ```json
    {
        "channelId":"30005",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX"//时间戳，毫秒
    }
    ```

  * 响应body：

    ```json
    {    
        "maxDepth": 100,//最大深度
        "direction": "BUY",//方向：BUY买SELL卖
        "symbol": "BTC/USDT",//交易对
        "items": [
            {
                "price": 0,//价格
                "amount": 0//数量
            },
            ...
        ]
    }
    ```

##### 订阅新订单通知

  * 订阅body：

    ```json
    {
        "channelId":"30101",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX",//时间戳，毫秒
        "sign":"xxx"//签名值
    }
    ```

  * 响应body：

    ```json
    {
        "orderId": "456789",//订单ID
        "userId": "123456",//用户ID
        "type": "MARKET_PRICE",//订单类型：MARKET_PRICE市价，LIMIT_PRICE限价
        "amount": 0,//买入或卖出量，对于市价买入单表
        "symbol": "BTC/USDT",//交易对
        "tradedAmount": 0,//成交量
        "turnover": 0,//成交额
        "coinSymbol": "BTC",//币单位
        "baseSymbol": "USDT",//结算币单位
        "status": "TRADING",//订单状态：TRADING交易中，COMPLETED已完成，CANCELED已取消，PARTFILLED部分成交，PARTFILL_CANCELD部分成交取消，OVERTIMED已超时
        "direction": "BUY",//方向：BUY买SELL卖
        "price": 0,//挂单价格
        "time": 1564450355000,//挂单时间
        "completedTime": 1564450357000,//交易完成时间戳
        "canceledTime": 1564450357000,//取消时间戳
        "useDiscount": "0",//是否使用折扣 0 不使用 1使用
    }
    ```

##### 订阅订单成交通知

  * 订阅body：

    ```json
    {
        "channelId":"30102",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX",//时间戳，毫秒
        "sign":"xxx"//签名值
    }
    ```

  * 响应body：

    ```json
    {
        "orderId": "456789",//订单ID
        "userId": "123456",//用户ID
        "type": "MARKET_PRICE",//订单类型：MARKET_PRICE市价，LIMIT_PRICE限价
        "amount": 0,//买入或卖出量，对于市价买入单表
        "symbol": "BTC/USDT",//交易对
        "tradedAmount": 0,//成交量
        "turnover": 0,//成交额
        "coinSymbol": "BTC",//币单位
        "baseSymbol": "USDT",//结算币单位
        "status": "TRADING",//订单状态：TRADING交易中，COMPLETED已完成，CANCELED已取消，PARTFILLED部分成交，PARTFILL_CANCELD部分成交取消，OVERTIMED已超时
        "direction": "BUY",//方向：BUY买SELL卖
        "price": 0,//挂单价格
        "time": 1564450355000,//挂单时间
        "completedTime": 1564450357000,//交易完成时间戳
        "canceledTime": 1564450357000,//取消时间戳
        "useDiscount": "0",//是否使用折扣 0 不使用 1使用
    }
    ```


##### 订阅订单完成通知

  * 订阅body：

    ```josn
    {
        "channelId":"30103",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX",//时间戳，毫秒
        "sign":"xxx"//签名值
    }
    ```

  * 响应body：

    ```json
    {
        "orderId": "456789",//订单ID
        "userId": "123456",//用户ID
        "type": "MARKET_PRICE",//订单类型：MARKET_PRICE市价，LIMIT_PRICE限价
        "amount": 0,//买入或卖出量，对于市价买入单表
        "symbol": "BTC/USDT",//交易对
        "tradedAmount": 0,//成交量
        "turnover": 0,//成交额
        "coinSymbol": "BTC",//币单位
        "baseSymbol": "USDT",//结算币单位
        "status": "TRADING",//订单状态：TRADING交易中，COMPLETED已完成，CANCELED已取消，PARTFILLED部分成交，PARTFILL_CANCELD部分成交取消，OVERTIMED已超时
        "direction": "BUY",//方向：BUY买SELL卖
        "price": 0,//挂单价格
        "time": 1564450355000,//挂单时间
        "completedTime": 1564450357000,//交易完成时间戳
        "canceledTime": 1564450357000,//取消时间戳
        "useDiscount": "0",//是否使用折扣 0 不使用 1使用
    }
    ```


##### 订阅订单撤销通知

  * 订阅body：

    ```json
    {
        "channelId":"30104",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX",//时间戳，毫秒
        "sign":"xxx"//签名值
    }
    ```

  * 响应body：

    ```json
    {
        "orderId": "456789",//订单ID
        "userId": "123456",//用户ID
        "type": "MARKET_PRICE",//订单类型：MARKET_PRICE市价，LIMIT_PRICE限价
        "amount": 0,//买入或卖出量，对于市价买入单表
        "symbol": "BTC/USDT",//交易对
        "tradedAmount": 0,//成交量
        "turnover": 0,//成交额
        "coinSymbol": "BTC",//币单位
        "baseSymbol": "USDT",//结算币单位
        "status": "TRADING",//订单状态：TRADING交易中，COMPLETED已完成，CANCELED已取消，PARTFILLED部分成交，PARTFILL_CANCELD部分成交取消，OVERTIMED已超时
        "direction": "BUY",//方向：BUY买SELL卖
        "price": 0,//挂单价格
        "time": 1564450355000,//挂单时间
        "completedTime": 1564450357000,//交易完成时间戳
        "canceledTime": 1564450357000,//取消时间戳
        "useDiscount": "0",//是否使用折扣 0 不使用 1使用
    }
    ```


##### 订阅订单变化通知

  * 订阅body：

    ```json
    {
        "channelId":"30105",//订阅类型，固定值
        "symbol":"BTC/USDT",//交易对，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX",//时间戳，毫秒
        "sign":"xxx"//签名值
    }
    ```

  * 响应body：

    ```json
    {
        "orderId": "456789",//订单ID
        "userId": "123456",//用户ID
        "type": "MARKET_PRICE",//订单类型：MARKET_PRICE市价，LIMIT_PRICE限价
        "amount": 0,//买入或卖出量，对于市价买入单表
        "symbol": "BTC/USDT",//交易对
        "tradedAmount": 0,//成交量
        "turnover": 0,//成交额
        "coinSymbol": "BTC",//币单位
        "baseSymbol": "USDT",//结算币单位
        "status": "TRADING",//订单状态：TRADING交易中，COMPLETED已完成，CANCELED已取消，PARTFILLED部分成交，PARTFILL_CANCELD部分成交取消，OVERTIMED已超时
        "direction": "BUY",//方向：BUY买SELL卖
        "price": 0,//挂单价格
        "time": 1564450355000,//挂单时间
        "completedTime": 1564450357000,//交易完成时间戳
        "canceledTime": 1564450357000,//取消时间戳
        "useDiscount": "0",//是否使用折扣 0 不使用 1使用
    }
    ```

##### 订阅用户资产变化

  * 订阅body：

    ```json
    {
        "channelId":"30201",//订阅类型，固定值
        "symbol":"USDT",//币种，依据实际情况传参
        "apiKey":"XXX",
        "timestamp":"XXX",//时间戳，毫秒
        "sign":"xxx"//签名值
    }
    ```

  * 响应body：

    ```json
    {
        "currency": "BTC",//币种名称
        "amount": "100",//数量
        "userId": "123456789",//用户ID
        "time": 1564450355000//时间
    }
    ```

##### 心跳

  * 请求body：

    ```json
    {
        "channelId":"11004",//订阅类型，固定值
        "apiKey":"XXX",
        "timestamp":"XXX"//时间戳，毫秒
    }
    ```

  * 响应body：发送请求会返回成功

##### 完整代码示例

  * CMD.java

    ```java
    package com.gow.ws;

    public enum CMD {
        HEART_BEAT((short) 11004, "心跳"),
    
        SUBSCRIBE((short) 20001, "订阅"),
        UNSUBSCRIBE((short) 20002, "取消订阅"),
    
        PUSH_SYMBOL_THUMB((short) 30001, "推送交易对概览信息"),
        PUSH_SYMBOL_KLINE((short) 30002, "推送K线消息"),
        PUSH_EXCHANGE_TRADE((short) 30003, "推送实时成交数据"),
        PUSH_EXCHANGE_PLATE((short) 30004, "推送盘口数据"),
        PUSH_EXCHANGE_DEPTH((short) 30005, "推送全部盘口数据"),
    
        PUSH_EXCHANGE_ORDER_NEW((short) 30101, "推送新订单数据"),
        PUSH_EXCHANGE_ORDER_TRADE((short) 30102, "推送订单成交数据"),
        PUSH_EXCHANGE_ORDER_COMPLETED((short) 30103, "推送订单完成数据"),
        PUSH_EXCHANGE_ORDER_CANCELED((short) 30104, "推送订单撤销数据"),
        PUSH_EXCHANGE_ORDER_CHANGED((short) 30105, "推送订单变化数据"),
    
        PUSH_USER_BALANCE_CHANGE((short) 30201, "推送用户资产变化数据");
    
        private short code;
        private String name;
    
        CMD(short code, String name) {
            this.code = code;
            this.name = name;
        }
    
        public short getCode() {
            return code;
        }
    
        public String getName() {
            return name;
        }
    
        public static CMD getByCode(short code) {
            for (CMD cmd : CMD.values()) {
                if (cmd.getCode() == code) {
                    return cmd;
                }
            }
            return null;
        }
    
        public static String getNameByCode(short code) {
            for (CMD cmd : CMD.values()) {
                if (cmd.getCode() == code) {
                    return cmd.getName();
                }
            }
            return null;
        }
    }
    ```

  * WebSocket.java

    ```java
    package com.gow.ws;
    
    import com.alibaba.fastjson.JSON;
    import com.alibaba.fastjson.JSONObject;
    import org.apache.commons.codec.binary.Hex;
    
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import java.io.ByteArrayOutputStream;
    import java.io.DataInputStream;
    import java.io.DataOutputStream;
    import java.io.IOException;
    import java.net.Socket;
    import java.nio.charset.StandardCharsets;
    import java.util.Date;
    
    public class WebSocket {
        public static final long sequenceId = 0;//sequenceId
        public static final int requestid = 0;//请求ID
        public static final int version = 1;
        public static final String terminal = "1003";  //安卓:1001,苹果:1002,WEB:1003,PC:1004
    
        private static DataInputStream dis = null;
        private static DataOutputStream dos = null;
    
        public static void main(String[] args) {
            try {
                Socket socket = new Socket("47.56.161.239", 36666);
                dis = new DataInputStream(socket.getInputStream());
                dos = new DataOutputStream(socket.getOutputStream());
    
                //apiKey
                String apiKey = "xxx";
                String secretKey = "xxx";
                String timestamp = String.valueOf(System.currentTimeMillis());
                String signStr = "apiKey=" + apiKey + "&timestamp=" + timestamp;
                Mac hmacSha512 = Mac.getInstance("HmacSHA512");
                SecretKeySpec secKey = new SecretKeySpec(secretKey.getBytes(StandardCharsets.UTF_8), "HmacSHA512");
                hmacSha512.init(secKey);
                byte[] hash = hmacSha512.doFinal(signStr.getBytes(StandardCharsets.UTF_8));
                String sign = Hex.encodeHexString(hash);
    
                JSONObject req = new JSONObject();
                req.put("channelId", CMD.PUSH_EXCHANGE_ORDER_CHANGED.getCode());
                req.put("symbol", "BTC/USDT");
                req.put("apiKey", apiKey);
                req.put("timestamp", timestamp);
                req.put("sign", sign);
                System.out.println(JSON.toJSONString(req));
                toRequest(CMD.SUBSCRIBE, req.toJSONString().getBytes());
    
                try {
                    while (true) {
                        try {
                            dealResponse();
                        } catch (IOException e) {
                            e.printStackTrace();
                            break;
                        }
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    
        private static void toRequest(CMD cmd, byte[] body) {
            try {
                byte[] requestBytes = buildRequest(cmd, body);
                dos.write(requestBytes);
                dos.flush();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    
        private static byte[] buildRequest(CMD cmd, byte[] body) {
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            DataOutputStream dos = new DataOutputStream(bos);
            try {
                int length = body == null ? 26 : (26 + body.length);
                dos.writeInt(length);
                dos.writeLong(sequenceId);
                dos.writeShort(cmd.getCode());
                dos.writeInt(version);
                byte[] terminalBytes = terminal.getBytes();
                dos.write(terminalBytes);
                dos.writeInt(requestid);
                if (body != null) {
                    dos.write(body);
                }
                System.out.println("请求的头部信息 >>> cmd:" + cmd.getCode() + ",length:" + length + ",sequenceId:" + sequenceId);
                return bos.toByteArray();
            } catch (IOException ex) {
                try {
                    dos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            return null;
        }
    
        private static void dealResponse() throws IOException {
            int length = dis.readInt();
            long sequenceId = dis.readLong();
            short cmd = dis.readShort();
            final int responseCode = dis.readInt();
            int requestId = dis.readInt();
            byte[] buffer = new byte[length - 22];
            int nIdx = 0;
            int nReadLen = 0;
            while (nIdx < buffer.length) {
                nReadLen = dis.read(buffer, nIdx, buffer.length - nIdx);
                if (nReadLen > 0) {
                    nIdx += nReadLen;
                } else {
                    break;
                }
            }
            final String str = new String(buffer);
    
            System.out.println("返回的固定信息" + new Date() + " >>> cmd:" + CMD.getNameByCode(cmd) + ", length:" + length + ", sequenceId:" +
                    sequenceId + ", responseCode:" + responseCode + ", requestId:" + requestId + ", str:" + str);
        }
    }

    ```
