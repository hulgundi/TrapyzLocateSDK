# TrapyzLocateSDK

# Instructions #

Download and extract or git clone this repository in the root directory of your project.
### Gradle Configuration###
*Add these dependencies in your App's build.gradle file

```
    compile 'com.android.support:appcompat-v7:26.+'
    compile 'com.google.android.gms:play-services-gcm:11.0.2'
    compile 'com.google.android.gms:play-services-location:11.0.2'
    compile 'com.amitshekhar.android:android-networking:1.0.0'
    compile 'com.github.pwittchen:reactivebeacons:0.5.1'
    compile 'io.reactivex:rxandroid:1.2.1'
    compile project(':trapyzlocate')

```

### Settings.Gradle Configuration###
*Add these dependencies in your App's build.gradle file
```
include ':app', ':trapyzlocate'

```

###Add Service to Manifest ###
* Update your AndroidManifest.xml file by adding the service inside your application block
```
  <service
            android:name= "com.trapyz.trapyzlocatesdk.TrapyzLocationService"
            android:exported="false"
            android:stopWithTask="false"
            android:process=":TrapyzLocationService"/>
            />
```

###Add Permissions to Manifest ###
* Update your AndroidManifest.xml file by adding these permissions
```
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.Manifest.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
```

###Add ApiKey to Manifest ###
* Add your ApiKey to the manifest file inside your application block
```
        <meta-data android:name="trapyz_sdk_api_key" android:value="YOUR_API_KEY" />

```

Location Permission Requests (Targeting SDK > 23 only)

In apps targeting Android SDK level 23 and above (6.0+), developers must explicitly request permissions from the user that Android deems “dangerous”, such as location. Since the Reveal Mobile SDK requires the “ACCESS_FINE_LOCATION” permission, implementers targeting SDK level 23+ must request this permission from the end users before the trapyz Mobile SDK can properly function. We suggest following Android’s guide to request permissions and handle the results here: http://developer.android.com/training/permissions/requesting.html.

###Implement the following methods in your MainActivity###
* Implement the following methods in your MainActivity
```

    @Override public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,
                                                     @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        final boolean isCoarseLocation = requestCode == PERMISSIONS_REQUEST_CODE_ACCESS_COARSE_LOCATION;
        final boolean permissionGranted = grantResults[0] == PERMISSION_GRANTED;

        if (isCoarseLocation && permissionGranted) {
            Intent intent = new Intent(Intent.ACTION_SYNC, null, this, com.trapyz.trapyzlocatesdk.TrapyzLocationService.class);
            startService(intent);
        }
    }

    private void requestCoarseLocationPermission() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            requestPermissions(new String[] { ACCESS_COARSE_LOCATION },
                    PERMISSIONS_REQUEST_CODE_ACCESS_COARSE_LOCATION);
        }
    }

    private boolean isFineOrCoarseLocationPermissionGranted() {
        boolean isAndroidMOrHigher = Build.VERSION.SDK_INT >= Build.VERSION_CODES.M;
        boolean isFineLocationPermissionGranted = isGranted(ACCESS_FINE_LOCATION);
        boolean isCoarseLocationPermissionGranted = isGranted(ACCESS_COARSE_LOCATION);

        return isAndroidMOrHigher && (isFineLocationPermissionGranted
                || isCoarseLocationPermissionGranted);
    }

    private boolean isGranted(String permission) {
        return ActivityCompat.checkSelfPermission(this, permission) == PERMISSION_GRANTED;
    }

```

###Start Service in your onCreate Block###

``` 
  	if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            // Check Permissions Now
            ActivityCompat.requestPermissions(this,
                    new String[] { Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.READ_PHONE_STATE },
                    0);
        }

        if (!isFineOrCoarseLocationPermissionGranted()) {
            requestCoarseLocationPermission();
        }

```

or


```
        stopService(new Intent(this, com.trapyz.trapyzlocatesdk.TrapyzLocationService.class));
```

*You can implement startService method in the onCreate block of your mainactivity and stopService method in the onStop or onDestroy block of your mainactivity
