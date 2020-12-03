---
  topics:
  - title: Creating Outgoing Call
    body: In this section you will learn how to start an outgoing call using $KANDY$ Mobile SDK.
  - title: Receiving Incoming Call
    body: In this section you will learn how to receive an incoming call using $KANDY$ Mobile SDK.
  - title: Managing Call Events
    body: In this section you will learn how to manage call events using $KANDY$ Mobile SDK.
  - title: Anonymous Calls
    body: In this section you will learn how to establish anonymous calls using $KANDY$ Mobile SDK.
---
# Voice and Video Calls

Voice and Video calls are managed by the Call Service which can be called from `CPaaS` instance. In order to receive and send events, `CPaaS` instance should be connected first. To see how to connect and set configurations, check **User Login** and **Configurations** sections.

Note that, In order to be able to call PSTN destinations, your user/project should have at least one telephone number assigned. For WebRTC destinations telephone number assignment is not required.

## Creating Outgoing Call

**WARNING:** Before creating an outgoing call, it is completely the application developer's responsibility to check if the related permissions are granted or not. If not granted, call should not be created in order to prevent crashes.

For Audio Call, `android.permission.RECORD_AUDIO` permission should be granted.
For Video Call, `android.permission.RECORD_AUDIO` and `android.permission.CAMERA` permissions should be granted.

<!-- tabs:start -->

#### ** Java Code **

```java
public class PermissionHelper{

  private static int REQUEST_WRITE_STORAGE_REQUEST_CODE = 100;
  private static String[] audioCallPermission = {Manifest.permission.RECORD_AUDIO};
  private static String[] videoCallPermissions = {Manifest.permission.CAMERA,
                                                    Manifest.permission.RECORD_AUDIO};
public static boolean hasAudioCallPermission(Context context){
        return (ContextCompat.checkSelfPermission(context,Manifest.permission.RECORD_AUDIO) == PackageManager.PERMISSION_GRANTED);
    }

public static boolean hasVideoCallPermissions(Context context){
        return (ContextCompat.checkSelfPermission(context,Manifest.permission.RECORD_AUDIO) == PackageManager.PERMISSION_GRANTED)
                && (ContextCompat.checkSelfPermission(context,Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED);
    }
public static void requestAudioCallPermission(Activity activity){
        if (android.os.Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
            return;
        }

        if (hasAudioCallPermission(activity)) {
            return;
        }

        ActivityCompat.requestPermissions(activity,
                audioCallPermission, REQUEST_WRITE_STORAGE_REQUEST_CODE); // your request code
    }


public static void requestVideoCallPermissions(Activity activity){
        if (android.os.Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
            return;
        }

        if (hasVideoCallPermissions(activity)) {
            return;
        }

        ActivityCompat.requestPermissions(activity,
                videoCallPermissions, REQUEST_WRITE_STORAGE_REQUEST_CODE); // your request code
    }
}    
```

```kotlin
class PermissionHelper {
        private val REQUEST_WRITE_STORAGE_REQUEST_CODE = 100
        private val audioCallPermission =
            arrayOf(Manifest.permission.RECORD_AUDIO)
        private val videoCallPermissions = arrayOf(
            Manifest.permission.CAMERA,
            Manifest.permission.RECORD_AUDIO
        )

        fun hasAudioCallPermission(context: Context): Boolean {
            return ContextCompat.checkSelfPermission(
                context,
                Manifest.permission.RECORD_AUDIO
            ) == PackageManager.PERMISSION_GRANTED
        }

        fun hasVideoCallPermissions(context: Context): Boolean {
            return (ContextCompat.checkSelfPermission(
                context,
                Manifest.permission.RECORD_AUDIO
            ) == PackageManager.PERMISSION_GRANTED
                && ContextCompat.checkSelfPermission(
                context,
                Manifest.permission.CAMERA
            ) == PackageManager.PERMISSION_GRANTED)
        }

        fun requestAudioCallPermission(activity: Activity) {
            if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
                return
            }
            if (hasAudioCallPermission(activity)) {
                return
            }
            ActivityCompat.requestPermissions(
                activity,
                audioCallPermission, REQUEST_WRITE_STORAGE_REQUEST_CODE
            ) // your request code
        }

        fun requestVideoCallPermissions(activity: Activity) {
            if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
                return
            }
            if (hasVideoCallPermissions(activity)) {
                return
            }
            ActivityCompat.requestPermissions(
                activity,
                videoCallPermissions, REQUEST_WRITE_STORAGE_REQUEST_CODE
            ) // your request code
        }
}
```

<!-- tabs:end -->

You can create **OutgoingCall** object by calling **createOutgoingCall** method in Call Service.

<!-- tabs:start -->

#### ** Java Code **

```java
callService.createOutgoingCall(callee, new OutgoingCallCreationCallback() {
            @Override
            public void callCreated(OutgoingCallInterface callInteface) {
                // Call successfully created, use the CallInterface object to process the call.
  			    callInterface.setRemoteVideoView(remoteVideoView); // Provide a VideoView widget object to show the local video on the UI
  			    callInterface.setLocalVideoView(localVideoView); // Provide a VideoView widget object to show the local video on the UI

                // For Video call
  			    callInterface.establishCall(true);

  			    // For Double M-Line Audio Call
  			    callInterface.establishCall(false);

  			    // For Single M-Line Audio Call
  			    callInterface.establishAudioCall();
            }

            @Override
            public void callCreationFailed(MobileError error) {
                // Call creation failed, handle the exception
                Log.e(TAG, "callCreationFailed: " + error.getErrorMessage());
            }
        });
```

#### ** Kotlin Code **

```kotlin

callService.createOutgoingCall(callee,object:OutgoingCallCreationCallback{
               override fun callCreated(callInterface: OutgoingCallInterface?) {
                   // Call successfully created, use the CallInterface object to process the call.
                   callInterface?.setRemoteVideoView(remoteVideoView) // Provide a VideoView widget object to show the local video on the UI
                   callInterface?.setLocalVideoView(localVideoView) // Provide a VideoView widget object to show the local video on the UI

                   // For Video call
                   callInterface?.establishCall(true)

                   // For Double M-Line Audio Call
                   callInterface?.establishCall(false)

                   // For Single M-Line Audio Call
                   callInterface?.establishAudioCall()
               }

               override fun callCreationFailed(error: MobileError?) {
                   // Call creation failed, handle the exception
                   Log.e(TAG, "callCreationFailed: " + error?.errorMessage)
               }

           })
```
<!-- tabs:end -->

You need to get `callId` to use call events. You can get `callId` by calling `callInterface.getId()` inside the success block of the `OutgoingCallCreationCallback`.

## Receiving Incoming Call

When $KANDY$ Mobile SDK receives an incoming call, it calls `incomingCall` method in the `CallApplicationListener`. Thus, `CallApplicationListener` should be set to the class where managed. Now, the application should receive incoming call events if any.

<!-- tabs:start -->

#### ** Java Code **

```java
//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view
public class Application implements CallApplicationListener {
  ...
}

Application application = ...;
callService.setCallApplicationListener(application);
```

#### ** Kotlin Code **

```kotlin
//implementing class should not be a disposable UI component, If so some notifications might be missed from app point of view
class Application : CallApplicationListener {
  ...
}

val application: Application = ...
callService.setCallApplicationListener(application)
}
```
<!-- tabs:end -->

### Managing Incoming Call

When `incomingCall` method is notified with the received call, call can be accepted with videoEnabled option, rejected or ignored. Application will be informed about success or fail via the callback methods on `CallApplicationListener` which are related to the operation.

#### Accepting Incoming Call

An incoming call can be accepted by calling the `acceptCall` method on the incoming call instance which is received from `CallApplicationListener`.

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.call.IncomingCall;

private void acceptIncomingCall() {
	IncomingCall incomingCall = // get from the CallApplicationListener with the callId
	// For video call
	incomingCall.setRemoteVideoView(remoteVideoView);
	incomingCall.setLocalVideoView(localVideoView);
	incomingCall.acceptCall(true);

    // For audio call
	incomingCall.acceptCall(false);
}
```

#### ** Kotlin Code **


```kotlin
import com.rbbn.cpaas.mobile.call.IncomingCall

private fun acceptIncomingCall() {
    val incomingCall: IncomingCall = // get from the CallApplicationListener with the callId
    // For video call
    incomingCall.setRemoteVideoView(remoteVideoView)
    incomingCall.setLocalVideoView(localVideoView)
    incomingCall.acceptCall(true)
    
    // For audio call
    incomingCall.acceptCall(false);

```
<!-- tabs:end -->

#### Rejecting Incoming Call

An incoming call can be rejected by calling the `rejectCall` method on the incoming call instance.

<!-- tabs:start -->

#### ** Java Code **

```java
private void rejectIncomingCall() {
	IncomingCall incomingCall = // get from the CallApplicationListener with the callId
	incomingCall.rejectCall();
}
```

#### ** Kotlin Code **

```kotlin
private fun rejectIncomingCall(){
    val incomingCall: IncomingCall = // get from the CallApplicationListener with the callId
    incomingCall.rejectCall()
}
```
<!-- tabs:end -->

#### Ignoring Incoming Call

An incoming call can be ignored by calling the `ignoreCall` method on the incoming call instance.

<!-- tabs:start -->

#### ** Java Code **

```java
private void ignoreIncomingCall() {
	IncomingCall incomingCall = // get from the CallApplicationListener with the callId
	incomingCall.ignoreCall();
}
```

#### ** Kotlin Code **

```kotlin
private fun ignoreIncomingCall(){
    val incomingCall: IncomingCall = // get from the CallApplicationListener with the callId
    incomingCall.ignoreCall()
}
```
<!-- tabs:end -->

#### Forwarding Incoming Call

An incoming call can be forwarded to another user by calling the `forwardCall` method on the incoming call instance. After a successful invokation of the forward operation, call will be ended on the client which invoked the operation, the user whose address is given to forward operation will receive an incoming call from the originator side.

<!-- tabs:start -->

#### ** Java Code **

```java
private void forwardIncomingCall() {
  IncomingCall incomingCall = // get from the CallApplicationListener with the callId
  String targetAddress = // address of the user to whom the call is forwarded
  incomingCall.forwardCall(targetAddress);
}
```

#### ** Kotlin Code **

```kotlin
private fun forwardIncomingCall(){
    val incomingCall: IncomingCall = // get from the CallApplicationListener with the callId
    val targetAddress = // address of the user to whom the call is forwarded
    incomingCall.forwardCall(targetAddress)
}
```
<!-- tabs:end -->

## Managing Call Events

Methods implemented in the `CallApplicationListener` are notified with the events occurred during the call, or `callStatusChanged` when state of the call is changed. By listening these methods, call events can be managed directly. To see all methods implemented in the `CallApplicationListener`, see API Reference Documents.

<!-- tabs:start -->

#### ** Java Code **

```java
	@Override
    public void callStatusChanged(CallInterface callInterface, CallState callState) {
        Log.d(TAG, "callStatusChanged: Call state is: " + callState.getType() + " call id is: " + callInterface.getId());
        // Now you can broadcast and process the state of the specific call with its unique ID.
    }
```

#### ** Kotlin Code **

```kotlin
override fun callStatusChanged(callInterface: CallInterface?, callState: CallState?) {
    Log.d(TAG, "callStatusChanged: Call state is: " + callState?.type + " call id is: " + callInterface?.id);
    // Now you can broadcast and process the state of the specific call with its unique ID.
}
```
<!-- tabs:end -->

### Starting Call Events

Both incoming call and outgoing call objects are inherited from `CallInterface`. Except particular properties and methods defined in incoming call and outgoing call classes, all methods and properties defined in `CallInterface` are suitable for both call type.

Note that, an application cannot start an event while any other operation is still in progress.

Furthermore, when a mid-call operation finishes which is called by the user, its *success* or *fail* method is triggered depending on result of the operation.

#### To end call

To end an incoming or outgoing call, `endCall` method on call instance can be called.

<!-- tabs:start -->

#### ** Java Code **

```java
call.endCall();
```

#### ** Kotlin Code **

```kotlin
call.endCall()
```
<!-- tabs:end -->

#### To hold or unhold call

While in a call, a participant may be placed on hold by calling `holdCall` method. When operation succeeds, media transfer between participants stops, and call state will change to `On Hold` state. Remote participant will see this call session in `Remotely Held` state.

To resume to the call, `unholdCall` method should be called. Note that, these operations take some time, thus listening operation results from `CallApplicationListener`, and acting accordingly is recommended.

Note that a call already in `On Hold` state may not be placed on hold. Similarly, attempting to `unhold` a call that is not in `On Hold` state will fail.

<!-- tabs:start -->

#### ** Java Code **

```java
call.holdCall();
call.unholdCall();
```

#### ** Kotlin Code **

```kotlin
call.holdCall()
call.unholdCall()
```
<!-- tabs:end -->

#### To start or stop sending video

<!-- tabs:start -->

#### ** Java Code **

```java
call.videoStart();
call.videoStop();
```

#### ** Kotlin Code **

```kotlin
call.videoStart()
call.videoStop()
```
<!-- tabs:end -->

#### To mute or unmute call

To stop sending audio from the microphone, `mute` method can be called. Until unmuting the call, participants cannot hear the voice from the device.

<!-- tabs:start -->

#### ** Java Code **

```java
call.mute();
call.unMute();
```

#### ** Kotlin Code **

```kotlin
call.mute()
call.unMute()
```
<!-- tabs:end -->

## Anonymous Calls

$KANDY$ offers to make voice and video calls even if the application user does not have a $KANDY$ account to login. This functionality is called as Anonymous Call on $KANDY$ system. Anonymous call feature can be useful for applications such as customer service support. Since the application user does not have a $KANDY$ account, only an outgoing call can be made to a registered $KANDY$ user. An anonymous user cannot receive and incoming call with this feature.

Please refer to [Anonymous Calls](/developer/quickstarts/rest-api/voice-and-video-calls#anonymous-calls) section to learn about how to implement anonymous calls solution.

### Creating an Anonymous Call

A server-side component is suggested to be used for logging in to $KANDY$ along with Mobile SDK for an anonymous call scenario. Once the application establishes the login, application should be able create outgoing calls using the Call Service on $KANDY$ Mobile SDK. Creating an anonymous outgoing call follows the same procedure as if it is a regular outgoing call. The main difference is an anonymous user cannot receive an incoming call, so application will not receive and incoming call notification on $KANDY$ Mobile SDK.

## Advanced Usage of Call Service

### Retrieve Audio and Video RTP/RTCP Statistics

$KANDY$ can retrieve audio and video RTP/RTCP statistics providing information including:

* Number of packets lost
* Number of packets sent/received
* Number of bytes sent/received
* Call Jitter received
* RTT (round trip delay)
* Local/Remote network addresses and ports
* Audio/Video codec names

**Note:** $KANDY$ does not keep the statistics after the call ends.It is application developer's responsibility to keep them. Since the statistics could be too long to log due to character limitation, it is adviced to write them to a file instead of logging for further usage. You can use the following class to see how to write it to a file :

<!-- tabs:start -->

#### ** Java Code **

```java
public class CallStatisticsHelper {

    private static final String TAG = "CallStatisticsHelper";

    private static File callStatisticsFile;

    public static void clearFile() {
        //clear the CPaaSAndroidCallStatistics.txt first
        File appDirectory = new File(Environment.getExternalStorageDirectory() + "/CPaaSSDKDemoApp");
        File statisticsDirectory = new File(appDirectory + "/statistics");
        File statisticsFile = new File(statisticsDirectory, "CPaaSSDKAndroidCallStatistics" + ".txt");

        PrintWriter writer;
        try {
            writer = new PrintWriter(statisticsFile);
            writer.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }

        Log.i(TAG, "Call Statistics file cleaned.");

    }

    public static void saveStatistics(String data) {
        BufferedWriter bufferedWriter = null;
        if (isExternalStorageWritable()) {

            File appDirectory = new File(Environment.getExternalStorageDirectory() + "/CPaaSSDKDemoApp");
            File statisticsDirectory = new File(appDirectory + "/statistics");
            callStatisticsFile = new File(statisticsDirectory, "CPaaSSDKAndroidCallStatistics" + ".txt");

            //create app folder
            if (!appDirectory.exists()) {
                appDirectory.mkdir();
            }

            //create statistics folder
            if (!statisticsDirectory.exists()) {
                statisticsDirectory.mkdir();
            }

            if (!callStatisticsFile.exists()) {
                try {
                    callStatisticsFile.createNewFile();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            try {
                FileWriter fileWriter = new FileWriter(callStatisticsFile, true);
                bufferedWriter = new BufferedWriter(fileWriter);
                bufferedWriter.write(data + "\n");
                bufferedWriter.flush();
                Log.i(TAG, "Statistics have been written to the file successfully.");
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (bufferedWriter != null)
                        bufferedWriter.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        } else if (isExternalStorageReadable()) {
            // only readable
            Log.i(TAG, "External storage is not writable, it's only readable. Writing statistics to file is failed.");
        } else {
            //not accessible
            LogManager.log(Constants.LogLevel.TRACE, TAG, "External storage is not accessible. Writing statistics to file is failed.");
        }
    }

    public static File getCallStatisticsFile() {
        return callStatisticsFile;
    }

    /* Checks if external storage is available for reading and writing */
    private static boolean isExternalStorageWritable() {
        String state = Environment.getExternalStorageState();
        return Environment.MEDIA_MOUNTED.equals(state);
    }

    /* Checks if external storage is available for at least reading */
    private static boolean isExternalStorageReadable() {
        String state = Environment.getExternalStorageState();
        return Environment.MEDIA_MOUNTED.equals(state) ||
                Environment.MEDIA_MOUNTED_READ_ONLY.equals(state);
    }
}
```

#### ** Kotlin Code **

```kotlin
object CallStatisticsHelper {
    private const val TAG = "CallStatisticsHelper"
    var callStatisticsFile: File? = null
        private set

    fun clearFile() {
        //clear the CPaaSAndroidCallStatistics.txt first
        val appDirectory = File(Environment.getExternalStorageDirectory().toString() + "/CPaaSSDKDemoApp")
        val statisticsDirectory = File("$appDirectory/statistics")
        val statisticsFile = File(statisticsDirectory, "CPaaSSDKAndroidCallStatistics" + ".txt")
        val writer: PrintWriter
        try {
            writer = PrintWriter(statisticsFile)
            writer.close()
        } catch (e: FileNotFoundException) {
            e.printStackTrace()
        }
        Log.i(TAG, "Call Statistics file cleaned.")
    }

    fun saveStatistics(data: String) {
        var bufferedWriter: BufferedWriter? = null
        when {
            isExternalStorageWritable -> {
                val appDirectory = File(
                    Environment.getExternalStorageDirectory().toString() + "/CPaaSSDKDemoApp"
                )
                val statisticsDirectory = File("$appDirectory/statistics")
                callStatisticsFile = File(statisticsDirectory, "CPaaSSDKAndroidCallStatistics" + ".txt")

                //create app folder
                if (!appDirectory.exists()) {
                    appDirectory.mkdir()
                }

                //create statistics folder
                if (!statisticsDirectory.exists()) {
                    statisticsDirectory.mkdir()
                }
                if (!callStatisticsFile!!.exists()) {
                    try {
                        callStatisticsFile!!.createNewFile()
                    } catch (e: IOException) {
                        e.printStackTrace()
                    }
                }
                try {
                    val fileWriter = FileWriter(callStatisticsFile, true)
                    bufferedWriter = BufferedWriter(fileWriter)
                    bufferedWriter.write(data + "\n")
                    bufferedWriter.flush()
                    Log.i(TAG, "Statistics have been written to the file successfully.")
                    
                } catch (e: IOException) {
                    e.printStackTrace()
                } finally {
                    try {
                        bufferedWriter?.close()
                    } catch (e: IOException) {
                        e.printStackTrace()
                    }
                }
            }
            isExternalStorageReadable -> {
                // only readable
                Log.i(TAG, "External storage is not writable, it's only readable. Writing statistics to file is failed.")
            }
            else -> {
                //not accessible
                Log.i(TAG,"External storage is not accessible. Writing statistics to file is failed." )
            }
        }
    }

    /* Checks if external storage is available for reading and writing */
    private val isExternalStorageWritable: Boolean
        get() {
            val state = Environment.getExternalStorageState()
            return Environment.MEDIA_MOUNTED == state
        }

    /* Checks if external storage is available for at least reading */
    private val isExternalStorageReadable: Boolean
        get() {
            val state = Environment.getExternalStorageState()
            return Environment.MEDIA_MOUNTED == state || Environment.MEDIA_MOUNTED_READ_ONLY == state
        }
}
```
<!-- tabs:end -->


Use the "getRTPStatistics" method in an Call object to retrieve a string in the JSON format containing RTP/RTCP statistics. The JSON-String includes objects of the RTCStatsReport class—a class which stores statistic details. This class has the following public variables:


 * timestamp -- Indicates the time at which the sample was taken for this statistics object.
 * type      -- Indicates the type of statistics the object contains. Types are listed below.
 * id        -- Uniquely identifies the object.


 ```javascript
type {
    "codec",            
    "inbound-rtp",
    "outbound-rtp",
    "remote-inbound-rtp",
    "remote-outbound-rtp",
    "media-source",
    "csrc",
    "peer-connection",
    "data-channel",
    "stream",
    "track",
    "transceiver",
    "sender",
    "receiver",
    "transport",
    "sctp-transport",
    "candidate-pair",
    "local-candidate",
    "remote-candidate",
    "certificate",
    "ice-server"
}
```

#### Example: Retrieving statistics

It is recommended to call this method every 10 seconds as long as call continues.

<!-- tabs:start -->

#### ** Java Code **

```java
currentCall.getRTPStatistics(new RTPStatisticsHandler() {
    @Override
    public void onReportReceived(String statReport) {
       CallStatisticsHelper.saveStatistics(statReport);              
    }
});
```

#### ** Kotlin Code **

```kotlin
currentCall?.getRTPStatistics {
            CallStatisticsHelper.saveStatistics(it)
        }
```
<!-- tabs:end -->



