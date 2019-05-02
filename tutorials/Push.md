# Push Notifications

When $KANDY$ cannot reach to the device using websocket channel, message will be sent through Firebase if implemented. Using the mobile push mechanism, communication between $KANDY$ and the device can be maintained even while the application is not awake or in the case of websocket connection failure. In order to register the device to the push channel and pass the message to $KANDY$ Mobile SDK, instructions below must be followed accurately.

## Registering Application For Push Services

Application should be registered with its package name to be able to receive push notifications from $KANDY$. Necessary information for registration step for Android applications are listed below, please contact with system administrator and share them when needed.

* Application package name(s)
* Firebase Server Key

Please refer to [Notification Channels](/developer/quickstarts/rest-api/get-started#notification-channels) section to learn more about how to register for push services.

## Registering the Application

In order to receive push notifications, Firebase Services should be added to correctly. Please follow Google's official tutorial to add Firebase to your Android project: https://firebase.google.com/docs/android/setup


To use `FirebaseMessagingService`, you need to add the following in your app manifest, `android:name` value points to Java class that you would like to use for Firebase events:

```xml
<service android:name=".java.MyFirebaseMessagingService">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

In order to subscribe push notification service, your app has to receive deviceToken from the Firebase and pass that value to `PushManager` of the $KANDY$.

```java
String fcmPushDeviceToken = FirebaseInstanceId.getInstance().getToken();
String packageNameOfTheApplication = getActivity().getPackageName();

CPaaS.getPushManager().subscribe(packageNameOfTheApplication, fcmPushDeviceToken, new PushSubscriptionCallback() {
    @Override
    public void onSuccess(String callbackUrl) {
        Log.i("pushNotification", " Callback url " + callbackUrl);
    }

    @Override
    public void onFail(MobileError error) {
        Log.i("pushNotification", " Error " + error.getErrorMessage());
    }
});
```

## Receiving Push Notification

After registering the push service, create your `MyFirebaseMessagingService` class to receive push events. You should override `onMessageReceived` method of the `FirebaseMessagingService` interface and inject the push notification payload to $KANDY$ using `injectPushMessage` method.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
	...
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        super.onMessageReceived(remoteMessage);
        CPaaS.getPushManager().injectPushMessage(remoteMessage.getData(), new PushInjectionCallback() {
            @Override
            public void onSuccess() {
                Log.i("FCM", "Push inject push message succeed");
            }

            @Override
            public void onFail(MobileError error) {
                Log.i("FCM", "Push inject push message failed : " + error.getErrorMessage());
            }
        });
    }
	...
}
```
