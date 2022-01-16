# Aper Mapping

Android API-permission mappings, mined from the [official docs](https://developer.android.com/reference) of Android framework.

[![GitHub license](https://img.shields.io/github/license/sqlab-sustech/APER-mapping)](https://github.com/sqlab-sustech/APER-mapping/blob/main/LICENSE)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5699434.svg)](https://doi.org/10.5281/zenodo.5699434)

## File Format

Each API level contains five files, such as:

+ [API30](https://github.com/aper-project/aper-mapping/tree/main/API30)
  - [annotations-Mappings.txt](https://github.com/aper-project/aper-mapping/blob/main/API30/annotations-Mappings.txt): Mappings specified by an API method's annotation
  - [docs-Mappings.txt](https://github.com/aper-project/aper-mapping/blob/main/API30/docs-Mappings.txt): Mappings specified by an API method's Javadoc
  - [normal-permissions.txt](https://github.com/aper-project/aper-mapping/blob/main/API30/normal-permissions.txt): The permissions of *normal* protection level
  - [dangerous-permissions.txt](https://github.com/aper-project/aper-mapping/blob/main/API30/dangerous-permissions.txt): The permissions of *dangerous* protection level
  - [signature-permissions.txt](https://github.com/aper-project/aper-mapping/blob/main/API30/signature-permissions.txt): The permissions of *signature* (and *signatureOrSystem*) protection level

As for the evolution of mappings, the folders contain the changes of permission-protected APIs, an example folder:

+ [29_30](https://github.com/aper-project/aper-mapping/tree/main/Evolution/29_30)
  - [00-API-Added.txt](https://github.com/aper-project/aper-mapping/blob/main/Evolution/29_30/00-API-Added.txt): APIs added in the new version
  - [01-API-Deleted.txt](https://github.com/aper-project/aper-mapping/blob/main/Evolution/29_30/01-API-Deleted.txt): APIs delete in the new version
  - [02-Permission-Changed.txt](https://github.com/aper-project/aper-mapping/blob/main/Evolution/29_30/02-Permission-Changed.txt): APIs that change their required permissions in the new version

Additionally, there are two text files:

+ [evolve_dangerous_apis.txt](https://github.com/aper-project/aper-mapping/blob/main/Evolution/evolve_dangerous_apis.txt): APIs that have changed (added/deleted/changed) from API level 23 to level 30
+ [unchange_dangerous_apis.txt](https://github.com/aper-project/aper-mapping/blob/main/Evolution/unchange_dangerous_apis.txt): APIs that have not changed since API level 23

## Mapping Specification Formats

In the Android Java framework, there are two ways to specified an API's required permissions.

### Annotation-based

The first type is specifying permissions by the `@RequiresPermission` annotation. For example, the API `requestLastKnownLocation` in the [source code](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/android10-release/location/java/android/location/LocationManager.java#1424) is a such type:

```java
/**
 * Returns a Location indicating the data from the last known
 * location fix obtained from the given provider.
 *
 * <p> This can be done
 * without starting the provider.  Note that this location could
 * be out-of-date, for example if the device was turned off and
 * moved to another location.
 *
 * <p> If the provider is currently disabled, null is returned.
 *
 * @param provider the name of the provider
 * @return the last known location for the provider, or null
 *
 * @throws SecurityException if no suitable permission is present
 * @throws IllegalArgumentException if provider is null or doesn't exist
 */
@RequiresPermission(anyOf = {ACCESS_COARSE_LOCATION, ACCESS_FINE_LOCATION})
@Nullable
public Location getLastKnownLocation(@NonNull String provider) {
    checkProvider(provider);
    String packageName = mContext.getPackageName();
    LocationRequest request = LocationRequest.createFromDeprecatedProvider(
            provider, 0, 0, true);
    try {
        return mService.getLastLocation(request, packageName);
    } catch (RemoteException e) {
        throw e.rethrowFromSystemServer();
    }
}
```

### Javadoc-based

The second type is specifying permissions in the Javadoc, typically with an `@link` tag. An example is the API `removeAllGeofences`, in its [source code](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/android10-release/location/java/android/location/LocationManager.java#1244):


```java
/**
 * Remove all geofences registered to the specified pending intent.
 *
 * @param intent a pending intent previously passed to {@link #addGeofence}
 *
 * @throws IllegalArgumentException if intent is null
 * @throws SecurityException if {@link android.Manifest.permission#ACCESS_FINE_LOCATION}
 * permission is not present
 *
 * @hide
 */
public void removeAllGeofences(@NonNull PendingIntent intent) {
    checkPendingIntent(intent);
    String packageName = mContext.getPackageName();
    try {
        mService.removeGeofence(null, intent, packageName);
    } catch (RemoteException e) {
        throw e.rethrowFromSystemServer();
    }
}
```

## Versions Used

We used the following branches for each Android version in the framework [code base](https://android.googlesource.com/platform/frameworks/base):

| API Level | Version Number |       Branch      |
|:---------:|:--------------:|:-----------------:|
|     23    |       6.0      | [android-6.0.1_r81](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-6.0.1_r81) |
|     24    |       7.0      | [android-7.0.0_r36](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-7.0.0_r36) |
|     25    |       7.1      | [android-7.1.2_r39](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-7.1.2_r39) |
|     26    |       8.0      | [android-8.0.0_r49](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-8.0.0_r49) |
|     27    |       8.1      | [android-8.1.0_r79](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-8.1.0_r79) |
|     28    |        9       | [android-9.0.0_r59](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-9.0.0_r59) |
|     29    |       10       | [android-10.0.0_r41](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-10.0.0_r41) |
|     30    |       11       | [android-11.0.0_r28](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-11.0.0_r28) |
|     31    |       12       | [android-12.0.0_r27](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-12.0.0_r27) |
