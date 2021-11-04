## <img src="azurePubSub.png" width="70" /> Azure PubSub 

## Links

[Azure Web PubSub service client library](https://docs.microsoft.com/en-us/dotnet/api/overview/azure/messaging.webpubsub-readme-pre)

[Getting Started with Azure Web PubSub Service](https://dailydotnettips.com/getting-started-with-azure-web-pubsub-service/)

[Demo for testing deployed service](https://azure.github.io/azure-webpubsub/demos/clientpubsub)

[Example chat platform with Auth](https://github.com/benc-uk/chatr)

## Using WebPubSubServiceClient to send messages

To register the `WebPubSubServiceClient` for ASP.NET Core dependency injection.

``` Csharp
    internal class AzurePubSubOptions
    {
        public string ConnectionString { get; set; }
        public string HubName { get; set; }
    }
    
    internal static class WebPubSubFeatureExtensions
    {
        internal static void AddWebPubSubServices(this IServiceCollection services, IConfiguration configuration)
        {
            services.Configure<AzurePubSubOptions>(configuration.GetSection("AzurePubSub"));
            services.AddAzureClients(builder =>
            {
                var options = new AzurePubSubOptions();
                configuration.GetSection("AzurePubSub").Bind(options);
                builder.AddWebPubSubServiceClient(options.ConnectionString, options.HubName);
            });
        }
    }
```

Define the required information within `appsettings.json` (only for testing) or in keyvault:
``` Csharp
{
  "AzurePubSub": {
    "ConnectionString": "<ConnectionString>",
    "HubName": "<HubName>"
  }
}
```


Inject client to a service and send messages to all:

``` Csharp
    public class SomeService
    {
        private readonly WebPubSubServiceClient _pubSubServiceClient;

        public SomeService(WebPubSubServiceClient pubSubServiceClient)
        {
            _pubSubServiceClient = pubSubServiceClient;
        }

        public async Task SendMessageAsync()
        {
            var telemetryDataPoint = new
            {
                temp = 123,
            };
            
            var messageString = JsonSerializer.Serialize(telemetryDataPoint);
            var response = await _pubSubServiceClient.SendToAllAsync(messageString, ContentType.ApplicationJson);
        }
    }
```

## Test client program

To test retrieve these messages from Azure the following console program provided by [microsoft tutorial](https://docs.microsoft.com/en-us/azure/azure-web-pubsub/tutorial-pub-sub-messages?tabs=csharp) can be used:

``` Csharp
  class Program
  {
    static async Task Main(string[] args)
    {
      if (args.Length != 2)
      {
        Console.WriteLine("Usage: subscriber <connectionString> <hub>");
        return;
      }
      var connectionString = args[0];
      var hub = args[1];

      // Either generate the URL or fetch it from server or fetch a temp one from the portal
      var serviceClient = new WebPubSubServiceClient(connectionString, hub);
      var url = serviceClient.GenerateClientAccessUri();

      using (var client = new WebsocketClient(url))
      {
        // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
        client.ReconnectTimeout = null;
        client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
        await client.Start();
        Console.WriteLine("Connected.");
        Console.Read();
      }
    }
  }
```

