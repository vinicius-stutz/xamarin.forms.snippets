# Download Files In Xamarin.Forms
## AndroidManifest
1. Expand your Android Project and open Properties.
2. Then add or check the following permissions.
	- INTERNET
	- WRITE EXTERNAL STORAGE
3.  Then click Save.
## info.plist
1. Expand your iOS Project and Open your Info.plist file with XML Editor.
2. Then add the following Permissions.
```
<key>NSPhotoLibraryAddUsageDescription </key>
<string>Need permission to save files.</string>
```
3. It provides permission to save file.
```
<key>NSPhotoLibraryUsageDescription </key>
<string>Need permission to access files.</string>
```
## Dependency Service
1. First, we need to create an interface in your PCL or Shared Projects. In my case, I have created an Interface named “IDownloader.cs”.
2. Then Paste the following code in that.
```
public  interface IDownloader
{
	void DownloadFile(string url, string folder);
	event EventHandler<DownloadEventArgs> OnFileDownloaded;
}
```
3. Here, I have create a custom event handler to notify app users about file download. You have to create a class named **“DownloadEventArgs”** and paste the following code.
```
public  class DownloadEventArgs : EventArgs
{
	public bool FileSaved = false;
	public DownloadEventArgs(bool fileSaved)
	{
		FileSaved = fileSaved;
	}
}
```
## Android
1.  Create a class named “AndroidDownloader.cs” in your Android Project and implement the class with “IDownloader” interface created in your Portable Library.
2.  We can use WebClient to download any file from the given URL. WebClient is used for both Android and iOS Platforms to download files.
3.  You can find the code used in Android Platform.
```
public class AndroidDownloader : IDownloader  
{  
    public event EventHandler<DownloadEventArgs> OnFileDownloaded;  
  
    public void DownloadFile(string url, string folder)  
    {  
        string pathToNewFolder = Path.Combine(Android.OS.Environment.ExternalStorageDirectory.AbsolutePath, folder);  
        Directory.CreateDirectory(pathToNewFolder);  
  
        try  
        {  
            var webClient = new WebClient();  
            webClient.DownloadFileCompleted += new AsyncCompletedEventHandler(Completed);  
            string pathToNewFile = Path.Combine(pathToNewFolder, Path.GetFileName(url));  
            webClient.DownloadFileAsync(new Uri(url), pathToNewFile);  
        }  
        catch (Exception ex)  
        {  
            if (OnFileDownloaded != null)  
                OnFileDownloaded.Invoke(this, new DownloadEventArgs(false));  
        }  
    }  
  
    private void Completed(object sender, AsyncCompletedEventArgs e)  
    {  
        if (e.Error != null)  
        {  
            if (OnFileDownloaded != null)  
                OnFileDownloaded.Invoke(this, new DownloadEventArgs(false));  
        }  
        else  
        {  
            if (OnFileDownloaded != null)  
                OnFileDownloaded.Invoke(this, new DownloadEventArgs(true));  
        }  
    }  
}  
```
4.  Here, WebClient has an async event for notifying the download event completion.
5.  The Download event is notified by invoking the custom event created with Dependency Service from Android Platform code.
## iOS
1.  Create a class named “iOSDownloader.cs” in your iOS Project and implement the class with “IDownloader” interface created in your Portable Library.
2.  We can use WebClient to download any file from the given URL. WebClient is used for both Android and iOS Platforms to download files.
3.  You can find the code used in iOS Platform.
```
public class IosDownloader : IDownloader  
{  
    public event EventHandler<DownloadEventArgs> OnFileDownloaded;  
  
    public void DownloadFile(string url, string folder)  
    {  
        string pathToNewFolder = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Personal), folder);  
        Directory.CreateDirectory(pathToNewFolder);  
  
        try  
        {  
            WebClient webClient = new WebClient();  
            webClient.DownloadFileCompleted += new AsyncCompletedEventHandler(Completed);  
            string pathToNewFile = Path.Combine(pathToNewFolder, Path.GetFileName(url));  
            webClient.DownloadFileAsync(new Uri(url), pathToNewFile);  
        }  
        catch (Exception ex)  
        {  
            if (OnFileDownloaded != null)  
                OnFileDownloaded.Invoke(this, new DownloadEventArgs(false));  
        }  
    }  
  
    private void Completed(object sender, AsyncCompletedEventArgs e)  
    {  
        if (e.Error != null)  
        {  
            if (OnFileDownloaded != null)  
                OnFileDownloaded.Invoke(this, new DownloadEventArgs(false));  
        }  
        else  
        {  
            if (OnFileDownloaded != null)  
                OnFileDownloaded.Invoke(this, new DownloadEventArgs(true));  
        }  
    }  
}  
```
4.  Here, WebClient has an async event for notifying the download event completion.
5.  The Download event is notified by invoking the custom event created with Dependency Service from iOS Platform code.

**OBS:** Don’t forget to add the following lines above the namespace of your Dependency Service classes `[assembly: Dependency(typeof(Dependency_Class_Name))]`
## Implementing the functionality in PCL
The following code shows the following points
-   How to subscribe the download event.
-   How to call the download function.
```
public partial class MainPage : ContentPage  
{  
    IDownloader downloader = DependencyService.Get<IDownloader>();  
    public MainPage()  
    {  
        InitializeComponent();  
        downloader.OnFileDownloaded += OnFileDownloaded;  
    }  
  
    private void OnFileDownloaded(object sender, DownloadEventArgs e)  
    {  
        if (e.FileSaved)  
        {  
            DisplayAlert("XF Downloader", "File Saved Successfully", "Close");  
        }  
        else  
        {  
            DisplayAlert("XF Downloader", "Error while saving the file", "Close");  
        }  
    }  
  
    private void DownloadClicked(object sender, EventArgs e)  
    {  
        downloader.DownloadFile("http://www.url.com/image/test.jpg", "XF_Downloads");  
    }  
}  
```
