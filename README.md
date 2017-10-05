# Debugging .NET apps on Cloud Foundry
The following is a guide on how to debug .NET Core applications on Cloud Foundry Linux containers from Visual Studio.

1. Download the package from the releases page
2. Publish your app with debug symbols `dotnet publish -f netcoreapp2.0 -r ubuntu.14.04-x64 -c Debug -o publish`
3. Extract the downloaded zip into your `publish\clrdbg` folder
4. `cf push` the app from publish folder
5. Create `launch.xml` file as following:
```xml
<PipeLaunchOptions xmlns="http://schemas.microsoft.com/vstudio/MDDDebuggerOptions/2014"
                   PipePath="cf.exe" 
                   PipeArguments="ssh YOUR_APP_NAME -command &quot;~/app/clrdbg/clrdbg --interpreter=mi&quot;"
                   TargetArchitecture="x64" 
                   MIMode="clrdbg" 
                   ProcessId="41">
  <LaunchCompleteCommand>None</LaunchCompleteCommand>
</PipeLaunchOptions>
```
Make sure to adjust PipeArguments

6. Run the following command and record PID of your app
```
> cf ssh YOUR_APP_NAME --command "ps -d"
```
7. Replace the value of `ProcessId` in `launch.xml` with PID for your app
8. From Visual Studio, open Command window (View > Other Windows > Command Window)
9. Run the following in VS Command Window
```
> Debug.MIDebugLaunch /Executable:dotnet /OptionsFile:"C:\FULL_PATH\launch.xml"
```

We should now be debugging

:warning: After you detach, the remote debugger terminates the process which will restart the container.
