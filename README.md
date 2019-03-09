# filelogger
Represents a .NET Standard 2.0 rolling file logger implementation

# How to use

## Add this to your Program.cs file
```C#

private string workingDirectory = null;

public static async Task Main(string[] args)
{
  workingDirectory = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location);
  Directory.SetCurrentDirectory(workingDirectory);
}
    
private static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
  var builder = WebHost.CreateDefaultBuilder(args)
  .UseStartup<Startup>()
  .ConfigureAppConfiguration(config =>
  {
    config.SetBasePath(workingDirectory);
    config.AddXmlFile($"{workingDirectory}\\app.config", false, true);
  });

  return builder;
}
```

## Add this to your Startup.cs file
```C#
public Startup(IHostingEnvironment hostingEnvironment, ILogger<Startup> logger)
{
  var workingDirectory = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location);
  Directory.SetCurrentDirectory(workingDirectory);
  this.configuration = new ConfigurationBuilder().SetBasePath(workingDirectory).AddXmlFile($"{workingDirectory}\\app.config", false, true).Build();
}
```

## Add this to your config file
```xml
  <logging path=".\rolling_log_file.log">
    <minimumLevel>Trace</minimumLevel>
    <switches>
      <switch name="MyNamespace">Debug</switch>
      <switch name="MyNamespace.Core">Debug</switch>
    </switches>
  </logging>
```
