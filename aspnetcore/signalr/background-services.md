---
title: 'ASP.NET Core узла SignalR в фоновых службах'
author: bradygaster
description: 'Узнайте, как отправить сообщения SignalR клиентам из классов .NET Core BackgroundService.'
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/background-services
ms.openlocfilehash: 810eff7ccb08ecc22ea255bf0a9fe3d22637179f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060109"
---
# <a name="host-aspnet-core-no-locsignalr-in-background-services"></a><span data-ttu-id="08eed-103">ASP.NET Core узла SignalR в фоновых службах</span><span class="sxs-lookup"><span data-stu-id="08eed-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="08eed-104">По [Брейди Гастер](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="08eed-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="08eed-105">В этой статье приводятся рекомендации по следующим вопросам.</span><span class="sxs-lookup"><span data-stu-id="08eed-105">This article provides guidance for:</span></span>

* <span data-ttu-id="08eed-106">Размещение SignalR концентраторов с помощью фонового рабочего процесса, размещенного в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08eed-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="08eed-107">Отправка сообщений на подключенные клиенты из [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).NET Core.</span><span class="sxs-lookup"><span data-stu-id="08eed-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08eed-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="08eed-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="08eed-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="08eed-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-no-locsignalr-in-startup"></a><span data-ttu-id="08eed-110">Включить SignalR при запуске</span><span class="sxs-lookup"><span data-stu-id="08eed-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08eed-111">Размещение концентраторов ASP.NET Core SignalR в контексте фонового рабочего процесса идентично размещению центра в веб-приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08eed-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="08eed-112">В `Startup.ConfigureServices` методе вызывается `services.AddSignalR` Добавление требуемых служб в уровень поддержки ASP.NET Core внедрения зависимостей (DI) SignalR .</span><span class="sxs-lookup"><span data-stu-id="08eed-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="08eed-113">В `Startup.Configure` `MapHub` метод вызывается в `UseEndpoints` обратном вызове для соединения конечных точек концентратора в конвейере запросов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08eed-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="08eed-114">Размещение концентраторов ASP.NET Core SignalR в контексте фонового рабочего процесса идентично размещению центра в веб-приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08eed-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="08eed-115">В `Startup.ConfigureServices` методе вызывается `services.AddSignalR` Добавление требуемых служб в уровень поддержки ASP.NET Core внедрения зависимостей (DI) SignalR .</span><span class="sxs-lookup"><span data-stu-id="08eed-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="08eed-116">В `Startup.Configure` `UseSignalR` метод вызывается для подключения конечных точек концентратора в конвейере запросов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08eed-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="08eed-117">В предыдущем примере `ClockHub` класс реализует `Hub<T>` класс для создания строго типизированного концентратора.</span><span class="sxs-lookup"><span data-stu-id="08eed-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="08eed-118">Объект `ClockHub` настроен в `Startup` классе для реагирования на запросы в конечной точке `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="08eed-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="08eed-119">Дополнительные сведения о строго типизированных концентраторах см. [в статье Использование концентраторов в SignalR для ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="08eed-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="08eed-120">Эта функция не ограничена классом [Hub \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="08eed-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="08eed-121">Любой класс, наследующий от [концентратора](xref:Microsoft.AspNetCore.SignalR.Hub), например [динамичуб](xref:Microsoft.AspNetCore.SignalR.DynamicHub), работает.</span><span class="sxs-lookup"><span data-stu-id="08eed-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="08eed-122">Интерфейс, используемый строго типизированным объектом, `ClockHub` является `IClock` интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="08eed-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-no-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="08eed-123">Вызов SignalR концентратора из фоновой службы</span><span class="sxs-lookup"><span data-stu-id="08eed-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="08eed-124">Во время запуска `Worker` класс ( `BackgroundService` ) включается с помощью `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="08eed-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="08eed-125">Так как SignalR также включается на `Startup` этапе, в котором каждый концентратор подключен к отдельной конечной точке в КОНВЕЙЕРЕ HTTP-запросов ASP.NET Core, каждый концентратор представляется `IHubContext<T>` на сервере.</span><span class="sxs-lookup"><span data-stu-id="08eed-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="08eed-126">Используя функции DI ASP.NET Core, другие классы, созданные на уровне размещения, такие как `BackgroundService` классы, классы контроллеров MVC или Razor модели страниц, могут получать ссылки на серверные концентраторы, принимая экземпляры `IHubContext<ClockHub, IClock>` во время создания.</span><span class="sxs-lookup"><span data-stu-id="08eed-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="08eed-127">Так как `ExecuteAsync` метод вызывается итеративно в фоновой службе, текущая дата и время сервера отправляются подключенным клиентам с помощью `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="08eed-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-no-locsignalr-events-with-background-services"></a><span data-ttu-id="08eed-128">Реагирование на SignalR события с помощью фоновых служб</span><span class="sxs-lookup"><span data-stu-id="08eed-128">React to SignalR events with background services</span></span>

<span data-ttu-id="08eed-129">Как и приложение с одним страницам, использующее клиент JavaScript для или классическое SignalR приложение .NET, может использовать с помощью <xref:signalr/dotnet-client> , а `BackgroundService` `IHostedService` также для подключения к SignalR концентраторам и реагирования на события.</span><span class="sxs-lookup"><span data-stu-id="08eed-129">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="08eed-130">`ClockHubClient`Класс реализует `IClock` интерфейс и интерфейс `IHostedService` .</span><span class="sxs-lookup"><span data-stu-id="08eed-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="08eed-131">Таким образом, его можно включить во время `Startup` непрерывного выполнения и реагирования на события концентратора с сервера.</span><span class="sxs-lookup"><span data-stu-id="08eed-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="08eed-132">Во время инициализации объект `ClockHubClient` создает экземпляр класса `HubConnection` и включает `IClock.ShowTime` метод в качестве обработчика для события концентратора `ShowTime` .</span><span class="sxs-lookup"><span data-stu-id="08eed-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="08eed-133">В `IHostedService.StartAsync` реализации `HubConnection` запускается асинхронно.</span><span class="sxs-lookup"><span data-stu-id="08eed-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="08eed-134">Во время выполнения `IHostedService.StopAsync` метода объект `HubConnection` уничтожается асинхронно.</span><span class="sxs-lookup"><span data-stu-id="08eed-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="08eed-135">В `IHostedService.StartAsync` реализации `HubConnection` запускается асинхронно.</span><span class="sxs-lookup"><span data-stu-id="08eed-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="08eed-136">Во время выполнения `IHostedService.StopAsync` метода объект `HubConnection` уничтожается асинхронно.</span><span class="sxs-lookup"><span data-stu-id="08eed-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="08eed-137">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="08eed-137">Additional resources</span></span>

* [<span data-ttu-id="08eed-138">Начало работы</span><span class="sxs-lookup"><span data-stu-id="08eed-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="08eed-139">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="08eed-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="08eed-140">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="08eed-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="08eed-141">Строго типизированные концентраторы</span><span class="sxs-lookup"><span data-stu-id="08eed-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
