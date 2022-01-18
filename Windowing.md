# Adding event handlers: Sounds, Dark Mode & Windowing (Part 2)
Note that in part 1 of the WinAppSDK Blog Series you implemented the visual layer of the DrumPad using WinUI 3 controls. However, if you click on any buttons, nothing happens, because we have not defined any actions, which we will do now. You will be guided to accomplish the following in this post: 
* Play a sound when a drum pad button is clicked
* Switch into dark mode when the toggle switch is turned on
* Go into a specific windowing mode when a presenter mode is selected



## Play a sound when a drum pad button is clicked

1. Download sound clips to your project's Assets folder

> **NOTE**: You may download any .wav sound clips for this part. Optionally, you could use my sound clips [here](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/tree/5c6e560edf74ceb0a40183e20b66741175a046d7/DrumPad/DrumPad/Assets) as well. Store the sound clips under your project's Assets folder because that is the folder path we will provide to specify which sound to play. 

2. Install the *System.Windows.Extensions* NuGet Package

> **NOTE**: Playing a sound in WinUI apps requires using a library called *System.Media*, accessible through a NuGet Package. Following these two steps to install the NuGet Package in Visual Studio:

* Navigate to **Solution Explorer**, under Dependencies, **right-click** on Packages, select Manage NuGet Packages
* Select the **Browse** tab, search **System.Windows.Extensions** and **install** it


3.	Add the following namespace to **MainAppWindow.xaml.cs**:
```csharp
using System.Media;
```

4.	Create a call-back function that plays a sound in *class MainWindow : Window* :

##### MainAppWindow.xaml.cs:
```csharp
private void pad_clicked(object sender, RoutedEventArgs e)
{
    // get the full path to your app’s folder where it is installed
    var installedPath = Windows.ApplicationModel.Package.Current.InstalledLocation.Path;
    // join path above with the sub-paths in your Assets folder and the specific sound file
    var soundFile = Path.Join(installedPath, "Assets", "rim.wav");

    SoundPlayer player = new System.Media.SoundPlayer(soundFile);
    player.Play();
}
```


5.	Link the call-back function above to a button via the **Click** parameter:

##### MainWindow.xaml:
```xml
<Button Grid.Row="0" Grid.Column="0" … … Click="pad_clicked" />
```



Repeat step 4 and 5 to attach another sound clip to a different button until all the buttons have a click action! For your reference, check out my [MainWindow.xaml.cs](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/blob/App_Logic/DrumPad/DrumPad/MainWindow.xaml.cs) and [MainWindow.xaml](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/blob/App_Logic/DrumPad/DrumPad/MainWindow.xaml) on GitHub.

## Implement dark mode
The dark mode is an app appearance where the content is laid out on a dark background. It gives the app a different aesthetic look and can improve readability, especially in a low-light environment.

1.	Define SolidColorBrush under the light and dark themes below the comment \<!-- Other app resources here -->: 

##### App.xaml:
```xml
<ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Light">
        <SolidColorBrush x:Key="HomePageBackgroundBrush" Color="Transparent"/>
    </ResourceDictionary>
    <ResourceDictionary x:Key="Dark">
        <SolidColorBrush x:Key="HomePageBackgroundBrush" Color="Black"/>
    </ResourceDictionary>
</ResourceDictionary.ThemeDictionaries>
```


2.	Indicate which theme to use via the **Background** parameter (here we specified it on the outermost Grid in MainWindow.xaml): 

##### MainWindow.xaml:
```xml
<Grid Background="{ThemeResource HomePageBackgroundBrush}">
```

3.	Define the action for ToggleSwitch in *class MainWindow : Window* :

##### MainWinow.xaml.cs:
```csharp
private void ToggleSwitch_Toggled(object sender, RoutedEventArgs e)
{
    ToggleSwitch toggleSwitch = sender as ToggleSwitch;
    if (toggleSwitch.IsOn) {
        ((FrameworkElement)this.Content).RequestedTheme = ElementTheme.Dark;
    }
    else {
        ((FrameworkElement)this.Content).RequestedTheme = ElementTheme.Light;
    }
}
```

4.	Link the call-back function above to the ToggleSwitch control:

##### MainWindow.xaml:
```xml
<ToggleSwitch … … Toggled="ToggleSwitch_Toggled" />
```

> **NOTE**: You can save the state of the theme resource and persist the option the user chose at the next time of app startup with four easy steps. Follow [this blog post](https://github.com/jingwei-a-zhang/test-MDfiles/blob/main/DarkModeExtra.md) to implement this functionality. 

## Managing window display
Using the Windowing features in WinAppSDK provides a lot of flexibility and customization to your app experience. One of these customization features is the ability to change the Presenter mode of your window. There are 3 presenters you can use: Compact  Overlay, Fullscreen, and Overlapped mode, with overlapped being the default presenter.   


1.	Add the following namespaces:

##### MainWindow.xaml.cs:
```csharp
using WinRT;
// Needed for WindowId
using Microsoft.UI;
// Needed for AppWindow
using Microsoft.UI.Windowing;
// Needed for XAML hwnd interop
using WinRT.Interop;
```

2.	Declare the following variable in *class MainWindow : Window* :

##### MainWindow.xaml.cs:
```csharp
AppWindow m_appWindow;
```

3.	Define a helper method *GetAppWindowForCurrentWindow()* :

##### MainWindow.xaml.cs:
```csharp
private AppWindow GetAppWindowForCurrentWindow()
{
    IntPtr hWnd = WinRT.Interop.WindowNative.GetWindowHandle(this);
    WindowId myWndId = Microsoft.UI.Win32Interop.GetWindowIdFromWindow(hWnd);
    return AppWindow.GetFromWindowId(myWndId);
}
```

> **NOTE**: The preceding code gets the window Id for the hwnd and then uses that window Id to get the current AppWindow. This will create the WinAppSDK window layer that gives you access to all the window customization features of the app window. 

4.	Call GetAppWindowForCurrentWindow in the **MainWindow() method**:

##### MainWindow.xaml.cs:
```csharp
m_appWindow = GetAppWindowForCurrentWindow();
```

5.	Set the current app window to the Compact Overlay presenter via the SetPresenter method in *class MainWindow : Window* : 

##### MainWindow.xaml.cs:
```csharp
private void SwtichPresenter_CompOverlay(object sender, RoutedEventArgs e)
{
    m_appWindow.SetPresenter(AppWindowPresenterKind.CompactOverlay);
}
```

6.	Modify the MenuFlyouItem control to add the SwtichPresenter_CompOverlay function using the **Click** parameter:

##### MainWindow.xaml:
```xml
<MenuFlyoutItem Text="Compact Overlay" Click="SwtichPresenter_CompOverlay"/>
```

>**NOTE**: Repeat the steps 5 and 6 to implement the FullScreen and Overlapped (the default) presenters. The only change needed is to modify step 5 to use AppWindowPresenterKind.Overlapped or AppWindowPresenterKind.FullScreen.

## Result
<p align="center">

![](Media1%20(1).gif)

</p>

Just like that, you can see how we can leverage the amazing Windowing features to customize windowing options for your app. If you run your app again and click the drop-down button and choose Compact Overlay, the app window will not allow other app windows to overlap on top of it. To learn more about Windowing, see [Manage app windows](https://docs.microsoft.com/en-us/windows/apps/windows-app-sdk/windowing/windowing-overview) for additional information on what you can do with this feature. 

[Here](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/tree/App_Logic) is the completed code covered in this post.

## Moving forward
Next, we will keep evolving the app with [single-instancing](https://github.com/jingwei-a-zhang/test-MDfiles/blob/main/SingleInstancing.md).  
