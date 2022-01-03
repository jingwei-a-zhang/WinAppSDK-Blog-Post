## Persist user choice for dark mode 
This short blog post is dedicated to demonstrating how to save the dark mode option a user has specified from the last session.


1.	Save the requested theme in local app settings in Toggle_Switch_Toggled:

##### App.xaml:
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

##### App.xaml:
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

3.	Apply the saved theme setting when the app is restarted: 

##### MainWindow.xaml:
```xml
<ToggleSwitch … Toggled="ToggleSwitch_Toggled" Loaded="ToggleSwitch_Loaded"/>
```


Launch the app again, you can switch between light/dark theme. You should see your choice for the theme is restored if you launch the app again.