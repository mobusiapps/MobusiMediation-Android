Mobusi Mediation Android SDK
=====
The current version has been tested using API 21, 23, 25 and 26 and require a minSdkVersion >= 15

### Importing to your project
**Add to build.gradle:**

```java
repositories {
  flatDir {
    dirs 'libs'
  }
}
```

```java
dependencies {
    compile(name: 'mobusimediation-base-{version}', ext:' ;aar')
    compile(name: 'MobusiSDK-{lastMobusiSDKVersion}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1001-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1002-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1005-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1004-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1003-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1008-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1007-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1009-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1012-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1013-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1014-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1015-{version}', ext: 'aar')
    compile(name: 'mobusimediation-mediation-1016-{version}', ext: 'aar')
    compile(name: 'AudienceNetwork', ext: 'aar')
    compile(name: 'HyprMediate-SDK-1.2.0', ext: 'aar')
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

### Usage

Initialize:

```java
MobusiMediation.INSTANCE.init(context, appId);
```

Display ads with the corresponding action according to the type desired:
```java
MobusiMediation.INSTANCE.showBanner(context, tag);
MobusiMediation.INSTANCE.showBanner(mobusiBannerView, tag);

MobusiMediation.INSTANCE.showInterstitial(context, tag);
MobusiMediation.INSTANCE.showVideo(context, tagone);
MobusiMediation.INSTANCE.showRewardedVideo(context, tag);
```

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

##### Additional

Signal to the SDK what the additional console information displays with the aim of correctly
determining the integration.

```java
MobusiMediation.INSTANCE.setLogging(true);
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
