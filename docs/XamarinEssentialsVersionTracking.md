# Version Tracking Using Xamarin.Essentials
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
### With current app version
```
VersionTracking.IsFirstLaunchEver; // Is First Launch Ever (bool)
VersionTracking.IsFirstLaunchForCurrentVersion; // Is First Launch for Current Version (bool)
VersionTracking.IsFirstLaunchForCurrentBuild; // Is First Launch for Current Build (bool)
VersionTracking.CurrentVersion; // Current version number (it's not string)
VersionTracking.CurrentBuild; // Current build number (it's not string)
```
### With app version history
```
VersionTracking.PreviousVersion; // Previous version number (it's not string, check if is null)
VersionTracking.PreviousBuild; // Previous build number (it's not string, check if is null)
VersionTracking.FirstInstalledVersion; // First Installed Version (it's not string)
VersionTracking.FirstInstalledBuild; // First Installed Build (it's not string)

foreach(string item in VersionTracking.VersionHistory) // IEnumerable<string>
{
  yourVar += $"{item}, "; // sample
}

foreach(string item in VersionTracking.BuildHistory) // IEnumerable<string>
{
  yourVar += $"{item}, "; // sample
}
```
