# Kandy CPaaS Android SDK

## Installation
To integrate Kandy CPaaS Android SDK to your project add gradle dependencies as described in above.

### Step 1
Add Kandy CPaaS Android SDK Maven url to your root level `build.gradle` file.
```
allprojects {
    repositories {
    ...
        maven {
            url "https://raw.githubusercontent.com/Kandy-IO/kandy-cpaas-android-sdk/master/dist/"
        }
    }
    ...
}
```

### Step 2
Add dependcy of Cpass Android SDK to your app level `build.gradle` file.

```
implementation 'com.kandy.mobile:kandycpaasmobilesdk:2.0.0'
```

That's all! You can use Kandy CPaaS Android SDK after you sync gradle.

## Compatibility
Compatible Android OS versions :

* Android 5.0 - Android 10.0

Tested on :

* Nexus 5, Samsung Note 3, Samsung Note 5, Samsung S7, LG G2, LG G3, LG G5, LG G6, HTC Desire 626, HTC One A9, HTC 10, SONY XPERIA Z5, SONY XPERIA XZ, General Mobile GM 5+

## Reference

The information about tutorials and documents can be found in the links below

* `Documents`: [API Docs](https://kandy-io.github.io/kandy-cpaas-android-sdk/docs)

* `Tutorials`: [User Guide](https://kandy-io.github.io/kandy-cpaas-android-sdk/tutorials)
