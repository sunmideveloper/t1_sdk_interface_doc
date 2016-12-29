## **快速集成**

- 直接将jar包导入到项目中或在Android Studio的app module下的build.gradle文件中声明
```
dependencies {
    compile 'com.sunmi:DS_Lib:1.0.2'
    compile 'com.google.code.gson:gson:2.6.2'//gson任意版本
}
```
- 在清单文件AndroidMainfest.xml的<application>节点下配置以下声明
```
<application>
....
        <receiver
            android:name="sunmi.ds.MsgReceiver">
            <intent-filter >
                <action android:name="com.sunmi.hcservice"></action>
                <action android:name="com.sunmi.hcservice.status"></action>
            </intent-filter>
        </receiver>
</application>
```
- 初始化SDK
```
DSKernel mDSKernel = DSKernel.newInstance();
mDSKernel.init(context, mConnCallback);
mDSKernel.addReceiveCallback(mReceiveCallback);
```
- 示例见Demo：
*DSC：主屏程序*
*DSD : 副屏程序*

## **SDK支持的发送类型**
- String类型的数据（一个json格式的字符串、一段文字信息等...）
- 单个文件（SD卡已存在的文件）
- 单个文件+String类型的数据
- 多个文件+String类型的数据

## **关键类说明**
<---
#### **包 sunmi.ds**
##### **DSKernel类**：SDK核心类，该类暴露了向副屏发送数据、初始化SDK的函数
+ **newInstance(): DSKernel**
```
说明：实例化DSKernel类的静态函数。
```


+ **getDSDPackageName(): String**
```
说明：获取副屏接收数据的app包名的静态函数。
返回值：packageName，副屏负责接收数据的App包名
```


+ **init(Context contetx, IConnectionCallback stateCallback, String vicePackageName): void**
```
说明：初始化SDK。
参数：
contetx：android上下文对象。
stateCallback：与副屏的连接状态回调。
vicePackageName：副屏接收数据的app包名，传null或空字符串则默认接收数据的app为Sunmi的副屏默认App。
```


+ **init(Context contetx, IConnectionCallback stateCallback): void**
```
说明：初始化SDK。
参数：
contetx：android上下文对象。
stateCallback：与副屏的连接状态回调。
```


+ **isConnected(): boolean**
```
说明：判断双屏通讯连接是否畅通
返回值：isConn  true为畅通，false为断开。
```


+ **sendData(DataPacket pack): void**
```
说明：发送数据。
参数：
pack：DataPacket类型的对象，封装了要发送的数据。
```


+ **sendCMD(String recePackageName, String cmd, long fileId, ISendCallback callback): void**
```
说明：发送CMD命令包，可指定要使用的缓存文件id。
参数：
recePackName：接收端包名。
cmd：命令。
fileId：要使用的缓存文件id，如果没有则传0。
callback：发送结果回调。
```


+ **sendCMD(DataPacket dataPacket): void**
```
说明：发送CMD命令包，可指定要使用的缓存文件id。
参数：
dataPacket：CMD类型数据包。
```



+ **sendQuery(DataPacket mPack, QueryCallback callback): void**
```
说明：发送数据，向副屏发送查询数据包时使用。
举例：在用户协议层，主屏向副屏发送一个表示查询副屏亮度的Query数据包，副屏App收到后获取亮度再调用
sendResult(long queryId)函数向主屏发回一个携带亮度的结果数据包，此Result数据包的queryId必须与Query
数据包的taskId一致，主屏才能识别到是之前的查询结果。
注意：使用QueryCallback接收结果数据包时，通过addReceiveCallback()注册的回调实例将不会被调用。
参数：
pack：DataPacket类型的对象，封装了要发送的数据。
callback 查询结果回调
```


+ **sendQuery(String recePackageName, String queryStr, ISendCallback sendCallback, QueryCallback callback): void**
```
说明：发送数据，向副屏发送查询数据包时使用。
举例：在用户协议层，主屏向副屏发送一个表示查询副屏亮度的Query数据包，副屏App收到后获取亮度再调用
sendResult(long queryId)函数向主屏发回一个携带亮度的结果数据包，此Result数据包的queryId必须与Query
数据包的taskId一致，主屏才能识别到是之前的查询结果。
注意：使用QueryCallback接收结果数据包时，通过addReceiveCallback()注册的回调实例将不会被调用。
参数：
recePackageName：接收端包名。
queryStr：要携带的字符串数据
sendCallback：发送结果回调
callback：查询结果回调
```


+ **sendResult(String recePackageName, String resultStr, long queryId, ISendCallback sendCallback): void**
```
说明：发送Result数据包。
参数：
recePackName：接收端包名。
resultStr：查询结果。
queryId：Query数据包的taskId。
sendCallback：发送结果回调。
```


+ **sendFile(String recePackName, String filePath, ISendCallback callback): long**
```
说明：发送文件。
参数：
recePackName：副屏接收的app package name。
filePath：文件路径。
callback：发送结果回调。
返回值：taskId，维护此任务Id直至收到发送成功的回调时可以向副屏发送指令对文件做自定义操作，比如：显示图片、打开文件等操作。
注意：文件传输到副屏以后会被缓存起来并且与返回的taskId形成映射关系，若想长期复用该文件则应将taskId持久化维护起来。
```


+ **sendFile(String recePackName, String msg, String filePath, ISendCallback callback): long**
```
说明：发送一个文件+String类型的数据
参数：
recePackName：副屏接收的app package name。
msg：String类型的数据，例如：一个json格式的字符串、一段文字信息等...
filePath：文件路径。
callback：发送结果回调。
返回值：taskId，维护此任务Id直至收到发送成功的回调时可以向副屏发送指令对文件做自定义操作，比如：显示图片、打开文件等操作。
注意：文件传输到副屏以后会被缓存起来并且与返回的taskId形成映射关系，若想长期复用该文件则应将taskId持久化维护起来。
```


+ **sendFiles(String recePackName, String msg, List<String> files, ISendFilesCallback callback): long**
```
说明：发送多个文件+String类型的数据
参数：
recePackName：副屏接收的app package name。
msg：String类型的数据，例如：一个json格式的字符串、一段文字信息等...
files：文件路径集合。
callback：多文件发送结果回调。
返回值：taskId，维护此任务Id直至收到发送成功的回调时可以向副屏发送指令对文件做自定义操作，比如：显示图片、打开文件等操作。
注意：文件传输到副屏以后会被缓存起来并且与返回的taskId形成映射关系，若想长期复用该文件则应将taskId持久化维护起来。
```


+ **checkFileExist(long fileId, final ICheckFileCallback callback): void**
```
说明：检查fileId对应的文件在副屏是否存在
参数：
fileId：文件Id。
callback：检查结果回调。
```



+ **addConnCallback(IConnectionCallback callback): void**
```
说明：注册一个监听连接的回调，可注册多个。
参数：
stateCallback：与副屏的连接状态回调。
```


+ **removeConnCallback(IConnectionCallback callback): void**
```
说明：移除监听连接的回调。
参数：
stateCallback：与副屏的连接状态回调。
```


+ **checkConnection(): void**
```
说明：检测与副屏的连接状态，有结果会回调注册的IConnectionCallback。
```


+ **addReceiveCallback(IReceiveCallback receiveCallback): void**
```
说明：注册数据接收回调，可注册多个。
参数：
receiveCallback：接收端用于接收发送端数据的回调接口。
```


+ **removeReceiveCallback(IReceiveCallback receiveCallback): void**
```
说明：移除数据接收回调。
参数：
receiveCallback：要注销的回调实例。
```
---
##### **FilesManager类**：持久化维护缓存接收到的文件，以发送文件任务的taskId为key缓存DSFile、DSFiles。
+ **getFile(Long taskId): DSFile**
```
说明：根据任务ID获取文件。
参数：
taskId：文件对应的任务ID。
```


+ **getFiles(Long taskId): DSFiles**
```
说明：根据任务ID获取多个文件。
参数：
taskId：文件对应的任务ID。
```
--->

<---
#### **包 sunmi.ds.callback**
##### **IReceiveCallback类**：接收数据时的回调接口。
+ **onReceiveData(DSData data): void**
```
说明：接收到数据时的回调。
参数：
data：接收到的数据
```


+ **onReceiveFile(DSFile file): void**
```
说明：接收到单个文件时的回调。
参数：
file：接收到的文件
```


+ **onReceiveFiles(DSFiles files): void**
```
说明：接收到>=1个文件+一段String类型数据时的回调。
参数：
files：接收到的文件+String数据
```


+ **onReceiveCMD(DSData cmd): void**
```
说明：接收到CMD类型数据时的回调。
参数：
cmd：接收到的CMD类型的数据
```
---


##### **QueryCallback类**：接收到查询结果数据包时的回调。
+ **onReceiveData(DSData data): void**
```
说明：接收到结果数据包时的回调。
参数：
data：接收到的数据
```
---

##### **IConnectionCallback类**：双屏通讯连接状态的回调接口，所有回调函数都运行在子线程。
+ **onDisConnect(): void**
```
说明：连接断开时的回调。
```


+ **onConnected(ConnState state): void**
```
说明：连接状态更新时回调。连接状态分为3种：1.与本地service链接正常、2.与副屏service连接正常（处于这种状态下就可以向副屏发送数据了）、3.与副屏APP连接正常。
参数：
state：连接状态
```
---



##### **ICheckFileCallback类**：检查文件结果回调接口。
+ **onCheckFail(): void**
```
说明：检查失败(通讯失败)。
```


+ **onResult(boolean exist): boolean**
```
说明：检查失败(通讯失败)。
参数：
exist：true表示存在，false表示不存在。
```
---

##### **IConnectionCallback类**：双屏通讯连接状态的回调接口，所有回调函数都运行在子线程。
+ **onDisConnect(): void**
```
说明：连接断开时的回调。
```


+ **onConnected(ConnState state): void**
```
说明：连接状态更新时回调。连接状态分为3种：1.与本地service链接正常、2.与副屏service连接正常（处于这种状态下就可以向副屏发送数据了）、3.与副屏APP连接正常。
参数：
state：连接状态
```
---


##### **ISendCallback类**：发送String数据或单个文件的回调接口，所有回调函数都运行在子线程。
+ **onSendSuccess(long taskId): void**
```
说明：发送成功时的回调。
taskId：任务Id，对应发送的数据或文件
```


+ **onSendFail(int errorId, String errorInfo): void**
```
说明：发送失败时的回调。
参数：
errorId：错误识别码。
errorInfo：错误描述。
```


+ **onSendProcess(long totle, long sended): void**
```
说明：发送进度的回调。
参数：
totle：数据的总大小，单位：byte。
sended：已发送的数据大小，单位：byte
```
---


##### **ISendFilesCallback类**：发送>=1个文件+String数据的回调接口，所有回调函数都运行在子线程。
+ **onAllSendSuccess(long fileId): void**
```
说明：多个文件都发送成功时的回调。
fileId：任务Id也是缓存在副屏文件对应的fileId。
```

+ **onSendSuccess(String path, long taskId): void**
```
说明：某一个文件发送成功时的回调。
taskId：任务Id，发送多个文件都属于同一任务所以taskId都相同。
```


+ **onSendFail(int errorId, String errorInfo): void**
```
说明：发送String数据失败时的回调，String数据发送失败后不会继续发送文件。
参数：
errorId：错误识别码。
errorInfo：错误描述。
```


+ **onSendFileFaile(String path, int errorId, String errorInfo): void**
```
说明：发送某一个文件失败时的回调。
参数：
path：发送失败对应的文件路径。
errorId：错误识别码。
errorInfo：错误描述。
```


+ **onSendProcess(String path, long totle, long sended): void**
```
说明：发送某个文件的进度回调。
参数：
path：对应的文件路径。
totle：数据的总大小，单位：byte。
sended：已发送的数据大小，单位：byte
```
--->

<---
#### **包 sunmi.ds.data**
##### **DataPacket类**：封装了发送数据和发送回调，调用DSKernel类的sendData(DataPacket pack)函数时需要的参数实体类。
---


##### **DataPacket.Builder类**：DataPacket的builder类。
+ **Builder(DSData.DataType dataType)**
```
说明：构造函数。
参数：
dataType：要build的数据类型。
```


+ **recPackName(String recPackName): Builder**
```
说明：指定接收数据的app包名。
参数：
recPackName：接收数据的app包名。
返回值：Builder实例
```


+ **data(String data): Builder**
```
说明：指定要发送的数据。
参数：
data：要发送的String数据。
返回值：Builder实例
```


+ **taskId(long taskId): Builder**
```
说明：指定任务Id，不指定则自动生成。
参数：
taskId：任务Id。
返回值：Builder实例
```


+ **fileId(long fileId): Builder**
```
说明：指定缓存文件Id。
参数：
fileId：缓存文件Id。
返回值：Builder实例
```


+ **queryId(long queryId): Builder**
```
说明：指定Query数据包的Id。
参数：
fileId：Query数据包的Id。
返回值：Builder实例
```



+ **isReport(boolean isReport): Builder**
```
说明：指定是否需要结果回调。
参数：
isReport：是否需要结果回调。
返回值：Builder实例
```


+ **addCallback(ISendCallback callback): Builder**
```
说明：设置发送结果的回调实例。
参数：
callback：发送结果的回调实例。
返回值：Builder实例
```


+ **build(): DataPacket**
```
说明：build一个DataPacket实例。
返回值：DataPacket实例
```
---


##### **DSData类**：双屏通讯的数据封装。
+ **sender: String**
```
说明：发送端app包名。
```


+ **taskId: long**
```
说明：任务Id。
```


+ **fileId: long**
```
说明：缓存文件Id。
```


+ **queryId: long**
```
说明：Query数据包的任务Id。
```


+ **dataType: DataType**
```
说明：数据类型。
```


+ **data: String**
```
说明：要发送的数据。
```
---


##### **DSData.DataType枚举类**：数据类型描述封装。
+ **DATA: DataType**
```
说明：表示数据。
```


+ **FILE: DataType**
```
说明：表示文件。
```


+ **CMD: DataType**
```
说明：表示命令。
```


+ **typeCode: int**
```
说明：类型code。
```
---


##### **DSFile类**：接收端接收的文件类。
+ **sender: String**
```
说明：发送端app包名。
```


+ **path: String**
```
说明：接收到的文件路径。
```


+ **taskId: long**
```
说明：任务Id。
```
---


##### **DSFiles类**：接收端接收的多文件类。
+ **filesDescribe: FilesDescribe**
```
说明：封装了接收到的String数据与文件数量。
```


+ **sender: String**
```
说明：发送端app包名。
```


+ **files: List<DSFile>**
```
说明：接收到的文件路径集合。
```


+ **taskId: long**
```
说明：任务Id。
```
---


##### **FilesDescribe类**：封装了接收到的String数据与文件数量。
+ **msg: String**
```
说明：接收到的String数据。
```


+ **fileCount: int**
```
说明：文件数量。
```
--->
