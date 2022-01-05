# Change Log

CPaaS Android SDK change log.

- This project adheres to [Semantic Versioning](http://semver.org/).
- This change log follows [keepachangelog.com](http://keepachangelog.com/) recommendations.

## 2.24.0 - 2022-01-03

## 2.23.0 - 2021-11-03

## 2.22.0 - 2021-09-27

## 2.21.0 - 2021-08-16

### Added
- JavaWebSocket version updated to 1.5.1 `KAE-647` 

## 2.20.0 - 2021-06-29

### Fixed
- Adding same payload of RED codec to SDP issue fixed. `KAE-982`

## 2.19.0 - 2021-06-01

## 2.18.0 - 2021-04-26

## 2.17.0 - 2021-03-26

### Added
- XCFramework support was added
- `List<Map> getAvailableCodecs(CodecType codecType)` added to Call class in order to get available codec attributes.

## 2.16.0 - 2021-02-26

## 2.15.0 - 2021-02-04

### Added
- Ringing feedback options`KAE-889` 

### Fixed
- Cannot update presence status is fixed. `KAE-907`

## 2.14.0 - 2020-12-30

### Fixed
- PCObserver timeout crash is fixed. `KAE-820`
- First time build issue is fixed. `KAE-812`
- Issue while fetching messages in one-to-one chat is fixed. `KAE-870`
- Null fields in OutboundMessage Object is fixed. `KAE-910`
- Issue in call transfer is fixed. `KAE-883`

## 2.13.0 - 2020-12-02

### Fixed
- onFail method returns from worker thread is fixed. `KAE-803`
- Crash on deleting group chat is fixed. `KAE-887`
- Printing call states is fixed. `KAE-885`
- Push unsubscribe issue is fixed. `KAE-910`
- Notification channel unsubscribe issue is fixed. `KAE-863`

### Removed
- `useSecureConnection`, `packageName`, `earlyMediaEnabled`, `channelLifeTime` and `DTLS` parameters removed from Configuration. `KAE-862`

### Changed
- `replaceCodecSet` parameter renamed as `codecPayloadTypeSet`. `KAE-862`

## 2.12.0 - 2020-11-04

### Fixed
- SMS history does not display date is fixed. `KAE-846`

## 2.11.0 - 2020-10-05

### Changed
- Return type of `CallInterface.getRTPStatistics` was changed as `String`

## 2.10.0 - 2020-08-28

### Added
- Trickle ICE support `KAE-79`

### Changed
- Usage of `Conversation.send(OutboundMessage message, MessagingCallback callback)` method was changed as `void send(OutboundMessage message, SendMessageCallback callback)`
- Usage of `PushSubscriptionCallback.onSuccess(String callbackUrl)` method was changed as `void onSuccess()`

## 2.9.0 - 2020-07-22

## 2.8.0 - 2020-07-03

### Changed

- Usage of `ChatListener.chatDeliveryStatusChanged` method was changed as `void chatDeliveryStatusChanged(String participant, MessageDeliveryStatus deliveryStatus, String messageID)` 
- Usage of `ChatListener.isComposingReceived` method was changed as `void isComposingReceived(String participant, MessageState state, long lastActive)`
- Usage of `ChatMessageStatusListener.onSuccess` method was changed as `void onSuccess(MessageDeliveryStatus status)`
- Usage of `SMSListener.SMSDeliveryStatusChanged` method was changed as `void SMSDeliveryStatusChanged(String participant, MessageDeliveryStatus deliveryStatus, String messageID)`
- Type of `Message.status` was changed as `MessageDeliveryStatus`
- Type of `Message.messageType` was changed as `MessageSessionType`
- Type of `Part.type` was changed as `MessageContentType`

### Note
- These changes is not backward compatible please check your related code base.

## 2.7.0 - 2020-06-12

## 2.6.0 - 2020-04-05

### Added
- Groups can now have multiple admins and Group properties such as name, subject and image can be changed. `KAE-533`

### Fixed
- After Session Complete notification the end call DELETE request must be sent. `KAE-669`
- Authentication disconnect issue is fixed. `KAE-652`

## 2.5.0 - 2020-03-20

## 2.4.0 - 2020-03-02

## 2.3.0 - 2020-01-09

### Added
- Call forward feature `KAE-80`

### Fixed
- Fixed SMS DID parsing issue during login with project credentials  `KAE-575`


## 2.2.0 - 2019-12-02

### Added
- WebRTC stack upgraded to version M78 `KAE-507`
- Custom CPaaS Agent HTTP Header is implemented `KAE-524`


## 2.1.0 - 2019-11-04

### Added
- Three-Way Call feature implemented `KAE-87`

### Fixed
- Default value for codec preferrence is set to reflect WebRTC default codec behavior `KAE-538`

### Deprecated
- `CallService.createOutgoingCall(String, CallApplicationListener, OutgoingCallCreationCallback)` and `CallService.createOutgoingCall(String, String, CallApplicationListener, OutgoingCallCreationCallback)` methods are deprecated and will be removed in future releases, since `CallApplicationListener` can already be set to CallService via a setter method. Instead `CallService.createOutgoingCall(String, OutgoingCallCreationCallback)` and `CallService.createOutgoingCall(String, String, OutgoingCallCreationCallback)` should be used.


## 2.0.0 - 2019-10-03

### Added
- Mobile SDK Distribution on Maven `KAE-425`
- Unified Plan Support `KAE-200`


## 1.9.0 - 2019-09-02

### Fixed
- Media problem when switching between two calls on Music-on-Hold is fixed `KAE-410`


## 1.8.0 - 2019-08-05

### Added
- Consultative Transfer feature implemented `KAE-85`


## 1.7.0 - 2019-07-05

### Added
- Blind (Direct) Transfer feature implemented `KAE-83`


## 1.6.0 - 2019-06-01


## 1.5.0.1 - 2019-05-06

### Fixed
- Hardware support of "Acoustic Echo Canceler" and "Noise Suppressor" configuration is fixed `KAE-369`


## 1.5.0 - 2019-05-02

### Added
- Query SMS History feature implemented `KAE-292`

### Changed
- Package name for VideoView is changed to "com.genband.mobile.core.webrtc.view". `KAE-368`
