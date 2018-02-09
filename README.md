# TrapyzLocateSDK

# Instructions #

Download and extract or git clone this repository in the root directory of your project.
### Gradle Configuration###
*Add these dependencies in your App's build.gradle file

```
    
    compile 'com.google.android.gms:play-services-gcm:11.0.2'
    compile 'com.google.android.gms:play-services-location:11.0.2'
    compile 'com.amitshekhar.android:android-networking:1.0.0'
    compile 'com.github.pwittchen:reactivebeacons:0.5.1'
    compile 'io.reactivex:rxandroid:1.2.1'
    compile project(':trapyzlocate')

```

### Settings.Gradle Configuration###
*Add these dependencies in your App's settings.gradle file
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
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    
    
```

###Add ApiKey to Manifest ###
* Add your ApiKey to the manifest file inside your application block
```
        <meta-data android:name="trapyz_sdk_api_key" android:value="YOUR_API_KEY" />

```

###Implement the following methods in your MainActivity###

```
String[] permissions = new String[]{
            Manifest.permission.ACCESS_FINE_LOCATION,
            Manifest.permission.WRITE_EXTERNAL_STORAGE,};
    private boolean checkPermissions() {
        int result;
        List<String> listPermissionsNeeded = new ArrayList<>();
        for (String p : permissions) {
            result = ContextCompat.checkSelfPermission(this, p);
            if (result != PackageManager.PERMISSION_GRANTED) {
                listPermissionsNeeded.add(p);
            }
        }
        if (!listPermissionsNeeded.isEmpty()) {
            ActivityCompat.requestPermissions(this, listPermissionsNeeded.toArray(new String[listPermissionsNeeded.size()]), 100);
            return false;
        } else {
            startService();
        }
        return true;
    }
    @Override
    public void onRequestPermissionsResult(int requestCode, String permissions[], int[] grantResults) {
        if (requestCode == 100) {
            boolean perGranted = true;
            for (int count : grantResults) {
                if (count != PackageManager.PERMISSION_GRANTED) {
                    perGranted = false;
                }
            }
            if (perGranted) {
                startService();
            } 
        }
    }
    private void startService() {
        TrapyzLocationService mSensorService=new TrapyzLocationService();
        if (!isMyServiceRunning(mSensorService.getClass())) {
            startService(new Intent(Intent.ACTION_SYNC, null, this,TrapyzLocationService.class));
        }else {
            stopService(new Intent(Intent.ACTION_SYNC, null, this,TrapyzLocationService.class));
            if (!isMyServiceRunning(mSensorService.getClass())) {
                startService(new Intent(Intent.ACTION_SYNC, null, this,TrapyzLocationService.class));
            }
        }
    }
    private boolean isMyServiceRunning(Class<?> serviceClass) {
        ActivityManager manager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
        for (ActivityManager.RunningServiceInfo service : manager.getRunningServices(Integer.MAX_VALUE)) {
            if (serviceClass.getName().equals(service.service.getClassName())) {
                return true;
            }
        }
        return false;
    }
 
 
 
 
 
###Start Service in your onCreate Block###
  	
 if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            checkPermissions();
        } else {
            startService();
        }

