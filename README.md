# nim

网易云IM Flutter版本插件

## Getting Started

目前版本没有写成插件，如果想要使用的话下载源码，做成本地插件。
具体实现方式：

1.下载代码，复制到项目中存放插件的文件夹。

2.在pubspec.yaml中添加依赖：

```
  #网易云IM
  nim:
    path: plugins/nim
```
Android 端使用：

1.在Android的AndroidManifest.xml 配置APPKEY：

```
    <meta-data
        android:name="com.netease.nim.appKey"
        android:value="你的APPKEY" />
```
2.在Application中初始化网易云IM

```
 NimGlobal.getInstance(this).initNim(MainActivity.class);
```
这里也可以使用官方UIKit里面的方法进行初始化。

iOS端使用

1.在AppDelegate.m中进行初始化

```
- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [GeneratedPluginRegistrant registerWithRegistry:self];
  // Override point for customization after application launch.
    //推荐在程序启动的时候初始化 NIMSDK
    NSString *appKey        = @"your app key";
    NIMSDKOption *option    = [NIMSDKOption optionWithAppKey:appKey];
//    option.apnsCername      = @"your APNs cer name";
//    option.pkCername        = @"your pushkit cer name";
    [[NIMSDK sharedSDK] registerWithOption:option];
  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}
```
在Flutter登录IM账号：

导入:

```
import 'package:nim/nim.dart';
```

登录账号：
```
  Nim nim=new Nim();
  nim.initImLogin("accid", "token");
```

启动单聊页面：


```
  Nim nim=new Nim();
  nim.startChat("to accid");//对方的accid
```

获取最近会话列表

```
     Nim nim=new Nim();
    nim.queryRecentContacts().then((map){
    //在会话数据返回之后，调用keepRecent 开实时的获取数据
      Nim.instance.keepRecent().then((map){
    println(map);
    _refreshData(map,ctx);
  });
      println(map);
      if(map["code"]==200){
        List<dynamic>  chatlist=jsonDecode(map["msg"]);
        print(chatlist);
      final List<ChatItemState> chatItemList=new List();

      for(int i=0;i<chatlist.length;i++){
        chatItemList.add(ChatItemState(chatData: chatlist[i]));
      }
        ctx.dispatch(ConversationListActionCreator.initAdapter(chatItemList));
      }else{
        ToastTools.showToast("暂无聊天记录");
      }
    });
    void _refreshData(Map map,Context<ConversationListState> ctx){
  _keepRecent(ctx);
  if(map["code"]==200){
    List<dynamic>  chatlist=jsonDecode(map["msg"]);
    print(chatlist);
    final List<ChatItemState> chatItemList=new List();
    for(int i=0;i<chatlist.length;i++){
      chatItemList.add(ChatItemState(chatData: chatlist[i]));
    }
    ctx.dispatch(ConversationListActionCreator.initAdapter(chatItemList));
  }else{
    ToastTools.showToast("暂无聊天记录");
  }
}
void _keepRecent(Context<ConversationListState> ctx){
  Nim.instance.keepRecent().then((map){
    println(map);
    _refreshData(map,ctx);
  });
}

```

