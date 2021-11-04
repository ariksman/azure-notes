## <img src="azurePubSub.png" width="70" /> Azure PubSub 

## Links
[Getting Started with Azure Web PubSub Service](https://dailydotnettips.com/getting-started-with-azure-web-pubsub-service/)

[Demo for testing deployed service](https://azure.github.io/azure-webpubsub/demos/clientpubsub)

[Example chat platform with Auth](https://github.com/benc-uk/chatr)

## Test client program

When sending messages to Azure PubSub with:

``` Csharp
var response = await _pubSubServiceClient.SendToAllAsync(messageString, ContentType.ApplicationJson);
```

Receiving og these messages to the Azure, can be tested with following program:

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

