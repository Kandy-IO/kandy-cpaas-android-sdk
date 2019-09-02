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

## Creating Outgoing Call

You can create **OutgoingCall** object by calling **createOutgoingCall** method in Call Service.

```java

callService.createOutgoingCall(callee, callApplicationListener, new OutgoingCallCreationCallback() {
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

You need to get `callId` to use call events. You can get `callId` by calling `callInterface.getId()` inside the success block of the `OutgoingCallCreationCallback`.

## Receiving Incoming Call

When $KANDY$ Mobile SDK receives an incoming call, it calls `incomingCall` method in the `CallApplicationListener`. Thus, `CallApplicationListener` should be set to the class where managed. Now, the application should receive incoming call events if any.

```java
CallApplicationListener callApplicationListener = new CallApplicationListener();
callApplicationListener.setContext(applicationContext);
```

### Managing Incoming Call

When `incomingCall` method is notified with the received call, call can be accepted with videoEnabled option, rejected or ignored. Application will be informed about success or fail via the callback methods on `CallApplicationListener` which are related to the operation.

#### Accepting Incoming Call

An incoming call can be accepted by calling the `acceptCall` method on the incoming call instance which is received from `CallApplicationListener`.

```java
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

#### Rejecting Incoming Call

An incoming call can be rejected by calling the `rejectCall` method on the incoming call instance.

```java
private void rejectIncomingCall() {
	IncomingCall incomingCall = // get from the CallApplicationListener with the callId
	incomingCall.rejectCall();
}
```

#### Ignoring Incoming Call

An incoming call can be ignored by calling the `ignoreCall` method on the incoming call instance.

```java
private void rejectIncomingCall() {
	IncomingCall incomingCall = // get from the CallApplicationListener with the callId
	incomingCall.ignoreCall();
}
```

## Managing Call Events

Methods implemented in the `CallApplicationListener` are notified with the events occurred during the call, or `callStatusChanged` when state of the call is changed. By listening these methods, call events can be managed directly. To see all methods implemented in the `CallApplicationListener`, see API Reference Documents.

```java
	@Override
    public void callStatusChanged(CallInterface callInterface, CallState callState) {
        Log.d(TAG, "callStatusChanged: Call state is: " + callState.getType() + " call id is: " + callInterface.getId());
        // Now you can broadcast and process the state of the specific call with its unique ID.
    }
```

### Starting Call Events

Both incoming call and outgoing call objects are inherited from `CallInterface`. Except particular properties and methods defined in incoming call and outgoing call classes, all methods and properties defined in `CallInterface` are suitable for both call type.

Note that, an application cannot start an event while any other operation is still in progress.

Furthermore, when a mid-call operation finishes which is called by the user, its *success* or *fail* method is triggered depending on result of the operation.

#### To end call

To end an incoming or outgoing call, `endCall` method on call instance can be called.

```java
call.endCall();
```

#### To hold or unhold call

While in a call, a participant may be placed on hold by calling `holdCall` method. When operation succeeds, media transfer between participants stops, and call state will change to `On Hold` state. Remote participant will see this call session in `Remotely Held` state.

To resume to the call, `unholdCall` method should be called. Note that, these operations take some time, thus listening operation results from `CallApplicationListener`, and acting accordingly is recommended.

Note that a call already in `On Hold` state may not be placed on hold. Similarly, attempting to `unhold` a call that is not in `On Hold` state will fail.

```java
call.holdCall();
call.unholdCall();
```

#### To start or stop sending video

```java
call.videoStart();
call.videoStop();
```

#### To mute or unmute call

To stop sending audio from the microphone, `mute` method can be called. Until unmuting the call, participants cannot hear the voice from the device.

```java
call.mute()
call.unMute()
```

## Anonymous Calls

$KANDY$ offers to make voice and video calls even if the application user does not have a $KANDY$ account to login. This functionality is called as Anonymous Call on $KANDY$ system. Anonymous call feature can be useful for applications such as customer service support. Since the application user does not have a $KANDY$ account, only an outgoing call can be made to a registered $KANDY$ user. An anonymous user cannot receive and incoming call with this feature.

Please refer to [Anonymous Calls](/developer/quickstarts/rest-api/voice-and-video-calls#anonymous-calls) section to learn about how to implement anonymous calls solution.

### Creating an Anonymous Call

A server-side component is suggested to be used for logging in to $KANDY$ along with Mobile SDK for an anonymous call scenario. Once the application establishes the login, application should be able create outgoing calls using the Call Service on $KANDY$ Mobile SDK. Creating an anonymous outgoing call follows the same procedure as if it is a regular outgoing call. The main difference is an anonymous user cannot receive an incoming call, so application will not receive and incoming call notification on $KANDY$ Mobile SDK.
