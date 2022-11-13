# 1k0n Network Discovery

## Getting Started

Include the library and decalre a _using_ to the __1k0n.NetworkDiscovery__ namespace.

	 using _1k0n.NetworkDiscovery;

## 1. Define Request message

This can be any class with a default constructor. The client broadcasts this message when looking for the server.

> Fields can be any type serializable to JSON

	public class MyDiscoveryRequest
	{
		public string? Field1 { get; set; }
		public string? Field2 { get; set; }

		public MyDiscoveryRequest()
		{
		}
	}

## 2. Define Response message

This can be any class with a default constructor. The server will send this back to the client after receiving the request.

> Fields can be any type serializable to JSON

	public class MyDiscoverResponse
	{
		public string? Field1 { get; set; }
		public string? Field2 { get; set; }

		public MyDiscoveryResponse()
		{
		}
	}



## 3. Implement DiscoveryServer


Create a class that inherits from _DiscoverServer_ and override the _GenerateResponse_ method.


	public class MyDiscoveryServer : DiscoveryServer<MyDiscoveryRequest, MyDiscoveryResponse>
	{
		protected override MyDiscoveryResponse GenerateResponse(MyDiscoveryRequest request)
		{
			var response = new MyDiscoveryResponse();

			...

			return response;
		}
	}

> The MyDiscoveryRequest class is defined in Step 1.

> The MyDiscoveryResponse class is defined in Step 2.



## 4. Searching for server

The _DiscoveryClient_ class has a static method to discover servers called _Search_.
The _Search_ method returns an __IAsyncEnumerable<TResponse?>__

### DiscoveryClient.Search<TRequest, TResponse>()

_DiscoveryClient.Search<TRequest, TResponse>(IPAddress? subnet, int port, TRequest request, CancellationToken token)_

#### Parameters:

- __subnet__ (IPAddress?): Pass in the subnet to search on. DiscoverySearch will use the default network if __null__ is supplied.
- __port__ (int): Port to do the search on. This needs to match the port the server is listening on.
- __request__ (TRequest): This is the request message that is broadcast in search of the server.
- __token__ (CancellationToken): Use this to cancell the search.


#### Example:

Searching is demonstrated in the method below.

 Searching will stop when either a server responds or the __CancellationToken__ is signaled.

 > The example method _DoSearch_ can be called on a background thread so ask not to block the UI.

	private async Task<MyDiscoveryResponse?> DoSearch(IPAddress? subnet, int discoveryPort, MyDiscoveryRequest request, CancellationToken token)
    {
		var searcher = DiscoveryClient.Search<MyDiscoveryRequest, MyDiscoveryResponse>(subnet, discoveryPort, request, token);

		MyDiscoveryResponse? response = null;

		await foreach (var discovery in searcher)
		{
			if (!(discovery is null))
			{
				response = discovery;
				break;
			}

		}

		return response;
	}

> The MyDiscoveryRequest class is defined in Step 1.

> The MyDiscoveryResponse class is defined in Step 2.


Copyright © 2022 John Meyer Jr.

To be used with the 1k0n Network Discovery library.
