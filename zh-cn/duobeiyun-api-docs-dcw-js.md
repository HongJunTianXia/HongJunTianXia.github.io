# 多贝云动态课件JS SDK使用文档

------
通过多贝云动态课件JS SDK，你可以在虚拟教室外进行动态课件的开发。

## 初始化

### 引入文件
在页面的最上方引入多贝云 JS SDK 的 js 文件。

src="http://static2.duobeiyun.com/dby-dcw-js-sdk/dby-dcw-js-sdk-[版本号].js
```html
<script type="text/javascript" src="http://static2.duobeiyun.com/dby-dcw-js-sdk/dby-dcw-js-sdk-2.3.10.js"></script>
```

### 初始化一个sdk实例
通过调用 `var sdk = new DCWSDK(option);`  来初始化一个 sdk 实例。

> 注意: 下述文档中出现的 `sdk` 均为此实例

构造函数 `DCWSDK` 可以传入一个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       option     |  N         |    初始化参数     |

`option` 的可选属性为：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       debug     |  N         |       是否设置为debug模式，为 `true` 时将在控制台中打印运行过程中的所有日志信息，为 `false` 时将不打印  |

示例：
```
var sdk = new DCWSDK({
    debug: true
});
```


## 接口

- ready 接口
    - [`ready`](#ready)
    - [`webPageReady`](#webpageready)

- 数据操作接口 
    - [`broadcastEvent`](#broadcastevent)，[`onBroadcastEvent`](#onbroadcastevent)，[`removeBroadcastEvent`](#removebroadcastevent)
    - [`broadcastSetOperation`](#broadcastsetoperation)，[`onBroadcastSetOperation`](#onbroadcastsetoperation)，[`removeBroadcastSetOperation`](#removebroadcastsetoperation) 
    - [`retrieveStatus`](#retrievestatus)，[`onRetrieveStatus`](#onretrievestatus)，[`removeRetrieveStatus`](#removeretrievestatus)
    - [`onPlaybackSeek`](#onplaybackseek)，[`removePlaybackSeek`](#remveplaybackseek)
- 数据同步接口
    - [`registerObject`](#registerobject)，[`updateObject`](#updateobject)，[`syncObject`](#syncobject)

- 封装好的组件接口
    - [`registerDrawable`](#registerdrawable)，[`registerClickable`](#registerclickable)，[`registerDraggable`](#registerdraggable)，[`registerLinkable`](#registerlinkable)

- 计数器接口
    - [`onOpenCounter`](#onopencounter)，[`removeOpenCounter`](#removeopencounter)，[`openCounter`](#opencounter)，[`openCounters`](#opencounters)
    - [`onCloseCounter`](#oncloseCounter)，[`removeCloseCounter`](#removecloseCounter)，[`closeCounter`](#closecounter)，[`closeCounters`](#closecounters)
    - [`onBroadcastCounter`](#onbroadcastcounter)，[`removeBroadcastCounter`](#removebroadcastcounter)，[`onBroadcastCounters`](#onbroadcastcounters)， [`removeBroadcastCounters`](#removebroadcastcounters)
    - [`updateCounter`](#updatecounter)
    - [`onRetrieveCounter`](#onretrievecounter)，[`removeRetrieveCounter`](#removeretrievecounter)，[`retrieveCounter`](#retrievecounter)   
- 调试接口（debug模式下才有效）
    - [`onReceiveData`](#onreceivedata)， [`removeReceiveData`](#removereceivedata) [`onSendData`](#onsenddata)，[`removeSendData`](#removesenddata)
    
    
### 详细说明

#### <span id="ready">ready</span>

本接口用来注册 sdk 初始化成功后的回调函数。

调用时需要传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |       回调函数，当 sdk 初始化成功之后会调用此函数  |

`cb` 将被传入下面几个参数组成的对象

| 参数名     | 说明      |
| -----     | ------    |
|    isLive   |  用来标记当前教室是直播还是回放  |
|    role   |  用来标记当前用户的角色，见 <a href="http://docs.duobeiyun.com/#请求进入房间" target="_blank">用户角色</a>  |
|    uid   |   进教室链接上的 uid  |
|    roomId   |   进教室链接上的 roomId  |

示例：
```
var sdk = new DCWSDK();
sdk.ready(function (initResult) {
    //业务逻辑
    console.log("sdk init successfully. ", initResult.isLive, initResult.role, initResult.uid, initResult.roomId);
});
```

#### <span id="broadcastEvent">broadcastEvent</span>

本接口用来给教室内的所有远端广播事件（数据），不会改变系统录制教室的状态。

调用时可以传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       event      |  Y          |       事件体对象  |
|       record     |  N          |       用来标识本事件是否应该被记录，默认值为 false，即默认不记录广播事件  |

`event` 的结构为：
```
{
    type: "xxx",
    data: {}
}
```
其中 `type` 用来标识 event 的类型；`data` 用来存储事件的数据，可以不存在。

示例：
```
sdk.broadcastEvent({
    type: "teacher_enter",
    data: {
        name: "tom",
        age: "20"
    }
}, true);
```

#### <span id="onBroadcastEvent">onBroadcastEvent</span>

本接口用来注册特定类型 event 的回调。

调用时可以传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       type      |  Y          |       监听事件的类型  |
|       cb     |  Y          |       事件的回调，形参是 data。即接口 `broadcastEvent` 传递的 data。  |

示例：
```
sdk.onBroadcastEvent("teacher_enter", function (data) {
   console.log("data ", data); // 输出 {name: "tom", age: "20"}
});
```


#### <span id="removeBroadcastEvent">removeBroadcastEvent(>=2.3.7)</span>

本接口用来移除 onBroadcastEvent 绑定的特定类型 event 的回调。

调用时可以传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       type      |  Y          |       监听事件的类型  |
|       cb     |       N     |       onBroadcastEvent中绑定的事件的回调，不填则移除该type下的所有回调  |



#### <span id="broadcastSetOperation">broadcastSetOperation</span>

本接口用来给教室内的所有远端广播数据更新操作，会改变系统录制教室的状态。

调用时可以传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       operation     |  Y  |  描述操作内容的对象  |
|       nameSpace     |  N          |       字符串，不能含有英文字符":"，用于限制retrieveStatus获取数据的规模(>=2.3.8)  |

`operation` 的结构为：
```
{
    "type": "xxx",
    "operas": {
        "SET": {},
        "DEL": {}
    }
}
```

其中 `type` 用来描述 operation 的类型, `SET` 为更新操作，`DEL` 为删除操作。

示例：
```
假设原始数据为：
{
    teacher: {
        name: "test_teacher",
        age: 1
    },
    student: {
        name: "test_student",
        age: 2
    }
}

如果想更新 teacher.name、student.name, 删除 teacher.age, 那么需要构造的参数结构为：

sdk.broadcastSetOperation({
  type: "update_classroom_info",
  operas: {
    "SET": {
      "teacher.name": "tom",
      "student.name": "jack"
    },
    "DEL": {
      "0": "teacher.age"
    }
  }
});
```

#### <span id="onBroadcastSetOperation">onBroadcastSetOperation</span>

本接口用来注册特定 operation 的回调。

调用时可传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       type      |  Y          |       监听的数据操作的类型  |
|       cb     |  Y          |       回调，形参是 operas。即接口 `broadcastSetOperation` 传递的operas  |

示例：
```
sdk.onBroadcastSetOperation("update_classroom_info", function (operas) {
  console.log("sdk.onBroadcastSetOperation ", operas);
});
```


#### <span id="removeBroadcastSetOperation">removeBroadcastSetOperation(>=2.3.7)</span>

本接口用来移除 onBroadcastSetOperation  绑定的特定数据操作的类型的回调。

调用时可以传入两个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       type      |  Y          |       监听的数据操作的类型  |
|       cb     |      N      |       onBroadcastSetOperation 中的回调，不填则移除该type下的所有回调  |



#### <span id="retrieveStatus">retrieveStatus</span>

本接口用来获取当前动态课件的完整状态或特定nameSpace下的状态。

同时支持直播和回放过程中的调用。

调用时可以传入一个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       nameSpace     |  N          |       registerObject中传入的nameSpace(>=2.3.7) |

无参数传入时调用获取当前动态课件的完整状态。注意，目前直播时调用只能获取到没有绑定nameSpace的状态，后期会支持返回完整状态。

> 本接口拉取的数据量可能会很大，请尽量传入参数或减少本接口的调用次数以降低服务器压力



#### <span id="onRetrieveStatus">onRetrieveStatus</span>

本接口用来注册获取到状态后的回调。

调用时可以传入一个参数：

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |       回调  |

`cb` 的形参是当前动态课件的完整状态，包含两个: `operaStatus` ，`registerStatus`。
其中 `operaStatus` 是所有 `broadcastSetOperation` 接口发送的 `SET` 接口的集合。

示例：
```
sdk.onRetrieveStatus(function (operaStatus, registerStatus) {

});
```


#### <span id="removeRetrieveStatus">removeRetrieveStatus(>=2.3.7)</span>

本接口用来移除 onRetrieveStatus 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onRetrieveStatus 注册的所有回调函数



#### <span id="registerObject">registerObject</span>

本接口将对象 obj 注册到 sdk 内部，sdk 将监听 obj 对象的变化。并在调用 `syncObject` 接口时，触发本端和远端的回调。
本接口返回一个 `ticket`，该 ticket 是 obj 在 sdk 内部的唯一标识，用来传递给 `syncObject` 接口做同步使用。

调用时可以传入三个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       obj     |  Y          |       被监视的对象。同一个 obj 对象可以传递给多个registerObject，即注册多个回调。但不同的 obj 需要有一个全局唯一的属性 `__id` 来做标识  |
|       cb     |  Y          |         本端和远端在调用 `syncObject` 后触发的回调 |
|       nameSpace     |  N          |       字符串，不能含有英文字符":"，用于限制retrieveStatus获取数据的规模(>=2.3.7)  |

`cb` 被传入的参数类型为 `obj` 被更新后的值。

示例：
```

var userInfo = {
    "__id": "d0c0bc7b1930e34f5faf4666533189a2",
    "name": "jack",
    "age": 8
};
//__id 需要单个动态课件内唯一。

var ticket = sdk.registerObject(userInfo, function (info, isInit, fromRemote) {
    console.log("new userInfo:", info); // 输出 {"name": "jack", "age": 10}
});
userInfo.age = 10;

sdk.syncObject(ticket);
//调用 syncObject 接口将会把 userInfo 的最新值同步给所有监听 userInfo 的远端用户。
```



#### <span id="syncObject">syncObject</span>

本接口将 ticket 对应的 obj 的最新状态同步给所有监听此 obj 的远端用户。

调用时需要传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       ticket     |  Y          |   调用 `registerObject(obj, cb)` 接口获取到的 ticket，本接口将 ticket 对应的 obj 同步给远端，并触发注册的 cb。   |



#### <span id="updateObject">updateObject(>=2.3.0)</span>

本接口用于更新本端的被监视对象 obj，调用前需先调用registerObject将对象 obj 注册到 sdk 内部

调用时传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       obj     |  Y          |       被更新的对象  |
|       part     |  Y          |         更新的部分 |

示例：
```
var userInfo = {
    "__id": "d0c0bc7b1930e34f5faf4666533189a2",
    "name": "jack",
    "age": 8,
};

var ticket = sdk.registerObject(userInfo, function (info, isInit, fromRemote) {
    console.log("new userInfo:", info); // 输出 {"name": "jack", "age": 10}
});

sdk.updateObject(userInfo,{ "age": 10, "gender": "female"});
// userInfo更新为{ "__id": "d0c0bc7b1930e34f5faf4666533189a2", "name": "jack", "age": 10, "gender": "female" };

sdk.syncObject(ticket);
//调用 syncObject 接口将会把 userInfo 的最新值同步给所有监听 userInfo 的远端用户。
```



#### <span id="onPlaybackSeek">onPlaybackSeek</span>

本接口只在回放状态下可用，用来注册用户点进度条时候的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |        回调 |

示例：
```
sdk.onPlaybackSeek(function () {
    //在回放的场景下，用户点击进度条的时候需要重置数据状态，然后等待 sdk 获取当前的 status，然后把数据渲染到页面上
});
```


#### <span id="removePlaybackSeek">removePlaybackSeek(>=2.3.7)</span>

本接口只在回放状态下可用，用来移除onPlaybackSeek中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onPlaybackSeek 注册的所有回调


#### <span id="registerDrawable">registerDrawable</span>

本接口将 elementId 对应的 `canvas` 节点注册到 sdk 内部，sdk 将监听该 canvas 节点上划线内容的变化，并自动将内容同步给所有监听本 canvas 节点的远端用户。

该接口可以传入四个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       elementId     |  Y          |       canvas节点的id，传入非canvas节点的id将无法工作  |
|      selfDrawAble     |  Y          |       本端是否可修改canvas的划线状态，默认值为false，此时本端用户无法在elementId对应的节点上划线  |
|       cb     |  N          |        注册canvas节点内容被远端/本端修改后触发的回调 |
|       nameSpace     |  N          |       字符串，不能含有英文字符":"，用于限制retrieveStatus获取数据的规模(>=2.3.7) |

`cb` 将被传入下面几个参数

| 参数名     | 说明      |
| -----     | ------    |
|    data   |  elementId对应 canvas 节点上所有划线的坐标  |
|   isInit  |  本次调用是否为初始化的情况下触发。 一般 `cb` 第一次被调用时为 true |
|   fromRemote |  本次调用是否为远端同步事件后触发，值为 `true` 时表示本次调用为远端触发；值为 `false` 时表示本次调用为本端触发。  |

示例：
```
//dom节点
<canvas id="drawable_canvas" width="400" height="300"></canvas>
//注册组件
sdk.registerDrawable("drawable_canvas", true, function (data, isInit, fromRemote) {
    console.log("canvas contents: ", data, isInit, fromRemote);
});
```


#### <span id="registerClickable">registerClickable</span>

本接口将 elementId 对应的节点注册到 sdk 内部，sdk 将监听该节点是否被点击，并同步点击状态给所有监听本节点点击状态的远端。

调用时可以传入四个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       elementId     |  Y          |       任意可点击dom节点的 id  |
|      selfClickAble     |  Y          |       本端是否可点击，默认值为false，此时本端点击elementId对应的节点后，状态不会被同步给远端  |
|       cb     |  N          |    注册被绑定元素被本端/远端点击后触发的回调     |
|       nameSpace     |  N          |       字符串，不能含有英文字符":"，用于限制retrieveStatus获取数据的规模(>=2.3.7)  |

`cb` 被传入的参数类型和 `registerDrawable` 相同

示例：
```
//dom节点
<button id="clickable_button">reset</button>
//注册组件
sdk.registerClickable("clickable_button", true, function (data) {
    console.log("clickable_button clicked!");
});
```

#### <span id="registerDraggable">registerDraggable</span>

本接口将 elementId 对应的节点注册到 sdk 内部，并监听该节点是否被拖动，将elementId对应节点的 `position` 设置为 `absolute`、将containerId对应节点的 `position` 设置为 `relative`， 如果elementId被拖动，将限制其在containerId的范围内；同时将拖拽状态同步给所有监听本节点拖拽状态的远端。

调用时可以传入五个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       elementId     |  Y          |       可拖拽节点的id  |
|      containerId     |  Y          |       指定可拖拽节点被放置范围的节点id  |
|      selfDraggable     |  Y          |       本端是否可拖动，默认为false，此时本端无法拖动elementId对应的节点  |
|       cb     |  N          |    注册被绑定元素被本端/远端拖拽后触发的回调     |
|       nameSpace     |  N          |       字符串，不能含有英文字符":"，用于限制retrieveStatus获取数据的规模 (>=2.3.7) |

`cb` 被传入的参数类型和 `registerDrawable` 相同

示例：
```
//dom节点
<div id="draggable_container">
    <div id="draggable_elem"></div>
</div>
//注册组件
sdk.registerDraggable("draggable_elem", "draggable_container", true, function (data) {
    console.log("draggable_elem position changed!");
});
```


#### <span id="registerLinkable">registerLinkable</span>

本接口将 elementId 对应的 canvas 节点注册到 sdk 内部，sdk 将监听该 canvas上内容的变化，并自动将内容同步给所有监听本 canvas 的远端。

调用时可以传入四个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       elementId     |  Y          |       canvas节点的id，传入非canvas节点的id将无法工作  |
|      selfLinkable     |  Y          |       本端是否可修改canvas的划线状态，默认值为false，此时本端用户无法在elementId对应的节点上连线  |
|       cb     |  N          |        注册canvas节点内容被远端/本端修改后触发的回调 |
|       nameSpace     |  N          |       字符串，不能含有英文字符":"，用于限制retrieveStatus获取数据的规模(>=2.3.7)  |

`cb` 被传入的参数类型和 `registerDrawable` 相同

示例：
```
//dom节点
<canvas id="linkable_canvas" width="400" height="300"></canvas>
//注册组件
sdk.registerLinkable("linkable_canvas", true, function (data) {
    console.log("new line!");
});
```


#### <span id="onOpenCounter">onOpenCounter(>=2.3.0)</span>

本接口用于添加计数器，并注册该计数器被开启时的回调函数

调用时传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      counterName     |  Y          |       计数器的名称    |
|       cb     |  Y          |        回调函数，该计数器被开启时调用 |

`cb` 被传入的参数类型为 数字， 是计数器开启时的初始值

示例：
```
sdk.onOpenCounter("counterName1", function (num) {
    console.log("counterName1被开启，初始值为：" + num);
});
```


#### <span id="removeOpenCounter">removeOpenCounter(>=2.3.7)</span>

本接口用来移除 onOpenCounter 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onOpenCounter 注册的所有回调函数


#### <span id="openCounter">openCounter(>=2.3.0)</span>

本接口用于开启一个计数器

调用时可以传入三个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      name     |  Y          |      计数器的名称    |
|       start     |  Y          |     计数器的起始值 |
|       prob     |  N          |      成功更新计数器的概率，0 <= num <= 1, 默认为1 |

！prob参数目前只支持值为1的情况，后续会完善该功能
示例：
```
sdk.openCounter(name, start, prob);
```



#### <span id="openCounters">openCounters(>=2.3.0)</span>

本接口用于开启多个计数器

调用时传入一个数组参数，数组元素为下面几个参数组成的对象:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      name     |  Y          |      计数器的名称    |
|       start     |  Y          |     计数器的起始值 |
|       prob     |  N          |      成功更新计数器的概率，0 <= num <= 1, 默认为1 |

！prob参数目前只支持值为1的情况，后续会完善该功能
参数示例：
```
[
    {
        name: "counterName2",
        start: 0,
        prob: 0.5
    },
    {
        name: "counterName3",
        start: 3,
    }
]
```

函数调用示例：
```
sdk.openCounters(arr);
```


#### <span id="onCloseCounter">onCloseCounter(>=2.3.0)</span>

本接口用于注册计数器被关闭时的回调函数

调用时可以传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      counterName     |  Y          |       计数器的名称    |
|       cb     |  Y          |        回调函数，该计数器被关闭时调用 |

`cb` 被传入的参数类型为 数字， 是计数器关闭时的计数器值

示例：
```
sdk.onCloseCounter("counterName1", function (num) {
    console.log("counterName1被关闭，值为：" + num);
});
```


#### <span id="removeCloseCounter">removeCloseCounter(>=2.3.7)</span>

本接口用来移除 onCloseCounter 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onCloseCounter 注册的所有回调函数



#### <span id="closeCounter">closeCounter(>=2.3.0)</span>

本接口用于关闭一个计数器

调用时传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      counterName     |  Y          |      计数器的名称    |


示例：
```
sdk.closeCounter("counterName1");
```



#### <span id="closeCounters">closeCounters(>=2.3.0)</span>

本接口用于关闭多个计数器

调用时传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      counterNames     |  Y          |      计数器的名称组成的数组    |


示例：
```
sdk.closeCounters(["counterName2","counterName3"]);
```


#### <span id="onBroadcastCounter">onBroadcastCounter(>=2.3.0)</span>

计数器被开启后会定时收到计数器当前值的广播，本接口用于注册计数器收到广播时的回调函数

调用时可以传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|      counterName     |  Y          |       计数器的名称    |
|       cb     |  Y          |        回调函数, 收到该计数器的广播时调用 |

`cb` 被传入的参数类型为 数字， 是计数器的当前值

示例：
```
sdk.onBroadcastCounter("counterName1", function (num) {
    console.log("counterName1的当前值为：" + num);
});
```


#### <span id="removeBroadcastCounter">removeBroadcastCounter(>=2.3.7)</span>

本接口用来移除 onBroadcastCounter 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onBroadcastCounter 注册的所有回调函数



#### <span id="onBroadcastCounters">onBroadcastCounters(>=2.3.7)</span>

计数器被开启后会定时收到计数器当前值的广播，本接口用于注册计数器收到广播时的回调函数

调用时传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |        回调函数, 收到计数器的广播时调用 |

`cb` 被传入的参数类型为 对象object， 是所有计数器的当前状态， eg. {c1: 3, c2: 3}

示例：
```
sdk.onBroadcastCounters(function (obj) {
    console.log("计数器的状态为：", obj);
});
```

#### <span id="removeBroadcastCounters">removeBroadcastCounters(>=2.3.7)</span>

本接口用来移除 onBroadcastCounters 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onBroadcastCounters 注册的所有回调函数


#### <span id="updateCounter">updateCounter(>=2.3.0)</span>

本接口用于更新计数器，改变计数器的值

调用时传入两个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       obj     |  Y          |        更新计数器的对象，键名为计数器名，键值为计数器增加的值，应该是一个正整数 |
|       cb     |  Y          |        回调函数，更新计数器完成时调用 |

obj 示例：
```
{
    counterName1: 1,
    counterName2: 2,
}
```
`cb` 被传入的参数为表示是否更新成功的bool值，更新成功时为 `true`

示例：
```
sdk.updateCounter(obj, function (isSuccess) {
    if(isSuccess) {
        console.log("counter is successfully updated");
    }
});
```


#### <span id="retrieveCounter">retrieveCounter(>=2.3.0)</span>

本接口用于请求当前所有已开启的计数器的状态

调用时不需要参数

同时支持直播和回放过程中的调用

示例：
```
sdk.retrieveCounter();
```



#### <span id="onRetrieveCounter">onRetrieveCounter(>=2.3.0)</span>

本接口用于注册接收到retrieveCounter请求的计数器状态时调用的回调函数

调用时可以传入2个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |        回调函数，接收到retrieveCounter请求的计数器状态时调用 |
|       arr     |  N          |        数组，元素为计数器名称 |

`cb` 被传入的参数为 包含可选参数`arr`中指定的计数器状态的对象，结构如下
```
{
    counterName1: 10,
    counterName2: 2,
    ...
    couterNameN: 56
}
```
未传入`arr`参数，obj则含有所有计数器的状态

示例：
```
// obj只含有"c1","c2"计数器的状态 eg. {c1: 3, c2: 3}
sdk.onRetrieveCounter(function (obj) {
    console.log(" call RetrieveCounter cb");
},["c1","c2"]);

// obj含有所有计数器的状态 eg. {c1: 3, c2: 3, c3: 3, c4:4}
sdk.onRetrieveCounter(function (obj) {
    console.log(" call RetrieveCounter cb");
});
```


#### <span id="removeRetrieveCounter">removeRetrieveCounter(>=2.3.7)</span>

本接口用来移除 onRetrieveCounter 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onRetrieveCounter 注册的所有回调函数



#### <span id="onSendData">onSendData(>=2.3.5)</span>

本接口用于注册debug环境下发送数据时调用的回调函数

调用时可以传入1个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |        回调函数，发送数据时调用 |

`cb` 被传入的参数为 发送的数据


示例：
```
sdk.onSendData(function (data) {
    console.log("send data:", data);
});
```



#### <span id="removeSendData">removeSendData(>=2.3.7)</span>

本接口用来移除 onSendData 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onSendData 注册的所有回调函数



#### <span id="onReceiveData">onReceiveData(>=2.3.5)</span>

本接口用于注册debug环境下接收到数据时调用的回调函数

调用时可以传入1个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |  Y          |        回调函数，接收到数据时调用 |

`cb` 被传入的参数为 接收到的数据


示例：
```
sdk.onReceiveData(function (data) {
    console.log("receive data:", data);
});
```


#### <span id="removeReceiveData">removeReceiveData(>=2.3.7)</span>

本接口用来移除 onReceiveData 中注册的回调。

该接口可以传入一个参数:

| 参数名     | 必填(Y/N)   | 说明      |
| -----     | -----      | ------    |
|       cb     |      N     |        回调 |

无参数传入则移除 onReceiveData 注册的所有回调函数



#### <span id="webPageReady">webPageReady(>=2.4.0)</span>

本接口用来通知服务端动态课件网页程序就绪，可以发送数据了。服务端会在确认收到该通知后再向sdk发送数据，解决初始化数据丢失问题。

> 注意：动态课件服务端代码正式上线后将不再支持2.4.0之前版本的sdk,使用之前版本开发的动态课件网页需要升级sdk至2.4.0，并在网页代码就绪后调用
           webPageReady接口，否则无法接收到服务端发送的信息。

该接口无需传入参数:

示例：
```
sdk.webPageReady();
```


## 更新历史
- 2017.12.7 更新到 `2.4.0`
    - 添加接口[`webPageReady`](#webpageready)，用于通知服务端动态课件网页程序就绪，可以发送数据了。服务端会在确认收到webPageReady发送的通知后再向sdk发送数据，解决初始化数据丢失问题。
    - 请知悉：动态课件服务端代码正式上线后不再支持2.4.0之前版本的sdk,使用之前版本开发的动态课件网页需要升级sdk至2.4.0，并在网页代码就绪后调用
    webPageReady接口，否则无法接收到服务端发送的信息。
- 2017.11.29 更新到 `2.3.10`
    - fix: 对信息进行过滤，不发送空信息
    
- 2017.10.31 更新到 `2.3.9`
    - 支持消息序列信息的日志收集

- 2017.10.24 更新到 `2.3.8`
    - [`broadcastSetOperation`](#broadcastsetoperation) 添加可选参数 nameSpace

- 2017.10.12 更新到 `2.3.7`
    - 添加移除回调函数的系列接口"removeX"
    - 添加[`onBroadcastCounters`](#onbroadcastcounters) 接口
    - [`retrieveStatus`](#retrievestatus)接口支持获取指定 nameSpace 下的课件状态，可以在 [`registerObject`](#registerobject)、[`registerDrawable`](#registerdrawable)、[`registerClickable`](#registerclickable)、 [`registerDraggable`](#registerdraggable)和 [`registerLinkable`](#registerlinkable) 传入对应的nameSpace；支持在回放过程中调用 [`retrieveStatus`](#retrievestatus)
    
- 2017.09.05 更新到 `2.3.5`
    - 添加调试接口[`onReceiveData`](#onreceivedata) [`onSendData`](#onsenddata)
    
- 2017.08.15 更新到 `2.3.0`
    - [`onRetrieveCounter`](#onretrievecounter)增加了一个过滤用的可选参数`arr`
    - 直播支持计数器
    - 增加更新对象接口 [`updateObject`](#updateobject)

- 2017.04.22 更新到 `1.2.2`
    - 修复在 register object 后，修改 object 的某个 value 为字符串时，导致 syncObject 出错的 bug

- 2017.04.19 更新到 `1.2.1`
    - 增加 [`onPlaybackSeek`](#onplaybackseek) 接口

- 2017.04.14 更新到 `1.2.0`
    - 支持回放
    - [`ready`](#ready) 接口的回调函数增加参数 `isLive` 用来标记课程当前是 `直播` 还是 `回放`，增加参数 `role` 用来标记当前用户的角色

- 2017.04.07 更新到 `1.1.0`
    - 支持直播