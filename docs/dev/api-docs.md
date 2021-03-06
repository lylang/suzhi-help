## API 简介


版本：V2.0（更新时间：2014-01-09)

简介：速致将逐渐开放一系列API提供给广大用户，以便更好地使用我们的产品。

使用对象：个人、企业用户。（如无特别说明，都可以使用）

注意

> 旧版 API 将逐步**弃用**，请使用我们 [新版 API](./api-v3.md)


### API 调用规定

请求地址：https://www.cdnzz.com/api/json

公共参数

- user（必传）:用户账号，例如test@abc.com
- signature（必传）:接口标识，在个人账户页面查询。

传输方式：POST

数据编码：UTF-8

返回格式：JSON

关于封禁：API 请不要滥用，否则会导致账号在 API 中封禁。封禁后一定时间后会自动解封，所以请小心操作，不要拿 API 进行大量测试操作。


### 清缓存

参数|说明
---|---
user|用户注册邮箱
signature|用户签名
method|PurgeCache
url|需要清缓存的 URL


**示例**
`user=test@abc.com&signature=SIGNATURE&method=PurgeCache&url=https://www.cdnzz.com/help/user_api`

每次提交可以组合多条url，url之间以逗号分隔，每次提交最多为100条。

如上例 `url=https://www.cdnzz.com/help/user_api1,https://www.cdnzz.com/help/user_api1`


**返回结果，JSON示例**
```
{"result":"error","error_code":"10001","msg":"Authentication/Authorization Failed"}
```

```
{"result":"success","msg":"","response":[]}
```

关于错误返回值与错误代码，可见 [错误代码]()


### 预加载

参数|说明
---|---
user|用户注册邮箱
signature|用户签名
method|AddPreload
url|需要预加载的 URL


接口的操作方法与清缓存一样

返回结果，JSON示例


```
{"result":"error","error_code":"10001","msg":"Authentication/Authorization Failed"}
```


```
{"result":"success","msg":"","response":[]}
```

### SDK

#### PHP

[GitHub 地址](https://github.com/GridSafe/grid-sdk-php)

#### Python2

[Github 地址](https://github.com/GridSafe/grid-sdk-python2)

#### Go

[GitHub 地址](https://github.com/GridSafe/grid-sdk-golang)

#### Java

[GitHub 地址](https://github.com/GridSafe/grid-sdk-java)

#### NodeJs

[GitHub 地址](https://github.com/GridSafe/grid-sdk-nodejs)

### 错误码

错误|错误信息|备注
---|---|---
10000|Data Transmission Error|Please use the POST method to send data
10001|Authentication/Authorization Failed|This error can be caused by an incorrect API username, or an invalid API signature. Make sure that all of these values are correct
10002|Missing Parameter|Required Parameter Missing : Unable to identify parameter
10003|Unspecified Method|No Method Specified
10004|Unspecified Method|Method Specified is not Supported
10005|Internal Error|Internal Error
10006|Authentication/Authorization|Failed Account is restricted
10007|Authentication/Authorization|Failed Account is not exists
10008|Parameter Empty|RequiredParameter can not be empty
10009|Network error|The server network error
10010|Upgrade Maintenance|Upgrade Maintenance
20001|Invalid URL|Please enter valid URL including http:// or https://
20002|Submit Frequently|Please Try It Later!
20003|Has In the Queue|Has in the queue
20004|Not find the log|Not find the log
30001|Invalid Email|Please enter a valid email
30002|User exists|User has exists
