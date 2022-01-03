# Adding event handlers: Sounds, Dark Mode & Windowing
Note that the code from the previous blog implements the visual layer of the DrumPad using WinUI 3 controls. However, if you click on any buttons, nothing happens, because we have not defined any actions, which we will do in this blog. In this blog, you will be guided to accomplish the following: 
* Play a sound when a drum pad button is clicked
* Switch into dark mode when the toggle switch is turned on
* Go into a specific windowing mode when something from the drop-down box is selected



## Play a sound when a drum pad button is clicked

1. Download sound clips to your project's Assets folder

> **NOTE**: You may download any .wav sound clips for this part. Optionally, you could use my sound clips here as well. Store the sound clips under your project's Assets folder because that is the folder path we will provide to specify which sound to play. 

2. Install a NuGet Package in Visual Studio

> **NOTE**: Playing a sound in WinUI apps requires using a library called *System.Media*, accessible through a NuGet Package. Following these two steps to install the NuGet Package in Visual Studio:

* Navigate to Solution Explorer, under Dependencies, right-click Packages, select Manage NuGet Packages
* Select the **Browse** tab, search **System.Windows.Extensions** and **install** it


3.	Add the following namespace to **MainAppWindow.xaml.cs**:
```csharp
using System.Media;
```

4.	Create a call-back function that plays a sound inside class MainWindow : Window

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



Repeat step 4 and 5 to attach another sound clip to a different button until all the buttons have a click action! For your reference, check out my MainWindow.xaml.cs and MainWindow.xaml on GitGub.

## Implement dark mode
The dark mode is an app appearance where the content is laid out on a dark background. It gives the app a different aesthetic look and can improve readability, especially in a low-light environment.

1.	Define SolidColorBrush under the light and dark themes, respectively: 

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

3.	Define the action for ToggleSwitch:

##### MainWinow.xaml.cs:
```csharp
private void ToggleSwitch_Toggled(object sender, RoutedEventArgs e)
{
    ToggleSwitch toggleSwitch = sender as ToggleSwitch;
    if (toggleSwitch != null)
    {
        if (toggleSwitch.IsOn == true)
        {
            if (this.Content is FrameworkElement frameworkElement)
            {
                frameworkElement.RequestedTheme = ElementTheme.Dark;
            }
        }
        else
        {
            if (this.Content is FrameworkElement frameworkElement)
            {
                frameworkElement.RequestedTheme = ElementTheme.Light;
            }
        }
    }
}
```

4.	Link the call-back function above to the ToggleSwitch control:

##### MainWindow.xaml:
```xml
<ToggleSwitch … … Toggled="ToggleSwitch_Toggled" />
```



## Managing window display
Using the Windowing features in WinAppSDK provides a lot of flexibility and customization to your app experience. One of these customization features is the ability to change the Presenter mode of your window. There are 3 presenters you can use: Compact  Overlay, Fullscreen, and Overlapped mode, with overlapped being the default presenter.   

> **NOTE**: It’s worth mentioning that when we build and run the project solution, there will already be some functionalities like minimizing, maximizing, and closing the app window in the title bar.

1.	Add the following namespaces in MainWindow.xaml.cs file:
```csharp
using WinRT;
// Needed for WindowId
using Microsoft.UI;
// Needed for AppWindow
using Microsoft.UI.Windowing;
// Needed for XAML hwnd interop
using WinRT.Interop;
```

2.	Declare the following variables inside the class of class MainWindow : Window:
```csharp
AppWindow m_appWindow;
```

3.	Define a helper function GetAppWindowForCurrentWindow:
```csharp
private AppWindow GetAppWindowForCurrentWindow()
{
    IntPtr hWnd = WinRT.Interop.WindowNative.GetWindowHandle(this);
    WindowId myWndId = Microsoft.UI.Win32Interop.GetWindowIdFromWindow(hWnd);
    return AppWindow.GetFromWindowId(myWndId);
}
```

> **NOTE**: The preceding code gets the window Id for the hwnd and then uses that window Id to get the current AppWindow. This will create the WinAppSDK window layer that gives you access to all the window customization features of the app window. If you run your app again and click the drop-down button and choose Compact Overlay, the app window will not allow other app windows to overlap on top of it.

4.	Call GetAppWindowForCurrentWindow in public MainWindow():
```csharp
m_appWindow = GetAppWindowForCurrentWindow();
```

5.	Set the current app window to the Compact Overlay presenter via the SetPresenter method: 
```csharp
private void SwitchPresenter(object sender, RoutedEventArgs e)
{
    m_appWindow.SetPresenter(AppWindowPresenterKind.CompactOverlay);
}
```

6.	Link the MenuFlyouItem to trigger the SwitchPresenter function via **Click**:
##### MainWindow.xaml:
```xml
<MenuFlyoutItem Text="Compact Overlay" Click="SwitchPresenter"/>
```

> **NOTE**: You can save the state of the theme resource and persist the option the user chose at the next time of app startup with three easy steps. Follow [this blog post](https://github.com/jingwei-a-zhang/test-MDfiles/blob/main/DarkMode.md) to implement this functionality. 

## Results
<p align="center">

![](Media1%20(1).gif)

</p>
Just like that, you can see how we can leverage the amazing Windowing features to customize windowing options for your app. Repeat the same step 5 and 6 to implement the Fullscreen and Overlapped (the default) presenters. To learn more about Windowing, see Manage app windows for additional information on what you can do with this feature. 

## Wrapping up
Next, we will keep evolving the app with [app single-instancing](https://github.com/jingwei-a-zhang/test-MDfiles/blob/main/single-instancing.md). This branch covers all the code introduced in this blog.
