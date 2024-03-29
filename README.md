# SpoofSense Android SDK

Official repository for the Passive Liveness Detection Android SDK developed at [SpoofSense](https://spoofsense.ai/ "Goto Official Website").


## Table of Contents

1. [Give Permissions](#give-permissions)
2. [Device Requirements](#device-requirements)
3. [Capture Process](#capture-process)
4. [Quick Start](#quick-start)
5. [Detailed Information](#detailed-information)
      * [SDK Initialization](#sdk-initialization)
      * [Starting the Capture](#starting-the-capture)
      * [Checking the Result](#checking-the-result)
      * [Result Codes](#result-codes)
      * [SDK Configuration](#sdk-configuration)
      * [Result](#result)
6. [Contact Us](#contact-us)


## Give Permissions

* Under the AndroidManifest.xml file, set the following permissions:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-feature
   android:name="android.hardware.sensor.gyroscope"
   android:required="true" />
<uses-feature
   android:name="android.hardware.sensor.accelerometer"
   android:required="true" />
```

* Under the AndroidManifest.xml file, set the following flags:

```xml
android:usesCleartextTraffic="true"
android:screenOrientation="portrait"
android:largeHeap="true"
android:hardwareAccelerated="false"
```

* Under the application level build.gradle, add the following implementations:

```
implementation project(path: '::SpoofSense-debug')
implementation("com.squareup.okhttp3:okhttp:4.9.0")
```

* Under settings.gradle, add the following:

```
include ':SpoofSense'
```

## Device Requirements

Devices must meet the following minimum requirements:
* Android > `5.0.1`
* Camera Resolution > `640 x 480`
* GLES > `3.1`
* Internet Connectivity


## Capture Process

1. Initialise the SDK
2. Initiate the capture with parameters
3. Camera, classes, helpers, etc are initialised
4. The capture loop starts, with a UI displaying the camera preview and guidelines to the subject
    * Obtain image from camera
    * Send to view for displaying as preview
    * Send image to backend for further processing
    * Once the backend has received enough information to make a decision, the device is signaled to stop the capture
    * Result is provided to the caller
    
**`Note:` A capture may end prematurely if a non-recoverable error occurs. Captures maybe retried as many times as is required**


## Quick Start

Initialize the SDK asynchonously to allow quicker startup of the activity.

```java
Bundle sessionParameters = new Bundle();
sessionParameters.putString("api_key", "my-api-key");
com.chakshu.spoofsense.Init.start(sessionParameters);
```

Create an Intent, and populate with the required `Configuration` for capture, and start the Activity.

```java
Intent captureIntent = new Intent("com.chakshu.spoofsense.CaptureActivity");
```

OR

```java
Intent captureIntent = new Intent(this,
com.chakshu.spoofsense.CaptureActivity.class);
startActivityForResult(captureIntent, 1);
```

> The second parameter - requestCode - can be anything you wish. If your code launches many activities, it may be useful to have different request codes for different Activities, so you know which Activity finished when onActivityResult is called.


Handle the result in the same class that launched the Intent.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent resultData) {
    if (requestCode == 1) {
        if (resultCode == Activity.RESULT_OK) {
            Integer result = resultData.getIntExtra("result", -1);
            switch (result) {
                case -1:
                    //Some kind of error occurred.
                    break;
                case 1000:
                    //The capture was successful.
                    break;
                case 1001:
                    // API HTTP hit failure
                    break;
                case 1002:
                    // API external hit failure
                    break;
                case 401:
                    //Capture timeout exceeded 60sec
                    break;
                default:
                    //Handle unexpected cases
                    break;
            }
        } else {
            //The activity was cancelled by the OS or User.
        }
    }
}
```

> Use the same requestCode passed to startActivityForResult.

> The result must be checked and handled accordingly.


## Detailed Information

### SDK Initialization

* com.chakshu.spoofsense.Init.start() will initialize the SDK and performs a number of lengthy operations. The start operation will immediately return, with the work being done in the background. This should be called at least 10 seconds before the Activity is launched, to speed up the start up of the activity. It is advised to call this function from the parent activity onCreate() function. If this initialization is not started, it will be called when the CaptureActivity is launched, delaying the capture process by up to 10 seconds.

* Calling this method multiple times has no effect. Any fatal errors are not obvious until the CaptureActivity is launched, or if the status is checked. com.chakshu.spoofsense.Init.start() requires a [Bundle](https://developer.android.com/reference/android/os/Bundle) to be passed populated with configuration. api_key is a required parameter, and authenticates the source of requests.

* A call to com.chakshu.spoofsense.Init.status() will return an Integer that indicates the status of the initialization. This is a convenience method, and does not need to be checked.

> Table 1. SDK Initialization Status

|Value|Meaning|
|-|-|
|-1|SDK has not been initialized yet|
|0|A fatal, irrecoverable error occurred during SDK initialization|
|1|SDK is currently undergoing initialization|
|10|SDK has been fully initialized|


### Starting the Capture

The capture is started by using an Intent to launch the CaptureActivity. Further `configuration` may be required and is passed as extra data to the Intent. For further information on how to call other Activities from Intents, see the Android [Javadoc](https://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent)) or [Tutorials](https://developer.android.com/training/basics/firstapp/starting-activity). Since you need to inspect returned data, you should make use of startActivityForResult rather than startActivity.


### Checking the Result

Assuming that the Capture proceeded correctly, the results are placed in the returned Intent. Of all `Results`, only result is guaranteed to be present.

### Result Codes

> Table 2. Result Codes

|Code|Purpose|
|-|-|
|-1|Some kind of error occurred|
|1000|The capture was successful|
|1001|`API HTTP` hit failure|
|1002|`API` other hit failure|
|401|Capture timeout|


### SDK Configuration

> Table 3. SDK Configuration

|Advanced|Type|Required|Purpose|
|-|-|-|-|
|ApiKey|String|Yes|The API key to access the backend|


### Result

The result from a capture operation contains useful information.

> Table 4. Result Fields

|Advanced|Type|Purpose|
|-|-|-|
|Code|Integer|The result of the Capture. See `Result Codes` for the meaning|
|prediction_result|String||
|prediction_percentage|String||


## Contact Us

Contact us at [kartikeya@spoofsense.com](mailto:kartikeya@spoofsense.com)
