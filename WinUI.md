# Build your first WinUI 3 app
Through this tutorial, you will learn how to write a WinUI 3 desktop app. You will use the Windows App SDK, which decouples APIs and tools from the operating system and provides a modernized UI framework for Windows 10/11 app development. This blog aims to help members new to Windows development quickly build familiarity using WinAppSDK by guiding you through a fun sample app I created. 



## Sample app overview
We are going to build an app with buttons that produce drum sounds (DrumPad). As simple as this demo app is, it shows some cool things you can do right away with WinAppSDK:
* Working with simple and easy-to-use UI controls from Windows UI Library (WinUI) 3
* Customizing windowing options so that the user can multitask with the app always on top or have a full immersive experience 
* Making the app single-instanced to reduce synchronization issues between instances 

**This blog is dedicated to showing you how to use WinUI 3 controls to create a simple working user interface.** I'll leave windowing and single instancing for the following two blogs. I've posted the code used in this blog on [GitHub](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/tree/b748f19d15fd7b3df10e24ebf5bb6a03bec6ada7) for your reference.

## Prerequisites 
1. Install tools for developing apps for Windows 10 and Windows 11 [here](https://docs.microsoft.com/en-us/windows/apps/windows-app-sdk/set-up-your-development-environment?tabs=vs-2022).
2. Create a WinUI 3 app that uses the Windows App SDK [here](https://docs.microsoft.com/en-us/windows/apps/winui/winui3/create-your-first-winui3-app?pivots=winui3-packaged-csharp).

> **Optional**: If you are new to Windows development, we recommend taking a [C# crash course](https://docs.microsoft.com/en-us/shows/CSharp-101/?WT.mc_id=Educationalcsharp-c9-scottha&page=1) to familiarize yourself with the syntax and other basics.

## Project template structure
In WinUI 3 project template, code is broken down into two main parts: App.xaml and MainWindow.xaml. Most of our code will live in MainWindow.xaml and MainWindow.xaml.cs
* **MainWindow.xaml**: this is a XAML file where we will define all of our UI elements, called “controls”, that will make up the visual layer of our application.
* **MainWindow.xaml.cs**: This is the C# code-behind file where we will put all the logic that lets us interact with our application.


## UI layout design
The following WinUI 3 controls are used to represent components of a drum pad:
* [DropDownButton](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.dropdownbutton?view=winui-3.0): includes a chevron to indicate a menu can be opened
* [ToggleSwitch](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.toggleswitch?view=winui-3.0): a switch that can be toggled between dark/light mode
* [Button](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.button?view=winui-3.0): has a click user interaction and will trigger a unique drum sound
* [Grid](https://docs.microsoft.com/en-us/windows/winui/api/microsoft.ui.xaml.controls.grid?view=winui-3.0): made of rows and columns and is used to position the other controls


![](Group%20123.png)

## UI layout implementation
1.	In Visual Studio, select **Create a new project**
2.	In the project drop-down filters, select **C#**, **Windows**, and **WinUI**, respectively
3.	Select the Blank App, Packaged (WinUI 3 in Desktop) template, name it **DrumPad**, and click Create
4.  After the project is created, choose your target platform. (Here we use  **x64**)
5.	Delete the following code blocks that came with the template:

##### MainWindow.xaml.cs:

```csharp
private void myButton_Click(object sender, RoutedEventArgs e)
{
    myButton.Content = "Clicked";
}
```

##### MainWindow.xaml:
```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" VerticalAlignment="Center">
    <Button x:Name="myButton" Click="myButton_Click">Click Me</Button>
</StackPanel>
```
> **NOTE**: From this point on, all the code will go into **MainWindow.xaml**

6.	Add all 3 Grids and specify how their inner controls should be positioned **within the \<Window> tags**:
```xml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>

    <Grid Margin="12">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
    </Grid>
    
    <Grid x:Name="Control1" Grid.Row="1" ColumnSpacing="30" RowSpacing="12"
          VerticalAlignment="Center" HorizontalAlignment="Center">
    </Grid>
</Grid>
```

> **NOTE**: Auto and * are used to specify the height and width above to create a responsive layout. The controls held within the Grids are not fixed in size and can adjust to fill in spaces as the user resizes the app window. 

7.	Add DropDownButton & ToggleSwitch **below </Grid.ColumnDefinitions>** :

```xml
<DropDownButton Content="Display" Grid.Column="0" VerticalAlignment="Center" HorizontalAlignment="Left" Width="118" >
    <DropDownButton.Flyout>
        <MenuFlyout Placement="Bottom">
            <MenuFlyoutItem Text="Default"/>
            <MenuFlyoutItem Text="Compact Overlay"/>
            <MenuFlyoutItem Text="Fullscreen"/>
        </MenuFlyout>
    </DropDownButton.Flyout>
</DropDownButton>

<ToggleSwitch AutomationProperties.Name="simple ToggleSwitch" x:Name="dark_switch" Grid.Column="1" CornerRadius="3" VerticalAlignment="Center"  HorizontalAlignment="Right" MinWidth="0" HorizontalContentAlignment="Center" VerticalContentAlignment="Center" />
```



8.	Add Buttons **above the second last \</Grid> tag**, which is close to the bottom of the codebase:

```xml
<Button Background="#FF0000" Grid.Row="0" Grid.Column="0" Content="Kick 1"/>
<Button Background="#C4425A" Grid.Row="0" Grid.Column="1" Content="Kick 2"/>
<Button Background="#D41DD8" Grid.Row="0" Grid.Column="2" Content="Kick 3"/>
<Button Background="#6A21A3" Grid.Row="0" Grid.Column="3" Content="Kick 4"/>

<Button Background="#C4425A" Grid.Row="1" Grid.Column="0" Content="Clap 1"/>
<Button Background="#D41DD8" Grid.Row="1" Grid.Column="1" Content="Clap 2"/>
<Button Background="#6A21A3" Grid.Row="1" Grid.Column="2" Content="Clap 3"/>
<Button Background="#473DB8" Grid.Row="1" Grid.Column="3" Content="Clap 4"/>

<Button Background="#D41DD8" Grid.Row="2" Grid.Column="0" Content="Open Hat 1"/>
<Button Background="#6A21A3" Grid.Row="2" Grid.Column="1" Content="Open Hat 2"/>
<Button Background="#473DB8" Grid.Row="2" Grid.Column="2" Content="Open Hat 3"/>
<Button Background="#26AAC7" Grid.Row="2" Grid.Column="3" Content="Open Hat 4"/>

<Button Background="#6A21A3" Grid.Row="3" Grid.Column="0" Content="Snare 1"/>
<Button Background="#473DB8" Grid.Row="3" Grid.Column="1" Content="Snare 2"/>
<Button Background="#26AAC7" Grid.Row="3" Grid.Column="2" Content="Snare 3"/>
<Button Background="#1A7BD6" Grid.Row="3" Grid.Column="3" Content="Snare 4"/>
```


9.	Specify how the bottom Grid should position those Buttons that just got added to it:
> **NOTE**: put the following code above \<Button Background="#FF0000" ... "Kick 1"/>
```xml
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
    </Grid.ColumnDefinitions>
    
    <Grid.RowDefinitions>
        <RowDefinition Height="*" />
        <RowDefinition Height="*" />
        <RowDefinition Height="*" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
```


10.	Finally, apply some standardized styling to all the Buttons in this grid:
> **NOTE**: put the following code below \</Grid.RowDefinitions> from step 9
```xml
<Grid.Resources>
    <Style TargetType="Button">
        <Setter Property="HorizontalAlignment" Value="Stretch" />
        <Setter Property="VerticalAlignment" Value="Stretch" />
        <Setter Property="CornerRadius" Value="5" />
        <Setter Property="Foreground" Value="White" />
        <Setter Property="FontSize" Value="36" />
    </Style>
</Grid.Resources>
```

## Result
As simple as that, we should have a working visual layer built out using WinUI 3 controls. Press F5 to build and run the solution, you should see: 

![](image%204%20(1).png)

## Wrapping up
Next, we will dive into [implementing the business logic](https://github.com/jingwei-a-zhang/test-MDfiles/blob/main/t2.md). Check out [this branch](https://github.com/jingwei-a-zhang/WinAppSDK-DrumPad/tree/b748f19d15fd7b3df10e24ebf5bb6a03bec6ada7) for the code used in this blog.

  [1]: image%203%20(2).png