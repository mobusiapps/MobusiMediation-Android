Mobusi Mediation Android SDK
=====
The current version has been tested using API 21, 23, 25 and 26 and require a minSdkVersion >= 15
## Download SDK

Download our sdk :

https://github.com/mobusiapps/MobusiMediationSDK-Android

Unzip the file and add all files in lib folder to your project's lib folder. Then add these lines to your build.gradle:

```gradle

repositories {
  flatDir {
    dirs 'libs'
  }
}

dependencies {
    compile(name: 'labcavemediation-base-2.1.3', ext:'aar')
    compile(name: 'labcavemediation-mediation-1002-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1003-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1004-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1005-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1007-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1008-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1009-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1012-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1013-2.1.3', ext: 'aar')
    compile(name: 'labcavemediation-mediation-1017-2.1.3', ext: 'aar')
    compile(name: 'AudienceNetwork', ext: 'aar')
    compile 'com.google.android.gms:play-services-ads:+'
    compile "com.android.support:design:+"
    compile 'com.android.support:multidex:1.0.2'
}

sourceSets {
    main {
      jniLibs.srcDirs = ['libs']
    }
  }
```

## Integrate SDK

Once you have added all files and gradle lines it's time to initialize the sdk. Important you must initialize the sdk and the begining of the execution of your app and only once.


```java
LabCaveMediation.INSTANCE.init(this, APP_HASH);
```
The appHash is the hash of your app, you can get it in https://mediation.labcavegames.com/panel/apps, "context" is your activity context.

When you init the sdk then you can request ads. Important the mediation sdk auto fetch all ads for you, when you call the init method also will fecth the first ads, so you only need to call the showMethods. Display ads with the corresponding action according to the type desired:

```java
LabCaveMediation.INSTANCE.showBanner(context, tag);
LabCaveMediation.INSTANCE.showBanner(labcaveBannerView, tag);

LabCaveMediation.INSTANCE.showInterstitial(context, tag);
LabCaveMediation.INSTANCE.showRewardedVideo(context, tag);
```
You must pass the conext of your activity and a string where the ad will be shown "main-menu", "options"... also can be an empty string.

If Banner ads are used, it is recommended to pause/resume ads with the onPause/onResume
method:

```java
@Override protected void onPause() {
    super.onPause();
    LabCaveMediation.INSTANCE.pause();
}

@Override protected void onResume() {
    super.onResume();
    LabCaveMediation.INSTANCE.resume();
}
```
### Advance integration

The sdk offers a delegate where you can receive the events of the ads. Important the the method "addListener"
add a new listener, so check you don't add the same listener more than once. The sdk will call all the listener added. You can remove a listener or all listeners added.

```java

 LabCaveMediation.INSTANCE.removeListener(@NonNull LabCaveMediationListener listener);

 LabCaveMediation.INSTANCE.clearListener();

 LabCaveMediation.INSTANCE.addListener(new LabCaveMediationListener() {
      // When the sdk is already initialized, if everything is ok, state will be true.
      @Override public void onInit(boolean state) {

      }
      // Will be called when any ad is loaded, it will tell you the type MediationType.BANNER, MediationType.INSTERSTITIAL and MediationType.REWARDED_VIDEO
      @Override public void onMediationTypeLoad(MediationType type) {

      }
      // When an ad is closed
      @Override public void onClose(MediationType type, String name, String extra) {

      }
      // When an ad is clicked
      @Override public void onClick(MediationType type, String name, String extra) {

      }
      // When we received an error loading or showing an ad
      @Override public void onError(String description, MediationType type, String extra) {

      }
      // When an ad is showed
      @Override public void onShow(MediationType type, String name, String extra, Info info) {

      }
      // When you must give a reward after a rewarded-video
      @Override public void onReward(@NonNull MediationType type, @NonNull String name, @NonNull String extra) {

      }
    });
```


You can enable loggin to check what is happening

```java
LabCaveMediation.INSTANCE.setLogging(true);
```

If you use proguard add these rules:

```java
-keep class com.androidnative.** { *; }
-keep class com.google.analytics.** { *; }
-keep class com.google.unity.** { *; }
-keep class com.google.android.gms.** { *; }
-keep class com.google.android.ads.** { *; }
-keep class com.vungle.** { *; }
-keep public class com.labcave.** { *; }
-keep class com.tapdaq.** { *; }
-keep class com.nerd.** { *; }
-keepclassmembers class * { @android.webkit.JavascriptInterface <methods>; }
-keepclassmembers class com.adcolony.sdk.ADCNative** { *;}
-keep class com.chartboost.** { *; }
-keep class com.oneaudience.** { *; }
-keepattributes SourceFile,LineNumberTable
-keepattributes JavascriptInterface
-keep class android.webkit.JavascriptInterface { *;}
-keep class com.unity3d.ads.** {*;}
-keep class com.facebook.** { *; }
-keepclassmembers class com.ironsource.sdk.controller.IronSourceWebView$JSInterface {
    public *;
}
-keepclassmembers class * implements android.os.Parcelable {
    public static final android.os.Parcelable$Creator *;
}
-keep public class com.google.android.gms.ads.** {
   public *;
}
-keep class com.ironsource.adapters.** { *;
}
-keep class com.startapp.** {
      *;
}

-keep class com.truenet.** {
      *;
}

-keepattributes Exceptions, InnerClasses, Signature, Deprecated, SourceFile,LineNumberTable, *Annotation*, EnclosingMethod
-dontwarn android.webkit.JavascriptInterface
-dontwarn com.startapp.**

-dontwarn org.jetbrains.annotations.**
-dontwarn com.moat.**
-keep class com.moat.** { public protected private *; }
```

# FAQ



## I added the files to lib folder but I can't see any references to the mediation in my project.

 Review that you copied the 'aar' files to your 'libs' folder, also check in your 'build.gradle' you have added all the compile command of the mediation. You can find all the information in the documentation.

## I receive multiple calls to my listener.

Notice that the method "addListener" add a new listener, so check the same listener is only added once.
