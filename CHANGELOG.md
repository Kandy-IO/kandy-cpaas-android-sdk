# Change Log

CPaaS Android SDK change log.

- This project adheres to [Semantic Versioning](http://semver.org/).
- This change log follows [keepachangelog.com](http://keepachangelog.com/) recommendations.

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
