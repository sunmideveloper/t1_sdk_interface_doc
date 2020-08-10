## **Quick Integration**

-Import the jar package directly into the project or declare it in the build.gradle file under the app module of Android Studio
```
dependencies {
    compile'com.sunmi:DS_Lib:1.0.2'
    compile'com.google.code.gson:gson:2.6.2'//gson any version
}
```
-Configure the following declaration under the <application> node of the manifest file AndroidMainfest.xml
```
<application>
....
        <receiver
            android:name="sunmi.ds.MsgReceiver">
            <intent-filter>
                <action android:name="com.sunmi.hcservice"></action>
                <action android:name="com.sunmi.hcservice.status"></action>
            </intent-filter>
        </receiver>
</application>
```
-Initialize SDK
```
DSKernel mDSKernel = DSKernel.newInstance();
mDSKernel.init(context, mConnCallback);
mDSKernel.addReceiveCallback(mReceiveCallback);
```
-See Demo for examples:
*DSC: main screen program*
*DSD: Secondary screen program*

## **Send Type Supported by SDK**
-String type data (a string in json format, a piece of text information, etc...)
-Single file (file that already exists on the SD card)
-Single file + String type data
-Multiple files + String type data

## **Key Class Description**
<---
#### **Package sunmi.ds**
##### **DSKernel class**: SDK core class, this class exposes functions for sending data to the secondary screen and initializing the SDK
+ **newInstance(): DSKernel**
```
Description: Instantiate the static function of the DSKernel class.
```


+ **getDSDPackageName(): String**
```
Description: Get the static function of the app package name of the data received by the secondary screen.
Return value: packageName, the name of the App package responsible for receiving data on the secondary screen
```


+ **init(Context contetx, IConnectionCallback stateCallback, String vicePackageName): void**
```
Description: Initialize the SDK.
parameter:
contetx: android context object.
stateCallback: The connection state callback with the secondary screen.
vicePackageName: The name of the app package for receiving data on the secondary screen. If you pass a null or empty string, the default app that receives data is Sunmi's default secondary screen app.
```


+ **init(Context contetx, IConnectionCallback stateCallback): void**
```
Description: Initialize the SDK.
parameter:
contetx: android context object.
stateCallback: The connection state callback with the secondary screen.
```


+ **isConnected(): boolean**
```
Description: Determine whether the dual-screen communication connection is smooth
Return value: isConn true means unblocked, false means disconnected.
```


+ **sendData(DataPacket pack): void**
```
Description: Send data.
parameter:
pack: An object of DataPacket type that encapsulates the data to be sent.
```


+ **sendCMD(String recePackageName, String cmd, long fileId, ISendCallback callback): void**
```
Description: Send the CMD command package, you can specify the cache file id to be used.
parameter:
recePackName: Receiver package name.
cmd: command.
fileId: The id of the cache file to be used, if not, pass 0.
callback: Send result callback.
```


+ **sendCMD(DataPacket dataPacket): void**
```
Description: Send the CMD command package, you can specify the cache file id to be used.
parameter:
dataPacket: CMD type data packet.
```



+ **sendQuery(DataPacket mPack, QueryCallback callback): void**
```
Description: Send data, used when sending query data packet to the secondary screen.
Example: At the user protocol layer, the main screen sends a Query packet indicating the brightness of the secondary screen to the secondary screen, and the secondary screen App gets the brightness after receiving it and then calls it
The sendResult(long queryId) function sends back a result data packet carrying brightness to the main screen. The queryId of this Result data packet must be the same as that of the Query
If the taskId of the data packet is the same, the main screen can recognize the previous query result.
Note: When using QueryCallback to receive the result packet, the callback instance registered by addReceiveCallback() will not be called.
parameter:
pack: An object of DataPacket type that encapsulates the data to be sent.
callback Query result callback
```


+ **sendQuery(String recePackageName, String queryStr, ISendCallback sendCallback, QueryCallback callback): void**
```
Description: Send data, used when sending query data packet to the secondary screen.
Example: At the user protocol layer, the main screen sends a Query packet indicating the brightness of the secondary screen to the secondary screen, and the secondary screen App gets the brightness after receiving it and then calls it
The sendResult(long queryId) function sends back a result data packet carrying brightness to the main screen. The queryId of this Result data packet must be the same as that of the Query
If the taskId of the data packet is the same, the main screen can recognize the previous query result.
Note: When using QueryCallback to receive the result packet, the callback instance registered by addReceiveCallback() will not be called.
parameter:
recePackageName: Receiver package name.
queryStr: string data to be carried
sendCallback: Send result callback
callback: query result callback
```


+ **sendResult(String recePackageName, String resultStr, long queryId, ISendCallback sendCallback): void**
```
Description: Send the Result packet.
parameter:
recePackName: Receiver package name.
resultStr: query result.
queryId: taskId of the Query packet.
sendCallback: Send result callback.
```


+ **sendFile(String recePackName, String filePath, ISendCallback callback): long**
```
Description: Send files.
parameter:
recePackName: App package name received by the secondary screen.
filePath: file path.
callback: Send result callback.
Return value: taskId. When maintaining this task Id until the callback of successful sending is received, you can send instructions to the secondary screen to perform custom operations on the file, such as displaying pictures, opening files, etc.
Note: After the file is transferred to the secondary screen, it will be cached and form a mapping relationship with the returned taskId. If you want to reuse the file for a long time, you should maintain the taskId persistently.
```


+ **sendFile(String recePackName, String msg, String filePath, ISendCallback callback): long**
```
Description: Send a file + String type data
parameter:
recePackName: App package name received by the secondary screen.
msg: String type data, for example: a string in json format, a piece of text information, etc...
filePath: file path.
callback: Send result callback.
Return value: taskId. When maintaining this task Id until the callback of successful sending is received, you can send instructions to the secondary screen to perform custom operations on the file, such as displaying pictures, opening files, etc.
Note: After the file is transferred to the secondary screen, it will be cached and form a mapping relationship with the returned taskId. If you want to reuse the file for a long time, you should maintain the taskId persistently.
```


+ **sendFiles(String recePackName, String msg, List<String> files, ISendFilesCallback callback): long**
```
Description: Send multiple files + String type data
parameter:
recePackName: App package name received by the secondary screen.
msg: String type data, for example: a string in json format, a piece of text information, etc...
files: file path collection.
callback: callback of multi-file sending result.
Return value: taskId. When maintaining this task Id until the callback of successful sending is received, you can send instructions to the secondary screen to perform custom operations on the file, such as displaying pictures, opening files, etc.
Note: After the file is transferred to the secondary screen, it will be cached and form a mapping relationship with the returned taskId. If you want to reuse the file for a long time, you should maintain the taskId persistently.
```


+ **checkFileExist(long fileId, final ICheckFileCallback callback): void**
```
Description: Check whether the file corresponding to fileId exists on the secondary screen
parameter:
fileId: File Id.
callback: Check the result callback.
```



+ **addConnCallback(IConnectionCallback callback): void**
```
Description: Register a callback for listening connection, and you can register multiple ones.
parameter:
stateCallback: The connection state callback with the secondary screen.
```


+ **removeConnCallback(IConnectionCallback callback): void**
```
Description: Remove the callback for listening connection.
parameter:
stateCallback: The connection state callback with the secondary screen.
```


+ **checkConnection(): void**
```
Description: Detect the connection status with the secondary screen, if there is a result, the registered IConnectionCallback will be called back.
```


+ **addReceiveCallback(IReceiveCallback receiveCallback): void**
```
Description: Register data receiving callback, you can register multiple.
parameter:
receiveCallback: The callback interface used by the receiver to receive data from the sender.
```


+ **removeReceiveCallback(IReceiveCallback receiveCallback): void**
```
Description: Remove the data receiving callback.
parameter:
receiveCallback: The callback instance to be cancelled.
```
---
##### **FilesManager class**: persistently maintain and cache the received files, and cache DSFile and DSFiles with the taskId of the file sending task as the key.
+ **getFile(Long taskId): DSFile**
```
Description: Obtain files based on task ID.
parameter:
taskId: The task ID corresponding to the file.
```


+ **getFiles(Long taskId): DSFiles**
```
Description: Obtain multiple files based on the task ID.
parameter:
taskId: The task ID corresponding to the file.
```
--->
<---
#### **Package sunmi.ds.callback**
##### **IReceiveCallback class**: The callback interface when receiving data.
+ **onReceiveData(DSData data): void**
```
Description: Callback when data is received.
parameter:
data: received data
```


+ **onReceiveFile(DSFile file): void**
```
Description: The callback when a single file is received.
parameter:
file: the received file
```


+ **onReceiveFiles(DSFiles files): void**
```
Description: Callback when receiving >=1 file + a piece of String type data.
parameter:
files: received file + String data
```


+ **onReceiveCMD(DSData cmd): void**
```
Description: Callback when CMD type data is received.
parameter:
cmd: CMD type data received
```
---


##### **QueryCallback class**: the callback when the query result packet is received.
+ **onReceiveData(DSData data): void**
```
Description: The callback when the result packet is received.
parameter:
data: received data
```
---

##### **IConnectionCallback class**: The callback interface of the dual-screen communication connection status, all callback functions run in the child thread.
+ **onDisConnect(): void**
```
Description: Callback when the connection is disconnected.
```


+ **onConnected(ConnState state): void**
```
Description: Call back when the connection status is updated. The connection status is divided into 3 types: 1. The connection with the local service is normal, 2. The connection with the secondary screen service is normal (in this state, you can send data to the secondary screen), 3. The connection with the secondary screen APP is normal.
parameter:
state: connection state
```
---



##### **ICheckFileCallback class**: Check file result callback interface.
+ **onCheckFail(): void**
```
Description: Check failed (communication failure).
```


+ **onResult(boolean exist): boolean**
```
Description: Check failed (communication failure).
parameter:
exist: true means it exists, false means it does not exist.
```
---

##### **IConnectionCallback class**: The callback interface of the dual-screen communication connection status, all callback functions run in the child thread.
+ **onDisConnect(): void**
```
Description: Callback when the connection is disconnected.
```


+ **onConnected(ConnState state): void**
```
Description: Call back when the connection status is updated. The connection status is divided into 3 types: 1. The connection with the local service is normal, 2. The connection with the secondary screen service is normal (in this state, you can send data to the secondary screen), 3. The connection with the secondary screen APP is normal.
parameter:
state: connection state
```
---


##### **ISendCallback class**: The callback interface for sending String data or a single file, all callback functions run in child threads.
+ **onSendSuccess(long taskId): void**
```
Description: Callback when sending successfully.
taskId: task Id, corresponding to the sent data or file
```


+ **onSendFail(int errorId, String errorInfo): void**
```
Description: Callback when sending fails.
parameter:
errorId: Error identification code.
errorInfo: error description.
```


+ **onSendProcess(long totle, long sended): void**
```
Description: Callback of sending progress.
parameter:
totle: the total size of the data, unit: byte.
sented: the size of the sent data, unit: byte
```
---


##### **ISendFilesCallback class**: a callback interface for sending >=1 file + String data, all callback functions run in sub-threads.
+ **onAllSendSuccess(long fileId): void**
```
Description: Callback when multiple files are sent successfully.
fileId: The task Id is also the fileId corresponding to the file cached in the secondary screen.
```

+ **onSendSuccess(String path, long taskId): void**
```
Description: Callback when a certain file is sent successfully.
taskId: Task Id, sending multiple files belong to the same task, so the taskId is the same.
```


+ **onSendFail(int errorId, String errorInfo): void**
```
Description: Callback when sending String data fails, the file will not continue to be sent after String data sending fails.
parameter:
errorId: Error identification code.
errorInfo: error description.
```


+ **onSendFileFaile(String path, int errorId, String errorInfo): void**
```
Description: Callback when sending a file fails.
parameter:
path: The file path corresponding to the failed sending.
errorId: Error identification code.
errorInfo: error description.
```


+ **onSendProcess(String path, long totle, long sended): void**
```
Description: The progress callback of sending a certain file.
parameter:
path: the corresponding file path.
totle: the total size of the data, unit: byte.
sented: the size of the sent data, unit: byte
```
--->

<---
#### **Package sunmi.ds.data**
##### **DataPacket class**: Encapsulates sending data and sending callbacks, the parameter entity class required when calling the sendData(DataPacket pack) function of the DSKernel class.
---


##### **DataPacket.Builder class**: The builder class of DataPacket.
+ **Builder(DSData.DataType dataType)**
```
Description: Constructor.
parameter:
dataType: The type of data to be built.
```


+ **recPackName(String recPackName): Builder**
```
Description: Specify the app package name for receiving data.
parameter:
recPackName: The app package name that receives the data.
Return value: Builder instance
```


+ **data(String data): Builder**
```
Description: Specify the data to be sent.
parameter:
data: String data to be sent.
Return value: Builder instance
```


+ **taskId(long taskId): Builder**
```
Note: Specify the task Id, if not specified, it will be automatically generated.
parameter:
taskId: Task Id.
Return value: Builder instance
```


+ **fileId(long fileId): Builder**
```
Description: Specify the cache file Id.
parameter:
fileId: cache file Id.
Return value: Builder instance
```


+ **queryId(long queryId): Builder**
```
Description: Specify the Id of the Query packet.
parameter:
fileId: Id of the Query packet.
Return value: Builder instance
```



+ **isReport(boolean isReport): Builder**
```
Description: Specify whether the result callback is required.
parameter:
isReport: Whether the result callback is required.
Return value: Builder instance
```


+ **addCallback(ISendCallback callback): Builder**
```
Description: Set the callback instance of the sending result.
parameter:
callback: The callback instance of the sending result.
Return value: Builder instance
```


+ **build(): DataPacket**
```
Description: Build a DataPacket instance.
Return value: DataPacket instance
```
---


##### **DSData class**: Data package for dual-screen communication.
+ **sender: String**
```
Description: The sender app package name.
```


+ **taskId: long**
```
Description: Task Id.
```


+ **fileId: long**
```
Description: Cache file Id.
```


+ **queryId: long**
```
Description: Task Id of the Query packet.
```


+ **dataType: DataType**
```
Description: Data type.
```


+ **data: String**
```
Description: The data to be sent.
```
---


##### **DSData.DataType enumeration class**: Data type description package.
+ **DATA: DataType**
```
Description: Represents data.
```


+ **FILE: DataType**
```
Description: Represents a file.
```


+ **CMD: DataType**
```
Description: Indicates a command.
```


+ **typeCode: int**
```
Description: Type code.
```
---


##### **DSFile class**: the file class received by the receiver.
+ **sender: String**
```
Description: The sender app package name.
```


+ **path: String**
```
Description: The received file path.
```


+ **taskId: long**
```
Description: Task Id.
```
---


##### **DSFiles class**: Multi-file class received by the receiver.
+ **filesDescribe: FilesDescribe**
```
Description: encapsulates the number of String data and files received.
```


+ **sender: String**
```
Description: The sender app package name.
```


+ **files: List<DSFile>**
```
Description: The received file path collection.
```


+ **taskId: long**
```
Description: Task Id.
```
---


##### **FilesDescribe class**: encapsulates the received String data and the number of files.
+ **msg: String**
```
Description: String data received.
```


+ **fileCount: int**
```
Description: Number of files.
```
--->
