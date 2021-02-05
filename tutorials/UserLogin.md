# User Login

To receive events or start $KANDY$ services, user login should be completed successfully. Events receives to $KANDY$ Mobile SDK by websocket or push channel and all requests need authorization, thus when **Authentication Token** is gathered, keep it safe. Each time the application wakes up, user login must connect websocket.

A connection can be established with different methods. How to use each method is explained and shown below. Channel lifetime is specified with the lifetime value. $KANDY$ Mobile SDK extends the lifetime automatically, however that doesn't guarantee connection will last indefinitely. Note that, server related configurations should be set before calling connect.

When a connection is established, SDK uses the given Authentication Token to communicate with services. If the Authentication Token is invalidated or expired, service requests will fail. In these cases, **Authentication Token** should be renewed with the valid one by watching connection status changes.

## Application context

Mobile SDK needs to know about the application context. Application developers should set the application context to Mobile SDK, even before setting the configuration. Best place to do this is the "Application" structure of Android. Application developers should extend this and set the application context to Mobile SDK when application is created.

### Example: Setting application context

<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.utilities.Globals;

public class ExampleApplication extends Application {

    public void onCreate() {
        super.onCreate();

        Context context = getApplicationContext();
        Globals.setApplicationContext(context);
    }
}
```

#### ** Kotlin Code **

```kotlin
import android.app.Application
import com.rbbn.cpaas.mobile.utilities.Globals

class ExampleApplication:Application() {

    override fun onCreate() {
        super.onCreate()
        Globals.setApplicationContext(applicationContext)
    }
}
```
<!-- tabs:end -->

## Configurations

The first step for any application that will use the $KANDY$ Mobile SDK is setting the configurations. When doing this, you can customize certain features by providing a configurations object. All feature configurations can be set with instance of the `Configurations` object. Developers should set server related configurations before using any functionality of the SDK and all other customizations are optional.Note that,config should be set before creating a service provider.

### Base URL

This is the API Marketplace HTTPS entry point that you will use for authentication, REST services and WebSocket notifications.

```
$KANDYFQDN$
```

### ICE Servers

Use these primary and secondary URIs as the ICE Servers in the JavaScript, iOS or Android SDKs configuration when connecting and making calls. This is needed in order to ensure that calls can be established even the call peers are on different networks, behind firewalls. When the ICE server connects, it will try the Primary URL first. If that fails, it will try the Secondary URL.

##### Primary URL:

```
$KANDYTURN1$

$KANDYSTUN1$
```

##### Secondary URL:

```
$KANDYTURN2$

$KANDYSTUN2$
```
<!-- tabs:start -->

#### ** Java Code **

```java
import com.rbbn.cpaas.mobile.utilities.Configuration;
import com.rbbn.cpaas.mobile.utilities.webrtc.ICEServers;

Configuration configuration = Configuration.getInstance();
configuration.setRestServerUrl("$KANDYFQDN$");

// Setting ICE Servers
ICEServers iceServers = new ICEServers();
iceServers.addICEServer("$KANDYTURN1$");
iceServers.addICEServer("$KANDYTURN2$");
iceServers.addICEServer("$KANDYSTUN1$");
iceServers.addICEServer("$KANDYSTUN2$");
configuration.setICEServers(iceServers);
```

#### ** Kotlin Code **

```kotlin
import com.rbbn.cpaas.mobile.utilities.Configuration
import com.rbbn.cpaas.mobile.utilities.webrtc.ICEServers

Configuration.getInstance().restServerUrl = "$KANDYFQDN$"

//Setting ICE Servers
val iceServers = ICEServers()
iceServers.addICEServer("$KANDYTURN1$")
iceServers.addICEServer("$KANDYTURN2$")
iceServers.addICEServer("$KANDYSTUN1$")
iceServers.addICEServer("$KANDYSTUN2$")
Configuration.getInstance().iceServers = iceServers
```
<!-- tabs:end -->

#### Capturing Logs and Troubleshooting Problems

The log level configs are used to change the severity of logging output from $KANDY$ Mobile SDK. This allows for more logged messages, such as debug information, warnings, and errors, which can help to explain what SDK is doing.

$KANDY$ Mobile SDK also provides application developers to set their customized logger implementation into Mobile SDK.

<!-- tabs:start -->

#### ** Java Code **

```java
class CustomizedLogger implements Logger {
    @Override
    public void log(LogLevel loglevel, String tag, String message) {
        // a customized implementation
    }

    @Override
    public void log(LogLevel loglevel, String tag, String message, Exception ex) {
        // a customized implementation
    }
}

Configuration configuration = Configuration.getInstance();
// Set the log level to 'TRACE', it's the default option and you can prefer using more or less verbose LogLevels.
configuration.setLogLevel(LogLevel.TRACE);
configuration.setLogger(new CustomizedLogger());
```

#### ** Kotlin Code **

```kotlin
class CustomizedLogger: Logger {

    override fun log(logLevel: LogLevel?, tag: String?, message: String?) {
        // a customized implementation
    }

    override fun log(logLevel: LogLevel?, tag: String?, message: String?, ex: Exception?) {
        // a customized implementation
    }
}

// Set the log level to 'TRACE', it's the default option and you can prefer using more or less verbose LogLevels.
Configuration.getInstance().logLevel = LogLevel.TRACE
Configuration.getInstance().logger = CustomizedLogger()
```
<!-- tabs:end -->

##### Log Levels
###### Trace_WebRTC
The application developer should use this level to view all webrtc and SDK logs.
###### Trace
This is really fine-grained information. When you're at this level, you're basically looking to capture every detail you possibly can about the application's behavior. Think of this level as the method you should use for all the logging you will do while tracking a specific problem and most likely get rid of it once you find the problem.

App developer should use this level to view all messages on the sdk side. The logs coming from this level will lead to the solution of the SDK error. However, it should be noted that webrtc logs will not be displayed at this level.
###### Info
Info messages correspond to normal application behavior. They provide the skeleton of what happened. A service started or stopped. The notification engine started, you got a new message or network status changed. Think of it as all the messages you'd like to see in the log when creating call for example, or in other words - anything that might help you resolve or at least identify a problem without looking in the source code.

Logs at this level can either solve the problem or have ideas to solve the problem, but this is not guaranteed. It contains less information than the trace level.However, it should be noted that webrtc logs will not be displayed at this level.
###### Warning
Use this log level to indicate that you might have a problem and that you've detected an unusual situation. Maybe you were trying to invoke a service and it failed a couple of times before connecting on an automatic retry or you were trying to start a service which already running. It's unexpected and unusual, but no real harm done, and it's not known whether the issue will persist or recur. The application can tolerate warning messages, but they should always be justified and examined.

This level can be use to see errors and warnings from Sdk. Developer can view where the error is, but may not be able to find root cause of an error.Webrtc logs will not be displayed at this level
###### Error
An error is a serious issue and represents the failure of something important going on in your application. No system can tolerate items logged on this level. Maybe you've got something like dropped WebSocket connections or the inability to access a service.

This level can be use to see only errors from Sdk. Developer can view where the error is, but may not be able to find root cause of an error.Webrtc logs will not be displayed at this level

##### Storing logs
Our recommendation is to store the logs in memory and provide the ability to send them log file over e-mail to our support team if issues are encountered.Please check our sample below.

You can store logs like :

<!-- tabs:start -->

#### ** Java Code **

```java
public class LogHelper {
    public static File logFile;

    public static void saveLog() {
        if (isExternalStorageWritable()) {

            File appDirectory = new File(Environment.getExternalStorageDirectory() + "/CPaaSDemoApp");
            File logDirectory = new File(appDirectory + "/log");
            LogHelper.logFile = new File(logDirectory, "CPaaSAndroidLogs" + ".txt");

            // create app folder
            if (!appDirectory.exists()) {
                appDirectory.mkdir();
            }

            // create log folder
            if (!logDirectory.exists()) {
                logDirectory.mkdir();
            }

            try {
                Process process = Runtime.getRuntime().exec("logcat -c");
                process = Runtime.getRuntime().exec("logcat -f " + LogHelper.logFile);
            } catch (IOException e) {
                e.printStackTrace();
            }

        } else if (isExternalStorageReadable()) {
            // only readable
        } else {
            // not accessible
        }
    }

    /* Checks if external storage is available for read and write */
    public static boolean isExternalStorageWritable() {
        String state = Environment.getExternalStorageState();
        return Environment.MEDIA_MOUNTED.equals(state);
    }

    /* Checks if external storage is available to at least read */
    public static boolean isExternalStorageReadable() {
        String state = Environment.getExternalStorageState();
        return Environment.MEDIA_MOUNTED.equals(state) ||
                Environment.MEDIA_MOUNTED_READ_ONLY.equals(state);
    }
}
```

#### ** Kotlin Code **

```kotlin
object LogHelper {
    var logFile: File? = null
    
    fun saveLog() {
        when {
            isExternalStorageWritable -> {
                val appDirectory =
                    File(Environment.getExternalStorageDirectory().toString() + "/CPaaSDemoApp")
                val logDirectory = File("$appDirectory/log")
                logFile = File(logDirectory, "CPaaSAndroidLogs" + ".txt")

                // create app folder
                if (!appDirectory.exists()) {
                    appDirectory.mkdir()
                }

                // create log folder
                if (!logDirectory.exists()) {
                    logDirectory.mkdir()
                }
                try {
                    var process = Runtime.getRuntime().exec("logcat -c")
                    process = Runtime.getRuntime().exec("logcat -f $logFile")
                } catch (e: IOException) {
                    e.printStackTrace()
                }
            }
            isExternalStorageReadable -> {
                // only readable
            }
            else -> {
                // not accessible
            }
        }
    }

    /* Checks if external storage is available for read and write */
    private val isExternalStorageWritable: Boolean
        get() {
            val state = Environment.getExternalStorageState()
            return Environment.MEDIA_MOUNTED == state
        }

    /* Checks if external storage is available to at least read */
    private val isExternalStorageReadable: Boolean
        get() {
            val state = Environment.getExternalStorageState()
            return Environment.MEDIA_MOUNTED == state || Environment.MEDIA_MOUNTED_READ_ONLY == state
        }
}
```
<!-- tabs:end -->

## connect(String idToken, int lifetime, ConnectionCallback callback)

Establishes a connection for the user with given ID Token, which will last until the time given with lifetime is elapsed.Getting access and id token is explained in [**Getting Access and Id Token from $KANDY$**](GetStarted.md#getting-access-and-id-token-from-$KANDY$) section in detail.

Authentication needs access token in order to get and establish Websocket subscription. So before using this method, access token should be given to the Authentication with calling setToken method.

<!-- tabs:start -->

#### ** Java Code **

```java
 try{
    cPaaS.getAuthentication().setToken(YOUR_ACCESS_TOKEN);
    cpaas.getAuthentication().connect(YOUR_ID_TOKEN, 3600, new ConnectionCallback() {
        public void onSuccess(String connectionToken) {
            Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
        }

        public void onFail(MobileError error) {
            Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
        }
    });
 } catch (MobileException e) {
     ...       
 }
```

#### ** Kotlin Code **

```kotlin
try {
    cpaas.authentication.setToken(YOUR_ACCESS_TOKEN)
    cpaas.authentication.connect(YOUR_ID_TOKEN,3600,object:ConnectionCallback{
        override fun onSuccess(connectionToken: String?) {
            Log.i("CPaaS Authentication","Connected to websocket successfully")
        }

        override fun onFail(error: MobileError?) {
            Log.i("CPaaS Authentication","Connection to websocket failed")
        }

    })
} catch (e:MobileException){
    ...
}
```
<!-- tabs:end -->

## connect(String idToken, String accessToken, int lifetime, ConnectionCallback callback)

Establishes a connection for the user with given ID Token, which will last until the time given with lifetime is elapsed, using given accessToken. accessToken will be set internally and then connection will be established just like the connect method in (a).

<!-- tabs:start -->

#### ** Java Code **

```java
 try{
    cpaas.getAuthentication().connect(YOUR_ID_TOKEN, YOUR_ACCESS_TOKEN, 3600, new ConnectionCallback() {
        public void onSuccess(String connectionToken) {
                Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
        }

        public void onFail(MobileError error) {
                Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
        }
    });
 } catch (MobileException e) {
     ...       
 }
```

#### ** Kotlin Code **

```kotlin
try {
    cpaas.authentication.connect(YOUR_ID_TOKEN,YOUR_ACCESS_TOKEN,3600,object:ConnectionCallback{
        override fun onSuccess(connectionToken: String?) {
            Log.i("CPaaS Authentication","Connected to websocket successfully")
        }

        override fun onFail(error: MobileError?) {
            Log.i("CPaaS Authentication","Connection to websocket failed")
        }

    })
} catch (e:MobileException){
    ...
}
```
<!-- tabs:end -->

## connect(String idToken, int lifetime, String channelInfo, ConnectionCallback callback)

Channel-info consists of information about lifetime and channel URL of the Websocket channel. Instead of getting new channel URL, this method can be called if channel-info is known in order to connect specified channel URL. If lifetime information doesn't expired in the channel-info, Authentication uses lifetime and channel URL information in the channel-info in order to connect and returns new channel-info in the ConnectionBlock. If lifetime is expired, then a new connection is established by given ID Token.

<!-- tabs:start -->

#### ** Java Code **

```java
 try{
    cPaaS.getAuthentication().setToken(YOUR_ACCESS_TOKEN);
    cpaas.getAuthentication().connect(YOUR_ID_TOKEN, 3600, "channelInfo", new ConnectionCallback() {
        public void onSuccess(String connectionToken) {
            Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
        }

        public void onFail(MobileError error) {
            Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
        }
    });
 } catch (MobileException e) {
     ...       
 }
```

#### ** Kotlin Code **

```kotlin
try {
    cpaas.authentication.setToken(YOUR_ACCESS_TOKEN)
    cpaas.authentication.connect(YOUR_ID_TOKEN,3600,"channelInfo",object:ConnectionCallback{
        override fun onSuccess(connectionToken: String?) {
            Log.i("CPaaS Authentication","Connected to websocket successfully")
        }

        override fun onFail(error: MobileError?) {
            Log.i("CPaaS Authentication","Connection to websocket failed")
        }

    })
} catch (e:MobileException){
    ...
}
```
<!-- tabs:end -->

## connect(String idToken, String accessToken, int lifetime, String channelInfo, ConnectionCallback callback)

Similar to the previous method, access token can be also given within the same method. Method will set the access token internally.

<!-- tabs:start -->

#### ** Java Code **

```java
 try{
    cpaas.getAuthentication().connect(YOUR_ID_TOKEN,YOUR_ACCESS_TOKEN, 3600, "channelInfo", new ConnectionCallback() {
        public void onSuccess(String connectionToken) {
            Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
        }

        public void onFail(MobileError error) {
            Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
        }
    });
 } catch (MobileException e) {
     ...       
 }
```

#### ** Kotlin Code **

```kotlin
try {
    cpaas.authentication.connect(YOUR_ID_TOKEN,YOUR_ACCESS_TOKEN,3600,"channelInfo",object:ConnectionCallback{
        override fun onSuccess(connectionToken: String?) {
            Log.i("CPaaS Authentication","Connected to websocket successfully")
        }

        override fun onFail(error: MobileError?) {
            Log.i("CPaaS Authentication","Connection to websocket failed")
        }

    })
} catch (e:MobileException){
    ...
}
```
<!-- tabs:end -->
