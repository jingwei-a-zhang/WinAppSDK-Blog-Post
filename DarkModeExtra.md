## Persist user's choice for dark mode across sessions
This short blog post is dedicated to demonstrating how to save the dark mode option a user has specified from [the previous session](https://github.com/jingwei-a-zhang/WinAppSDK-Blog-Post/blob/main/Windowing.md).


1.	Add the following namespace:

##### MainWindow.xaml.cs:
```csharp
using Windows.Storage;
```

2. Save the requested theme in local app settings in *class MainWindow : Window* :

##### MainWindow.xaml.cs:
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
    ApplicationData.Current.LocalSettings.Values["themeSetting"] = ((ToggleSwitch)sender).IsOn ? 0 : 1;
}
```


3.	Retrieve the saved theme setting when the app is restarted in *class MainWindow : Window* :

##### MainWindow.xaml.cs:
```csharp
private void ToggleSwitch_Loaded(object sender, RoutedEventArgs e)
{
    if (ApplicationData.Current.LocalSettings.Values.TryGetValue("themeSetting", out object themeSetting) && (int)themeSetting == 0)
    {
        dark_switch.IsOn = true;
    } else
    {
        dark_switch.IsOn = false;
    }
}
```

4.	Apply the saved theme setting when the app is restarted: 

##### MainWindow.xaml:
```xml
<ToggleSwitch ... ... Loaded="ToggleSwitch_Loaded"/>
```

## Wrapping up
Run your app and choose a theme. After that, restart the app. You will see that the choice you made from the previous app session gets restored at the next app launch. 

[Here](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/tree/9b0fd39911734e045b570bfe50575308a5bf7c44) is the code used in this blog for your reference. Click [here](https://github.com/jingwei-a-zhang/WinAppSDK-Blog-Post/blob/main/Windowing.md) to resume where you left off from the previous blog.