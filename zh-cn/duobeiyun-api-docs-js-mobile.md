# 多贝云JS SDK for mobile使用文档

------
通过多贝云JS SDK for mobile, 你可以和教室进行数据交互。
(以下内容中，JS SDK for mobile 将简称 SDK)

## 初始化 SDK

#### 引入文件
在页面的最上方引入 SDK 的最新版 js 文件。

```html
<script type="text/javascript" src="http://static2.duobeiyun.com/duobeiyun-js-sdk-mobile/duobeiyun-mobile-js-sdk-0.0.8-min.js"></script>
```


## APIs

`DBYMobile.onReady( func )` :<br>
用来注册 SDK 可用之后的回调，参数 `func` 的类型是 `function`<br>
当 SDK 可用之后，SDK 会调用 `func()`<br>

<br>

`DBYMobile.onNewComment( func )` :<br>
用来注册教室内有新讨论的回调，参数 `func` 的类型是 `function` <br>
当有新讨论时，SDK 会调用 `func(msg)` <br>
其中 msg 为 comment 的封装，格式为：

| 参数名     |  说明 |
| -----     | -----  |
| commentType  | 消息的类型，1为用户消息，2为系统消息 |
| userName     | 用户的昵称  |
| userRole     | 用户的角色，1为老师，2为学生，3为管理员，4为助教 |
| comment      | 讨论的内容 |
| ts           | 消息发送时的时间戳|

<br>

`DBYMobile.onUpdateOnlineNum( func )` :<br>
用来注册教室内在线人数更新时的回调，参数 `func` 的类型是 `function` <br>当教室内在线人数更新时，会调用 `func(msg)` <br>
其中 msg 为 playProgress 的封装，格式为：

| 参数名     |  说明 |
| -----     | -----  |
| currentNum  | 教室内当前在线人数 |
| ts           | 消息发送时的时间戳|

<br>

`DBYMobile.sendComment( comment )` :<br>
用来通过 SDK 向教室内发送评论内容，参数 `comment` 的类型是 `string`<br>
该 api 有返回值，true 表示发送成功，false 表示发送失败

<br>

`DBYMobile.onCoursePlayProgressUpdate( func )` :<br>
用来注册课程播放进度更新时的回调，参数 `func` 的类型是 `function` <br>当课程播放进度更新时，会调用 `func(msg)` <br>
其中 msg 为 JSON 格式的字符串，格式为：

| 参数名     |  说明 |
| -----     | -----  |
| currentTime  | 课程当前播放的时间点 |
| totalTime    | 课程的总时长 |

<br>

`DBYMobile.onCoursePlayProgressFirstUpdate( func )` :<br>
用来注册课程播放进度更新时的回调，参数 `func` 的类型是 `function` <br>当课程播放进度「第一次」更新时，会调用 `func(msg)` <br>
其中 msg 为 JSON 格式的字符串，格式为：

| 参数名     |  说明 |
| -----     | -----  |
| currentTime  | 课程当前播放的时间点 |
| totalTime    | 课程的总时长 |

<br>

`DBYMobile.getCoursePlayProgress( func )` :<br>
用来主动获取课程播放进度的接口<br>
返回值为 JSON 格式的字符串，格式为：

| 参数名     |  说明 |
| -----     | -----  |
| currentTime  | 课程当前播放的时间点 |
| totalTime    | 课程的总时长 |

<br>

`DBYMobile.debug` :<br>
用来开启或关闭 SDK 的 debug 功能。SDK 的 debug 开启时，将 alert 错误信息，请确保在生产环境下关闭 debug。<br>
赋值为 true 时表示打开，赋值为 false 时表示关闭。

