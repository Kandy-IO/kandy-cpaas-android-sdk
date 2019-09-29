# User Login

To receive events or start $KANDY$ services, user login should be completed successfully. Events receives to $KANDY$ Mobile SDK by websocket or push channel and all requests need authorization, thus when **Authentication Token** is gathered, keep it safe. Each time the application wakes up, user login must connect websocket.

A connection can be established with different methods. How to use each method is explained and shown below. Channel lifetime is specified with the lifetime value. $KANDY$ Mobile SDK extends the lifetime automatically, however that doesn't guarantee connection will last indefinitely. Note that, server related configurations should be set before calling connect.

When a connection is established, SDK uses the given Authentication Token to communicate with services. If the Authentication Token is invalidated or expired, service requests will fail. In these cases, **Authentication Token** should be renewed with the valid one by watching connection status changes.

## Application context

Mobile SDK needs to know about the application context. Application developers should set the application context to Mobile SDK, even before setting the configuration. Best place to do this is the "Application" structure of Android. Application developers should extend this and set the application context to Mobile SDK when application is created.

### Example: Setting application context

```java
public class ExampleApplication extends Application {

    public void onCreate() {
        super.onCreate();

        Context context = getApplicationContext();
        Globals.setApplicationContext(context);
    }
}
```

## Configurations

The first step for any application that will use the $KANDY$ Mobile SDK is setting the configurations. When doing this, you can customize certain features by providing a configurations object. All feature configurations can be set with instance of the `Configurations` object. Developers should set server related configurations before using any functionality of the SDK and all other customizations are optional.

```java
Configuration configuration = Configuration.getInstance();
configuration.setUseSecureConnection(true);
configuration.setRestServerUrl("cpaasapi.example.com");

// Setting ICE Servers
ICEServers iceServers = new ICEServers();
iceServers.addICEServer("turns:turn-example.com:443?transport=tcp");
iceServers.addICEServer("turn:turn-example.com:3535?transport=udp");
configuration.setICEServers(iceServers);
```

#### Capturing Logs and Troubleshooting Problems

The log level configs are used to change the severity of logging output from $KANDY$ Mobile SDK. This allows for more logged messages, such as debug information, warnings, and errors, which can help to explain what SDK is doing.

$KANDY$ Mobile SDK also provides application developers to set their customized logger implementation into Mobile SDK.

```java
class CustomizedLogger extends Logger {
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

## connect(String idToken, int lifetime, ConnectionCallback callback)

Establishes a connection for the user with given ID Token, which will last until the time given with lifetime is elapsed.

Authentication needs access token in order to get and establish Websocket subscription. So before using this method, access token should be given to the Authentication with calling setToken method.

```java
cPaaS.getAuthentication().setToken("access_token");
cpaas.getAuthentication().connect("id_token", 3600, new ConnectionCallback() {
    public void onSuccess(String connectionToken) {
        Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
    }

    public void onFail(MobileError error) {
        Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
    }
});
```

## connect(String idToken, String accessToken, int lifetime, ConnectionCallback callback)

Establishes a connection for the user with given ID Token, which will last until the time given with lifetime is elapsed, using given accessToken. accessToken will be set internally and then connection will be established just like the connect method in (a).

```java
cpaas.getAuthentication().connect("id_token", "access_token" 3600, new ConnectionCallback() {
	public void onSuccess(String connectionToken) {
			Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
	}

	public void onFail(MobileError error) {
			Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
	}
});

```

## connect(String idToken, int lifetime, String channelInfo, ConnectionCallback callback)

Channel-info consists of information about lifetime and channel URL of the Websocket channel. Instead of getting new channel URL, this method can be called if channel-info is known in order to connect specified channel URL. If lifetime information doesn't expired in the channel-info, Authentication uses lifetime and channel URL information in the channel-info in order to connect and returns new channel-info in the ConnectionBlock. If lifetime is expired, then a new connection is established by given ID Token.

```java
cPaaS.getAuthentication().setToken("access_token");
cpaas.getAuthentication().connect("id_token", 3600, "channelInfo", new ConnectionCallback() {
    public void onSuccess(String connectionToken) {
        Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
    }

    public void onFail(MobileError error) {
        Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
    }
});
```

## connect(String idToken, String accessToken, int lifetime, String channelInfo, ConnectionCallback callback)

Similar to the previous method, access token can be also given within the same method. Method will set the access token internally.

```java
cpaas.getAuthentication().connect("id_token", "access_token", 3600, "channelInfo", new ConnectionCallback() {
    public void onSuccess(String connectionToken) {
        Log.i(“CPaaS.Authentication”, “Connected to websocket successfully”);
    }

    public void onFail(MobileError error) {
        Log.i(“CPaaS.Authentication”, “Connection to websocket failed”);
    }
});
```
