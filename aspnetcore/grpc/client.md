---
title: Вызов служб gRPC с помощью клиента .NET
author: jamesnk
description: Узнайте, как вызывать службы gRPC с помощью клиента gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 6515e87845cc5aa101532c18711d175a73581bee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722713"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="974dd-103">Вызов служб gRPC с помощью клиента .NET</span><span class="sxs-lookup"><span data-stu-id="974dd-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="974dd-104">Клиентская библиотека .NET gRPC доступна в пакете NuGet [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client).</span><span class="sxs-lookup"><span data-stu-id="974dd-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="974dd-105">В этом документе объясняется, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="974dd-105">This document explains how to:</span></span>

* <span data-ttu-id="974dd-106">Настройка клиента gRPC для вызова служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="974dd-107">Вызовы gRPC для унарного метода, методов потоковой передачи сервера, потоковой передачи клиента и двунаправленной потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="974dd-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="974dd-108">Настройка клиента gRPC</span><span class="sxs-lookup"><span data-stu-id="974dd-108">Configure gRPC client</span></span>

<span data-ttu-id="974dd-109">Клиенты gRPC являются конкретными типами клиентов, [создаваемыми в файлах *\*.proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="974dd-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="974dd-110">Конкретный клиент gRPC использует методы, которые выполняют преобразование для служб gRPC в файле *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="974dd-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="974dd-111">Например, служба с именем `Greeter` создает тип `GreeterClient` с методами для вызова службы.</span><span class="sxs-lookup"><span data-stu-id="974dd-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="974dd-112">Клиент gRPC создается из канала.</span><span class="sxs-lookup"><span data-stu-id="974dd-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="974dd-113">Для начала воспользуйтесь `GrpcChannel.ForAddress`, чтобы создать канал, а затем используйте канал для создания клиента gRPC:</span><span class="sxs-lookup"><span data-stu-id="974dd-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="974dd-114">Канал представляет собой долгосрочное подключение к службе gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="974dd-115">При создании канала он настраивается с параметрами, связанными с вызовом службы.</span><span class="sxs-lookup"><span data-stu-id="974dd-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="974dd-116">Например, `HttpClient`, используемый для выполнения вызовов, максимальный размер сообщения для отправки и получения, а также ведение журнала можно указать в `GrpcChannelOptions` и использовать с `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="974dd-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="974dd-117">Полный список параметров см. в разделе, [посвященном параметрам конфигурации клиента](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="974dd-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="974dd-118">Настройка TLS</span><span class="sxs-lookup"><span data-stu-id="974dd-118">Configure TLS</span></span>

<span data-ttu-id="974dd-119">Клиент gRPC должен использовать ту же систему безопасности на уровне подключения, что и вызванная служба.</span><span class="sxs-lookup"><span data-stu-id="974dd-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="974dd-120">Протокол TLS для клиента gRPC настраивается при создании канала gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="974dd-121">Если система безопасности на уровне подключения канала и службы не совпадает, при вызове службы от клиента gRPC поступает сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="974dd-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="974dd-122">Чтобы настроить канал gRPC для использования протокола TLS, убедитесь, что адрес сервера начинается с `https`.</span><span class="sxs-lookup"><span data-stu-id="974dd-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="974dd-123">Например, `GrpcChannel.ForAddress("https://localhost:5001")` использует протокол HTTPS.</span><span class="sxs-lookup"><span data-stu-id="974dd-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="974dd-124">Канал gRPC автоматически согласует подключение, защищенное с помощью TLS, и использует безопасное соединение для выполнения вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="974dd-125">gRPC поддерживает проверку подлинности на основе сертификата клиента по протоколу TLS.</span><span class="sxs-lookup"><span data-stu-id="974dd-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="974dd-126">Сведения о настройке сертификатов клиента с помощью канала gRPC см. в разделе <xref:grpc/authn-and-authz#client-certificate-authentication>.</span><span class="sxs-lookup"><span data-stu-id="974dd-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="974dd-127">Чтобы вызвать незащищенные службы gRPC, убедитесь, что адрес сервера начинается с `http`.</span><span class="sxs-lookup"><span data-stu-id="974dd-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="974dd-128">Например, `GrpcChannel.ForAddress("http://localhost:5000")` использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="974dd-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="974dd-129">В .NET Core 3.1 или более поздней версии для [вызова незащищенных служб gRPC с помощью клиента .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client) требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="974dd-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="974dd-130">Производительность клиента</span><span class="sxs-lookup"><span data-stu-id="974dd-130">Client performance</span></span>

<span data-ttu-id="974dd-131">Производительность и использование канала и клиента:</span><span class="sxs-lookup"><span data-stu-id="974dd-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="974dd-132">Создание канала может потребовать значительных ресурсов.</span><span class="sxs-lookup"><span data-stu-id="974dd-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="974dd-133">Повторное использование канала для вызовов gRPC обеспечивает выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="974dd-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="974dd-134">Клиенты gRPC создаются с помощью каналов.</span><span class="sxs-lookup"><span data-stu-id="974dd-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="974dd-135">Клиенты gRPC являются облегченными объектами и не нуждаются в кэшировании или повторном использовании.</span><span class="sxs-lookup"><span data-stu-id="974dd-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="974dd-136">Из одного канала можно создать несколько клиентов gRPC, включая различные типы клиентов.</span><span class="sxs-lookup"><span data-stu-id="974dd-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="974dd-137">Канал и клиенты, созданные из канала, могут безопасно использоваться несколькими потоками.</span><span class="sxs-lookup"><span data-stu-id="974dd-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="974dd-138">Клиенты, созданные из канала, могут выполнять несколько одновременных вызовов.</span><span class="sxs-lookup"><span data-stu-id="974dd-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="974dd-139">`GrpcChannel.ForAddress` — не единственный вариант создания клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="974dd-140">При вызове службы gRPC из приложения ASP.NET Core, рассмотрите возможность [интеграции фабрики клиента gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="974dd-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="974dd-141">Интеграция gRPC с `HttpClientFactory` предлагает централизованную альтернативу созданию клиентов gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="974dd-142">Вызов gRPC через HTTP/2 с `Grpc.Net.Client` в настоящее время не поддерживается в Xamarin.</span><span class="sxs-lookup"><span data-stu-id="974dd-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="974dd-143">Мы работаем над улучшением поддержки HTTP/2 в будущих выпусках Xamarin.</span><span class="sxs-lookup"><span data-stu-id="974dd-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="974dd-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) и [gRPC-Web](xref:grpc/browser) являются приемлемыми работающими альтернативами, которые доступны на сегодняшний день.</span><span class="sxs-lookup"><span data-stu-id="974dd-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="974dd-145">Вызовы gRPC</span><span class="sxs-lookup"><span data-stu-id="974dd-145">Make gRPC calls</span></span>

<span data-ttu-id="974dd-146">Вызов gRPC инициируется путем вызова метода в клиенте.</span><span class="sxs-lookup"><span data-stu-id="974dd-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="974dd-147">Клиент gRPC будет выполнять сериализацию сообщений и направлять вызов gRPC к правильной службе.</span><span class="sxs-lookup"><span data-stu-id="974dd-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="974dd-148">gRPC имеет различные типы методов.</span><span class="sxs-lookup"><span data-stu-id="974dd-148">gRPC has different types of methods.</span></span> <span data-ttu-id="974dd-149">Способ использования клиента для выполнения вызова gRPC зависит от типа вызываемого метода.</span><span class="sxs-lookup"><span data-stu-id="974dd-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="974dd-150">Типы методов gRPC:</span><span class="sxs-lookup"><span data-stu-id="974dd-150">The gRPC method types are:</span></span>

* <span data-ttu-id="974dd-151">Унарный</span><span class="sxs-lookup"><span data-stu-id="974dd-151">Unary</span></span>
* <span data-ttu-id="974dd-152">Потоковая передача сервера</span><span class="sxs-lookup"><span data-stu-id="974dd-152">Server streaming</span></span>
* <span data-ttu-id="974dd-153">Потоковая передача клиента</span><span class="sxs-lookup"><span data-stu-id="974dd-153">Client streaming</span></span>
* <span data-ttu-id="974dd-154">Двунаправленная потоковая передача</span><span class="sxs-lookup"><span data-stu-id="974dd-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="974dd-155">Унарный вызов</span><span class="sxs-lookup"><span data-stu-id="974dd-155">Unary call</span></span>

<span data-ttu-id="974dd-156">Унарный вызов начинается с клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="974dd-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="974dd-157">После завершения работы службы возвращается ответное сообщение.</span><span class="sxs-lookup"><span data-stu-id="974dd-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="974dd-158">Каждый унарный метод службы в файле *\*PROTO* приведет к появлению двух методов .NET в конкретном типе клиента gRPC для вызова метода: асинхронного метода и блокирующего метода.</span><span class="sxs-lookup"><span data-stu-id="974dd-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="974dd-159">Например, в `GreeterClient` существует два способа вызова `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="974dd-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="974dd-160">`GreeterClient.SayHelloAsync` — асинхронный вызов службы `Greeter.SayHello`.</span><span class="sxs-lookup"><span data-stu-id="974dd-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="974dd-161">Может быть ожидаемым.</span><span class="sxs-lookup"><span data-stu-id="974dd-161">Can be awaited.</span></span>
* <span data-ttu-id="974dd-162">`GreeterClient.SayHello` — вызов службы `Greeter.SayHello` и блокировка до завершения.</span><span class="sxs-lookup"><span data-stu-id="974dd-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="974dd-163">Не используйте его в асинхронном коде.</span><span class="sxs-lookup"><span data-stu-id="974dd-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="974dd-164">Вызов потоковой передачи сервера</span><span class="sxs-lookup"><span data-stu-id="974dd-164">Server streaming call</span></span>

<span data-ttu-id="974dd-165">Вызов потоковой передачи сервера начинается с клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="974dd-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="974dd-166">`ResponseStream.MoveNext()` считывает сообщения, переданные в службу путем потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="974dd-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="974dd-167">Вызов потоковой передачи сервера завершается, когда `ResponseStream.MoveNext()` возвращает `false`.</span><span class="sxs-lookup"><span data-stu-id="974dd-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="974dd-168">Если используется C# 8 или более поздней версии, для чтения сообщений можно использовать синтаксис `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="974dd-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="974dd-169">Метод расширения `IAsyncStreamReader<T>.ReadAllAsync()` считывает все сообщения из потока ответов:</span><span class="sxs-lookup"><span data-stu-id="974dd-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="974dd-170">Вызов потоковой передачи клиента</span><span class="sxs-lookup"><span data-stu-id="974dd-170">Client streaming call</span></span>

<span data-ttu-id="974dd-171">Вызов потоковой передачи клиента начинается *без* клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="974dd-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="974dd-172">Клиент может выбрать отправку сообщений с помощью `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="974dd-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="974dd-173">Когда клиент завершит отправку сообщений, следует вызвать `RequestStream.CompleteAsync()`, чтобы уведомить службу.</span><span class="sxs-lookup"><span data-stu-id="974dd-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="974dd-174">Вызов завершается, когда служба возвращает ответное сообщение.</span><span class="sxs-lookup"><span data-stu-id="974dd-174">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="974dd-175">Вызов двунаправленной потоковой передачи</span><span class="sxs-lookup"><span data-stu-id="974dd-175">Bi-directional streaming call</span></span>

<span data-ttu-id="974dd-176">Вызов двунаправленной потоковой передачи начинается *без* клиента, отправляющего сообщение с запросом.</span><span class="sxs-lookup"><span data-stu-id="974dd-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="974dd-177">Клиент может выбрать отправку сообщений с помощью `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="974dd-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="974dd-178">Сообщения, переданные в службу путем потоковой передачи, доступны с `ResponseStream.MoveNext()` или `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="974dd-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="974dd-179">Вызов двунаправленной потоковой передачи завершается, когда `ResponseStream` больше не содержит сообщений.</span><span class="sxs-lookup"><span data-stu-id="974dd-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="974dd-180">Чтобы обеспечить наилучшую производительность и избежать ненужных ошибок в клиенте и службе, старайтесь правильно выполнять двунаправленные потоковые вызовы.</span><span class="sxs-lookup"><span data-stu-id="974dd-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="974dd-181">Двунаправленный вызов завершается корректно, когда сервер завершил чтение потока запроса, а клиент завершил чтение потока ответа.</span><span class="sxs-lookup"><span data-stu-id="974dd-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="974dd-182">Предыдущий пример вызова — это один из примеров двунаправленного вызова, который завершается корректно.</span><span class="sxs-lookup"><span data-stu-id="974dd-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="974dd-183">При вызове клиент:</span><span class="sxs-lookup"><span data-stu-id="974dd-183">In the call, the client:</span></span>

1. <span data-ttu-id="974dd-184">Запускает новый двунаправленный потоковый вызов путем вызова `EchoClient.Echo`.</span><span class="sxs-lookup"><span data-stu-id="974dd-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="974dd-185">Создает фоновую задачу для чтения сообщений из службы с помощью `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="974dd-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="974dd-186">Отправляет сообщения на сервер с помощью `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="974dd-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="974dd-187">Сообщает серверу, что он закончил отправку сообщений с помощью `RequestStream.CompleteAsync()`.</span><span class="sxs-lookup"><span data-stu-id="974dd-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="974dd-188">Ожидает, пока фоновая задача не прочитает все входящие сообщения.</span><span class="sxs-lookup"><span data-stu-id="974dd-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="974dd-189">Во время вызова двунаправленной потоковой передачи клиент и служба могут обмениваться сообщениями в любое время.</span><span class="sxs-lookup"><span data-stu-id="974dd-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="974dd-190">Наиболее подходящая логика клиента для взаимодействия с вызовом двунаправленной потоковой передачи зависит от логики службы.</span><span class="sxs-lookup"><span data-stu-id="974dd-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="974dd-191">Доступ к трейлерам gRPC</span><span class="sxs-lookup"><span data-stu-id="974dd-191">Access gRPC trailers</span></span>

<span data-ttu-id="974dd-192">Вызовы gRPC могут возвращать трейлеры gRPC.</span><span class="sxs-lookup"><span data-stu-id="974dd-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="974dd-193">Трейлеры gRPC содержат метаданные имени и значения для вызова.</span><span class="sxs-lookup"><span data-stu-id="974dd-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="974dd-194">Трейлеры содержат аналогичные функции для заголовков HTTP, но они принимаются в конце вызова.</span><span class="sxs-lookup"><span data-stu-id="974dd-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="974dd-195">Трейлеры gRPC доступны с помощью `GetTrailers()`, которая возвращает коллекцию метаданных.</span><span class="sxs-lookup"><span data-stu-id="974dd-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="974dd-196">Трейлеры возвращаются после завершения ответа, поэтому перед обращением к трейлеру необходимо дождаться конца ответа.</span><span class="sxs-lookup"><span data-stu-id="974dd-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="974dd-197">Унарные и клиентские вызовы потоковой передачи должны дождаться `ResponseAsync` перед вызовом `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="974dd-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="974dd-198">Вызовы сервера и двунаправленной потоковой передачи должны завершить ожидание ответного потока перед вызовом `GetTrailers()`:</span><span class="sxs-lookup"><span data-stu-id="974dd-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="974dd-199">Трейлеры gRPC также доступны из `RpcException`.</span><span class="sxs-lookup"><span data-stu-id="974dd-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="974dd-200">Служба может вернуть трейлеры вместе со статусом gRPC, отличным от "ОК".</span><span class="sxs-lookup"><span data-stu-id="974dd-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="974dd-201">В этой ситуации трейлеры получаются из исключения, вызываемого клиентом gRPC:</span><span class="sxs-lookup"><span data-stu-id="974dd-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="974dd-202">Настройка крайнего срока</span><span class="sxs-lookup"><span data-stu-id="974dd-202">Configure deadline</span></span>

<span data-ttu-id="974dd-203">Настраивать крайний срок в вызове gRPC рекомендуется по той причине, что он ограничивает длительность выполнения вызова.</span><span class="sxs-lookup"><span data-stu-id="974dd-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="974dd-204">Это позволяет предотвратить бесконечное выполнение служб и исчерпание ресурсов сервера.</span><span class="sxs-lookup"><span data-stu-id="974dd-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="974dd-205">Крайние сроки — это полезное средство для повышения надежности приложений.</span><span class="sxs-lookup"><span data-stu-id="974dd-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="974dd-206">Чтобы задать крайний срок для вызова gRPC, настройте `CallOptions.Deadline`:</span><span class="sxs-lookup"><span data-stu-id="974dd-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="974dd-207">Для получения дополнительной информации см. <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="974dd-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="974dd-208">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="974dd-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
