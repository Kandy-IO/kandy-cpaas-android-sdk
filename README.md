# Kandy CPaaS Android SDK

<p>
    <img alt="GitHub release" src="https://img.shields.io/github/v/release/kandy-io/kandy-cpaas-android-sdk">
</p>

## Installation
To integrate Kandy CPaaS Android SDK to your project add gradle dependencies as described below.

### Step 1
Add Kandy CPaaS Android SDK maven repository url to your root level `build.gradle` file.

```
allprojects {
  ...
  repositories {
    ...
    maven {
      url "https://raw.githubusercontent.com/Kandy-IO/kandy-cpaas-android-sdk/master/dist/"
    }
  }
}
```

### Step 2
Add dependency of Kandy CPaaS Android SDK to your app level `build.gradle` file.

```
implementation 'com.kandy.mobile:kandycpaasmobilesdk:2.3.0'
```

That's all! You can use Kandy CPaaS Android SDK after you sync gradle.

## Installation for versions before 2.0.0
Versions before 2.0.0 will require manual installation. To download an older version, switch to tag of that specific version. Then download the CPaaS_Android_SDK-1.x.x.zip file under "dist" directory.

## Compatibility
Compatible Android OS versions :

* Android 5.0 - Android 10.0

Tested on :

* Nexus 5, Samsung Note 3, Samsung Note 5, Samsung S7, LG G2, LG G3, LG G5, LG G6, HTC Desire 626, HTC One A9, HTC 10, SONY XPERIA Z5, SONY XPERIA XZ, General Mobile GM 5+

## Reference

The information about tutorials and documents can be found in the links below

* `Documents`: [API Docs](https://kandy-io.github.io/kandy-cpaas-android-sdk/docs)

* `Tutorials`: [User Guide](https://kandy-io.github.io/kandy-cpaas-android-sdk/tutorials)
