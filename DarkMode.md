## Implementing dark mode
The dark mode is an app appearance where the content is laid out on a dark background. It improves readability, especially in a low-light environment, and give the app a different aesthetic appearance.

1.	Define Light and Dark themes in App.xaml: 
```xml
<ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Light">

    </ResourceDictionary>
    <ResourceDictionary x:Key="Dark">

    </ResourceDictionary>
</ResourceDictionary.ThemeDictionaries>
```


2.	Add these SolidColorBrush to the Light and Dark Mode:
```xml
<ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Light">
        <SolidColorBrush x:Key="HomePageBackgroundBrush" Color="Transparent"/>
        <SolidColorBrush x:Key="PageHeaderForegroundBrush" Color="Black"/>
        <SolidColorBrush x:Key="ButtonColor1" Color="Red" />

    </ResourceDictionary>
    <ResourceDictionary x:Key="Dark">
        <SolidColorBrush x:Key="HomePageBackgroundBrush" Color="Black"/>
        <SolidColorBrush x:Key="PageHeaderForegroundBrush" Color="White"/>
        <SolidColorBrush x:Key="ButtonColor1" Color="DarkRed"  />
    </ResourceDictionary>
</ResourceDictionary.ThemeDictionaries>
```


3.	Specify a theme on the outermost grid in MainWindow.xaml: 
```xml
<Grid Background="{ThemeResource HomePageBackgroundBrush}">
```

4.	Define ToggleSwitch action in MainWinow.xaml.cs:
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

5.	Add above call back function to ToggleSwitch in MainWindow.xaml:
```xml
<ToggleSwitch … … Toggled="ToggleSwitch_Toggled" />
```


> **Optional**: You can save the state of the theme resource and persist the option the user chose at the next app launch
1.	Save the requested theme in local app settings in Toggle_Switch_Toggled:

```csharp
private void ToggleSwitch_Toggled(object sender, RoutedEventArgs e)
{
    ToggleSwitch toggleSwitch = sender as ToggleSwitch;
    if (toggleSwitch != null)
    {
        …
    }

    ApplicationData.Current.LocalSettings.Values["themeSetting"] = ((ToggleSwitch)sender).IsOn ? 0 : 1;
}
```


2.	Retrieve the saved theme setting when the app is restarted
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

3.	Apply the saved theme setting when the app is restarted in MainWindow.xaml: 
```xml
<ToggleSwitch … Toggled="ToggleSwitch_Toggled" Loaded="ToggleSwitch_Loaded"/>
```


Lauch the app again, you can switch between light/dark theme. You should see your choice for the theme is restored if you launch the app again.