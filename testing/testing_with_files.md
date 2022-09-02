# How to use test files as data source

The following snipped uses test data from json-file

```Csharp
public static Stream GetEmbeddedResourceStream(this Assembly assembly, string relativeResourcePath)
{
    if (string.IsNullOrEmpty(relativeResourcePath))
    {
        throw new ArgumentNullException(nameof(relativeResourcePath));
    
    var resourcePath =
        $"{Regex.Replace(assembly.ManifestModule.Name, @"\.(exe|dll)$", string.Empty, RegexOptions.IgnoreCase)}.{relativeResourcePath}"
    var stream = assembly.GetManifestResourceStream(resourcePath);
    if (stream is null)
    {
        throw new ArgumentException($"The specified embedded resource \"{relativeResourcePath}\" is not found.");
    }
    
    return stream;
}
```
Add files as embedded resources:
```xml
<ItemGroup>
  <None Remove="TestData\data.json" />
  <EmbeddedResource Include="TestData\data.json" />
</ItemGroup>
```

How to use:

``` Csharp
const string fileName = "data.json";
var dataStream = Assembly.GetExecutingAssembly().GetEmbeddedResourceStream($"TestData.{fileName}");
var data = await BinaryData.FromStreamAsync(dataStream);
```
