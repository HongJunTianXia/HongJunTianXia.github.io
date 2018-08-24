# 多贝云JAVA SDK使用文档

------
通过多贝云JAVA SDK，您可以创建、修改房间，上传、绑定文档，并通过访问生成的特定连接直接从web页面进入房间，实现对虚拟房间的自动化控制。

## 创建多贝云SDK实例

#### 引入文件
1，将『Download』中Java SDK的jar包下载，并导入到项目中。
2，使用构建工具导入jar包。

gradle方式
```java
compile 'com.duobeiyun.sdk:duobeiyun_sdk:2.3.5'
```

maven方式
```java
<dependency>
  <groupId>com.duobeiyun.sdk</groupId>
  <artifactId>duobeiyun_sdk</artifactId>
  <version>2.3.5</version>
  <type>pom</type>
</dependency>
```

#### 创建SDK实例

> 注意: 如果您的服务器时区不是`Asia/Shanghai`
> 请在调用SDK前将服务器时区设定为东八区，避免添加课程时时间出错

首先添加SDK命名空间引用

```java
import com.duobeiyun.DuobeiYunClient;
```

引入后 使用下列代码生成一个实例`client`

```java
DuobeiYunClient client = new DuobeiYunClient();
```

> 注意: 下述文档中出现的`client`均为此实例

## 使用多贝云JAVA SDK控制房间

### 创建房间

#### 调用代码

```java
String result = client.createRoom(
	"测试教室01",	// title
	new Date(),		// startTime
	2,				// duration
	true,			// video
	DuobeiYunClient.COURSE_TYPE_1vN	// roomType
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      title       |  Y          |       房间标题   |
|     video     |  Y          |   是否开启视频。开启视频填`true`, 不开启视频填`false`   |
|      startTime     |  Y          |      房间开始时间,格式 为`Date`类型   |
|     duration     |  Y          |   房间时长,单位小时, 可选值为 1 , 2, 3,4,5   |
|      roomType     |  Y          |       房间类型。`DuobeiYunClient.COURSE_TYPE_1v1`表示 1v1课程,`DuobeiYunClient.COURSE_TYPE_1vN`表示 1vn 课程, `DuobeiYunClient.COURSE_TYPE_SMALL`表示小班flash课程(`需先申请权限`)，`DuobeiYunClient.COURSE_TYPE_SMALL_CLIENT`表示小班客户端课程(`需先申请权限`)|

#### 返回参数

返回参数为`json`字符串，格式示例如下

成功情况:

```json
{
  "success": true,
  "room": {
    "roomId": "jzf243856e3949455894c5ce48bfee6163",
    "title": "测试多贝云01",
    "startTime": "2014-12-04 15:51:00",
    "endTime": "2014-12-04 18:51:00",
    "validEndTime": "2099-01-01 23:59",
    "video": true,
    "hostCode": "jzk38iygsd"
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


### 创建房间(方式2)

#### 调用代码

```java
String result = client.createRoomByMinute(
	"测试教室01",	// title
	new Date(),		// startTime
	45,				// length
	true,			// video
	DuobeiYunClient.COURSE_TYPE_1vN	// roomType
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      title       |  Y          |       房间标题   |
|     video     |  Y          |   是否开启视频。开启视频填`true`, 不开启视频填`false`   |
|      startTime     |  Y          |      房间开始时间,格式 为`Date`类型   |
|     length     |  Y          |   房间时长,单位分钟,  不能小于30分钟 不能大于300分钟   |
|      roomType     |  Y          |房间类型 `DuobeiYunClient.COURSE_TYPE_1v1`表示 1v1课程,`DuobeiYunClient.COURSE_TYPE_1vN`表示 1vn 课程, `DuobeiYunClient.COURSE_TYPE_SMALL`表示小班flash课程(`需先申请权限`)，`DuobeiYunClient.COURSE_TYPE_SMALL_CLIENT`表示小班客户端课程(`需先申请权限`)|

#### 返回参数

返回参数为`json`字符串，格式示例如下

成功情况:

```json
{
  "success": true,
  "room": {
    "roomId": "jzf243856e3949455894c5ce48bfee6163",
    "title": "测试多贝云01",
    "startTime": "2014-12-04 15:51:00",
    "endTime": "2014-12-04 18:51:00",
    "validEndTime": "2099-01-01 23:59",
    "video": true,
    "hostCode": "jzk38iygsd"
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


### 更新房间标题

#### 调用代码

```java
String result = client.updateRoomTitle(
	"jzf243856e3949455894c5ce48bfee6163", // roomId
	"测试新多贝云01"                       // title
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| roomId     |  Y          | 房间Id   |
|      title       |  Y          |       新的房间标题   |


#### 返回参数

返回参数为`json`字符串，格式示例如下

成功情况:

```json
{
  "success": true,
  "room": {
    "roomId": "jzf243856e3949455894c5ce48bfee6163",
    "title": "测试新多贝云01",
    "startTime": "2014-12-04 15:51:00",
    "endTime": "2014-12-04 18:51:00",
    "validEndTime": "2099-01-01 23:59",
    "video": true,
    "hostCode": "jzk38iygsd"
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

#### 调用代码

```java
String result = client.updateRoomSchedule(
	"jzf243856e3949455894c5ce48bfee6163", // roomId
	new Date(),					         // startTime
	3                                     // duration
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| roomId       |  Y          | 房间的 Id   |
|      startTime     |  Y          |      房间开始时间,格式为PHP时间类型   |
|     duration     |  Y          |   房间时长,单位小时, 可选值为 1 , 2, 3,4,5   |

> 注意
> 如果房间的开始时间已经超过了当前时间，则无法修改房间开始时间

#### 返回参数

返回参数为`json`字符串，格式示例如下

成功情况:

```json
{
  "room": {
    "roomId": "jzada6bf11b6f34ba0b181f60995fdd043", "title": "测试修改开始时间 api",
    "startTime": "2013-12-17 15:11:00",
    "endTime": "2013-12-17 17:11:00", "validEndTime": "2013-12-24 23:59:59",
    "video": false,
    "hostCode": "jz3db11c0694ba4e3b84512298339590ed"
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


### 修改房间开始时间和时长（2）

#### 调用代码

```java
String result = client.updateRoomScheduleByMinute(
	"jzf243856e3949455894c5ce48bfee6163", // roomId
	new Date(),					         // startTime
	55                                     // length
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| roomId       |  Y          | 房间的 Id   |
|      startTime     |  Y          |      房间开始时间,格式为PHP时间类型   |
|     length     |  Y          |   房间时长,单位分钟, 可选值为 30-300   |

> 注意
> 如果房间的开始时间已经超过了当前时间，则无法修改房间开始时间


### 上传文档

#### 调用代码
```java
String result = client.uploadDocument(
	"file name to show",			// filename
	new File("/path/to/your/file")	// file object
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| filename	| Y			| 显示在教室中的文件名 |
| slidesFile       |  Y          |       上传的文档对象  |

> 文件支持的格式有 word、ppt、 pdf、jpg、png,大小不超过 20M

#### 返回参数

返回参数为`json`字符串，格式示例如下

成功情况:

```json
{
  "uuid": "41d71a76-0091-4a2b-b8d1-2670a5e66043",
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

#### 调用代码
```java
String result = client.attatchDocument(
	"jzf243856e3949455894c5ce48bfee6163",   // roomId
	"41d71a76-0091-4a2b-b8d1-2670a5e660b3"  // documentId
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| documentId       |  Y          |       文档的 uuid   |
| roomId     |  Y          |   房间的唯一 id   |

#### 返回参数

返回参数为`json`字符串，格式示例如下

成功情况:

```json
{
  "documentId": "ccd0ec55-a3f6-42da-a02f-e785eb56dd8f",
  "roomId":"jz1659df5674e947dcbb948723e983f7b0",
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

上传的文档在上传后会自动由转换为房间支持的格式，
转换好的文档才可以在房间内使用。
借由该方法可以查询文档的转换状态

#### 调用代码
```java
String result = client.getDocumentStatus(
	"41d71a76-0091-4a2b-b8d1-2670a5e660b3"  // documentId
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| documentId       |  Y          |       文档id   |

#### 返回参数

返回参数为`json`字符串，格式示例如下

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
|status  | 转换状态，共三种converted(已转换) converting(转换中) failed(转换失败)        |
| title  | 房间标题           |
| error     | 错误代码,可在文档尾部查询|
| success   | 成功与否标志,为 true/false |

### 生成请求进入房间URL

#### 调用代码
```java
String result = client.generateRoomEnterUrl(
	java.util.UUID.randomUUID().toString() // uid
	"testStudent",                       	// nickname
	"jzf243856e3949455894c5ce48bfee6163", // roomId
	DuobeiYunClient.ROLE_STUDENT,         // userRole
    DuobeiYunClient.DEVICE_TYPE_PC        //deviceType
);
System.out.println(result);
```

如果您的系统没有为听众或主讲人分配唯一id，您可以使用`java.util.UUID.randomUUID().toString()`生成一个唯一id

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| uid       |  Y          |       用户在自己网站上的唯一标识,一定 不能重复   |
| roomId     |  Y          |   房间 id   |
| nickname     |  Y        |      用户在房间内的昵称，不能为空，昵称首尾不能包含空格   |
| userRole     |  Y          |   `DuobeiYunClient.ROLE_TEACHER`表示以 老师身份进入教室，`DuobeiYunClient.ROLE_STUDENT`表示以学生身份进入教室。`DuobeiYunClient.ROLE_MONITOR`表示以隐身监课者身份进入教室。`DuobeiYunClient.ROLE_ASSISTANT`表示以助教身份进入教室。当请求不加该参数时默认以听众身份进入    |
| deviceType | Y | 可选值`DuobeiYunClient.DEVICE_TYPE_PC`, `DuobeiYunClient.DEVICE_TYPE_MOBILE`.  当值为null时, 会根据userAgent判断用户访问设备, 并返回页面; 值为`DuobeiYunClient.DEVICE_TYPE_PC`时返回PC客户端; 值为`DuobeiYunClient.DEVICE_TYPE_MOBILE`时返回手机端教室 |

#### 返回参数

返回结果为标准的HTTP URL字符串
```
http://api.duobeiyun.com/api/v3/room/enter?uid=80d18656-2b14-4663-a056-b3b7bcd29663&nickname=testTeacher&roomId=jzf243856e3949455894c5ce48bfee6163&userRole=1&partner=20141201162325&timestamp=1417684739000&sign=7a54dc179678f388f4ca248dc20afa38
```
> 可以将此链接(父页面支持https，请使用https)嵌入`iframe`，访问该链接即可进入房间

> 最新版本的chrome，64版本，使用iframe加载包含flash的页面时会出现无法获取音视频的问题，解决方法如下:
   在加载iframe时增加allow属性，属性值为geolocation; microphone; camera，示例如下
   ```<iframe src="https://api.duobeiyun.com" allow="geolocation; microphone; camera">```

成功进入房间情况:

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

房间过期情况:

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
<script>
var _gaq = _gaq || [];
_gaq.push(['_setAccount', 'UA-46636440-1']);
_gaq.push(['_setDomainName', 'jiangzuotong.com']);
_gaq.push(['_trackPageview']);
(function() {
  var ga = document.createElement('script'); ga.type =
  'text/javascript'; ga.async = true;
  ga.src = ('https:' == document.location.protocol ? 'https://ssl' :
  'http://www') + '.google-analytics.com/ga.js';
  var s = document.getElementsByTagName('script')[0];
  s.parentNode.insertBefore(ga, s);
  })();
  </script>
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

### 生成获取authinfo信息的URL（将返回值传给App的sdk使用）

#### 调用代码
```java
String result = client.generateAuthInfoUrl(
	java.util.UUID.randomUUID().toString() // uid
	"testStudent",                       	// nickname
	"jzf243856e3949455894c5ce48bfee6163", // roomId
	DuobeiYunClient.ROLE_STUDENT,         // userRole
);
System.out.println(result);
```

如果您的系统没有为听众或主讲人分配唯一id，您可以使用java.util.UUID.randomUUID().toString()生成一个唯一id

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| uid       |  Y          |       用户在自己网站上的唯一标识,一定 不能重复   |
| roomId     |  Y          |   房间 id   |
| nickname     |  Y        |      用户在房间内的昵称，不能为空，昵称首尾不能包含空格   |
| userRole     |  Y          |   `DuobeiYunClient.ROLE_TEACHER`表示以 老师身份进入教室，`DuobeiYunClient.ROLE_STUDENT`表示以学生身份进入教室。`DuobeiYunClient.ROLE_MONITOR`表示以隐身监课者身份进入教室。`DuobeiYunClient.ROLE_ASSISTANT`表示以助教身份进入教室。当请求不加该参数时默认以听众身份进入    |

#### 返回参数

返回结果为标准的HTTP URL字符串
```plain
http://api.duobeiyun.com/api/v4/room/authinfo?sign=ee230181d419210c78fea5fc1d2df943&timestamp=1499079025015&uid=kldsjfldsjf&nickname=zhangsansan&roomId=jz0e8b7faef3649a4db9b13d1648e90451b6&partner=2015123011165546625&userRole=2
```

将此链接交由app的SDK调用即可。

### 获取房间文档列表

获取房间文档列表

#### 调用代码
```java
String result = client.listDocuments(
	"jzf243856e3949455894c5ce48bfee6163",   // roomId
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| roomId       |  Y          |       房间id   |


#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
  "documents": [
    {
      "originalDocName": "看日记学git.pdf",
      "createTime": 1415938350000,
      "status": "converted",
      "size": 897974,
      "documentId": "0a2ce3e0-bf19-4ae0-ad64-67f823ef957a",
      "slideCount": 112,
      "documentType": "application/octet-stream"
    },
    {
      "originalDocName": "多贝讲座通产品介绍.pdf",
      "createTime": 1420789511000,
      "status": "converting",
      "size": 439593,
      "documentId": "f7565e95-ceeb-49cb-8ab2-83af9f80788c",
      "slideCount": 0,
      "documentType": "application/octet-stream"
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


#### 调用代码
```java
    String result = client.removeDocument(roomId, documentId);
    System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
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



### 查看文档列表、按标题搜索文档

#### 调用代码
```java
	String result = client.listAllDocument(title, pageNo);
	System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      title       |  N          |       根据标题模糊查询, 可以为null   |
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


### 查看文档详情

#### 调用代码

```java
String result = client.detailDocumet(
	"f243856e3949455894c5ce48bfee6163",	// title
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      documentId       |  Y          |       文档id   |

#### 返回参数

返回参数为`json`字符串，格式示例如下


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

#### 调用代码

```java
String result = client.deleteDocumet(
	"f243856e3949455894c5ce48bfee6163",	// title
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      documentId       |  Y          |       文档id   |

#### 返回参数

返回参数为`json`字符串，格式示例如下


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

#### 调用代码
```java
	String result = client.merchantDetail();
	System.out.println(result);
```


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

#### 调用代码
```java
	String result = client.roomDetail(roomId);
	System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| roomId       |  Y          | 房间的 Id   |

#### 返回参数

返回参数为`json`，格式示例如下

成功情况:

```json
{
    "course": {
        "roomId": "jzce5129a599044fd3a1b10cc46e329fa3",
        "title": "考研英语语法",
        "startTime": "2015-05-26 18:00:00",
        "endTime": "2015-05-26 20:00:00",
        "validEndTime": "2099-01-01 23:59",
        "video": false,
        "convertStatus": 1,
        "hostCode": "jzhkg176c3",
        "weixinLiving": true,
        "teacherName": "老师",
        "teacherBrief": "老师简介",
        "description": "简介"
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

#### 调用代码
```java
	String result = client.listRoom(title, pageNo, startTime, endTime);
	System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      title       |  N          |       根据标题模糊查询，可以为null   |   |
|      startTime     |  N   |      开始时间早于这个时间的房间,格式 2013-10-27 11:11，可以为null   |
| endTime     |  N  |      开始时间晚于这个时间的房间,格式 2013-11-27 11:11，可以为null   |
| pageNo | N | 第几页， 可以为null|

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



### 生成预览文档URL


#### 调用代码
```java
String result = client.generateDocPreviewUrl(
	"8275dd63-985a-486d-9002-cbac618b14a0"         // uuid
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
| uuid       |  Y          |       课件的唯一id   |

#### 返回参数

返回结果为标准的HTTP URL字符串
```plain
http://api.duobeiyun.com/api/v3/document/preview?partner=20150817193014&sign=699b686d9c4f2c3bb3744e1d559845ca&timestamp=1443519367533&uuid=8275dd63-985a-486d-9002-cbac618b14a0
```
可以将此链接嵌入`iframe`，访问该链接即可预览文档



### 删除房间

#### 调用代码
```java
String result = client.deleteRoom(
	"jzfe066357d11911b921fa452237250d6" //roomId
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
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

#### 调用代码
```java
String result = client.openWeixinLive(
	"jz25c115a17a914fd39bcdeb904e229684", //roomId
	"老师", //teacherName
	"老师简介", //teacherBrief
	"简介" //description
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
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
		"roomId": "jz0aa3151476334fc38e4c7446c0263a90",
		"title": "美国往事",
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

#### 调用代码
```java
String result = client.closeWeixinLive(
	"jz25c115a17a914fd39bcdeb904e229684" //roomId
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
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

#### 调用代码
```java
String result = client.updateWeixinLive(
	"jz25c115a17a914fd39bcdeb904e229684", //roomId
	"老师", //teacherName
	"老师简介", //teacherBrief
	"简介" //description
);
System.out.println(result);
```

#### 提交参数

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
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
		"roomId": "jz0aa3151476334fc38e4c7446c0263a90",
		"title": "美国往事",
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
