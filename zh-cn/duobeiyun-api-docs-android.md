---
### 多贝云androidSDK直播说明文档
---
#### 第一步：sdk的集成
```		
		1.将dbysdk-release.aar放到app模块的下的libs目录下。
		2.在app中添加对SDK的依赖
		在app模块中的build.gradle文件中android域添加  
			repositories {
					 flatDir {
							dirs 'libs' 
								} 
						}
		3.添加相关的依赖：
		//sdk
		compile(name: 'dbysdk-release', ext: 'aar’)
		4.sdk内部依赖了gson和Picasso;所以需要添加对他们的依赖；如果项目中有这两个；则可以不添加
		compile 'com.google.code.gson:gson:2.6.2’
		compile 'com.squareup.picasso:picasso:2.5.2'
		
		5.如果有离线下载的功能；可以使用 `filedowner` 的下载，SDK提供的demo是依赖于这个库
		compile 'com.liulishuo.filedownloader:library:1.2.2’
```
#### 第二部：sdk代码的实现

##### 1.需要在application的`oncreate()`方法中初始化SDK；

```
		DBYHelper.getInstance().initDBY(this);
```
		
##### 2.sdk需要添加相应的`权限`

```
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.READ_PHONE_STATE" /> 
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	<uses-permission android:name="android.permission.CAMERA" />
	<uses-permission android:name="android.permission.RECORD_AUDIO" />
	<uses-permission android:name="android.permission.WAKE_LOCK" />
	<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" /> 
	<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
	<uses-permission android:name="android.permission.WAKE_LOCK" />
	<uses-permission android:name="android.permission.CHANGE_CONFIGURATION" />
	

```
##### 3.创建直播`activity`，引入`liveplayer`;（直播主要分1v1直播和1vn直播）

###### 3.1直播player相应的初始化操作

1vn的直播：

```
			 //teacherImage老师的视频窗口，
			 player.setVideoView(teacherImg, null);
			//设置相应的pid和appkey
        	player.setAuth(Const.PID, Const.APPKEY);
        	//第一种；通过客户端自己传递相依的参数进入
      		//角色，目前Android端只有学生，直接设置RoleType.ROLE_TYPE_STUDENT
			int role = RoleType.ROLE_TYPE_STUDENT;
			player.setPlayInfo(uid, roomId, nickname, role, false, this);
			
			//(2)第二种方式(通过url的方式进入)
		// nativePlayer.setPlayInfo(url,false);

						
```
添加默认ppt，sdk在教师端未开启PPT的时候将不会展示PPT，该方法可以设置默认的图片作为课前ppt
		``` nativePlayer.setBeforePpt("",R.drawable.xxx);//可选
		```

1v1的直播：

```
		 //设置学生的显示区域
        nativePlayer.setSurfaceView(studentView);
		 //设置老师的显示区域
        nativePlayer.setVideoView(teacherView, null);
        //设置相应的pid和appkey
        player.setAuth(Const.PID, Const.APPKEY);
        player.setPlayInfo(uid, roomId, nickname, role, false, this);
        	
       //(2)第二种方式(通过url的方式进入)
		// nativePlayer.setPlayInfo(url,false);

       //添加默认ppt，sdk在教师端未开启PPT的时候将不会展示PPT，该方法可以设置默认的图片作为课前ppt
		// nativePlayer.setBeforePpt("",R.drawable.xxx);

```
       
客户端添加划线功能(如果在1v1的客户端需要学生端划线和教师端互动，则调用该方法，默认不开启划线功能)
       ``` nativePlayer.canClientDraw(true);//如果不需要学生和老师互动；则可以不调用，或者传递false
       ```

###### 3.2 activity相应的生命周期方法的调用player的生命周期方法
 
 ```
  @Override
    protected void onResume() {
        super.onResume();

        if (!isFirstRunning) {
            if (player != null) {
                player.recovery();
            }
        }
        if (isFirstRunning) {
            isFirstRunning = false;
        }
        
       
    }
    
    @Override
    protected void onStart() {
        super.onStart();
        if (player != null) {
            player.start();
        }
    }
    
     @Override
    protected void onPause() {
        getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        load.setVisibility(View.GONE);
        if (player != null) {
            player.pause();
        }
        super.onPause();
    }


    @Override
    protected void onDestroy() {
        if (player != null) {
            player.release();
        }
        if (load.isShown()) {
            load.setVisibility(View.GONE);
        }
        super.onDestroy();
    }
 ```
###### 3.3实现相应的接口；通过接口的数据做相应的处理<br/>
 3.3.1直播接口的说明
 	
 消息回调接口`LiveMessageCallback`说明：
 
```
void loading();//开始加载

    void handleContent(ChatBean chatBean); //返回单条消息

    void handleContent(ArrayList<ChatBean> list); //返回聊天列表

    void handleClearChat(); //断线重连时，清理上次的聊天内容

    void handleAnnounceMessage(String message);//公告消息

    void connected();//连接成功

    void connectFail(String msg); //连接失败

    void connectTimeout(); //重连

    void kickoff();//重复登录

    void networkNotConnected();//网络未连接

    void voteStart(int type);//投票开始

    void voteEnd();//投票结束

    void voteClose();//投票关闭

    void voteInfo(JSONObject jsonObject);//投票数据

    void statusCode(int code);//状态码

    void exit();//退出播放器

    void teacherOnline();//老师在线

    void pptMessage(int currentpage, int totalpage);//ppt相关的信息

    void onlineUserCount(int countnumber);//在线人数
```
网络重连接口`ReconnectionCallback `调用	
   	
```
/***
 ***Create dby-android-sdk by yangge at 2017/6/21 
 * 重连的提示的回调（；直接通过statucode判断重连的相关操作）
 * 此回调都可能会触发多次；请自行处理用户提示相关操作
 **/
public interface ReconnectionCallback {

    /**
     * 网络较好
     */
    void netIsFine();

    /**
     * 网络较差
     */
    void netIsPoor();

    /**
     * 正在重连音频
     */
    void reconnecting();

    /**
     * 重连成功音频
     */
    void reconnectedSuccess();

    /**
     * 重连失败音频
     */
    void reconnectError();


}
```

这样基本上一个完成的直播就基本上完成了

3.3.2其他接口相应的调用（按业务需求对接<br/>
学生举手调用`player.raiseHand()` 学生举手`RaiseHandCallback `回调

```
/***
 ***Create dby-android-sdk by yangge at 2017/8/1 
 * 举手的相关回调
 **/
public interface RaiseHandCallback {

    /**
     * 举手成功
     */
    void handSuccess();

    /**
     * 举手失败
     */
    void handFail();

    /**
     * 老师取消当前学生的举手
     */
    void downHand();

}
```
老师给麦`MicRequestCallback `回调

```
/***
 ***Create dby-android-sdk by yangge at 2017/8/1 
 * 学生发送mic的回调（老师给麦；老师取消给麦）
 **/
public interface MicRequestCallback {

    void startSendMic();

    void closeMic();
    
}
```
答题调用`player.vote(int answer)`
答题`AnswerCallback `的回调

```
/***
 ***Create dby-android-sdk by yangge at 2017/8/1 
 * 答题的相关操作
 **/
    public interface AnswerCallback {

    /**
     * 答题成功
     */
    void answerSuccess();

    /**
     * 答题失败
     */
    void answerFial();
}
```
直播相应的状态吗的说明：
在程序调试过程中或运行过程中可以根据`void statusCode(int code);`获取程序运行过程中的相应的状态；并根据相应的状态查找相应的原因。

```
10;		//apps服务器连接成功
11;		//apps服务器连接错误
12; 	//apps服务器重连
100;  	//语音连接成功
101;  	//语音连接失败
102;  	//语音重连
200;   //视频连接成功
201;   //视频连接失败
202;  	//视频重连
300; 	//开始获取authinfo信息
301; 	//获取authinfo成功
302; 	//获取authinfo失败
400;  //开始获取视频
401;  //获取视频
402; 	//获取视频
0;  	//获取所有信息成功
900;   //authinfo 解析失败
901; 	//http错误
10000; //返回失败
0001;	//课程未开始
10008; //apps连接失败

300000;//参数异常
    
300001;//老师在线
    
300002;//老师不在线
    
300003;//发言太频繁
    
300004;//图片加载失败(
300005;//全员禁言
    
300006;//需要先调用initPlayInfo方法

```
---

### 离线播放说明

---

注：sdk内部没有提供下载的相应的逻辑；下载部分和解压操作均有自己开发；具体的离线资源的下载地址为
`DuobeiYunClient.BASE_URL + roomId + ".zip"` 

下载到本地的存放路径自己决定放到什么位置；sdk需要获取你本地下载的资源存放的目录路径；调用
`DuobeiYunClient.setSavePath("存放离线资源的目录");`DuobeiYunClient.savePath为SDK提供的默认路径
该方法建议在初始化的时候或者在播放离线资源之前调用，

#### 离线播放器的初始化
```
		player.init(Const.DES_KEY);//Const.DES_KEY请向商务获取
        player.setCallback(roomId, this);//roomId为你下载资源的roomId相对应
        player.setSeekBar(portraitSeekbar);//
        player.setLanSeekBar(landscapeSeekbar);
```
`player.setSeekBar()；`说明：因为SDK内部只提供了ppt和音视频的展示；关于播放的控制需要自己完成。
该方法主要是为了设置进度条；可以根据自己的UI定制相应的界面；seekBar的相关控制已经由sdk完成。

##### 对应activity生命周期的player的生命周期

```
	 @Override
    protected void onPause() {
        if (player != null) {
            player.pause();
        }
        super.onPause();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (player != null) {
            player.destroy();
        }

    }

```
#### 离线播放'OfflinePlaybackMessageCallback'相应的回调
```
/**
 * Created by Administrator on 2017/2/28.
 * 离线回放回调（开发者调用）
 */

public interface OfflinePlaybackMessageCallback {

    void handleContentList(List<ChatBean> list); // 返回消息列表

    void handleContent(ChatBean chatBean); // 返回单条消息

    void handleErrorMessage(Exception exception);// 错误回调

    void loadStart();// 开始加载

    void loadFinish();// 结束加载

    void playFinish();//播放完成

    void statusCode(int code);//状态码回调

    void exit();//退出

	/**
     * @param timeStr  当前的时间
     * @param progress 进度
     */
    void currentTime(String timeStr, int progress);//当前的时间

	/**
     * @param duration    //总时间
     * @param totalPage   ppt总页数
     * @param currentPage ppt当前页数
     */
    void initPPT(String duration, String totalPage, String currentPage);//初始化pp,
}


```
#### 离线播放的控制
```
 player.play();//视频播放
 player.pause();//视频暂定
 player.setPlaybackSpeed(speed);//视频的倍数播放 （1.0f~2.0f）

```
---
### 在线播放
---
采用webView形式播放；由后台提供相应的连接；或者本地拼接相应的连接打开webview;具体可以参考DefPlaybackActivity，建议不要直接使用；方便自己自定义一些操作
###end
关于具体的SDK的更详细的代码说明；请参考sdk提供的demo;<br/>
直播参考：CustomizedLiveActivity、NativeLiveActivity
离线播放参考 CustomizedOffinePlayBackActivity
在线回放参考：DefPlaybackActivity
