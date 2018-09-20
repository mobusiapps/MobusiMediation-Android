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
    compile(name: 'mobusimediation-base-1.14.5', ext:'aar')
    compile(name: 'MobusiSDK-3.0.0', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1001-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1002-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1003-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1004-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1005-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1007-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1008-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1009-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1012-1.14.5', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1013-1.14.5', ext: 'aar')
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
MobusiMediation.INSTANCE.init(context, appHash);
```

The appHash is the hash of your app, your manager will give you this info, "context" is your activity context.

When you init the sdk then you can request ads. Important the mediation sdk auto fetch all ads for you, when you call the init method also will fecth the first ads, so you only need to call the showMethods:

```java
MobusiMediation.INSTANCE.showBanner(context, tag);
MobusiMediation.INSTANCE.showBanner(mobusiBannerView, tag);

MobusiMediation.INSTANCE.showInterstitial(context, tag);
MobusiMediation.INSTANCE.showRewardedVideo(context, tag);
```

You must pass the conext of your activity and a string where the ad will be shown "main-menu", "options"... also can be an empty string.

If Banner ads are used, it is recommended to pause/resume ads with the onPause/onResume
method:

```java
@Override protected void onPause() {
    super.onPause();
    MobusiMediation.INSTANCE.pause();
}

@Override protected void onResume() {
    super.onResume();
    MobusiMediation.INSTANCE.resume();
}
```

### Advance integration

The sdk offers a delegate where you can receive the events of the ads. Important the the method "addListener"
add a new listener, so check you don't add the same listener more than once. The sdk will call all the listener added. You can remove a listener or all listeners added.

```java

 MobusiMediation.INSTANCE.removeListener(@NonNull MobusiMediationListener listener);

 MobusiMediation.INSTANCE.clearListener();

 MobusiMediation.INSTANCE.addListener(new MobusiMediationListener() {
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
MobusiMediation.INSTANCE.setLogging(true);
```

If you use proguard add these rules:

```java
-keep class com.androidnative.** { *; }
-keep class com.google.analytics.** { *; }
-keep class com.google.unity.** { *; }
-keep class com.google.android.gms.** { *; }
-keep class com.google.android.ads.** { *; }
-keep class com.vungle.** { *; }
-keep public class com.mobusi.** { *; }
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


### Example

```java

public class MainActivity extends AppCompatActivity implements View.OnClickListener {
  private static final String APP_ID = "insert app id here";

  private MobusiMediationBannerView bannerView;
  private final Handler UIHandler = new Handler(Looper.getMainLooper());

  private Button showBanner;
  private Button showInterstitial;
  private Button showRewarded;
  private Button showVideo;

  @Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    bannerView = findViewById(R.id.banner);

    Button showTest = findViewById(R.id.showTest);
    showBanner = findViewById(R.id.showBanner);
    showInterstitial = findViewById(R.id.showInterstitial);
    showRewarded = findViewById(R.id.showRewarded);
    showVideo = findViewById(R.id.showVideo);

    showTest.setOnClickListener(this);
    showBanner.setOnClickListener(this);
    showInterstitial.setOnClickListener(this);
    showRewarded.setOnClickListener(this);
    showVideo.setOnClickListener(this);

    initMobusiMediationLayer();
  }

  private void initMobusiMediationLayer() {
    MobusiMediation.INSTANCE.setAutoFetch(true);
    MobusiMediation.INSTANCE.setLogging(true);
    //Notice that this method add a new listener, so don't add the same listener more than once
    MobusiMediation.INSTANCE.addListener(new MobusiMediationListener() {
      @Override public void onInit(boolean state) {

      }

      @Override public void onMediationTypeLoad(final MediationType type) {
        UIHandler.post(new Runnable() {
          @Override public void run() {
            switch (type) {
              case BANNER:
                showBanner.setVisibility(View.VISIBLE);
                break;

              case INTERSTITIAL:
                showInterstitial.setVisibility(View.VISIBLE);
                break;

              case REWARDED_VIDEO:
                showRewarded.setVisibility(View.VISIBLE);
                break;

              case VIDEO:
                showVideo.setVisibility(View.VISIBLE);
                break;
            }
          }
        });
      }

      @Override public void onClose(MediationType type, String name, String extra) {

      }

      @Override public void onClick(MediationType type, String name, String extra) {

      }

      @Override public void onError(String description, MediationType type, String extra) {

      }

      @Override public void onShow(MediationType type, String name, String extra, Info info) {

      }

      @Override public void onReward(@NonNull MediationType type, @NonNull String name, @NonNull String extra) {

      }
    });

    MobusiMediation.INSTANCE.init(this, APP_ID);
  }

  @Override protected void onPause() {
    super.onPause();
    MobusiMediation.INSTANCE.pause();
  }

  @Override protected void onResume() {
    super.onResume();
    MobusiMediation.INSTANCE.resume();
  }

  @Override public void onClick(View v) {
    switch (v.getId()) {
      case R.id.showTest:
        MobusiMediation.INSTANCE.initTest(this, APP_ID);
        break;

      case R.id.showBanner:
        MobusiMediation.INSTANCE.showBanner(bannerView, "banner");
        break;

      case R.id.showInterstitial:
        MobusiMediation.INSTANCE.showInterstitial(MainActivity.this, "inter");
        break;

      case R.id.showRewarded:
        MobusiMediation.INSTANCE.showRewardedVideo(MainActivity.this, "rewarded");
        break;

      case R.id.showVideo:
        MobusiMediation.INSTANCE.showVideo(MainActivity.this, "video");
        break;
    }
  }
}
```


# FAQ



## I added the files to lib folder but I can't see any references to the mediation in my project.

 Review that you copied the 'aar' files to your 'libs' folder, also check in your 'build.gradle' you have added all the compile command of the mediation. You can find all the information in the documentation.

## I receive multiple calls to my listener.

Notice that the method "addListener" add a new listener, so check the same listener is only added once.