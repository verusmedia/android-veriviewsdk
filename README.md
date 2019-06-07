# Android-Veriview-SDK

##### Requirements:

* Java JDK 8
* Android device version: Lolipop or higher
* Android SDK 21
* Gradle 3.2.1 or higher

# Guide to use Video Capture library

### Installation
•	Change your minSdkVersion to >=21 in the Module:app Gradle.

•	Add the jitpack repository to the Project Gradle.
```
allprojects {
    repositories {
        google()
        jcenter()
        maven { url 'https://jitpack.io' }
    }
}
```
•	Add the following code in the Module:app Gradle.
```
Android{
    ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ...
}
```
•	Add the veriview-sdk and android dependencies to your gradle.
```
dependencies{
    ...
    //Android core dependencies
    final SUPPORT_LIBRARY_VERSION = '28.0.0'
    implementation "com.android.support:appcompat-v7:$SUPPORT_LIBRARY_VERSION"
    implementation 'com.android.support.constraint:constraint-layout:1.1.3'
    
    //Veriview-sdk dependency
    implementation 'com.gitlab.jinglz.apps:android-veriview-sdk:1.0.18-beta'
    ...
}

```

### Permissions
You have to add these three permissions to your AndroidManifest.xml

```xml

    ...

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.INTERNET" />

    ...

```
### Implementation:

#### AndroidManifest.xml
You have to add the android:configChanges="orientation|screenSize" property to the activity you want to use the videocapture.
```xml

    ...

    <activity android:name=".MainActivity" android:configChanges="orientation|screenSize">

    ...

```

#### activity_layout.xml
You have to add a FrameLayout and set and id on the layout.xml of the activity where you want to start the videocapture.
Do not set the visibility of the FrameLayout to INVISIBLE or HIDE

```xml

    ...

    <FrameLayout
    android:id="@+id/container"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"></FrameLayout>


    ...

```
#### MainActivity.java
You have to implement the OnVideoCaptureListener to get the events from the videocapture.

```java
public class MainActivity extends AppCompatActivity {
    ...
}


```

You also need to add the VeriviewFragment attribute to your class and initialize it.

```java
VeriviewFragment fragment;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    fragment = new VeriviewFragment(this, R.id.container,"YourAPIKey", "youResourceId","yourReproductionId", OnVideoCaptureListener);
}

```
You can implement the OnVideoCaptureListener on your class or you can create a new one in the OnVideoCapture field of the attachFragment field.

These methods will be called while you are sending data through the fragment. You have to add your reproductionId, VideoId and video second to the methods.

```java
@Override
public int getVideoSecond() {
    return yourVideoSecond;
}

@Override
public String getVideoId() {
    return "yourResourceId";
}

@Override
public String getReproductionId() {
    return "YourReproductionId";
}

```

You also need to handle these events in order to stop and resume the videocapture functions.

```java
@Override
protected void onPause() {
    if(!(fragment==null)){fragment.stopRecordingVideo();}
    super.onPause();
}

@Override
protected void onResume() {
    if(!(fragment==null)){
        fragment.startRecordingVideo();}
    super.onResume();
}

@Override
protected void onStop() {
    if(!(fragment==null)){ fragment.stopRecordingVideo();}
    super.onStop();
}

@Override
protected void onDestroy() {
    if(!(fragment==null)){fragment.onDestroyView();}
    super.onDestroy();
}

```
#### Handling SDK Methods
When you implement the onVideoCaptureListener it will have the following methods:
```java
    /**
     * The didEngage method will be called when the service recognizes eyes
     */
    @Override
    public void didEngage() {}
    
    /**
     * The didNotEngage method will be called when the service doesn't recognizes eyes
     */
    @Override
    public void didNotEngage() {}
    
    /**
     * The volumeIsFull method will be called when the volume is at least on 30%
     */
    @Override
    public void volumeIsFull() {}
    
    /**
     * The volumeIsDown method will be called when the volume is lower than 30%
     */
    @Override
    public void volumeIsDown() {}

```

### Sending Events
The SDK provides a way to send events through the EventsManager class. You'll have to send the event using the Events enum, your id, the second when the event happens and the resource type.

Supported events:
```java
    ENGAGED
    NOT_ENGAGED
    VOLUME_DOWN
    VOLUME_UP
    SKIP_VIDEO
    COMPLETED_VIDEO
```
##### Usage of EventsManager
The following code sends the RESUMED_BY_VOL event using the EventsManager class and the Events enum.
```java
EventsManager eventsManager = new EventsManager();
eventsManager.registerEvent(Events.RESUMED_BY_VOL, yourID, second, "video");
```
