# Named Pipe Wrapper for .NET 4.0

A simple, easy to use, strongly-typed wrapper around .NET named pipes.

# NuGet Package

Available as a [NuGet package](https://www.nuget.org/packages/NamedPipeWrapper.New/).

# Features

*  Create named pipe servers that can handle multiple client connections simultaneously.
*  Send strongly-typed messages between clients and servers: any serializable .NET object can be sent over a pipe and will be automatically serialized/deserialized, including cyclical references and complex object graphs.
*  Messages are sent and received asynchronously on a separate background thread and marshalled back to the calling thread (typically the UI).
*  Supports large messages - up to 300 MiB.

# Requirements

Requires .NET 4.0 full.

# Usage

Server:

```csharp
var server = new NamedPipeServer<SomeClass>("MyServerPipe");

server.ClientConnected += delegate(NamedPipeConnection<SomeClass, SomeClass> conn)
    {
        Console.WriteLine("Client {0} is now connected!", conn.Id);
        conn.PushMessage(new SomeClass { Text = "Welcome!" });
    };

server.ClientMessage += delegate(NamedPipeConnection<SomeClass, SomeClass> conn, SomeClass message)
    {
        Console.WriteLine("Client {0} says: {1}", conn.Id, message.Text);
    };

// Start up the server asynchronously and begin listening for connections.
// This method will return immediately while the server runs in a separate background thread.
// This method returns false if there is already a NamedPipeServer instance with the same pipe name started on current computer.
if(!server.Start())
{
	Console.WriteLine("There is already a NamedPipeServer instance with pipe name {0} started.", server.PipeName);
	return;
}

// ...
```

Client:

```csharp
var client = new NamedPipeClient<SomeClass>("MyServerPipe");

client.ServerMessage += delegate(NamedPipeConnection<SomeClass, SomeClass> conn, SomeClass message)
    {
        Console.WriteLine("Server says: {0}", message.Text);
    };

// Start up the client asynchronously and connect to the specified server pipe.
// This method will return immediately while the client runs in a separate background thread.
client.Start();

// ...
```

# MIT License

Named Pipe Wrapper for .NET is licensed under the [MIT license](LICENSE.txt).
