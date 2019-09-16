# Xamarin Essentials DeviceInfo
First, add Xamarin Essentials via NuGet.

## Platform-Specific (Android) Setup
In MainActivity.cs file...
```
public override void OnRequestPermissionsResult(...)
{
    Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
}
```

## Let's code...
```
DeviceInfo.Name; // Device Name
DeviceInfo.DeviceType; // DeviceType (it's not string)
DeviceInfo.Model; // Model
DeviceInfo.Manufacturer; // Manufacturer
DeviceInfo.Platform; // Platform
DeviceInfo.Version; // Version (not a string, but can be used as a string)
```
