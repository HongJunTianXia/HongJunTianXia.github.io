# 多贝云API使用文档

------
通过多贝云API，您可以创建、修改房间，上传、绑定文档，并通过访问生成的特定连接直接从web页面进入房间，实现对虚拟房间的自动化控制。

## 参数封装规则

### 编码规则

请求及返回使用 UTF-8 编码

### 请求链接规则

基本链接为`https://api.duobeiyun.com/`
后缀为接口路径

### 生成sign规则

提交的参数除了`sign`之外,根据`参数名=参数值`(参数值首尾不能包含空格)的格式，按首字符字典顺序 (ASCII值大小)升序排序。若遇到相同首字符，则判断第二个字符。以此类推，待签名字符串需要以`参数名1=参数值1&参数名2=参数值2...&参数名N=参数值N`的规则进行拼接, 最后得出的字符串再直接连接上`appkey`，之后进行md5Hex得出`sign`。

> 注意: 没有值的参数无需传递,也无需包含到待签名数据中;
> 即不能出现类似`nickname=&roomId=`这样的没有值的拼接
> 否则会返回错误码`app_sign_error`

### 签名例子

`appKey`是`3af12186256845b4bc2441e223829bfc `
待签名字符串如下

```plain
duration=2&partner=20151119143137&startTime=2015-12-17 17:11&title=test course&video=0&timestamp=1497502288959
```
拼接后为

```plain
duration=2&partner=20151119143137&startTime=2015-12-17 17:11&timestamp=1497502288959&title=test course&video=03af12186256845b4bc2441e223829bfc
```

 之后对拼接后字符串进行md5Hex得出`sign`的值为`88df34581719235f50b0ed66ade2d302`，可使用所给参数计算sign，看是否和给出的sign值一致。


## 房间控制API

### 创建房间

#### URL
`https://api.duobeiyun.com/api/v3/room/create`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      title       |  Y          |       房间标题   |
|     video     |  Y          |   是否开启视频。开启视频填1，不开启填0   |
|      startTime     |  Y          |      房间开始时间,格式 2013-10-27 11:11   |
|     duration     |  Y          |   房间时长,单位小时, 可选值为 1 , 2, 3,4,5   |
|      roomType     |  Y          |       房间类型。 可选值为 1,2,8。 “1” 表示 1v1 课程,“2”表示 1vn 课程, "4"表示小班flash课程(`需先申请该权限`), "8"表示小班客户端(`需先申请该权限`)|

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true, 
    "room": {
        "roomId": "jz31f3febb8bfe4139b7636cb6ade87733", 
        "title": "2017-06-09 API_v3_1vn教室", 
        "startTime": "2017-06-15 12:59:00", 
        "endTime": "2017-06-15 13:59:00", 
        "validEndTime": "2099-01-01 23:59", 
        "video": false, 
        "hostCode": "42524253afd64d8689ef5d4e94da1533", 
        "coverImgUrl": null, 
        "convertStatus": 0, 
        "courseType": 2, 
        "vod": false, 
        "weixinLiving": false, 
        "audio": false
    }
}

```
失败情况(error 的参考文档的文档尾部):
```json
  {
    "error": "app_sign_error",
    "success": false
  }
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |
| vod | 是否是点播课|
| audio | 是否是音频课 |
| convertStatus | 课程转换状态，0 默认，1 转换成功，2 转换失败 3：空录像|



### 创建房间(方式2)

#### URL
`https://api.duobeiyun.com/api/v4/room/create`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      title       |  Y          |       房间标题   |
|     video     |  Y          |   是否开启视频。开启视频填1，不开启填0   |
|      startTime     |  Y          |      房间开始时间,格式 2013-10-27 11:11   |
|     length     |  Y          |   房间时长,单位分钟, 不能小于30分钟 不能大于300分钟   |
|      roomType     |  Y          |       房间类型。 可选值为 1, 2, 4, 8。 “1” 表示 1v1 课程,“2”表示 1vn 课程, "4"表示小班flash课程, "4"表示小班flash课程(`需先申请该权限`),  "8"表示小班客户端(`需先申请该权限`)|

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true, 
    "room": {
        "roomId": "jz0d900e29d9d847899959de936cfea233", 
        "title": "2017-06-09 API_v4_1vn教室", 
        "startTime": "2017-06-15 13:04:00", 
        "endTime": "2017-06-15 14:44:00", 
        "validEndTime": "2099-01-01 23:59", 
        "video": false, 
        "hostCode": "e01587ba9db346ffaee1b09aaa14fe33", 
        "coverImgUrl": null, 
        "convertStatus": 0, 
        "courseType": 2, 
        "vod": false, 
        "weixinLiving": false, 
        "audio": false
    }
}

```
失败情况(error 的参考文档的文档尾部):
```json
  {
    "error": "app_sign_error",
    "success": false
  }
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |



### 创建点播课

1，引入Plupload <a href = "http://www.plupload.com/download">Plupload下载</a> (http://www.plupload.com/download)，建议 2.1.1 及以上版本，引入plupload.full.min.js

2，引入 qiniu.js 获取SDK源码 git clone git@github.com:qiniupd/qiniu-js-sdk.git，qiniu.js 位于src目录内

3，将下面代码引入到js文件中，修改uptoken_url: '/material/upload'，/material/upload需要开发人员在后台调用api获取uptoken, 将api返回的Json数据直接return即可。

```javascript
var initUploader = function() {
    Qiniu.uploader({
        runtimes: 'html5,flash,html4',
        browse_button: 'uploadButton',
        uptoken_url: '/material/upload',
        domain: 'http://dapeng367.qiniudn.com/',
        max_file_size: '500mb',
        flash_swf_url: 'js/plupload/Moxie.swf',
        max_retries: 5,
        chunk_size: '4mb',
        auto_start: true,
        save_key: true,
        filters: {
            mime_types : [
                { title : "video files", extensions : "mp4,flv,avi,wmv,mpeg,mov" },
            ],
            prevent_duplicates : true,
        },
        init: {
            'BeforeUpload': function(up, file) {
                //每个文件上传前,处理相关的事情
            },
            'UploadProgress': function(up, file) {
                //每个文件上传时,处理相关的事情,例如打印上传速度
                var speed = up.total.bytesPerSec / 1024 / 1024;
                console.log(speed.toFixed(1) + "M/s");
            },
            'Error': function(up, err, errTip) {
                //上传出错时,处理相关的事情
            },
            'FileUploaded': function (up, file, info) {
                //每个文件上传成功后,处理相关的事情
            }
        }
    });
}

initUploader();
```
#### 1.api获取uptoken,URL 
`https://api.duobeiyun.com/api/v3/video/uptoken`

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |

返回值

```json
{
    "uptoken": "dbe8228a-3d57-4500-8049-0fd40f05c738",
    "success": true
}
```

#### 2.api创建点播课,URL
`https://api.duobeiyun.com/api/v3/room/video/create`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      title       |  Y          |       房间标题   |
|     videoKey     |  Y          |   上传视频文件后，生成的key   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true,
    "room": {
        "roomId": "jz3be78b35072c45978e89f016d0ef6af4",
        "title": "新版视频课2"
    }
}
```
#### 参考

>创建点播课DEMO在Download中：upload_video.zip，可以部署到php服务器上直接运行(需要修改DuobeiyunApiConfig.php里的api配置)
>
七牛文档: http://developer.qiniu.com/code/v6/sdk/javascript.html
失败情况(error 的参考文档的文档尾部):


返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |



### 更新房间标题

#### URL
`https://api.duobeiyun.com/api/v3/room/update/title`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId     |  Y          | 房间Id   |
|      title       |  Y          |       新的房间标题   |


#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true, 
    "room": {
        "roomId": "jz6d960549e3e64af4b2cb590c0a2bd333", 
        "title": "张三张三张三张三", 
        "startTime": "2015-12-07 15:30:00", 
        "endTime": "2015-12-07 15:36:48", 
        "validEndTime": "2099-01-01 23:59", 
        "video": false, 
        "hostCode": "42524253afd64d86893f5d4e94da1533", 
        "coverImgUrl": null, 
        "convertStatus": 3, 
        "courseType": 2, 
        "vod": false, 
        "weixinLiving": false, 
        "audio": false
    }
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |

### 修改房间开始时间和时长

#### URL
`https://api.duobeiyun.com/api/v3/room/update/time`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId       |  Y          | 房间的 Id   |
|      startTime     |  Y          |      房间开始时间,格式 2013-10-27 11:11   |
|     duration     |  Y          |   房间时长,单位小时, 可选值为 1 , 2, 3,4,5   |

> 注意
> 如果房间的开始时间已经超过了当前时间，则无法修改房间开始时间

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true, 
    "room": {
        "roomId": "jz5f0156b7b1f84b2491bb2b889ebe282f", 
        "title": "测试修改时长", 
        "startTime": "2017-06-15 20:10:00", 
        "endTime": "2017-06-15 22:10:00", 
        "validEndTime": "2099-01-01 23:59", 
        "video": false, 
        "hostCode": "42524253afd64d86893f5d4e94da1533", 
        "coverImgUrl": null, 
        "convertStatus": 0, 
        "courseType": 2, 
        "vod": false, 
        "weixinLiving": false, 
        "audio": false
    }
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 修改房间开始时间和时长(方式2)

#### URL
`https://api.duobeiyun.com/api/v4/room/update/time`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId       |  Y          | 房间的 Id   |
|      startTime     |  Y          |      房间开始时间,格式 2013-10-27 11:11   |
|     length     |  Y          |   房间时长,单位分钟, 不能小于30分钟 不能大于300分钟   |

> 注意
> 如果房间的开始时间已经超过了当前时间，则无法修改房间开始时间

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true, 
    "room": {
        "roomId": "jz5f0156b7b1f84b2491bb2b889ebe282f", 
        "title": "测试修改时长", 
        "startTime": "2017-06-15 13:48:00", 
        "endTime": "2017-06-15 17:08:00", 
        "validEndTime": "2099-01-01 23:59", 
        "video": false, 
        "hostCode": "42524253afd64d86893f5d4e94da1533", 
        "coverImgUrl": null, 
        "convertStatus": 0, 
        "courseType": 2, 
        "vod": false, 
        "weixinLiving": false, 
        "audio": false
    }
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 上传文档

#### URL
`https://api.duobeiyun.com/api/v3/documents/upload`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| slidesFile       |  Y          |       上传的文档内容  |

> 这个 api 比较特殊,参数加密时 不需要带上 slidesFile
> 并且使用文件 流的格式上传。支持的格式有 word、ppt、 pdf、jpg、png,大小不超过 20M

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
  "uuid": "dbe8228a-3d57-4500-8049-0fd40f05c738",
  "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|uuid  | 课件的唯一 id        |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 附加文档到房间

#### URL
`https://api.duobeiyun.com/api/v3/room/attachDocument`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| documentId       |  Y          |       文档的 uuid   |
| roomId     |  Y          |   房间的唯一 id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
  "documentId": "ccd0ec55-a3f6-42da-a02f-e785eb56dd7e",
  "roomId":"jz1659df5674e947dcbb948723e983f7af",
  "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 成功安排的房间唯一 id        |
| slidesUuid  | 成功安排的课件唯一 id           |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 查询文档转换状态

#### URL
`https://api.duobeiyun.com/api/v3/documents/status`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| documentId       |  Y          |       文档id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
  "status": "converted",
  "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 获取房间文档列表

#### URL
`https://api.duobeiyun.com/api/v3/room/listDocuments`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId     |  Y          |   房间的唯一 id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "documents": [
        {
            "originalDocName": "1.pptx", 
            "createTime": 1493714601000, 
            "status": "converted", 
            "size": 31609, 
            "documentId": "b84b4598-cc82-4531-8d83-09bb64c0786d", 
            "slideCount": 1, 
            "documentType": "application/vnd.openxmlformats-officedocument.presentationml.presentation"
        }, 
        {
            "originalDocName": "1.png", 
            "createTime": 1493716009000, 
            "status": "converted", 
            "size": 187291, 
            "documentId": "c2e45a62-ca28-4f61-8d28-2a796a54d4f8", 
            "slideCount": 1, 
            "documentType": "image/png"
        }
    ], 
    "success": true
}
```

失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
| documents  | 文档对象数组        |
| originalDocName  | 文档原始名称           |
| createTime  | 关联到房间时间戳           |
| status  | 转换状态           |
| size  | 文档字节数           |
| documentId  | 上传后获取到的documentId           |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 从房间删除已附加文档

#### URL
`https://api.duobeiyun.com/api/v3/room/removeDocument`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| documentId       |  Y          |       文档的 uuid   |
| roomId     |  Y          |   房间的唯一 id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
  "documentId": "ccd0ec55-a3f6-42da-a02f-e785eb56dd7e",
  "roomId":"jz1659df5674e947dcbb948723e983f7af",
  "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_sign_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 成功安排的房间唯一 id        |
| slidesUuid  | 成功安排的课件唯一 id           |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |


### 请求进入房间

#### URL
`https://api.duobeiyun.com/api/v3/room/enter`

 > 注意: 使用iframe嵌入，父页面请使用https。在chrome60以及以上版本，若父页面非https，则可能会无法访问麦克风和摄像头。
 
 > 最新版本的chrome，64版本，使用iframe加载包含flash的页面时会出现无法获取音视频的问题，解决方法如下:
   在加载iframe时增加allow属性，属性值为geolocation; microphone; camera，示例如下
   ```<iframe src="https://api.duobeiyun.com" allow="geolocation; microphone; camera">```

#### 支持请求返回数据的格式
`HTML` , 讲座页面的尺寸大小是 1040 * 600。可以针对这个尺寸做自己适合 的嵌入

#### HTTP请求方式
`GET`
>使用拼接好的进教室链接，访问api接口， 将返回html页面，开发人员以iframe的形式嵌入网页中即可。

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| uid       |  Y          |       用户在自己网站上的唯一标识,一定 不能重复   |
| roomId     |  Y          |   房间 id   |
| nickname     |  Y        |      用户在房间内的昵称，不能为空，昵称首尾不能包含空格   |
| userRole     |  Y          |   可选值为1,2,3,4。 值为1时表示以 主讲人身份进入教室，值为2时表示以听众身份进入教室，值为3时表示以隐身监课者身份进入，值为4时表示以房间助教身份进入教室。当请求不加该参 数时默认以听众身份进入   |
| deviceType | N | 可选值1,2.  当不填deviceType时, 会根据userAgent判断用户访问设备, 并返回页面; 值为1时返回PC客户端; 值为2时返回手机端教室 |
| h5 | N | 可选值 true,false，用来强制 PC 端使用 H5版回放（课程必须是音频课，浏览器只能使用 chrome、safari） |
| psTime | N | 回放开始播放时间，相对于实际播放时间，例如：100:20 表示100分钟20秒的时刻开始播放 |
| peTime | N | 回放结束播放时间，相对于实际播放时间，例如：120:23 表示120分钟23秒的时刻开始播放 |
| tape | N | 可选值: true、false 或 1、0。如果值为true则忽略剪辑、忽略指定的时间播放参数，播放未剪辑的回放内容 |
| adminDisableChat | N | 是否允许管理员发言, 默认为false, 允许发言. 只有当userRole=3时, 该参数生效. 可选值: true、false |

#### 返回参数

返回参数为`html`，格式示例如下

成功情况:

```html
<body style="padding: 0;margin: 0;height: 100%;">
<script type="text/javascript" language="javascript" src="/jquery-1.7.min.js"></script>
<script type="text/javascript">
    $(function(){
        var $iframe = $("#dby_room_iframe");
        var $window = $(window);
        function setIframeSize() {
            $iframe.css({
                "height": $window.height(),
                "width": $window.width()
            });
        }
        setIframeSize();
        $iframe.on("load", setIframeSize);
        $window.on("resize", setIframeSize);
    });
</script>
<iframe id="dby_room_iframe" src="xxx" scrolling="no" frameborder="0" ></iframe>
</body>
```

过期情况:
```html
<!doctype html>
<html>
  <head>
  <meta charset="utf-8">
  <title>提示</title>
  <link type="text/css"
  href="http://guanli.duobeiyun.com/styles/enter_tip.min.css?v=c2dc9
  285" rel="stylesheet" charset="utf-8">
  <!--[if lt IE 9]>
  <script type="text/javascript"
  src="http://guanli.duobeiyun.com/javascript/bower_components/es5-s
  him/es5-shim.min.js" charset="utf-8"></script>
  <![endif]-->
  </head>
  <body>
    <div class="wrap end"><!-- 1. error 转换失败 2. end 已经结束 3,4. 什么都不加 未开始、转换中 -->
    <div id="content"> <h1>提示:</h1>
    <div class="meeting_info">
    <p class="title cf">
    <span class="label fl">活动名称:</span>
    <strong class="detail fl">测试余额小于 0</strong> </p>
    <p class="time">
    <span class="label">活动时间:</span> 2013-12-16 16:00 - 2013-12-16 18:00</p>
    <p class="organizer">
    <span class="label">组织者:</span> 李真荣 1</p>
    </div>
    <p class="meeting_status">回放有效期已过</p> </div>
    </div>
  </body>
</html>
```

其他失败情况(error 的参考文档的文档尾部)
```html
<html>
<head></head>
<body>
  app_error
</body>
</html>
```

### 获取authinfo接口。（app进教室）

#### URL
`http://api.duobeiyun.com/api/v4/room/authinfo`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`GET`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      uid       |  Y          |       用户在自己网站上的唯一标识,一定 不能重复   |
| roomId | Y | 房间 id|
| nickname | Y | 用户在房间内的昵称，不能为空，昵称首尾不能包含空格 |
| userRole | Y |值为2时表示以学生身份进入教室 |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "authInfo": {
        "accessToken": "4f55621f74b68a8a70d53c4677ddacfa", 
        "audioVideoStreamMerge": false, 
        "avatar": "http://www.wangxiaotong.com/avatar/20130730/f80972762f1241b9aef71edad7a5b442.jpg", 
        "chatAvaliable": true, 
        "courseEndTime": 1468251720000, 
        "courseStartTime": 1468233720000, 
        "courseTitle": "Y2VzaGljZXNoaXRlc3R0ZXN0", 
        "courseType": 2, 
        "currentTimestamp": 1468240802612, 
        "endTimestamp": 1468251720000, 
        "from": "http://api.duobeiyun.com/api/v3/room/enter", 
        "nickname": "aGVsbG93b3JsZA==", 
        "permissions": [
            "videoCourse.*", 
            "newWeixinLive.*", 
            "mobile1Vn.*"
        ], 
        "playEndTime": 0, 
        "playStartTime": 0, 
        "recordable": "false", 
        "roomId": "jzf788fbb92bd94fc7ac0949ec9986cfdf", 
        "roomVideo": "false", 
        "sid": "618afee8ea3f093474fbb921079e3c60", 
        "teacherImg": "http://avatar.wangxiaotong.com/avatar/20130730/d45e18c0811e45dbb68228674d54d8b8.jpg", 
        "testServers": [
            "123.57.138.79:601"
        ], 
        "tid": "1468240802611", 
        "uniqueUserId": "jzcbd4f7cde4e844579d0727d78088047d", 
        "userId": "jzcbd4f7cde4e844579d0727d78088047d", 
        "userRole": "2"
    }, 
    "status": 2, 
    "success": true
}
```
#### 返回参数说明

| 参数名     |  说明      |
| -----     |  ------    |
| authinfo   |  authinfo信息   |
| status   |  教室状态, 1未开始，2正在进行，3可以正常观看回放，4没有人参加直播，5回放转化失败，6回放转换中   |
| success   |  true请求成功，false请求失败   |


### 查看机构文档列表、按标题搜索文档

#### URL
`https://api.duobeiyun.com/api/v3/document/list`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      docName       |  N          |       根据标题模糊查询   |
| pageNo | N | 第几页|

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "pagination": {
        "pageNo": 1, 
        "pageSize": 20,
        "countTotal": true, 
        "sliderCount": 5, 
        "items": [
            {
			    "createTime": 1430309490000, 
			    "status": 3, 
			    "docSize": "158.0K", 
			    "uniqueId": "8a4691ad-d061-4d54-87be-ed709102ff9b", 
			    "slideCount": 1, 
			    "documentType": "application/octet-stream", 
			    "originalDocName": "IntelliJIDEA_ReferenceCard.pdf"
			},
            ......
        ], 
        "totalCount": 9, 
        "totalPages": 1, 
        "lastPage": true, 
        "nextPage": 1, 
        "hasNextPage": false, 
        "firstPage": true, 
        "prePage": 1, 
        "hasPrePage": false, 
        "slider": [1]
    }, 
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
  {
    "error": "app_sign_error",
    "success": false
  }
```

返回参数说明:

##### pagination 参数说明

|  参数名       |  说明        |
| -------      | -------     |
|pageNo |当前页  |
|pageSize |一页显示条数 |
|totalCount |记录总数 |
|slider | 系统生成的页码 |
|totalPages |总页数  |
|firstPage  |是否是第一页 |
|lastPage |是否是最后一页  |
|hasNextPage  |是否有下一页 |
|nextPage |下一页  |
|hasPrePage |是否有上一页 |
|prePage  |上一页  |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |

##### items 参数说明

|  参数名       |  说明        |
| -------      | -------     |
|originalDocName | 文档标题        |
| documentType  | 文档类型          |
| createTime  | 文档上传时间     |
| uniqueId  | 文档uniqueId      |
| docSize | 文档大小 |
| slideCount  |   文档页数 |


### 查询文档详细信息

#### URL
`https://api.duobeiyun.com/api/v3/document/detail`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      documentId       |  Y          |       文档的唯一id   |


#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "document": {
        "createTime": 1430309490000,
        "status": 3,
        "docSize": "158.0K",
        "uniqueId": "8a4691ad-d061-4d54-87be-ed709102ff9b",
        "slideCount": 1,
        "documentType": "application/octet-stream",
        "originalDocName": "IntelliJIDEA_ReferenceCard.pdf"
    },
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):

```json
  {
    "error": "document_not_found",
    "success": false
  }
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
| success | true 成功 / false 失败 |
|originalDocName | 文档标题        |
| documentType  | 文档类型          |
| createTime  | 文档上传时间     |
| uniqueId  | 文档uniqueId      |
| docSize | 文档大小 |
| slideCount  |   文档页数 |


### 删除文档

#### URL
`https://api.duobeiyun.com/api/v3/document/delete`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      documentId       |  Y          |       文档的唯一id   |


#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):

```json
  {
    "error": "document_not_found",
    "success": false
  }
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
| success | true 成功 / false 失败 |


### 查看机构详情

#### URL
`https://api.duobeiyun.com/api/v3/merchant`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "merchant": {
        "name": "宇宙中心",
        "videoUnitPrice": 4,
        "unitPrice": 2,
        "mobile": "1326009xxxx",
        "remainMoney": 4,
        "totalChargeMoney": 20,
        "consumptionMoney": 16
    },
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
  {
    "error": "app_sign_error",
    "success": false
  }
```

返回参数说明:

##### pagination 参数说明
|  参数名       |  说明        |
| -------      | -------     |
| name|	机构名称|
|	videoUnitPrice|	视频课单价|
|	unitPrice|	普通课程单价|
|	mobile| 	机构登陆手机号|
|	remainMoney|	账户余额|
|	totalChargeMoney|	机构总充值总额|
|	consumptionMoney|	机构总消费额|



### 查看教室房间详情

#### URL
`https://api.duobeiyun.com/api/v3/room/detail`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
| roomId       |  Y          | 房间的 Id   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "course": {
        "roomId": "jza26a4e6300b5456eb3d04ecd21d6dc4d", 
        "title": "新版视频课2", 
        "startTime": "2016-03-17 11:02:39", 
        "endTime": "2016-03-17 12:02:39", 
        "validEndTime": "2017-03-17 14:02:39", 
        "video": true, 
        "hostCode": null, 
        "coverImgUrl": "http://7xjsiy.media1.z0.glb.clouddn.com/sucai_video.png", 
        "convertStatus": 1, 
        "weixinLiving": false, 
        "teacherName": null, 
        "teacherBrief": null, 
        "description": null, 
        "courseType": 2, 
        "vod": true
    }, 
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
{
  "error": "app_course_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| success   | 成功与否标志,为 true/false |
| convertStatus | 0默认状态; 1转换成功; 2转换失败; 3空录像 |
| weixinLiving | false未开通 true已开通 |
| teacherName  |  老师名字 |
| teacherBrief |  老师简介 |
| description | 课程简介 |



### 查看房间列表、按时间或标题搜索房间

#### URL
`https://api.duobeiyun.com/api/v3/room/list`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
|      title       |  N          |       根据标题模糊查询   |   |
|      startTime     |  N   |      开始时间早于这个时间的房间,格式 2013-10-27 11:11   |
| endTime     |  N  |      开始时间晚于这个时间的房间,格式 2013-11-27 11:11   |
| pageNo | N | 第几页|

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "pagination": {
        "pageNo": 1, 
        "pageSize": 20,
        "countTotal": true, 
        "sliderCount": 5, 
        "items": [
            {
                "roomId": "jzb3a6e9daa3b047029e8dfe4e64abb872", 
                "title": "上传视频课2", 
                "startTime": "2015-05-22 12:10:23", 
                "endTime": "2015-05-22 13:10:23", 
                "validEndTime": "2016-05-22 15:10:23", 
                "video": true,
                "convertStatus": 1,
                "hostCode": "uyenl2"
            }, 
            ......
        ], 
        "totalCount": 9, 
        "totalPages": 1, 
        "lastPage": true, 
        "nextPage": 1, 
        "hasNextPage": false, 
        "firstPage": true, 
        "prePage": 1, 
        "hasPrePage": false, 
        "slider": [1]
    }, 
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):
```json
  {
    "error": "app_sign_error",
    "success": false
  }
```

返回参数说明:

##### pagination 参数说明

|  参数名       |  说明        |
| -------      | -------     |
|pageNo   |当前页  |
|pageSize |一页显示条数 |
|totalCount |记录总数  |
|slider | 系统生成的页码 |
|totalPages |总页数  |
|firstPage  |是否是第一页 |
|lastPage |是否是最后一页  |
|hasNextPage  |是否有下一页 |
|nextPage |下一页  |
|hasPrePage |是否有上一页 |
|prePage  |上一页  |
| error     | 错误代码,可在文档尾部查询 |
| success   | 成功与否标志,为 true/false |

##### items 参数说明

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| convertStatus | 0默认状态; 1转换成功; 2转换失败; 3空录像 |


### 请求预览文档

#### URL
`http://api.duobeiyun.com/api/v3/document/preview`

 > 注意: 这里请使用`http`请求而非`https`请求

#### HTTP请求方式
`GET`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| uuid       |  Y          |       课件的唯一id  |


#### 成功返回
显示课件的预览图


### 删除教室

#### URL
`https://api.duobeiyun.com/api/v3/room/delete`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId     |  Y          |   房间的唯一 id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "success": true,
    "room": {
        "roomId": "jzfe066357d119411b921fa452237250d6",
        "title": "ppt小白入门",
        "startTime": "2015-11-24 21:00:00",
        "endTime": "2015-11-24 23:00:00",
        "validEndTime": "2099-01-01 23:59",
        "video": false,
        "hostCode": "jz4adc7",
        "coverImgUrl": null,
        "convertStatus": 3
    }
}
```
失败情况(error 的参考文档的文档尾部):

```json
{
  "error": "app_course_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 成功安排的房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间     |
| endTime   | 房间结束时间      |
| validEndTime  | 回放有效期截止时间 |
| video     |   是否开启视频 |
| hostCode   |  主讲人邀请码|
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |




### 开通微信直播

#### URL
`https://api.duobeiyun.com/api/v3/course/weixin/live/open`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId     |  Y          |   房间的唯一 id   |
| teacherName  | Y  | 老师名字 |
| teacherBrief | Y | 老师简介 |
| description | Y | 课程简介 |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{

	"course": {
		"roomId": "jz0aa3151476334fc38e4c7446c0263a90", "title": "美国往事",
		"startTime": 1436774400000,
		"endTime": 1436781600000,
    	"teacherName": "zhangsan22",
    	"teacherBrief": "brief22",
    	"description": "description22"
	},
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):

```json
{
  "error": "app_course_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 成功安排的房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间，从1970年1月1日到该时刻的毫秒数     |
| endTime   | 房间结束时间，从1970年1月1日到该时刻的毫秒数      |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |
| teacherName  |  老师名字 |
| teacherBrief |  老师简介 |
| description | 课程简介 |




### 关闭微信直播

#### URL
`https://api.duobeiyun.com/api/v3/course/weixin/live/close`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId     |  Y          |   房间的唯一 id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "roomId": "jz0aa3151476334fc38e4c7446c0263a90",
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):

```json
{
  "error": "app_course_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 成功安排的房间唯一 id        |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |



### 更新微信直播

#### URL
`https://api.duobeiyun.com/api/v3/course/weixin/live/update`

#### 支持请求返回数据的格式
`Json`

#### HTTP请求方式
`POST`

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| partner   |  Y          |  合作 id   |
|      sign        |  Y          |      加密校验值   |
|     timestamp   |  Y          |   从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟   |
| roomId     |  Y          |   房间的唯一 id   |
| teacherName  | N  | 老师名字 |
| teacherBrief | N | 老师简介 |
| description | N | 课程简介 |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{

	"course": {
		"roomId": "jz0aa3151476334fc38e4c7446c0263a90", "title": "美国往事",
		"startTime": 1436774400000,
		"endTime": 1436781600000,
    	"teacherName": "zhangsan22",
    	"teacherBrief": "brief22",
    	"description": "description22"
	},
    "success": true
}
```
失败情况(error 的参考文档的文档尾部):

```json
{
  "error": "app_course_error",
  "success": false
}
```

返回参数说明:

|  参数名       |  说明        |
| -------      | -------     |
|roomId  | 成功安排的房间唯一 id        |
| title  | 房间标题           |
| startTime  | 房间开始时间，从1970年1月1日到该时刻的毫秒数     |
| endTime   | 房间结束时间，从1970年1月1日到该时刻的毫秒数      |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |
| teacherName  |  老师名字 |
| teacherBrief |  老师简介 |
| description | 课程简介 |


### 下载教室的聊天记录

#### URL
`http://api.duobeiyun.com/api/v3/room/chatDownload?partner={partner}&timestamp={timestamp}&sign={sign}`

>(需要将{partner}等替换为分配给机构的值)

#### HTTP请求方式
`POST`

#### 支持请求返回数据的格式
`Json`


**POST数据示例**

```json
{
	"roomIds":[
		"jz0dbea6678d584e3d9bc54f7f6fa9c1d3",
		"jz0dbea6678d584e3d9bc54f7f6fa9c1d3",
		....
	]
}
```

#### 提交参数

| 参数 | 是否必须 | 说明 |
| :--: | :--: | :--: |
| partner | Y | 合作 id |
| timestamp | Y | 从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟 |
| sign | Y | 加密校验值 |
| roomIds | Y | 需要更新的roomId列表 |


#### 返回参数

```json
{
	"infos": [
		{
			"roomId": "jz155a4866e7944cfbbff45b71372a1ecb",
			"downloadUrl": "http://dby-chat.duobeiyun.com/jz155a4866e7944cfbbff45b71372a1ecb/chat.json?e=1481334698&token=EGNMWujXSXCYPtR6oT-9v5x8COWETXXlyRA70bvZ:bj99P3oz",
			"expireTime": "2016-12-10 09:51",
			"msg": "success",
			"success": true
		}
	],
	"success": true
}
```

| 参数 | 说明 |
| :--: | :--:|
| roomId | 教室id |
| msg | 返回信息 |
| success | 是否查询成功 |
| expireTime | 下载链接过期时间 |
| downloadUrl | 聊天记录下载地址, 链接有效期为24小时 |




### 直播时，教室相关数据统计

#### URL
`https://api.duobeiyun.com/api/v3/statistics/zhibo/room`

#### HTTP请求方式
`POST`

#### 支持请求返回数据的格式
`Json`

#### 提交参数

|参数名|必填（Y/N）|说明|
|:--:|:--:|:--:|
|partner|Y|合作 id|
|sign|Y|加密校验值|
|timestamp|Y|从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟|
|roomId|Y|房间唯一id|

#### 返回参数

返回参数为json，格式如下

```json
{
    "roomZhiboDetail": {
        "roomId": "jzce02f6a49756491a9f84591a05ffa291", 	//房间唯一id
        "title": "《追风筝的人》英文原著精读25", 	// 房间名
        "statisticEntity": {
            "maxStudents": 48, 	//最大在线人数
            "studentDurationDetail": {
                "total": 211300, 	//学生总的观看时长(秒)
                "max": 5950, 	//最大观看时长
                "median": 3200, 	//中位数
                "min": 75, 	//最小观看时长
                "avg": 3301.56	//平均观看时长
            }, 
            "studentSeries": {
                "end": 1494169271782,      //最后一个学生结束观看的时间戳
                "start": 1494162361248,    //第一个学生开始观看的时间戳
                "step": 300000,       //数据统计的步进值（毫秒）
                "series": [
                    1,      //0-5分钟， 观看的学生人数
                    21, 	//5-10分钟，观看的学生人数
                    42,    //.....
                    48, 
                    46, 
                    47, 
                    45, 
                    46, 
                    44, 
                    44, 
                    43, 
                    43, 
                    46, 
                    45, 
                    41, 
                    25, 
                    13, 
                    11, 
                    10, 
                    10,
                    8, 
                    7, 
                    6, 
                    4, 
                    3
                ]
            }, 
            "mobileStudentNum": 30,		//移动端学生个数
            "pcStudentNum": 34, 		//pc端学生个数
            "totalStudentNum": 64		//总学生个数
        }
    }, 
    "success": true
}
```

### 直播时，用户相关的数据统计（需向商务申请开通权限）

#### URL
`https://api.duobeiyun.com/api/v3/statistics/zhibo/user`

#### HTTP请求方式
`POST`

#### 支持请求返回数据的格式
`Json`

#### 提交参数

|参数名|必填（Y/N）|说明|
|:--:|:--:|:--:|
|partner|Y|合作 id|
|sign|Y|加密校验值|
|timestamp|Y|从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟|
|roomId|Y|房间唯一id|
|uid|Y|用户在自己网站上的唯一标识|

#### 返回参数

返回参数为json，格式如下

```
{
    "userZhiboDetail": {
                           "roomId": "jza3dcbc30782a49858adfed4bbabdb9ee", 	//房间的唯一id
                           "uid": "57728", 	//用户的唯一id
                           "details": [
                               {
                                   "duration": 75000, 	//持续时间（毫秒）
                                   "timestamp": 1493899273372,     //开始观看的时间戳
                                   "platform": "pc"     //使用的平台， pc / mobile
                               }, 
                               {
                                   "duration": 4325000, 
                                   "timestamp": 1493899446607, 
                                   "platform": "pc"
                               }
                           ]
                       }, 
    "success": true
}
```

### 课程回放观看的数据统计（只提供一周内的数据）（需向商务申请开通权限）

#### URL
`https://api.duobeiyun.com/api/v3/statistics/playback/room`

#### HTTP请求方式
`POST`

#### 支持请求返回数据的格式
`Json`

#### 请求参数

|参数名|必填（Y/N）|说明|
|:--:|:--:|:--:|
|partner|Y|合作 id|
|sign|Y|加密校验值|
|timestamp|Y|从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟|
|roomId|Y|房间唯一id|

#### 返回参数

返回参数为json，格式如下

```
{
    "success": true, 
    "roomPlayBackDetails": [       //数组，一周内（按天），该教室回放观看的数据统计。
        {
            "id": "590d4d0627814eac288b1a95",     //该条记录的唯一id
            "timestamp": 1494000000000,      //某天的时间戳，以该天的0点为准
            "roomId": "jz7f94c9e2011742aa8cb4fa6826f70b07",     //房间唯一id
            "watchedTrend": {    //最多有24个Key，范围是0~23，值为：第N小时观看了这个教室的回放xx次
                "12": 1,
                "14": 3, //第14小时， 观看回放3次
                "15": 2, 
                "17": 1, 
                "18": 2, 
                "19": 2, 
                "21": 1
            }, 
            "watchedDurationTrend": {	 // 最多有24个Key，范围是0~23，值为：第N小时观看了这个教室的回放xx分钟。
                "12": 3, 
                "14": 56,  //第14小时， 观看回放共56分钟
                "15": 1, 
                "17": 8, 
                "18": 50, 
                "19": 48, 
                "21": 1
            }, 
            "details": {	// 理论上Key的个数为回放的分钟数。值为：观看第N分钟的人次。(key为，相对于未剪辑视频的分钟数)
                "22": 9, //相对于未剪辑的视频，第22分钟的观看人次为9。
                "23": 9, 
                "24": 6, 
                "25": 5, 
                "26": 6, 
                "27": 6, 
                "28": 5, 
                "29": 5, 
                "30": 4, 
                "31": 4, 
                "32": 5, 
                "33": 4, 
                "34": 4, 
                "35": 4, 
                "36": 3, 
                "37": 3, 
                "38": 3, 
                "39": 4, 
                "40": 4, 
                "41": 4, 
                "42": 4, 
                "43": 3, 
                "44": 3, 
                "45": 3, 
                "46": 3, 
                "47": 4, 
                "48": 3, 
                "49": 2, 
                "50": 2, 
                "51": 2, 
                "52": 3, 
                "53": 3, 
                "54": 2, 
                "55": 2, 
                "56": 2, 
                "57": 2, 
                "58": 1, 
                "59": 2, 
                "60": 2, 
                "61": 1, 
                "62": 1, 
                "63": 2, 
                "64": 2, 
                "65": 1, 
                "66": 1, 
                "67": 1, 
                "68": 2, 
                "69": 2, 
                "70": 1, 
                "71": 1, 
                "72": 1
            }
        }
    ]
}
```

### 某学生观看课程回放的数据统计（只提供一周内的数据）（需向商务申请开通权限）

#### URL
```https://api.duobeiyun.com/api/v3/statistics/playback/user```

#### HTTP请求方式
`POST`

#### 支持请求返回数据的格式
`Json`


#### 提交参数

|参数名|必填（Y/N）|说明|
|:--:|:--:|:--:|
|partner|Y|合作 id|
|sign|Y|加密校验值|
|timestamp|Y|从 1970 到现在的时间差的毫秒数, 该时间戳的有效期是 5 分钟|
|roomId|Y|房间唯一id|
|uid|Y|用户在自己网站上的唯一标识|

#### 返回参数

返回参数为json，格式如下

```json
{
    "userPlayBackDetails": [
        {
            "uid": "962796", //唯一的uid
            "roomId": "jz4ac3909e10b64120ae161ca958b65585", //房间的roomId
            "isMobile": true,  // 是否是手机观看 true / false
            "endTime": 1492507980474,     //用户离开播放页的时间戳
            "startTime": 1492505730022,  //用户进入回放播放页的时间戳
            "watchedTime": 2678432,  //用户观看的总时间（毫秒）
            "watchedUniqueTime": 2678432, // 用户观看的唯一时间（毫秒）。(第5-10分钟，观看了10次（拖动进度条的方式）， 这里只记录5分钟的毫秒数)
            "jumpRecords": [  //观看记录
                {
                    "from": 1458298774521, //直播课播放点对应的时间戳。
                    "to": 1458298791768,  //直播课播放点对应的时间戳。
                    "startTime": 1492505737730, //用户开始观看的时间戳
                    "endTime": 1492505752430 //本次观看结束的时间戳
                }, 
                {   //当用户有一次拖动动作，比如拖动快进等， 就会添加一条记录。
                    "from": 1458299040594, 
                    "to": 1458299046959, 
                    "startTime": 1492505753112, 
                    "endTime": 1492505758475
                }, 
                {
                    "from": 1458299162555, 
                    "to": 1458301817375, 
                    "startTime": 1492505759255, 
                    "endTime": 0  //用户已离开。可以通过to - from确认本地观看的时长。
                }
            ]
        }, 
        {
            "uid": "962796", 
            "roomId": "jz4ac3909e10b64120ae161ca958b65585", 
            "isMobile": true, 
            "endTime": 1492521173883, 
            "startTime": 1492516910820, 
            "watchedTime": 6232421, 
            "watchedUniqueTime": 6125264, 
            "jumpRecords": [
                {
                    "from": 1458302492780, 
                    "to": 1458303608551, 
                    "startTime": 1492516917305, 
                    "endTime": 1492517671104
                }, 
                {
                    "from": 1458303608499, 
                    "to": 1458304344343, 
                    "startTime": 1492517691723, 
                    "endTime": 1492518182329
                }, 
                {
                    "from": 1458304634664, 
                    "to": 1458304635596, 
                    "startTime": 1492518185055, 
                    "endTime": 1492518185715
                }, 
                {
                    "from": 1458304805713, 
                    "to": 1458304806526, 
                    "startTime": 1492518182986, 
                    "endTime": 1492518183561
                }, 
                {
                    "from": 1458304848736, 
                    "to": 1458304848827, 
                    "startTime": 1492518184287, 
                    "endTime": 1492518184393
                }, 
                {
                    "from": 1458305019618, 
                    "to": 1458305020682, 
                    "startTime": 1492518186507, 
                    "endTime": 1492518187253
                }, 
                {
                    "from": 1458305020641, 
                    "to": 1458305021774, 
                    "startTime": 1492518187467, 
                    "endTime": 1492518188258
                }, 
                {
                    "from": 1458305148665, 
                    "to": 1458305149946, 
                    "startTime": 1492518192395, 
                    "endTime": 1492518193292
                }, 
                {
                    "from": 1458305233641, 
                    "to": 1458307720329, 
                    "startTime": 1492518194080, 
                    "endTime": 1492519851898
                }, 
                {
                    "from": 1458305233664, 
                    "to": 1458305235374, 
                    "startTime": 1492518188981, 
                    "endTime": 1492518190154
                }, 
                {
                    "from": 1458305276712, 
                    "to": 1458305277846, 
                    "startTime": 1492518190858, 
                    "endTime": 1492518191648
                }, 
                {
                    "from": 1458307457667, 
                    "to": 1458307463269, 
                    "startTime": 1492519852515, 
                    "endTime": 1492519856293
                }, 
                {
                    "from": 1458307621711, 
                    "to": 1458309502069, 
                    "startTime": 1492519856950, 
                    "endTime": 0
                }
            ]
        }
    ]
}
```

注：错误信息json格式如下

```
{
    "error": "partner is not exists", 
    "success": false
}
```

```
{
    "error": "room is not exists", 
    "success": false
}
```

注：没有数据的情况

```json
{
    "roomPlayBackDetails": null, 
    "success": true
}
```

---






## 错误码解释表

| 序号      | error       | 解释          |
| --------  | --------- | ----------- |
|1  |other_error  |其他异常,空指针等 |
|2  | app_error   | app 不存在   |
|3 |  app_sign_error  | sign 值校验不对 |
|4  |app_course_error | 讲座不存在      |
|5  | app_room_full_error |   直播房间满|
|6  | app_parameter_error | 参数不符合要求,非空字段 为空等 |
|7  |app_afford_error   |资金不够创建邀请码 |
|8  |app_course_out_of_capacity_error |超过讲座的人数限制 |
|9  |app_course_length_error |讲座时长不对 |
|10 |app_course_type_error  |课程类型不对 |
|11 |repeat_arrange_to_course_error |重复安排讲义到讲座 |
|12 |app_slides_error |课件不存在 |
|13 |app_userRole_error|进教室角色错误|
|14|app_timestamp_out_date_error|时间戳过期|
|15|course_endtime_out_of_date_error|课程结束时间小于当前时间|
| 16 | chat_file_not_found | 聊天记录不存在 |
| 17 | course_convert_error | 课程还未转换成功，可能课程正在直播或正在转换 |
