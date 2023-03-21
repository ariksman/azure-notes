# Setup NuGet feeds

Below are instructions how to modify the NuGet feeds and use private & local folders as the source of packages.

## How to use external NuGet plugin to authenticate to the private feed

Most simples way to achieve the desired goal is to use additional NuGet plugin provided by Microsoft: https://github.com/Microsoft/artifacts-credprovider
It works well on Windows and OSX for Azure hosted NuGet feeds. Instructions available via the link.

## How to manually customize NuGet.Config file

First download the NuGet.exe tool from https://www.nuget.org/downloads

Instructions how to use NuGet packages with the notebooks:
https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md

Add feed with user and password authentication
```
.\nuget.exe sources add -name "<PrivateNugetFeed>" -source <url> -username <username> -password <password>
```

Add api-key
```
.\nuget.exe setapikey <api-key> -source <url>
```

The above command will result as following NuGet.Config file in your local user folder: `C:\Users\{UserName}\AppData\Roaming\NuGet` or on OSX `/Users/{username}/.nuget/NuGet/NuGet.Config`.
Additionally, below is an example of using local folder and its content as NuGet feed.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
    <add key="LocalFolder" value="C:\Development\CustomNugetFolder" />
    <add key="PrivateNugetFeed" value="<url>" />
  </packageSources>
  <packageSourceCredentials>
    <OutotecApiPlatformShared>
        <add key="Username" value="<username>" />
        <add key="Password" value="<password-auto-filled-by-nuget-command>" />
      </OutotecApiPlatformShared>
  </packageSourceCredentials>
  <apikeys>
    <add key="<url>" value="<api-key-autofilled-by-nuget-command>" />
  </apikeys>
</configuration>
```
