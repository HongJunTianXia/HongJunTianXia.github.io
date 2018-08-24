# 多贝云JS SDK使用文档

------
通过多贝云JS SDK，你可以在虚拟教室外进行网页间的消息通信。

## 初始化多贝云JS SDK

#### 引入文件
在页面的最上方引入多贝云 JS SDK 的最新版 js 文件。

```html
<script type="text/javascript" src="http://static2.duobeiyun.com/duobeiyun-js-sdk/duobeiyun-js-sdk-0.1.1-min.js"></script>
```

#### 初始化
通过调用`Duobeiyun.init()` 来初始化。

该函数需要传入的参数：

``` json
{
	"uid": "xxx",
	"roomId": "xxx",
	"debug": "true/false"
}
```

其中：

> uid 为该页面嵌入教室对应用户的 uid，必须和页面进教室 api 链接内的 uid 相同
>
> roomId 为该页面嵌入教室的 roomId，必须和页面进教室 api 链接内的 roomId 相同
>
> debug 为可选参数，设置为 true 时将打印所有运行时 log，反之不打印。


## 使用多贝云 JS SDK 发送/接收消息
多贝云 JS SDK 使用类似于 jQuery 的事件处理 API 来定义事件处理和触发事件，屏蔽了内部的消息发送细节。

#### 发送消息（触发事件）
使用`Duobeiyun.trigger()` 来发送消息。

该接口需要传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      event     |  Y          |       事件名，用来标记消息的类型  |
|     option    |  Y          |   事件对应的处理参数，将在事件被处理时，传递给回调函数|

#### 接收消息（响应事件）
使用`Duobeiyun.on()` 来接收消息。
该接口需要传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      event     |  Y          |       事件名，用来标记消息的类型  |
|     callback    |  Y          |   事件对应的处理函数，将在事件被将在事件触发时被调用|

##### 注意

> 所有的`Duobeiyun.on` 都需要在`Duobeiyun.ready()` 内调用。


## 完整流程

假设页面 A 和页面 B 分别为同一教室内的老师和学生进入教室的页面。

页面 A：

```javascript

//初始化
Duobeiyun.init({
	"uid": "user_teacher_uid_xxx"
	"roomId": "roomIdxxxx",
	"debug": true
});

Duobeiyun.ready(function () {
	//注册事件 'student_call_teacher' 的回调
	Duobeiyun.on("student_call_teacher", function (option) {
		alert("hello teacher, I'm " + option.name);
	});
});

$(".btn_call_student").click(function () {
	//触发事件 'teacher_call_student'
	Duobeiyun.trigger("teacher_call_student", {
		"name": "teacher_A"
	});
});

```

页面 B：

```javascript

//初始化
Duobeiyun.init({
	"uid": "user_student_uid_xxx"
	"roomId": "roomIdxxxx",
	"debug": true
});

Duobeiyun.ready(function () {
	//注册事件 'teacher_call_student' 的回调
	Duobeiyun.on("teacher_call_student", function (option) {
		alert("hello student, I'm " + option.name);
	});
});

$(".btn_call_teacher").click(function () {
	//触发事件 `student_call_teacher`, SDK 将把消息发往页面 A，并调用页面 A 上事件 `student_call_teacher` 对应的回调函数，并传入参数 `{"name": "student_B"}`
	Duobeiyun.trigger("student_call_teacher", {
		"name": "student_B"
	});
});

```

## 使用多贝云 JS SDK 触发预设调用

#### 预设调用列表
| 调用名     | 说明      |
| -----     | ------    |
| DBY_CALL_HELP | 用户呼叫帮助 |


#### 触发调用
使用`Duobeiyun.trigger()` 来触发调用。

该接口需要传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      event     |  Y          |       调用名，用来标记调用  |
|     option    |  Y          |   传入调用对应的辅助信息，对象必须存在，但内容可为空对象|

## 完整流程
假设页面 A 和页面 B 分别为同一教室内的老师和学生进入教室的页面。

页面A，老师端触发HELP调用：

```javascript

//初始化
Duobeiyun.init({
	"uid": "user_teacher_uid_xxx"
	"roomId": "roomIdxxxx",
	"debug": true
});

$(".btn_call_help").click(function () {
	//触发调用 'DBY_TEACHER_CALL_HELP'
	Duobeiyun.trigger("DBY_CALL_HELP", {
		"name": "teacher_A",
		"type": "default/student_no_show/student_bad_network/..."
	});
});

```

页面B，学生端触发HELP调用：

```javascript

//初始化
Duobeiyun.init({
	"uid": "user_student_uid_xxx"
	"roomId": "roomIdxxxx",
	"debug": true
})

$(".btn_call_help").click(function () {
	//触发调用 'DBY_STUDENT_CALL_HELP'
	Duobeiyun.trigger("DBY_CALL_HELP", {
		"name": "student_A",
		"type": "default/student_no_show/student_bad_network/..."
	});
});

```

页面C，管理员触发HELP调用：

```javascript

//初始化
Duobeiyun.init({
	"uid": "user_admin_uid_xxx"
	"roomId": "roomIdxxxx",
	"debug": true
})

$(".btn_call_help").click(function () {
	//触发调用 'DBY_ADMIN_CALL_HELP'
	Duobeiyun.trigger("DBY_CALL_HELP", {
		"name": "admin_A",
		"type": "student_no_show/student_bad_network/..."
	});
});

```
备注: 
参数对象非必填，可以为空， 例如:
    Duobeiyun.trigger("DBY_CALL_HELP", {});

## 使用多贝云 JS SDK 监听PPT事件
#### 预设监听事件列表
| 调用名     | 说明      |
| -----     | ------    |
| DBY_CALL_EVENT | ppt打开及翻页事件监听 |


#### 监听调用


该接口有5个参数:

| 参数名     | 值示例   | 说明      |
| -----     | -----      | ------    |
|      time     |   1495521014000        |       unix时间戳  |
|     slidesName    |    L2_U3_L1      |   课件名|
|event| 1| 1 ppt打开事件 、 2 ppt翻页事件
|targetPage|3|当前ppt页数 ( **如果该课件为动态课件，则值为0** )
|slidesId|952e8cd75e2640e2ba2177188f594047|当前课件Id

当 event 值为1（ppt打开事件）:

| targetPage值 | 说明 |
|----| ---- |
| 0 | 打开的课件为动态课件 |
| >=1| 打开的课件为静态课件|

当 event 值为2（ppt翻页事件）:

| targetPage值 | 说明 |
|----| ---- |
| >=1| 当前页数(打开的课件为静态课件, **动态课件不会有翻页事件**)|

## 完整流程
老师端:

``` javascript
//初始化
Duobeiyun.init({
    "uid": "user_teacher_uid_xxx"
    "roomId": "roomIdxxxx",
    "debug": true
});

Duobeiyun.ready(function () {
    //注册事件 'DBY_CALL_EVENT' 的回调
    Duobeiyun.on("DBY_CALL_EVENT", function (msg) {
        console.log(msg);
    });
});
```
学生端:

``` javascript
//初始化
Duobeiyun.init({
    "uid": "user_teacher_uid_xxx"
    "roomId": "roomIdxxxx",
    "debug": true
});

Duobeiyun.ready(function () {
    //注册事件 'DBY_CALL_EVENT' 的回调
    Duobeiyun.on("DBY_CALL_EVENT", function (msg) {
        console.log(msg);
    });
});
```

## 使用多贝云 JS SDK 监听及获取老师摄像头状态

#### 预设监听事件列表
| 调用名     | 说明      | 返回值|
| -----     | ------    | ---|
| DBY_CAM_STATUS | 老师摄像头状态(是否为开启状态) | true/false|



#### 触发调用


使用`Duobeiyun.trigger()` 来触发调用。

|方法名|方法说明|参数|
|---|---|---|
|DBY_GET_CAM_STATUS|获取摄像头当前状态|无|

## 完整流程
老师端:

``` javascript
//初始化
Duobeiyun.init({
    "uid": "user_teacher_uid_xxx"
    "roomId": "roomIdxxxx",
    "debug": true
});

Duobeiyun.ready(function () {
    //注册事件 'DBY_CAM_STATUS' 的回调
    Duobeiyun.on("DBY_CAM_STATUS", function (msg) {
        console.log(msg);
    });
});
$(".btn").click(function () {
	//触发调用 'DBY_GET_CAM_STATUS'
	Duobeiyun.trigger("DBY_GET_CAM_STATUS", {});
});


```


