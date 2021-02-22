# order.github.io

接口文档
=
文档内容最后更新于：
<span style="color:red !important;"> 特别注意：</span>
-
1.**时间戳为秒级，非毫秒级，毫秒级请/1000**

2.**returnUrl/notifyUrl 为完整地址,含有协议+端口。如果回调通知地址（notifyUrl）不传，平台不会发起异步回调，需要调用查询接口确认订单状态。**

3.**金额为整数，非小数，以分为单位，不能包含有“·”号**,例：123 即 1.23 元。

4.**商户编号需要从商户后台首页获取，并非登陆账号**，商户密钥（apikey）每次刷新都会重新随机生成，保存好最后一次刷新的密钥进行对接即可。

5.**商户接收异步通知时，不要写死固定参数接收，请使用通用的json/map 对象接收，这样可接收完整参数，然后对json/map 里面的参数进行签名校验。如果只接收固定参数，会导致签名验证失败。后期如果通知增加参数，也可以不用修改代码**

6.**商户测试时如果要确认能不能回调以及验证签名是否成功，可生成订单后直接取消，取消后系统便会有通知。测试完取消通过后，再联系客服测试成功订单**

接口规范
-
1.字符编码：UTF-8

2.Content-Type：application/json

3.URL 传输参数需要对参数进行 UrlEncode

接口调用所需条件
-
1.网关地址：请联系客服

2.商户编号(merchantNo)

3.商户密钥(apiKey)

签名（sign）算法
-
DigestUtils.md5Hex(originalStr + "&key=" + apiKey)

1.originalStr: 除sign参数外其他参数值非空（空值或者空字符串）的参数按参数名称字母正序排序然后以name=UrlEncode(value)形式组合， 通过&拼接得到结果将apiKey拼接在最后。<br>
i.注：空值（空值或者空字符串）不参与签名。<br>
ii.注：value需要进行UrlEncode编码

示例:
amount=100&merchantNo=20200113185052721173545318&notifyUrl=https%3A%2F%2Fwww.baidu.com%2F&orderNo=123456789000&payMode=ebank&returnUrl=https%3A%2F%2Fwww.baidu.com%2F&ts=1581920707&key=06f231e8483243e28296229


2.DigestUtils.md5Hex(originalStr + "&key=" + apiKey) <br>
i.用DigestUtils.md5Hex算法将“originalStr + "&key=" + apiKey”进行加密得到签名信息

3.[c# demo](https://github.com/vvpay/hero.github.io/tree/master/C%23-demo)

4.[java demo](https://github.com/vvpay/hero.github.io/tree/master/java)

5.[php demo](https://github.com/vvpay/hero.github.io/tree/master/php)


1.订单接口内容
-
1.创建订单接口 <br>
i.请求地址：网关地址 + /open/api/order<br>
ii.请求方式：POST <br>
iii.Content-Type：application/json <br>

iv.请求参数
参数名称  | 必须  | 数据类型 | 示例| 参数说明
 ---- | ----- | ------------  | ------    | ------
 amount  | 是 | 整数 | 100 | 金额,以分为单位；最小值100，即1元
 orderNo  | 是 | 字符串(<50) | 123456789000 | 商户订单编号
 payMode  | 是 | 字符串 | 固定传: ebank | 支付模式：网银
 merchantNo  | 是 | 字符 | 20200113185052721173545318 | 商户编号
 userId | 否 | 字符串(<100) | 12345 | 商户的玩家标识(用于提高成功率)
 payName | 否 | 字符串(< 30) | 李四 | 付款人姓名, 如果参数为空, 会先跳转到系统收集付款人姓名页面
 ts  | 是 | 整数 | 1575948756 | 商户订单时间戳（秒级）
 notifyUrl  | 否 | 字符串 | https://www.baidu.com/notify | 后台通知地址
 returnUrl  | 否 | 字符串 | https://www.baidu.com | 支付完成用户返回地址
 sign  | 是 | 字符串 | 2A1FEB481909CBE0CA823D6FA31... | 参数签名，请按照签名算法生成
 

ivv.响应参数
参数名称  | 必须  | 数据类型 | 示例| 参数说明
 ---- | ----- | ------  | ------    | ------
 code  | 是 | 整数 | 200 | 响应代码
 comment  | 是 | 字符串 | 成功 | 注释
 gatewayUrl  | 否 | 字符串 | 支付链接 | 创建成功返回支付链接
 merchantNo  | 是 | 字符 | 20200113185052721173545318 | 商户编号
 orderNo | 是 | 字符串 | 123456789000 | 商户订单编号
 systemOrderNo | 否 | 字符串 | 202134134312432 | 系统订单编号


vvv.响应代码说明
code  | comment  
 ---- | -----
200 | 成功	
999 | 系统维护中	
500 | 系统其他错误	系统其他错误(保留请求与响应报文联系客服)
100 | 商户编号错误	
101 | 账户未开放	联系客服
102 | 密钥不存在	前往管理后台生成
103 | 账号配置异常	联系客服
104 | 账号设置异常	联系客服
105 | 签名异常	
106 | 时间戳错误	订单时间戳超出范围
107 | 订单号重复	
108 | 订单已存在	
109 | 金额不在指定范围	



I.请求示例: <br>
	{
	
		"amount": 100,

		"orderNo": "123456",

		"payMode": "ebank",

		"merchantNo": "20200113185052721",

		"userId": "123456"

		"ts": 1590820729,

		"notifyUrl": "https://www.baidu.com/notify",

		"returnUrl": "",

		"sign": "D6AD3B550C0D646F3A71F807E02EB2FE",
	}

II.响应示例 <br>
	{
	
		"code": 200,
		
		"comment": "成功",
		
		"gatewayUrl": "支付链接",
		
		"merchantNo": "20200113185052721",
		
		"orderNo": "123456",
		
		"systemOrderNo": "123456"
	}

2.查询订单接口
-

i.使用场景：查询订单信息。<br>
ii.请求方式：POST<br>
iii.Content-Type：application/json<br>
iv.请求地址：网关地址 + /open/api/order/query  <br>

v.请求参数
参数名称  | 必须  | 数据类型 | 示例| 参数说明
 ---- | ----- | ------  | ------    | ------
 merchantNo  | 是 | 字符串 | 20200113185052721173545318 | 商户编号
 orderNo  | 是 | 字符串(<50) | 123456789000 | 商户订单编号
 ts  | 是 | 整数 | 1575948756 | 商户订单时间戳（秒级）
 sign  | 是 | 字符串 | 2A1FEB481909CBE0CA823D6FA31... | 参数签名，请按照签名算法生成
 
 vv.响应参数(参数必须: T = 查询成功必须返回)
参数名称  | 必须  | 数据类型 | 示例| 参数说明
 ---- | ----- | ------  | ------    | ------
 code  | 是 | 整数 | 200 | 响应代码
 comment  | 是 | 字符串 | 成功 | 注释
 merchantNo  | 是 | 字符(<100) | 20200113185052721173545318 | 商户编号
 orderNo | 是 | 字符串 | 123456789000 | 商户订单编号
 systemOrderNo | T | 字符串 | 202134134312432 | 系统订单编号
 amount  | T | 整数 | 100 | 订单金额
 realAmount  | T | 整数 | 99 | 订单实际支付金额
 payMode  | T | 字符串 | ebank | 支付模式
 productName  | 否 | 字符串 | 张三 | 持卡人
 bankNo  | 否 | 字符串 | 628888888888 | 银行卡号
 bankName  | 否 | 字符串 | 中国工商银行 | 银行名称
 bankCode  | 否 | 字符串 | ICBC | 银行简称
 payStatus  | T | 整数 | 30 | 支付状态(忽略,判断订单状态即可)
 orderStatus  | T | 整数 | 50 | 订单状态(见订单状态说明)
 ts  | T | 整数 | 1575948756 | 商户订单时间
 payTime  | 否 | 整数 | 1575948756 | 支付时间
 sign  | T | 字符串 | 2A1FEB481909CBE0CA | 签名
 
 
 vvv.响应代码说明
 code  | comment  
 ---- | ----- 
 200 | 成功	
 500 | 系统其他错误	系统其他错误(保留请求与响应报文联系客服)
 100 | 商户编号错误	
 102 | 密钥不存在	前往管理后台生成
 105 | 签名异常	
 106 | 时间戳错误	订单时间戳超出范围
 110 | 订单不存在	

 
 IVV.订单状态（orderStatus）<br>
 -
 支付超时后状态可能会收到支付成功状态通知，请注意处理
 -
 值  | 说明  
 ---- | -----   
 30  | 支付等待中
 -30  | 用户取消订单
 -40  | 用户支付超时
 -50  | 订单失败
 50  | 订单已完成


 3.异步回调 （notifyUrl）
 -
 i.当创建订单时传入异步回调地址时，订单结束后（用户取消订单(-30)、用户支付超时（-40）、订单失败（-50）、订单已完成（50））进行通知，总共通知3次，间隔时间分别为0s,15s,60s，超时时间为10s，处理成  功后返回 success，返回其他字符表示处理失败，会继续进行后续通知。

ii.通知参数
参数名称  | 必须  | 数据类型 | 示例| 参数说明
 ---- | ----- | ------  | ------    | ------
 merchantNo  | 是 | 字符(<100) | 20200113185052721173545318 | 商户编号
 orderNo | 是 | 字符串 | 123456789000 | 商户订单编号
 systemOrderNo | 是 | 字符串 | 202134134312432 | 系统订单编号
 amount  | 是 | 整数 | 100 | 订单金额
 realAmount  | 是 | 整数 | 99 | 订单实际支付金额
 payMode  | 是 | 字符串 | ebank | 支付模式
 productName  | 否 | 字符串 | 张三 | 持卡人
 bankNo  | 否 | 字符串 | 628888888888 | 银行卡号
 bankName  | 否 | 字符串 | 中国工商银行 | 银行名称
 bankCode  | 否 | 字符串 | ICBC | 银行简称
 payStatus  | 是 | 整数 | 30 | 支付状态(忽略,判断订单状态即可)
 orderStatus  | 是 | 整数 | 50 | 订单状态(见订单状态说明)
 ts  | 是 | 整数 | 1575948756 | 商户订单时间
 payTime  | 否 | 整数 | 1575948756 | 支付时间
 sign  | 是 | 字符串 | 2A1FEB481909CBE0CA | 签名
 
  例：
  curl -X POST "回调地址" <br>
  -H 'content-type: application/json' <br>
  -d '{
	      
	      "merchantNo":"20200113185052721173545318",
	      
	      "orderNo":"o-1008614",
	      
	      "systemOrderNo":"20200113185052721173545",
	      
	      "amount":100,
	      
	      "realAmount":99,
	      
	      "payMode":"ebank",
	      
	      "productName":"张三",
	      
	      "bankNo":"628888888888",
	      
	      "bankName":"中国工商银行",
	      
	      "bankCode":"ICBC",
	      
	      "payStatus":30,
	      
	      "orderStatus":50,
	      
	      "ts":1581585888,
	      
	      "payTime":1581586702,
	      
	      "sign":"3aff08ebde950423acbc267e363588ec"
  }'
 
 
