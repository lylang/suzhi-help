
# 速致视频转码 API 文档
------------

* 更新时间 1: 2015/08/20
* 更新时间 2: 2015/11/10
* 更新时间 3: 2015/12/03

## 基本说明

描述，使用 API 前需要在网站控制中心设置“转码方案”，详情请看 [这里](../user-guide/transcode/)

API 请求域名 - `https://avapi.cdnzz.com/`

基于 HTTPS 使用 POST 方式传递参数。

具体的 API 验证方法:

  1. 首先需要通过速致 API 获取相应的 token（ [参考在线文档](https://docs.cdnzz.com/dev/api-v3/#token) ）
  2. 通过获取到的 token 进行视频转码的 API 请求

描述，通过速致 API 获取到的 token 可以设置过期时间，过期时间内可以通过 token 去调用别的相关 API。

Python 代码示例:

```
import requests

def get_token():
    rd = dict(method="FetchToken", secretkey=key, user=user)
    rv = requests.post('https://www.cdnzz.com/apiv3/json', data=rd)
    rv = rv.json()
    token = rv['result']['token']
    return token

token = get_token()
data = dict(user=user, token=token, space='suzhi', path='/test/hello-world.mp4')
requests.post('https://avapi.cdnzz.com/video/upload', data=data,
              files={'file': open('path/to/file.mp4', 'rb')})
```

## 视频文件上传自动转码
- **说明**: 添加一个视频文件到存储空间并进行具体的转码处理
- **调用地址**: `https://avapi.cdnzz.com/video/upload`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| file | True | binary data | 视频文件的二进制数据内容 |
| callback | False | string | （可选）视频转码后进行信息回调的地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 对于 callback 参数，当提供一个可用回调 url，我们的转码程序会在视频文件处理完成后进行回调通知，回调 url 必须能访问且接收 POST 的请求，请求内容为包括 <path, user, space\> 信息的 JSON 格式序列化数据
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&file=<video binary data\>
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "",
  "result": {
      'info':"" //视频简略信息
  }
  "session-id": "" //该次转码留存key
}
```

## 视频转码确认
- **说明**: 添加一个视频到存储中进行转码
- **调用地址**: `https://avapi.cdnzz.com/video/confirm`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，不是m3u8文件地址
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 urlpath 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
    'urlpath': "*.m3u8"
    'info': "" //视频原始简略信息
  }
}
```


## 视频截图请求
- **说明**: 对已上传好的视频进行截图请求
- **调用地址**: `https://avapi.cdnzz.com/video/screenshot`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| seek | True | string | 截图起始秒数，例如`20` |
| count | False | string | 截图数量，如果无此参数则请求单张截图 |
| offset | False | string | 多张截图时间间隔，如果count大于1,则该参数必选，如果count无参数，则该参数无效 |
| reverse | False | string | 多张截图时，选择正向或反向截图，仅在reverse存在且为1时采用逆序截图 |

- **描述**:
    - 只有企业用户才可以使用
    - 暂时不支持m3u8
    - 当请求多张截图时，仅返回视频范围内的截图
    - seek 与 offset 本身以秒为单位，但均可使用浮点数例如 `1.1`, `3.1415`, 当使用浮点数时，可以表示毫秒单位的时间。
    - 返回图片命名以视频截图秒数为名，类似请求位于3.1415秒的截图，返回的即是3_142.jpg，默认小数精度保留3位。
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&seek=seek&count=count&offset=offset&reverse=1
- **返回**:


```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
    img_sources:{
        path/3_000.jpg,
        path/4_000.jpg
    }
  }
}
```


## 视频信息请求
- **说明**: 对已上传好的视频进行信息请求
- **调用地址**: `https://avapi.cdnzz.com/video/information`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| is_full | False | string | 仅当该参数为1时，返回全部信息。 默认返回简单信息。 |

- **描述**:
    - 只有企业用户才可以使用
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&is_full=1
- **返回**:


```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
    "bit_rate": "265680", 
    "duration": "1437.467000", 
    "height": 240, 
    "size": "47738423", 
    "width": 320
  }
}
```

## 视频文件上传
- **说明**: 添加一个视频文件到存储空间,不进行任何处理
- **调用地址**: `https://avapi.cdnzz.com/avhub/video/upload`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| file | True | binary data | 视频文件的二进制数据内容 |

- **描述**:
    - 只有企业用户才可以使用
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&file=<video binary data\>
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "",
  "result": {
      'info':"" //视频简略信息
  }
  "session-id": "" //该次转码留存key
}
```

## 提交视频链接自动下载
- **说明**: 添加一个视频文件的链接, 后台自动下载都指定的存储目录
- **调用地址**: `https://avapi.cdnzz.com/video/download`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| url | True | string | 视频文件的链接地址 |
| callback | False | string | （可选）视频下载信息回调的地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 经过测试可以下载的视频完整包括优酷/网易视频/腾讯视频等主流的视频网站(2016.06.15)
    - 对于 callback 参数，当提供一个可用回调 url，我们的转码程序会在视频文件处理完成后进行回调通知，回调 url 必须能访问且接收 POST 的请求，请求内容为包括 <user, savepath, url, status, msg\> 信息的 JSON 格式序列化数据
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&file=http://xxx.xxx.com/xxx/xxx.mp4
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "", // 成功为ok, 或者时出错提示
  "result": {
  }
}
```

## 添加视频转码任务
- **说明**: 对存储中存在的视频进行转码
- **调用地址**: `https://avapi.cdnzz.com/video/transcode`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| callback | False | string | （可选）视频转码后进行信息回调的地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 对于 callback 参数，当提供一个可用回调 url，我们的转码程序会在视频文件处理完成后进行回调通知，回调 url 必须能访问且接收 POST 的请求，请求内容为包括 <path, user, space\> 信息的 JSON 格式序列化数据
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "",
  "result": {
      "info": {},//视频简略信息
      "space": ""
  }
  "session-id": "" //该次转码留存key
}
```


## 视频提取音频
- **说明**:对已存储的视频进行音频提取
- **调用地址**: `https://avapi.cdnzz.com/avhub/audio/extract`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，不是m3u8文件地址
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 urlpath 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
     "urlpath": "*.mp3",
  }
}
```

## 视频提取音频确认
- **说明**:对已存储的视频转码为音频确认
- **调用地址**: `https://avapi.cdnzz.com/avhub/audio/confirm`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，不是m3u8文件地址
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 urlpath 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
     "urlpath": "*.mp3",
  }
}
```
