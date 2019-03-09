# filelogger
Represents a .NET Standard 2.0 rolling file logger implementation

# Installation
```cmd
Install-Package FileLogger -Version 1.0.0
```

# Configuration

## Add this to your Program.cs file
```C#

public static async Task Main(string[] args)
{
	workingDirectory = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location);
	Directory.SetCurrentDirectory(workingDirectory);
}

private static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
	var builder = WebHost.CreateDefaultBuilder(args)
		.UseStartup<Startup>()
		.ConfigureAppConfiguration((context, config) =>
		{
			config.SetBasePath(workingDirectory);
			config.AddXmlFile(Path.Combine(workingDirectory, "app.config"), false, true);
		});

	return builder;
}
```

## Add this to your Startup.cs file
```C#
public Startup(IHostingEnvironment hostingEnvironment, IConfiguration configuration, ILogger<Startup> logger)
{
    this.configuration = configuration;
}

public void ConfigureServices(IServiceCollection services)
{
	services.AddLogging(logging =>
	{
		logging.ClearProviders();
		logging.SetMinimumLevel(Enum.Parse<LogLevel>(this.configuration.GetValue<string>("logging:minimumLevel")));

		logging.AddFile(options =>
		{
			options.FileLogSwitches = this.configuration.GetSection("logging:switches:switch").GetChildren().Select(c => new FileLogSwitch(c.Key, Enum.Parse<LogLevel>(c.Value)));
			options.FilePath = this.configuration.GetValue<string>("logging:path");
		});
	});
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
