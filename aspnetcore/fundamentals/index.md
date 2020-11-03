---
title: Основы ASP.NET Core
author: rick-anderson
description: Познакомьтесь с основными принципами создания приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/index
ms.openlocfilehash: 25348f8486ec6ccb53ebf527ad4519638dd5f73e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059381"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="c00d7-103">Основы ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c00d7-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c00d7-104">В этой статье приведен обзор основных тем, связанных с разработкой приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="c00d7-105">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="c00d7-105">The Startup class</span></span>

<span data-ttu-id="c00d7-106">В классе `Startup` делается следующее.</span><span class="sxs-lookup"><span data-stu-id="c00d7-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="c00d7-107">Настраиваются службы, необходимые приложению.</span><span class="sxs-lookup"><span data-stu-id="c00d7-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="c00d7-108">Конвейер обработки запросов приложения определен как ряд компонентов ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="c00d7-109">Пример класса `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c00d7-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="c00d7-110">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="c00d7-111">Введение зависимостей (службы)</span><span class="sxs-lookup"><span data-stu-id="c00d7-111">Dependency injection (services)</span></span>

<span data-ttu-id="c00d7-112">ASP.NET Core включает встроенную платформу внедрения зависимостей, позволяющую обращаться к настроенным службам в рамках приложения.</span><span class="sxs-lookup"><span data-stu-id="c00d7-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="c00d7-113">Например, службой является компонент ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c00d7-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="c00d7-114">Код для настройки (или *регистрации* ) служб добавляется в метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-114">Code to configure (or *register* ) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c00d7-115">Пример:</span><span class="sxs-lookup"><span data-stu-id="c00d7-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="c00d7-116">Как правило, службы разрешаются из системы внедрения зависимостей с помощью внедрения конструктора.</span><span class="sxs-lookup"><span data-stu-id="c00d7-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="c00d7-117">При внедрении конструктора класс объявляет параметр конструктора, который может быть требуемым типом или интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="c00d7-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="c00d7-118">Платформа внедрения зависимостей предоставляет экземпляр этой службы во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c00d7-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="c00d7-119">В следующем примере показано использование внедрения конструктора для разрешения `:::no-loc(Razor):::PagesMovieContext` из системы внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-119">The following example uses constructor injection to resolve a `:::no-loc(Razor):::PagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="c00d7-120">Если встроенный контейнер с инверсией управления (IoC) не удовлетворяет всем потребностям приложения, вместо него можно использовать сторонний контейнер IoC.</span><span class="sxs-lookup"><span data-stu-id="c00d7-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="c00d7-121">Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="c00d7-122">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c00d7-122">Middleware</span></span>

<span data-ttu-id="c00d7-123">Конвейер обработки запросов состоит из ряда компонентов ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="c00d7-124">Каждый компонент выполняет операции в `HttpContext`, а затем либо вызывает следующий компонент в конвейере, либо завершает запрос.</span><span class="sxs-lookup"><span data-stu-id="c00d7-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="c00d7-125">По принятому соглашению компонент ПО промежуточного слоя добавляется в конвейер вызовом метода расширения `Use...` в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="c00d7-126">Например, чтобы включить отрисовку статических файлов, вызовите `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="c00d7-127">В следующем примере показана настройка конвейера обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="c00d7-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="c00d7-128">ASP.NET Core содержит большой набор встроенного ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="c00d7-129">Кроме того, можно создать пользовательские компоненты ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="c00d7-130">Для получения дополнительной информации см. <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="c00d7-131">Узел</span><span class="sxs-lookup"><span data-stu-id="c00d7-131">Host</span></span>

<span data-ttu-id="c00d7-132">При запуске приложение ASP.NET Core создает *узел*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="c00d7-133">Узел инкапсулирует все ресурсы приложения:</span><span class="sxs-lookup"><span data-stu-id="c00d7-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="c00d7-134">Реализация HTTP-сервера</span><span class="sxs-lookup"><span data-stu-id="c00d7-134">An HTTP server implementation</span></span>
* <span data-ttu-id="c00d7-135">Компоненты ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c00d7-135">Middleware components</span></span>
* <span data-ttu-id="c00d7-136">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="c00d7-136">Logging</span></span>
* <span data-ttu-id="c00d7-137">Службы внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="c00d7-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="c00d7-138">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="c00d7-138">Configuration</span></span>

<span data-ttu-id="c00d7-139">Существует два различных узла:</span><span class="sxs-lookup"><span data-stu-id="c00d7-139">There are two different hosts:</span></span> 

* <span data-ttu-id="c00d7-140">Универсальный узел .NET</span><span class="sxs-lookup"><span data-stu-id="c00d7-140">.NET Generic Host</span></span>
* <span data-ttu-id="c00d7-141">Веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c00d7-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="c00d7-142">Рекомендуется использовать универсальный узел .NET.</span><span class="sxs-lookup"><span data-stu-id="c00d7-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="c00d7-143">Веб-узел ASP.NET Core доступен только для обеспечения обратной совместимости.</span><span class="sxs-lookup"><span data-stu-id="c00d7-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="c00d7-144">В следующем примере показано создание универсального узла .NET.</span><span class="sxs-lookup"><span data-stu-id="c00d7-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="c00d7-145">Методы `CreateDefaultBuilder` и `ConfigureWebHostDefaults` применяются для настройки узла с набором параметров по умолчанию, например:</span><span class="sxs-lookup"><span data-stu-id="c00d7-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="c00d7-146">Использование [Kestrel](#servers) в качестве веб-сервера и поддержка интеграции с IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="c00d7-147">Загрузка конфигурации из файлов *:::no-loc(appsettings.json):::* и *appsettings.{Environment Name}.json* , переменных среды, аргументов командной строки и других источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c00d7-147">Load configuration from *:::no-loc(appsettings.json):::* , *appsettings.{Environment Name}.json* , environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="c00d7-148">Отправка выходных данных журнала в поставщики служб консоли и отладки.</span><span class="sxs-lookup"><span data-stu-id="c00d7-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="c00d7-149">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="c00d7-150">Сценарии, не связанные с Интернетом</span><span class="sxs-lookup"><span data-stu-id="c00d7-150">Non-web scenarios</span></span>

<span data-ttu-id="c00d7-151">Универсальный узел позволяет приложениям других типов использовать перекрестные расширения платформ, такие как средства ведения журналов, внедрения зависимостей, настройки и управления жизненным циклом.</span><span class="sxs-lookup"><span data-stu-id="c00d7-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="c00d7-152">Дополнительные сведения см. в разделах <xref:fundamentals/host/generic-host> и <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="c00d7-153">Серверы</span><span class="sxs-lookup"><span data-stu-id="c00d7-153">Servers</span></span>

<span data-ttu-id="c00d7-154">Приложение ASP.NET Core использует реализацию HTTP-сервера для приема HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c00d7-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="c00d7-155">Сервер отправляет приложению запросы в виде набора [функций запросов](xref:fundamentals/request-features), объединенных в `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="c00d7-156">Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="c00d7-157">ASP.NET Core предоставляет следующие реализации серверов:</span><span class="sxs-lookup"><span data-stu-id="c00d7-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="c00d7-158">*Kestrel*  — это кроссплатформенный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="c00d7-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="c00d7-159">Он часто запускается в реконфигурации прокси-сервера с помощью [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="c00d7-160">В ASP.NET Core 2.0 и более поздних версиях можно использовать Kestrel в роли общедоступного пограничного сервера с прямым доступом через Интернет.</span><span class="sxs-lookup"><span data-stu-id="c00d7-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="c00d7-161">*HTTP-сервер IIS*  — это сервер для Windows, который использует службы IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="c00d7-162">Он позволяет запускать приложение ASP.NET Core и службы IIS в одном процессе.</span><span class="sxs-lookup"><span data-stu-id="c00d7-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="c00d7-163">*HTTP.sys*  — это сервер для Windows, который не используется со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="c00d7-164">macOS</span><span class="sxs-lookup"><span data-stu-id="c00d7-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="c00d7-165">ASP.NET Core предоставляет реализацию кроссплатформенного сервера *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="c00d7-166">В ASP.NET Core 2.0 и более поздних версиях можно использовать Kestrel в роли общедоступного пограничного сервера с прямым доступом через Интернет.</span><span class="sxs-lookup"><span data-stu-id="c00d7-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="c00d7-167">Он часто запускается в реконфигурации прокси-сервера с помощью [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="c00d7-168">Linux</span><span class="sxs-lookup"><span data-stu-id="c00d7-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="c00d7-169">ASP.NET Core предоставляет реализацию кроссплатформенного сервера *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="c00d7-170">В ASP.NET Core 2.0 и более поздних версиях можно использовать Kestrel в роли общедоступного пограничного сервера с прямым доступом через Интернет.</span><span class="sxs-lookup"><span data-stu-id="c00d7-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="c00d7-171">Он часто запускается в реконфигурации прокси-сервера с помощью [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="c00d7-172">Для получения дополнительной информации см. <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="c00d7-173">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="c00d7-173">Configuration</span></span>

<span data-ttu-id="c00d7-174">ASP.NET Core предоставляет платформу конфигурации, которая получает параметры в виде пар "имя-значение" от упорядоченного набора поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c00d7-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="c00d7-175">Доступны встроенные поставщики конфигурации для различных источников, таких как файлы *JSON* , *XML* , переменные среды и аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="c00d7-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="c00d7-176">Для поддержки других источников можно создать настраиваемые поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c00d7-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="c00d7-177">[По умолчанию](xref:fundamentals/configuration/index#default) приложения ASP.NET Core настроены для чтения из файла *:::no-loc(appsettings.json):::* , переменных среды, командной строки и т. д.</span><span class="sxs-lookup"><span data-stu-id="c00d7-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *:::no-loc(appsettings.json):::* , environment variables, the command line, and more.</span></span> <span data-ttu-id="c00d7-178">При загрузке конфигурации приложения значения из переменных среды переопределяют значения из файла *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="c00d7-178">When the app's configuration is loaded, values from environment variables override values from *:::no-loc(appsettings.json):::*.</span></span>

<span data-ttu-id="c00d7-179">Предпочтительный способ чтения связанных значений конфигурации — использование [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="c00d7-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c00d7-180">Дополнительные сведения см. в статье [Привязка иерархических данных конфигурации с помощью шаблона параметров](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="c00d7-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="c00d7-181">Для управления конфиденциальными данными конфигурации, например паролями, ASP.NET Core предоставляет [Менеджер секретов](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="c00d7-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="c00d7-182">Для секретов в рабочей среде рекомендуется использовать [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c00d7-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="c00d7-183">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="c00d7-184">Среды</span><span class="sxs-lookup"><span data-stu-id="c00d7-184">Environments</span></span>

<span data-ttu-id="c00d7-185">Среды выполнения, такие как `Development`, `Staging` и `Production`, являются ключевыми компонентами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="c00d7-186">Указать среду, в которой запускается приложение, можно с помощью переменной среды `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="c00d7-187">ASP.NET Core считывает переменную среды при запуске приложения и сохраняет ее значение в реализации `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="c00d7-188">Эта реализация доступна в любом месте приложения посредством внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="c00d7-189">В следующем примере показана настройка приложения для предоставления подробных сведений об ошибке при выполнении в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="c00d7-190">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="c00d7-191">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="c00d7-191">Logging</span></span>

<span data-ttu-id="c00d7-192">ASP.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками.</span><span class="sxs-lookup"><span data-stu-id="c00d7-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="c00d7-193">Доступные следующие поставщики:</span><span class="sxs-lookup"><span data-stu-id="c00d7-193">Available providers include:</span></span>

* <span data-ttu-id="c00d7-194">Консоль</span><span class="sxs-lookup"><span data-stu-id="c00d7-194">Console</span></span>
* <span data-ttu-id="c00d7-195">Отладка</span><span class="sxs-lookup"><span data-stu-id="c00d7-195">Debug</span></span>
* <span data-ttu-id="c00d7-196">Трассировка событий Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="c00d7-197">Журнал событий Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-197">Windows Event Log</span></span>
* <span data-ttu-id="c00d7-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c00d7-198">TraceSource</span></span>
* <span data-ttu-id="c00d7-199">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="c00d7-199">Azure App Service</span></span>
* <span data-ttu-id="c00d7-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="c00d7-200">Azure Application Insights</span></span>

<span data-ttu-id="c00d7-201">Для создания журналов необходимо разрешить службу <xref:Microsoft.Extensions.Logging.ILogger%601> из системы внедрения зависимостей (DI) и вызвать методы ведения журналов, такие как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="c00d7-202">Пример:</span><span class="sxs-lookup"><span data-stu-id="c00d7-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="c00d7-203">Методы ведения журналов, такие как `LogInformation`, поддерживают любое количество полей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="c00d7-204">Как правило, эти поля используются для создания сообщения `string`, но некоторые поставщики ведения журналов отправляют их в хранилище данных в виде отдельных полей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="c00d7-205">Благодаря этому поставщики могут реализовывать [семантическое (структурированное) ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="c00d7-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="c00d7-206">Для получения дополнительной информации см. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="c00d7-207">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="c00d7-207">Routing</span></span>

<span data-ttu-id="c00d7-208">*Маршрут*  — это шаблон URL-адреса, сопоставляемый с обработчиком.</span><span class="sxs-lookup"><span data-stu-id="c00d7-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="c00d7-209">Обычно обработчик представляет собой страницу :::no-loc(Razor):::, метод действия в контроллере MVC или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-209">The handler is typically a :::no-loc(Razor)::: page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="c00d7-210">Маршрутизация ASP.NET Core позволяет контролировать URL-адреса, используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="c00d7-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="c00d7-211">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="c00d7-212">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="c00d7-212">Error handling</span></span>

<span data-ttu-id="c00d7-213">ASP.NET Core содержит встроенные функции обработки ошибок, такие как:</span><span class="sxs-lookup"><span data-stu-id="c00d7-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="c00d7-214">Страница исключений для разработчика</span><span class="sxs-lookup"><span data-stu-id="c00d7-214">A developer exception page</span></span>
* <span data-ttu-id="c00d7-215">Настраиваемые страницы ошибок</span><span class="sxs-lookup"><span data-stu-id="c00d7-215">Custom error pages</span></span>
* <span data-ttu-id="c00d7-216">Статические страницы с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="c00d7-216">Static status code pages</span></span>
* <span data-ttu-id="c00d7-217">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="c00d7-217">Startup exception handling</span></span>

<span data-ttu-id="c00d7-218">Для получения дополнительной информации см. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="c00d7-219">Создание HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="c00d7-219">Make HTTP requests</span></span>

<span data-ttu-id="c00d7-220">ASP.NET Core включает реализацию интерфейса `IHttpClientFactory` для создания экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="c00d7-221">Фабрика:</span><span class="sxs-lookup"><span data-stu-id="c00d7-221">The factory:</span></span>

* <span data-ttu-id="c00d7-222">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c00d7-223">Например, можно зарегистрировать и использовать клиент *github* для доступа к сайту GitHub.</span><span class="sxs-lookup"><span data-stu-id="c00d7-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="c00d7-224">Можно зарегистрировать и настроить клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="c00d7-225">Поддержка регистрации и связывания в цепочки множества делегирующих обработчиков для создания конвейера ПО промежуточного слоя под исходящие запросы.</span><span class="sxs-lookup"><span data-stu-id="c00d7-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c00d7-226">Этот шаблон похож на входящий конвейер ПО промежуточного слоя для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="c00d7-227">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="c00d7-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="c00d7-228">Интеграция с *Polly*  — популярной сторонней библиотекой для обработки временных сбоев.</span><span class="sxs-lookup"><span data-stu-id="c00d7-228">Integrates with *Polly* , a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="c00d7-229">Управление созданием пулов и временем существования базовых экземпляров `HttpClientHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="c00d7-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="c00d7-230">Настройка параметров ведения журнала (через <xref:Microsoft.Extensions.Logging.ILogger>) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="c00d7-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c00d7-231">Для получения дополнительной информации см. <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="c00d7-232">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="c00d7-232">Content root</span></span>

<span data-ttu-id="c00d7-233">Корневой каталог содержимого — это базовый путь к следующим элементам:</span><span class="sxs-lookup"><span data-stu-id="c00d7-233">The content root is the base path for:</span></span>

* <span data-ttu-id="c00d7-234">Исполняемый файл, в котором размещено приложение ( *EXE* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-234">The executable hosting the app ( *.exe* ).</span></span>
* <span data-ttu-id="c00d7-235">Скомпилированные сборки, составляющие приложение ( *DLL* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-235">Compiled assemblies that make up the app ( *.dll* ).</span></span>
* <span data-ttu-id="c00d7-236">Файлы содержимого, используемые приложением, например:</span><span class="sxs-lookup"><span data-stu-id="c00d7-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="c00d7-237">Файлы :::no-loc(Razor)::: ( *CSHTML* , *RAZOR* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-237">:::no-loc(Razor)::: files ( *.cshtml* , *.razor* )</span></span>
  * <span data-ttu-id="c00d7-238">Файлы конфигурации ( *JSON* , *XML* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-238">Configuration files ( *.json* , *.xml* )</span></span>
  * <span data-ttu-id="c00d7-239">Файлы данных ( *DB* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-239">Data files ( *.db* )</span></span>
* <span data-ttu-id="c00d7-240">[Корневой веб-каталог](#web-root), обычно это папка *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="c00d7-241">Во время развертывания корень содержимого по умолчанию сбрасывается до корневого каталога проекта.</span><span class="sxs-lookup"><span data-stu-id="c00d7-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="c00d7-242">Этот каталог является базовым путем к файлам содержимого приложения и [корневому веб-каталогу](#web-root).</span><span class="sxs-lookup"><span data-stu-id="c00d7-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="c00d7-243">Альтернативный корневой путь к содержимому может быть указан при [создании узла](#host).</span><span class="sxs-lookup"><span data-stu-id="c00d7-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="c00d7-244">Дополнительные сведения: [Корень содержимого](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="c00d7-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="c00d7-245">Корневой веб-узел</span><span class="sxs-lookup"><span data-stu-id="c00d7-245">Web root</span></span>

<span data-ttu-id="c00d7-246">Корневой веб-каталог — это базовый путь к общедоступным файлам статических ресурсов, например:</span><span class="sxs-lookup"><span data-stu-id="c00d7-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="c00d7-247">Таблицы стилей ( *CSS* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-247">Stylesheets ( *.css* )</span></span>
* <span data-ttu-id="c00d7-248">JavaScript ( *JS* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-248">JavaScript ( *.js* )</span></span>
* <span data-ttu-id="c00d7-249">Изображения ( *PNG* , *JPG* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-249">Images ( *.png* , *.jpg* )</span></span>

<span data-ttu-id="c00d7-250">По умолчанию статические файлы обслуживаются только в корневом веб-каталоге и его подкаталогах.</span><span class="sxs-lookup"><span data-stu-id="c00d7-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="c00d7-251">По умолчанию используется путь *{корневой каталог содержимого}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="c00d7-252">Альтернативное расположение корневого веб-каталога можно указать при [создании узла](#host).</span><span class="sxs-lookup"><span data-stu-id="c00d7-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="c00d7-253">Дополнительные сведения см. в разделе [Корневой веб-каталог](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="c00d7-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="c00d7-254">Запретите публикацию файлов в *wwwroot* с помощью [элемента проекта \<Content>](/visualstudio/msbuild/common-msbuild-project-items#content) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="c00d7-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="c00d7-255">В следующем примере запрещается публикация содержимого в каталоге *wwwroot/local* и его подкаталогах:</span><span class="sxs-lookup"><span data-stu-id="c00d7-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c00d7-256">Для указания на корневой каталог файлов :::no-loc(Razor)::: ( *CSHTML* ) используется символ тильды и косой черты `~/`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-256">In :::no-loc(Razor)::: *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="c00d7-257">Путь, начинающийся с `~/`, называется *виртуальным путем*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="c00d7-258">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c00d7-259">В этой статье приведен обзор основных тем, связанных с разработкой приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="c00d7-260">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="c00d7-260">The Startup class</span></span>

<span data-ttu-id="c00d7-261">В классе `Startup` делается следующее.</span><span class="sxs-lookup"><span data-stu-id="c00d7-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="c00d7-262">Настраиваются службы, необходимые приложению.</span><span class="sxs-lookup"><span data-stu-id="c00d7-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="c00d7-263">Определяется конвейер обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="c00d7-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="c00d7-264">*Службы*  — это компоненты, которые используются в приложении.</span><span class="sxs-lookup"><span data-stu-id="c00d7-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="c00d7-265">Например, службой является компонент ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c00d7-265">For example, a logging component is a service.</span></span> <span data-ttu-id="c00d7-266">Код для настройки (или *регистрации* ) служб добавляется в метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-266">Code to configure (or *register* ) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="c00d7-267">Конвейер обработки запросов состоит из ряда компонентов *ПО промежуточного* слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="c00d7-268">Например, это ПО может обрабатывать запросы для статических файлов или перенаправлять HTTP-запросы в HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="c00d7-269">Каждый компонент ПО промежуточного слоя выполняет асинхронные операции в `HttpContext`, а затем либо вызывает следующий компонент в конвейере, либо завершает запрос.</span><span class="sxs-lookup"><span data-stu-id="c00d7-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="c00d7-270">Код для настройки конвейера обработки запросов добавляется в метод `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="c00d7-271">Пример класса `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c00d7-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="c00d7-272">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="c00d7-273">Введение зависимостей (службы)</span><span class="sxs-lookup"><span data-stu-id="c00d7-273">Dependency injection (services)</span></span>

<span data-ttu-id="c00d7-274">ASP.NET Core включает встроенную платформу внедрения зависимостей, позволяющую классам приложения обращаться к настроенным службам.</span><span class="sxs-lookup"><span data-stu-id="c00d7-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="c00d7-275">Одним из вариантов получения экземпляра службы в классе является создание конструктора с параметром требуемого типа.</span><span class="sxs-lookup"><span data-stu-id="c00d7-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="c00d7-276">Параметр может быть типом службы или интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="c00d7-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="c00d7-277">Система внедрения зависимостей предоставляет службу во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c00d7-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="c00d7-278">Ниже показан класс, который использует внедрение зависимостей для получения объекта контекста Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="c00d7-279">Выделенная строка является примером внедрения через конструктор.</span><span class="sxs-lookup"><span data-stu-id="c00d7-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="c00d7-280">Несмотря на то, что система внедрения зависимостей является встроенной, она позволяет вам при желании подключать сторонний контейнер с инверсией управления (IoC).</span><span class="sxs-lookup"><span data-stu-id="c00d7-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="c00d7-281">Для получения дополнительной информации см. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="c00d7-282">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c00d7-282">Middleware</span></span>

<span data-ttu-id="c00d7-283">Конвейер обработки запросов состоит из ряда компонентов ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="c00d7-284">Каждый компонент выполняет асинхронные операции в `HttpContext`, а затем либо вызывает следующий компонент в конвейере, либо завершает запрос.</span><span class="sxs-lookup"><span data-stu-id="c00d7-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="c00d7-285">По принятому соглашению компонент ПО промежуточного слоя добавляется в конвейер вызовом метода расширения `Use...` в методе `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="c00d7-286">Например, чтобы включить отрисовку статических файлов, вызовите `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="c00d7-287">Код настройки конвейера обработки запросов выделен в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="c00d7-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="c00d7-288">ASP.NET Core включает обширный набор встроенного ПО промежуточного слоя и позволяет вам писать свое собственное.</span><span class="sxs-lookup"><span data-stu-id="c00d7-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="c00d7-289">Для получения дополнительной информации см. <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="c00d7-290">Узел</span><span class="sxs-lookup"><span data-stu-id="c00d7-290">Host</span></span>

<span data-ttu-id="c00d7-291">Приложение ASP.NET Core создает *узел* при запуске.</span><span class="sxs-lookup"><span data-stu-id="c00d7-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="c00d7-292">Узел — это объект, который инкапсулирует все ресурсы приложения.</span><span class="sxs-lookup"><span data-stu-id="c00d7-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="c00d7-293">Реализация HTTP-сервера</span><span class="sxs-lookup"><span data-stu-id="c00d7-293">An HTTP server implementation</span></span>
* <span data-ttu-id="c00d7-294">Компоненты ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="c00d7-294">Middleware components</span></span>
* <span data-ttu-id="c00d7-295">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="c00d7-295">Logging</span></span>
* <span data-ttu-id="c00d7-296">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="c00d7-296">DI</span></span>
* <span data-ttu-id="c00d7-297">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="c00d7-297">Configuration</span></span>

<span data-ttu-id="c00d7-298">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="c00d7-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="c00d7-299">Доступны два узла: веб-узел и универсальный узел.</span><span class="sxs-lookup"><span data-stu-id="c00d7-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="c00d7-300">В ASP.NET Core 2.x универсальный узел используется в сценариях, не связанных с Интернетом.</span><span class="sxs-lookup"><span data-stu-id="c00d7-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="c00d7-301">Код для создания узла находится в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c00d7-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="c00d7-302">Метод `CreateDefaultBuilder` применяется для настройки узла с часто используемыми параметрами, например:</span><span class="sxs-lookup"><span data-stu-id="c00d7-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="c00d7-303">Использование [Kestrel](#servers) в качестве веб-сервера и поддержка интеграции с IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="c00d7-304">Загрузка конфигурации из файлов *:::no-loc(appsettings.json):::* и *appsettings.{Environment Name}.json* , переменных среды, аргументов командной строки и других источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c00d7-304">Load configuration from *:::no-loc(appsettings.json):::* , *appsettings.{Environment Name}.json* , environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="c00d7-305">Отправка выходных данных журнала в поставщики служб консоли и отладки.</span><span class="sxs-lookup"><span data-stu-id="c00d7-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="c00d7-306">Для получения дополнительной информации см. <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="c00d7-307">Сценарии, не связанные с Интернетом</span><span class="sxs-lookup"><span data-stu-id="c00d7-307">Non-web scenarios</span></span>

<span data-ttu-id="c00d7-308">Универсальный узел позволяет приложениям других типов использовать перекрестные расширения платформ, такие как средства ведения журналов, внедрения зависимостей, настройки и управления жизненным циклом.</span><span class="sxs-lookup"><span data-stu-id="c00d7-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="c00d7-309">Дополнительные сведения см. в разделах <xref:fundamentals/host/generic-host> и <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="c00d7-310">Серверы</span><span class="sxs-lookup"><span data-stu-id="c00d7-310">Servers</span></span>

<span data-ttu-id="c00d7-311">Приложение ASP.NET Core использует реализацию HTTP-сервера для приема HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="c00d7-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="c00d7-312">Сервер отправляет приложению запросы в виде набора [функций запросов](xref:fundamentals/request-features), объединенных в `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="c00d7-313">Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="c00d7-314">ASP.NET Core предоставляет следующие реализации серверов:</span><span class="sxs-lookup"><span data-stu-id="c00d7-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="c00d7-315">*Kestrel*  — это кроссплатформенный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="c00d7-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="c00d7-316">Он часто запускается в реконфигурации прокси-сервера с помощью [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="c00d7-317">Kestrel можно использовать в роли общедоступного пограничного сервера с прямым доступом из Интернета.</span><span class="sxs-lookup"><span data-stu-id="c00d7-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="c00d7-318">*HTTP-сервер IIS*  — это сервер для Windows, который использует службы IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="c00d7-319">Он позволяет запускать приложение ASP.NET Core и службы IIS в одном процессе.</span><span class="sxs-lookup"><span data-stu-id="c00d7-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="c00d7-320">*HTTP.sys*  — это сервер для Windows, который не используется со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="c00d7-321">macOS</span><span class="sxs-lookup"><span data-stu-id="c00d7-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="c00d7-322">ASP.NET Core предоставляет реализацию кроссплатформенного сервера *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="c00d7-323">Kestrel можно использовать в роли общедоступного пограничного сервера с прямым доступом из Интернета.</span><span class="sxs-lookup"><span data-stu-id="c00d7-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="c00d7-324">Он часто запускается в реконфигурации прокси-сервера с помощью [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="c00d7-325">Linux</span><span class="sxs-lookup"><span data-stu-id="c00d7-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="c00d7-326">ASP.NET Core предоставляет реализацию кроссплатформенного сервера *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="c00d7-327">Kestrel можно использовать в роли общедоступного пограничного сервера с прямым доступом из Интернета.</span><span class="sxs-lookup"><span data-stu-id="c00d7-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="c00d7-328">Он часто запускается в реконфигурации прокси-сервера с помощью [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="c00d7-329">Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="c00d7-330">ASP.NET Core предоставляет следующие реализации серверов:</span><span class="sxs-lookup"><span data-stu-id="c00d7-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="c00d7-331">*Kestrel*  — это кроссплатформенный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="c00d7-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="c00d7-332">Он часто запускается в реконфигурации прокси-сервера с помощью [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="c00d7-333">Kestrel можно использовать в роли общедоступного пограничного сервера с прямым доступом из Интернета.</span><span class="sxs-lookup"><span data-stu-id="c00d7-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="c00d7-334">*HTTP.sys*  — это сервер для Windows, который не используется со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="c00d7-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="c00d7-335">macOS</span><span class="sxs-lookup"><span data-stu-id="c00d7-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="c00d7-336">ASP.NET Core предоставляет реализацию кроссплатформенного сервера *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="c00d7-337">Kestrel можно использовать в роли общедоступного пограничного сервера с прямым доступом из Интернета.</span><span class="sxs-lookup"><span data-stu-id="c00d7-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="c00d7-338">Он часто запускается в реконфигурации прокси-сервера с помощью [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="c00d7-339">Linux</span><span class="sxs-lookup"><span data-stu-id="c00d7-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="c00d7-340">ASP.NET Core предоставляет реализацию кроссплатформенного сервера *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="c00d7-341">Kestrel можно использовать в роли общедоступного пограничного сервера с прямым доступом из Интернета.</span><span class="sxs-lookup"><span data-stu-id="c00d7-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="c00d7-342">Он часто запускается в реконфигурации прокси-сервера с помощью [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c00d7-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c00d7-343">Для получения дополнительной информации см. <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="c00d7-344">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="c00d7-344">Configuration</span></span>

<span data-ttu-id="c00d7-345">ASP.NET Core предоставляет платформу конфигурации, которая получает параметры в виде пар "имя-значение" от упорядоченного набора поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c00d7-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="c00d7-346">Доступны встроенные поставщики конфигурации для различных источников, таких как файлы *JSON* , *XML* , переменные среды и аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="c00d7-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="c00d7-347">Кроме того, вы можете писать поставщики конфигурации сами.</span><span class="sxs-lookup"><span data-stu-id="c00d7-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="c00d7-348">Например, можно указать, что источником конфигурации являются файл *:::no-loc(appsettings.json):::* и переменные среды.</span><span class="sxs-lookup"><span data-stu-id="c00d7-348">For example, you could specify that configuration comes from *:::no-loc(appsettings.json):::* and environment variables.</span></span> <span data-ttu-id="c00d7-349">В этом случае при запросе значения *ConnectionString* платформа сначала выполнит поиск в файле *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="c00d7-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="c00d7-350">Если значение будет найдено не только в нем, но и в переменной среды, приоритет получит значение из переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c00d7-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="c00d7-351">Для управления конфиденциальными данными конфигурации, например паролями, ASP.NET Core предоставляет специальный инструмент [Менеджер секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c00d7-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="c00d7-352">Для секретов в рабочей среде рекомендуется использовать [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c00d7-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="c00d7-353">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="c00d7-354">Параметры</span><span class="sxs-lookup"><span data-stu-id="c00d7-354">Options</span></span>

<span data-ttu-id="c00d7-355">Когда это возможно, ASP.NET Core следует *шаблону параметров* для хранения и получения значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="c00d7-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="c00d7-356">Шаблон параметров использует классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="c00d7-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="c00d7-357">Например, следующий код задает параметры WebSockets:</span><span class="sxs-lookup"><span data-stu-id="c00d7-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="c00d7-358">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="c00d7-359">Среды</span><span class="sxs-lookup"><span data-stu-id="c00d7-359">Environments</span></span>

<span data-ttu-id="c00d7-360">Среды выполнения, включая среду *разработки* , *промежуточную* и *рабочую* среды, являются ключевыми компонентами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-360">Execution environments, such as *Development* , *Staging* , and *Production* , are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="c00d7-361">Указать среду, в которой запускается приложение, можно с помощью переменной среды `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="c00d7-362">ASP.NET Core считывает переменную среды при запуске приложения и сохраняет ее значение в реализации `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="c00d7-363">Объект среды можно получить в любом месте приложения при помощи внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="c00d7-364">В следующем примере кода из класса `Startup` показана настройка приложения для вывода подробных сведений об ошибках исключительно при запуске в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="c00d7-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="c00d7-365">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="c00d7-366">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="c00d7-366">Logging</span></span>

<span data-ttu-id="c00d7-367">ASP.NET Core поддерживает API ведения журналов, который работает с разными встроенными и сторонними поставщиками.</span><span class="sxs-lookup"><span data-stu-id="c00d7-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="c00d7-368">Доступны следующие поставщики:</span><span class="sxs-lookup"><span data-stu-id="c00d7-368">Available providers include the following:</span></span>

* <span data-ttu-id="c00d7-369">Консоль</span><span class="sxs-lookup"><span data-stu-id="c00d7-369">Console</span></span>
* <span data-ttu-id="c00d7-370">Отладка</span><span class="sxs-lookup"><span data-stu-id="c00d7-370">Debug</span></span>
* <span data-ttu-id="c00d7-371">Трассировка событий Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="c00d7-372">Журнал событий Windows</span><span class="sxs-lookup"><span data-stu-id="c00d7-372">Windows Event Log</span></span>
* <span data-ttu-id="c00d7-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c00d7-373">TraceSource</span></span>
* <span data-ttu-id="c00d7-374">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="c00d7-374">Azure App Service</span></span>
* <span data-ttu-id="c00d7-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="c00d7-375">Azure Application Insights</span></span>

<span data-ttu-id="c00d7-376">Записи в журналы можно вносить в любом месте кода приложения. Для этого нужно получить объект `ILogger` из внедрения зависимостей и вызвать методы ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="c00d7-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="c00d7-377">Ниже приведен пример кода, где используется объект `ILogger`. В коде выделены строки внедрения конструктора и вызова методов ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="c00d7-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="c00d7-378">Интерфейс `ILogger` позволяет передавать в поставщик ведения журналов любое количество полей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="c00d7-379">Как правило, поля используются для создания строки сообщения, но поставщик также может отправлять их в хранилище данных в виде отдельных полей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="c00d7-380">Благодаря этому поставщики могут реализовывать [семантическое (структурированное) ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="c00d7-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="c00d7-381">Для получения дополнительной информации см. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="c00d7-382">Маршрутизация</span><span class="sxs-lookup"><span data-stu-id="c00d7-382">Routing</span></span>

<span data-ttu-id="c00d7-383">*Маршрут*  — это шаблон URL-адреса, сопоставляемый с обработчиком.</span><span class="sxs-lookup"><span data-stu-id="c00d7-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="c00d7-384">Обычно обработчик представляет собой страницу :::no-loc(Razor):::, метод действия в контроллере MVC или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="c00d7-384">The handler is typically a :::no-loc(Razor)::: page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="c00d7-385">Маршрутизация ASP.NET Core позволяет контролировать URL-адреса, используемые приложением.</span><span class="sxs-lookup"><span data-stu-id="c00d7-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="c00d7-386">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="c00d7-387">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="c00d7-387">Error handling</span></span>

<span data-ttu-id="c00d7-388">ASP.NET Core содержит встроенные функции обработки ошибок, такие как:</span><span class="sxs-lookup"><span data-stu-id="c00d7-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="c00d7-389">Страница исключений для разработчика</span><span class="sxs-lookup"><span data-stu-id="c00d7-389">A developer exception page</span></span>
* <span data-ttu-id="c00d7-390">Настраиваемые страницы ошибок</span><span class="sxs-lookup"><span data-stu-id="c00d7-390">Custom error pages</span></span>
* <span data-ttu-id="c00d7-391">Статические страницы с кодами состояния</span><span class="sxs-lookup"><span data-stu-id="c00d7-391">Static status code pages</span></span>
* <span data-ttu-id="c00d7-392">Обработка исключений при запуске</span><span class="sxs-lookup"><span data-stu-id="c00d7-392">Startup exception handling</span></span>

<span data-ttu-id="c00d7-393">Для получения дополнительной информации см. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="c00d7-394">Создание HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="c00d7-394">Make HTTP requests</span></span>

<span data-ttu-id="c00d7-395">ASP.NET Core включает реализацию интерфейса `IHttpClientFactory` для создания экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="c00d7-396">Фабрика:</span><span class="sxs-lookup"><span data-stu-id="c00d7-396">The factory:</span></span>

* <span data-ttu-id="c00d7-397">Центральное расположение для именования и настройки логических экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c00d7-398">Например, можно зарегистрировать и использовать клиент *github* для доступа к GitHub.</span><span class="sxs-lookup"><span data-stu-id="c00d7-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="c00d7-399">Можно зарегистрировать клиент по умолчанию для других целей.</span><span class="sxs-lookup"><span data-stu-id="c00d7-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c00d7-400">Поддержка регистрации и связывания в цепочки множества делегирующих обработчиков для создания конвейера ПО промежуточного слоя под исходящие запросы.</span><span class="sxs-lookup"><span data-stu-id="c00d7-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c00d7-401">Этот шаблон похож на входящий конвейер ПО промежуточного слоя в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c00d7-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c00d7-402">Шаблон предоставляет механизм управления сквозной функциональностью HTTP-запросов, включая кэширование, обработку ошибок, сериализацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="c00d7-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="c00d7-403">Интеграция с *Polly*  — популярной сторонней библиотекой для обработки временных сбоев.</span><span class="sxs-lookup"><span data-stu-id="c00d7-403">Integrates with *Polly* , a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="c00d7-404">Управление созданием пулов и временем существования базовых экземпляров `HttpClientHandler` с целью избежать обычных проблем с DNS, которые возникают при управлении временем существования `HttpClient` вручную.</span><span class="sxs-lookup"><span data-stu-id="c00d7-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c00d7-405">Настройка параметров ведения журнала (через `ILogger`) для всех запросов, отправленных через клиентов, созданных фабрикой.</span><span class="sxs-lookup"><span data-stu-id="c00d7-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c00d7-406">Для получения дополнительной информации см. <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="c00d7-407">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="c00d7-407">Content root</span></span>

<span data-ttu-id="c00d7-408">Корневой каталог содержимого — это базовый путь к таким элементам:</span><span class="sxs-lookup"><span data-stu-id="c00d7-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="c00d7-409">Исполняемый файл, в котором размещено приложение ( *EXE* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-409">Executable hosting the app ( *.exe* ).</span></span>
* <span data-ttu-id="c00d7-410">Скомпилированные сборки, составляющие приложение ( *DLL* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-410">Compiled assemblies that make up the app ( *.dll* ).</span></span>
* <span data-ttu-id="c00d7-411">Файлы содержимого, не относящиеся к коду, используемые приложением, например:</span><span class="sxs-lookup"><span data-stu-id="c00d7-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="c00d7-412">Файлы :::no-loc(Razor)::: ( *CSHTML* , *RAZOR* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-412">:::no-loc(Razor)::: files ( *.cshtml* , *.razor* )</span></span>
  * <span data-ttu-id="c00d7-413">Файлы конфигурации ( *JSON* , *XML* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-413">Configuration files ( *.json* , *.xml* )</span></span>
  * <span data-ttu-id="c00d7-414">Файлы данных ( *DB* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-414">Data files ( *.db* )</span></span>
* <span data-ttu-id="c00d7-415">[Корневой веб-каталог](#web-root), обычно опубликованная папка *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="c00d7-416">Во время разработки:</span><span class="sxs-lookup"><span data-stu-id="c00d7-416">During development:</span></span>

* <span data-ttu-id="c00d7-417">Корень содержимого по умолчанию сбрасывается до корневого каталога проекта.</span><span class="sxs-lookup"><span data-stu-id="c00d7-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="c00d7-418">Корневой каталог проекта используется для создания:</span><span class="sxs-lookup"><span data-stu-id="c00d7-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="c00d7-419">пути к файлам содержимого, не являющихся кодом приложения, в корневом каталоге проекта;</span><span class="sxs-lookup"><span data-stu-id="c00d7-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="c00d7-420">[корневого веб-каталога](#web-root), обычно папка *wwwroot* в корневом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="c00d7-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="c00d7-421">Альтернативный корневой путь к содержимому может быть указан при [создании узла](#host).</span><span class="sxs-lookup"><span data-stu-id="c00d7-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="c00d7-422">Для получения дополнительной информации см. <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="c00d7-423">Корневой веб-узел</span><span class="sxs-lookup"><span data-stu-id="c00d7-423">Web root</span></span>

<span data-ttu-id="c00d7-424">Корневой веб-каталог — это базовый путь к общедоступным, не кодовым файлам статических ресурсов, например:</span><span class="sxs-lookup"><span data-stu-id="c00d7-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="c00d7-425">Таблицы стилей ( *CSS* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-425">Stylesheets ( *.css* )</span></span>
* <span data-ttu-id="c00d7-426">JavaScript ( *JS* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-426">JavaScript ( *.js* )</span></span>
* <span data-ttu-id="c00d7-427">Изображения ( *PNG* , *JPG* ).</span><span class="sxs-lookup"><span data-stu-id="c00d7-427">Images ( *.png* , *.jpg* )</span></span>

<span data-ttu-id="c00d7-428">Статические файлы обслуживаются по умолчанию только в корневом веб-каталоге (и подкаталогах).</span><span class="sxs-lookup"><span data-stu-id="c00d7-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="c00d7-429">По умолчанию используется путь *{корневой каталог содержимого}/wwwroot*. Альтернативное расположение можно задать при [создании узла](#host).</span><span class="sxs-lookup"><span data-stu-id="c00d7-429">The web root path defaults to *{content root}/wwwroot* , but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="c00d7-430">Дополнительные сведения см. в разделе [Корневой веб-каталог](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="c00d7-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="c00d7-431">Запретите публикацию файлов в *wwwroot* с помощью [элемента проекта \<Content>](/visualstudio/msbuild/common-msbuild-project-items#content) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="c00d7-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="c00d7-432">В следующем примере запрещается публикация содержимого в каталоге *wwwroot/local* и подкаталогах:</span><span class="sxs-lookup"><span data-stu-id="c00d7-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c00d7-433">Для указания на корневой каталог файлов :::no-loc(Razor)::: ( *CSHTML* ) используется символ тильды и косой черты `~/`.</span><span class="sxs-lookup"><span data-stu-id="c00d7-433">In :::no-loc(Razor)::: ( *.cshtml* ) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="c00d7-434">Путь, начинающийся с `~/`, называется *виртуальным путем*.</span><span class="sxs-lookup"><span data-stu-id="c00d7-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="c00d7-435">Для получения дополнительной информации см. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c00d7-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
