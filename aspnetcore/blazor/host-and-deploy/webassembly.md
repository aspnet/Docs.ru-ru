---
title: Размещение и развертывание ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и страниц GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
- appsettings.json
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7ae462ff9abd06fe4ab4b3e00a71515b76b0ee7d
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94339988"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c4515-103">Размещение и развертывание ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c4515-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="c4515-104">Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).</span><span class="sxs-lookup"><span data-stu-id="c4515-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="c4515-105">При использовании [модели размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="c4515-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="c4515-106">Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.</span><span class="sxs-lookup"><span data-stu-id="c4515-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="c4515-107">Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.</span><span class="sxs-lookup"><span data-stu-id="c4515-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="c4515-108">Поддерживаются следующие стратегии развертывания:</span><span class="sxs-lookup"><span data-stu-id="c4515-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="c4515-109">Приложение Blazor обслуживается приложением ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4515-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="c4515-110">Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="c4515-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="c4515-111">Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="c4515-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="c4515-112">Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.</span><span class="sxs-lookup"><span data-stu-id="c4515-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="c4515-113">Сжатие</span><span class="sxs-lookup"><span data-stu-id="c4515-113">Compression</span></span>

<span data-ttu-id="c4515-114">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="c4515-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="c4515-115">Используются следующие алгоритмы сжатия:</span><span class="sxs-lookup"><span data-stu-id="c4515-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="c4515-116">[Brotli](https://tools.ietf.org/html/rfc7932) (высший уровень)</span><span class="sxs-lookup"><span data-stu-id="c4515-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="c4515-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="c4515-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="c4515-118">Для обслуживания соответствующих сжатых файлов в Blazor используется ведущий проект.</span><span class="sxs-lookup"><span data-stu-id="c4515-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="c4515-119">При использовании размещенного проекта ASP.NET Core ведущий проект может выполнять согласование содержимого и обслуживать статически сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="c4515-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="c4515-120">При размещении автономного приложения Blazor WebAssembly, возможно, потребуется реализовать дополнительные действия для обслуживания статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="c4515-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="c4515-121">Сведения о конфигурации сжатия `web.config` IIS см. в статье [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="c4515-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="c4515-122">При размещении в статических решениях размещения, которые не поддерживают согласование содержимого статически сжатых файлов, например на страницах GitHub, рассмотрите возможность настройки приложения для извлечения и декодирования сжатых файлов Brotli:</span><span class="sxs-lookup"><span data-stu-id="c4515-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="c4515-123">Получите декодер JavaScript Brotli из [репозитория GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="c4515-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c4515-124">По состоянию на сентябрь 2020 г. файл декодера называется `decode.js` и находится в [папке `js`](https://github.com/google/brotli/tree/master/js) репозитория.</span><span class="sxs-lookup"><span data-stu-id="c4515-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="c4515-125">Регрессия содержится в сокращенной версии скрипта `decode.js` (`decode.min.js`) в [репозитории GitHub google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="c4515-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c4515-126">Либо сократите скрипт самостоятельно, либо используйте [пакет npm](https://www.npmjs.com/package/brotli), пока проблема [Параметр Window.BrotliDecode не задан в decode.min.js (google/brotli № 844)](https://github.com/google/brotli/issues/844) не будет устранена.</span><span class="sxs-lookup"><span data-stu-id="c4515-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="c4515-127">В примере кода в этом разделе используется **не сокращенная** версия скрипта.</span><span class="sxs-lookup"><span data-stu-id="c4515-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="c4515-128">обновите приложение для использования декодера.</span><span class="sxs-lookup"><span data-stu-id="c4515-128">Update the app to use the decoder.</span></span> <span data-ttu-id="c4515-129">Измените разметку в закрывающем теге `<body>` в `wwwroot/index.html` следующим образом.</span><span class="sxs-lookup"><span data-stu-id="c4515-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="c4515-130">Чтобы отключить сжатие, добавьте свойство `BlazorEnableCompression` MSBuild в файл проекта приложения и задайте для него значение `false`.</span><span class="sxs-lookup"><span data-stu-id="c4515-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="c4515-131">Свойство `BlazorEnableCompression` можно передать в команду [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с помощью следующего синтаксиса в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="c4515-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="c4515-132">Переопределение URL-адресов для маршрутизации</span><span class="sxs-lookup"><span data-stu-id="c4515-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="c4515-133">Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к размещенному приложению Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c4515-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="c4515-134">Рассмотрим сценарий с приложением Blazor WebAssembly с двумя компонентами:</span><span class="sxs-lookup"><span data-stu-id="c4515-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="c4515-135">`Main.razor`. загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="c4515-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="c4515-136">`About.razor`: компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="c4515-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="c4515-137">При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="c4515-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="c4515-138">Браузер отправляет запрос.</span><span class="sxs-lookup"><span data-stu-id="c4515-138">The browser makes a request.</span></span>
1. <span data-ttu-id="c4515-139">Возвращается страница по умолчанию; обычно это `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c4515-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="c4515-140">Страница `index.html` обеспечивает начальную загрузку приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="c4515-141">Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.</span><span class="sxs-lookup"><span data-stu-id="c4515-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="c4515-142">На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`.</span><span class="sxs-lookup"><span data-stu-id="c4515-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="c4515-143">Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.</span><span class="sxs-lookup"><span data-stu-id="c4515-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="c4515-144">Маршрутизатор обрабатывает запросы внутренним образом.</span><span class="sxs-lookup"><span data-stu-id="c4515-144">The router handles the requests internally.</span></span>

<span data-ttu-id="c4515-145">Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="c4515-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="c4515-146">Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).</span><span class="sxs-lookup"><span data-stu-id="c4515-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="c4515-147">Так как браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c4515-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="c4515-148">Когда `index.html` возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.</span><span class="sxs-lookup"><span data-stu-id="c4515-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="c4515-149">При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом `web.config` приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="c4515-150">Дополнительные сведения см. в описании [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="c4515-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="c4515-151">Размещенное развертывание с использованием ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4515-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="c4515-152">*Размещенное развертывание* обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="c4515-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="c4515-153">Клиентское приложение Blazor WebAssembly будет опубликовано в папку `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` серверного приложения вместе со статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="c4515-154">Оба приложения развертываются вместе.</span><span class="sxs-lookup"><span data-stu-id="c4515-154">The two apps are deployed together.</span></span> <span data-ttu-id="c4515-155">Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным.</span><span class="sxs-lookup"><span data-stu-id="c4515-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c4515-156">Для размещаемого развертывания Visual Studio включает шаблон проекта **Приложение Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [`dotnet new`](/dotnet/core/tools/dotnet-new)) с выбранным параметром **`Hosted`** (`-ho|--hosted` при использовании команды `dotnet new`).</span><span class="sxs-lookup"><span data-stu-id="c4515-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="c4515-157">Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="c4515-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c4515-158">Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="c4515-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="c4515-159">Размещенное развертывание с несколькими приложениями Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c4515-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="c4515-160">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="c4515-160">App configuration</span></span>

<span data-ttu-id="c4515-161">Чтобы настроить размещенное решение Blazor для обслуживания нескольких приложений Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="c4515-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="c4515-162">Используйте существующее размещенное решение Blazor или создайте новое решение из размещенного шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="c4515-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="c4515-163">В файле проекта клиентского приложения добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `FirstApp`, чтобы задать базовый путь для статических ресурсов проекта.</span><span class="sxs-lookup"><span data-stu-id="c4515-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="c4515-164">Добавьте в решение второе клиентское приложение:</span><span class="sxs-lookup"><span data-stu-id="c4515-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="c4515-165">Добавьте папку с именем `SecondClient` в папку решения.</span><span class="sxs-lookup"><span data-stu-id="c4515-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="c4515-166">Создайте приложение Blazor WebAssembly с именем `SecondBlazorApp.Client` в папке `SecondClient` из шаблона проекта Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c4515-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="c4515-167">В файле проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="c4515-167">In the app's project file:</span></span>

    * <span data-ttu-id="c4515-168">Добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `SecondApp`:</span><span class="sxs-lookup"><span data-stu-id="c4515-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="c4515-169">Добавьте ссылку на проект в проект `Shared`:</span><span class="sxs-lookup"><span data-stu-id="c4515-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="c4515-170">Заполнитель `{SOLUTION NAME}` — это имя решения.</span><span class="sxs-lookup"><span data-stu-id="c4515-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="c4515-171">В файле проекта серверного приложения создайте ссылку на проект для добавленного клиентского приложения:</span><span class="sxs-lookup"><span data-stu-id="c4515-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="c4515-172">В файле серверного приложения `Properties/launchSettings.json` настройте `applicationUrl` профиля Kestrel (`{SOLUTION NAME}.Server`), чтобы получить доступ к клиентским приложениям через порты 5001 и 5002:</span><span class="sxs-lookup"><span data-stu-id="c4515-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="c4515-173">В методе `Startup.Configure` серверного приложения (`Startup.cs`) удалите эти строки, которые появляются после вызова <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="c4515-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="c4515-174">Добавьте ПО промежуточного слоя, которое сопоставляет запросы с клиентскими приложениями.</span><span class="sxs-lookup"><span data-stu-id="c4515-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="c4515-175">В следующем примере выполняется настройка ПО промежуточного слоя для запуска в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="c4515-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="c4515-176">Порт запроса — 5001 для исходного клиентского приложения или 5002 для добавленного клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="c4515-177">Узел запроса — `firstapp.com` для исходного клиентского приложения или `secondapp.com` для добавленного клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="c4515-178">В примере, приведенном в этом разделе, требуется дополнительная настройка для:</span><span class="sxs-lookup"><span data-stu-id="c4515-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="c4515-179">Доступа к приложениям в примерах доменов узла `firstapp.com` и `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="c4515-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="c4515-180">Сертификатов для клиентских приложений, чтобы включить средства безопасности TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c4515-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="c4515-181">Описание полной необходимой конфигурации выходит за рамки этой статьи и зависит от того, как размещается решение.</span><span class="sxs-lookup"><span data-stu-id="c4515-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="c4515-182">Дополнительные сведения см. в [статьях о размещении и развертывании](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="c4515-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="c4515-183">Поместите следующий код, где строки были удалены ранее:</span><span class="sxs-lookup"><span data-stu-id="c4515-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="c4515-184">В контроллере прогноза погоды серверного приложения (`Controllers/WeatherForecastController.cs`) замените существующий маршрут (`[Route("[controller]")]`) к `WeatherForecastController` следующими маршрутами:</span><span class="sxs-lookup"><span data-stu-id="c4515-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="c4515-185">ПО промежуточного слоя, добавленное в метод `Startup.Configure` серверного приложения ранее, изменяет входящие запросы к `/WeatherForecast` на `/FirstApp/WeatherForecast` или `/SecondApp/WeatherForecast` в зависимости от порта (5001/5002) или домена (`firstapp.com`/`secondapp.com`).</span><span class="sxs-lookup"><span data-stu-id="c4515-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="c4515-186">Предыдущие маршруты контроллеров необходимы для возврата данных о погоде из серверного приложения в клиентские приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="c4515-187">Статические ресурсы и библиотеки классов</span><span class="sxs-lookup"><span data-stu-id="c4515-187">Static assets and class libraries</span></span>

<span data-ttu-id="c4515-188">Для статических ресурсов используйте следующие подходы.</span><span class="sxs-lookup"><span data-stu-id="c4515-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="c4515-189">Если ресурс находится в папке `wwwroot` клиентского приложения, укажите пути к ним обычным образом:</span><span class="sxs-lookup"><span data-stu-id="c4515-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="c4515-190">Если ресурс находится в папке `wwwroot` [библиотеки классов (RCL) Razor](xref:blazor/components/class-libraries), ссылайтесь на статический ресурс в клиентском приложении, следуя инструкциям в статье об [RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="c4515-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="c4515-191">На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом.</span><span class="sxs-lookup"><span data-stu-id="c4515-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="c4515-192">Если какие либо компоненты нуждаются в таблицах стилей или файлах JavaScript, то файл `wwwroot/index.html` клиентского приложения должен содержать правильные ссылки на статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="c4515-192">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="c4515-193">Эти подходы демонстрируются в следующих примерах.</span><span class="sxs-lookup"><span data-stu-id="c4515-193">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="c4515-194">Добавьте следующий компонент `Jeep` в одно из клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="c4515-194">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="c4515-195">Компонент `Jeep` использует:</span><span class="sxs-lookup"><span data-stu-id="c4515-195">The `Jeep` component uses:</span></span>

* <span data-ttu-id="c4515-196">изображение из папки `wwwroot` клиентского приложения (`jeep-cj.png`);</span><span class="sxs-lookup"><span data-stu-id="c4515-196">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="c4515-197">изображение из папки `wwwroot` (`jeep-yj.png`) [добавленной библиотеки компонентов Razor](xref:blazor/components/class-libraries) (`JeepImage`).</span><span class="sxs-lookup"><span data-stu-id="c4515-197">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="c4515-198">Пример компонента (`Component1`) создается автоматически шаблоном проекта RCL при добавлении библиотеки `JeepImage` в решение.</span><span class="sxs-lookup"><span data-stu-id="c4515-198">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="c4515-199">**Не** публикуйте изображения автомобилей, если вы не являетесь владельцем этих изображений.</span><span class="sxs-lookup"><span data-stu-id="c4515-199">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="c4515-200">В противном случае вы рискуете нарушить авторские права.</span><span class="sxs-lookup"><span data-stu-id="c4515-200">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="c4515-201">Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки.</span><span class="sxs-lookup"><span data-stu-id="c4515-201">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="c4515-202">Файл `wwwroot/index.html` клиентского приложения запрашивает таблицу стилей библиотеки со следующим добавленным тегом `<link>`:</span><span class="sxs-lookup"><span data-stu-id="c4515-202">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="c4515-203">Добавьте навигацию в компонент `Jeep` в компоненте `NavMenu` клиентского приложения (`Shared/NavMenu.razor`):</span><span class="sxs-lookup"><span data-stu-id="c4515-203">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="c4515-204">Дополнительные сведения об RCL см. здесь:</span><span class="sxs-lookup"><span data-stu-id="c4515-204">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="c4515-205">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="c4515-205">Standalone deployment</span></span>

<span data-ttu-id="c4515-206">*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами.</span><span class="sxs-lookup"><span data-stu-id="c4515-206">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="c4515-207">Любой статический файловый сервер способен обслуживать приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="c4515-207">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="c4515-208">Ресурсы автономного развертывания публикуются в папке `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="c4515-208">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c4515-209">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="c4515-209">Azure App Service</span></span>

<span data-ttu-id="c4515-210">Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="c4515-210">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="c4515-211">Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="c4515-211">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="c4515-212">Сейчас образ сервера Linux для размещения приложения недоступен.</span><span class="sxs-lookup"><span data-stu-id="c4515-212">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="c4515-213">Ведется работа над реализацией этого сценария.</span><span class="sxs-lookup"><span data-stu-id="c4515-213">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="c4515-214">IIS</span><span class="sxs-lookup"><span data-stu-id="c4515-214">IIS</span></span>

<span data-ttu-id="c4515-215">IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="c4515-215">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="c4515-216">Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c4515-216">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="c4515-217">Опубликованные ресурсы создаются в папке `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="c4515-217">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="c4515-218">Разместите содержимое папки `publish` на веб-сервере или в службе размещения.</span><span class="sxs-lookup"><span data-stu-id="c4515-218">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="c4515-219">web.config.</span><span class="sxs-lookup"><span data-stu-id="c4515-219">web.config</span></span>

<span data-ttu-id="c4515-220">При публикации проекта Blazor создается файл `web.config` со следующей конфигурацией IIS:</span><span class="sxs-lookup"><span data-stu-id="c4515-220">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="c4515-221">Типы MIME заданы для следующих расширений файлов:</span><span class="sxs-lookup"><span data-stu-id="c4515-221">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="c4515-222">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="c4515-222">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="c4515-223">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="c4515-223">`.json`: `application/json`</span></span>
  * <span data-ttu-id="c4515-224">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="c4515-224">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="c4515-225">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c4515-225">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="c4515-226">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c4515-226">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="c4515-227">Сжатие HTTP включено для следующих типов MIME:</span><span class="sxs-lookup"><span data-stu-id="c4515-227">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="c4515-228">Задаются правила модуля переопределения URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="c4515-228">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="c4515-229">Предоставление подкаталога для размещения статических ресурсов приложения (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="c4515-229">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="c4515-230">Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (`wwwroot/index.html`).</span><span class="sxs-lookup"><span data-stu-id="c4515-230">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="c4515-231">Использование пользовательского файла web.config</span><span class="sxs-lookup"><span data-stu-id="c4515-231">Use a custom web.config</span></span>

<span data-ttu-id="c4515-232">Чтобы применить пользовательский файл `web.config`, поместите пользовательский файл `web.config` в корневую папку проекта.</span><span class="sxs-lookup"><span data-stu-id="c4515-232">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="c4515-233">Настройте проект для публикации ресурсов IIS, указав `PublishIISAssets` в файле проекта приложения, и опубликуйте проект:</span><span class="sxs-lookup"><span data-stu-id="c4515-233">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="c4515-234">Установка модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="c4515-234">Install the URL Rewrite Module</span></span>

<span data-ttu-id="c4515-235">[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="c4515-235">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="c4515-236">Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).</span><span class="sxs-lookup"><span data-stu-id="c4515-236">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="c4515-237">Модуль необходимо скачать с веб-сайта IIS.</span><span class="sxs-lookup"><span data-stu-id="c4515-237">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="c4515-238">Для его установки используйте установщик веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="c4515-238">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="c4515-239">Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="c4515-239">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="c4515-240">В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.</span><span class="sxs-lookup"><span data-stu-id="c4515-240">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="c4515-241">Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.</span><span class="sxs-lookup"><span data-stu-id="c4515-241">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="c4515-242">Скопируйте установщик на сервер.</span><span class="sxs-lookup"><span data-stu-id="c4515-242">Copy the installer to the server.</span></span> <span data-ttu-id="c4515-243">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="c4515-243">Run the installer.</span></span> <span data-ttu-id="c4515-244">Нажмите кнопку **Установить** и примите условия лицензионного соглашения.</span><span class="sxs-lookup"><span data-stu-id="c4515-244">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="c4515-245">Перезагрузка сервера после завершения установки не требуется.</span><span class="sxs-lookup"><span data-stu-id="c4515-245">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="c4515-246">Настройка веб-сайта</span><span class="sxs-lookup"><span data-stu-id="c4515-246">Configure the website</span></span>

<span data-ttu-id="c4515-247">Установите для веб сайта **физический путь** к папке приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-247">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="c4515-248">Папка содержит:</span><span class="sxs-lookup"><span data-stu-id="c4515-248">The folder contains:</span></span>

* <span data-ttu-id="c4515-249">Файл `web.config`, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.</span><span class="sxs-lookup"><span data-stu-id="c4515-249">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="c4515-250">Папку статических ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-250">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="c4515-251">Размещение в качестве дочернего приложения IIS</span><span class="sxs-lookup"><span data-stu-id="c4515-251">Host as an IIS sub-app</span></span>

<span data-ttu-id="c4515-252">Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="c4515-252">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="c4515-253">Отключите наследуемый обработчик модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4515-253">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="c4515-254">Удалите обработчик в опубликованном файле `web.config` приложения Blazor, добавив раздел `<handlers>` в файл:</span><span class="sxs-lookup"><span data-stu-id="c4515-254">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="c4515-255">Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:</span><span class="sxs-lookup"><span data-stu-id="c4515-255">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="c4515-256">Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c4515-256">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="c4515-257">Задайте базовый путь приложения в файле `index.html` приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.</span><span class="sxs-lookup"><span data-stu-id="c4515-257">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="c4515-258">Алгоритмы сжатия Brotli и Gzip</span><span class="sxs-lookup"><span data-stu-id="c4515-258">Brotli and Gzip compression</span></span>

<span data-ttu-id="c4515-259">*Этот раздел относится только к изолированным приложениям Blazor WebAssembly. Размещенные приложения Blazor используют файл `web.config` приложения ASP.NET Core по умолчанию, а не файл, привязанный в рамках этого раздела.*</span><span class="sxs-lookup"><span data-stu-id="c4515-259">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="c4515-260">С помощью файла `web.config` можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритма Brotli или Gzip, для изолированных приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c4515-260">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="c4515-261">Пример файла конфигурации см. по адресу [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="c4515-261">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="c4515-262">В следующих сценариях, возможно, потребуется дополнительно настроить пример файла `web.config`:</span><span class="sxs-lookup"><span data-stu-id="c4515-262">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="c4515-263">Спецификация приложения вызывает одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="c4515-263">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="c4515-264">обслуживание сжатых файлов, которые не настраиваются в примере файла `web.config`;</span><span class="sxs-lookup"><span data-stu-id="c4515-264">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="c4515-265">обслуживание сжатых файлов, настроенных в примере файла `web.config` в несжатом формате.</span><span class="sxs-lookup"><span data-stu-id="c4515-265">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="c4515-266">Конфигурация IIS сервера (например, `applicationHost.config`) предоставляет значения IIS по умолчанию на уровне сервера.</span><span class="sxs-lookup"><span data-stu-id="c4515-266">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="c4515-267">Требуемая для приложения конфигурация IIS может отличаться от представленной в примере файла `web.config`. Это зависит от конфигурации на уровне сервера.</span><span class="sxs-lookup"><span data-stu-id="c4515-267">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="c4515-268">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="c4515-268">Troubleshooting</span></span>

<span data-ttu-id="c4515-269">Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="c4515-269">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="c4515-270">Если модуль не установлен, IIS не может проанализировать файл `web.config`.</span><span class="sxs-lookup"><span data-stu-id="c4515-270">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="c4515-271">Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="c4515-271">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="c4515-272">Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="c4515-272">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="c4515-273">Хранилище Azure</span><span class="sxs-lookup"><span data-stu-id="c4515-273">Azure Storage</span></span>

<span data-ttu-id="c4515-274">Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="c4515-274">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="c4515-275">Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c4515-275">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="c4515-276">Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:</span><span class="sxs-lookup"><span data-stu-id="c4515-276">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="c4515-277">Задайте для параметра **Имя документа индекса** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c4515-277">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="c4515-278">Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c4515-278">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="c4515-279">Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов.</span><span class="sxs-lookup"><span data-stu-id="c4515-279">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="c4515-280">При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.</span><span class="sxs-lookup"><span data-stu-id="c4515-280">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="c4515-281">При этом возвращается большой двоичный объект `index.html`, и маршрутизатор Blazor загружает и обрабатывает путь.</span><span class="sxs-lookup"><span data-stu-id="c4515-281">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="c4515-282">Если файлы не загружаются во время выполнения из-за недопустимых типов MIME в заголовках файлов `Content-Type`, выполните одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="c4515-282">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="c4515-283">Настройте средства для установки правильных типов MIME (заголовков `Content-Type`) при развертывании файлов.</span><span class="sxs-lookup"><span data-stu-id="c4515-283">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="c4515-284">Измените типы MIME (заголовки `Content-Type`) для файлов после развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-284">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="c4515-285">В Обозревателе службы хранилища (портал Azure) для каждого файла сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c4515-285">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="c4515-286">Щелкните правой кнопкой мыши файл и выберите **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="c4515-286">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="c4515-287">Укажите значение для параметра **ContentType** и нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="c4515-287">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="c4515-288">См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="c4515-288">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="c4515-289">Nginx</span><span class="sxs-lookup"><span data-stu-id="c4515-289">Nginx</span></span>

<span data-ttu-id="c4515-290">Следующий файл `nginx.conf` упрощен для демонстрации того, как настроить Nginx для отправки файла `index.html`, когда не удается найти соответствующий файл на диске.</span><span class="sxs-lookup"><span data-stu-id="c4515-290">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="c4515-291">При задании [ограничения пиковой скорости NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) с помощью [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) приложениям Blazor WebAssembly может потребоваться большое значение параметра `burst` с учетом сравнительно большого количества запросов, выполняемых приложением.</span><span class="sxs-lookup"><span data-stu-id="c4515-291">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="c4515-292">Изначально задайте для этого параметра значение не менее 60.</span><span class="sxs-lookup"><span data-stu-id="c4515-292">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="c4515-293">Увеличьте значение, если средства разработчика в браузере или средство контроля сетевого трафика сообщают о том, что запросы возвращают код состояния *503, служба недоступна*.</span><span class="sxs-lookup"><span data-stu-id="c4515-293">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="c4515-294">Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="c4515-294">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="c4515-295">Nginx в Docker</span><span class="sxs-lookup"><span data-stu-id="c4515-295">Nginx in Docker</span></span>

<span data-ttu-id="c4515-296">Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine.</span><span class="sxs-lookup"><span data-stu-id="c4515-296">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="c4515-297">Измените Dockerfile, скопировав файл `nginx.config` в контейнер.</span><span class="sxs-lookup"><span data-stu-id="c4515-297">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="c4515-298">Добавьте одну строку в Dockerfile, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="c4515-298">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="c4515-299">Apache</span><span class="sxs-lookup"><span data-stu-id="c4515-299">Apache</span></span>

<span data-ttu-id="c4515-300">Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="c4515-300">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="c4515-301">Создайте файл конфигурации Apache.</span><span class="sxs-lookup"><span data-stu-id="c4515-301">Create the Apache configuration file.</span></span> <span data-ttu-id="c4515-302">В следующем примере показан упрощенный файл конфигурации (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="c4515-302">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="c4515-303">Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="c4515-303">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="c4515-304">Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).</span><span class="sxs-lookup"><span data-stu-id="c4515-304">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="c4515-305">Перезапустите службу Apache.</span><span class="sxs-lookup"><span data-stu-id="c4515-305">Restart the Apache service.</span></span>

<span data-ttu-id="c4515-306">Дополнительные сведения см. в разделах [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="c4515-306">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="c4515-307">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="c4515-307">GitHub Pages</span></span>

<span data-ttu-id="c4515-308">Чтобы обеспечить переопределение URL-адресов, добавьте файл `wwwroot/404.html` со скриптом, который производит перенаправление запроса на страницу `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c4515-308">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="c4515-309">Пример см. в репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="c4515-309">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="c4515-310">[Пример веб-сайта](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="c4515-310">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="c4515-311">При использовании сайта проекта вместо сайта организации следует обновить тег `<base>` в файле `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="c4515-311">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="c4515-312">Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="c4515-312">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="c4515-313">В репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)база `href` обновляется при публикации [файлом конфигурации `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="c4515-313">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="c4515-314">[Репозиторий GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) не принадлежит, не обслуживается или не поддерживается .NET Foundation или корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c4515-314">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c4515-315">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="c4515-315">Host configuration values</span></span>

<span data-ttu-id="c4515-316">[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="c4515-316">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="c4515-317">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="c4515-317">Content root</span></span>

<span data-ttu-id="c4515-318">Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="c4515-318">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="c4515-319">В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-319">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="c4515-320">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="c4515-320">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c4515-321">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c4515-321">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="c4515-322">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="c4515-322">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c4515-323">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c4515-323">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="c4515-324">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="c4515-324">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c4515-325">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="c4515-325">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="c4515-326">Базовый путь</span><span class="sxs-lookup"><span data-stu-id="c4515-326">Path base</span></span>

<span data-ttu-id="c4515-327">Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред).</span><span class="sxs-lookup"><span data-stu-id="c4515-327">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="c4515-328">В следующих примерах `/relative-URL-path` — это базовый путь приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-328">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="c4515-329">Дополнительные сведения см. в описании [базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c4515-329">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c4515-330">В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="c4515-330">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="c4515-331">Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).</span><span class="sxs-lookup"><span data-stu-id="c4515-331">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="c4515-332">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="c4515-332">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c4515-333">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c4515-333">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="c4515-334">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="c4515-334">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c4515-335">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c4515-335">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="c4515-336">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="c4515-336">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c4515-337">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="c4515-337">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="c4515-338">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="c4515-338">URLs</span></span>

<span data-ttu-id="c4515-339">Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.</span><span class="sxs-lookup"><span data-stu-id="c4515-339">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="c4515-340">Передайте этот аргумент при локальном запуске приложения в командной строке.</span><span class="sxs-lookup"><span data-stu-id="c4515-340">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c4515-341">Перейдите в каталог приложения и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c4515-341">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="c4515-342">Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="c4515-342">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c4515-343">Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c4515-343">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="c4515-344">В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.</span><span class="sxs-lookup"><span data-stu-id="c4515-344">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c4515-345">Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="c4515-345">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="c4515-346">Настройка средства обрезки</span><span class="sxs-lookup"><span data-stu-id="c4515-346">Configure the Trimmer</span></span>

<span data-ttu-id="c4515-347">Blazor выполняет фильтрацию для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="c4515-347">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c4515-348">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="c4515-348">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="c4515-349">Настройка компоновщика</span><span class="sxs-lookup"><span data-stu-id="c4515-349">Configure the Linker</span></span>

<span data-ttu-id="c4515-350">Blazor выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="c4515-350">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c4515-351">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="c4515-351">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="c4515-352">Загрузка пользовательских ресурсов загрузки</span><span class="sxs-lookup"><span data-stu-id="c4515-352">Custom boot resource loading</span></span>

<span data-ttu-id="c4515-353">Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.</span><span class="sxs-lookup"><span data-stu-id="c4515-353">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="c4515-354">Используйте `loadBootResource` в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="c4515-354">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="c4515-355">Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или `dotnet.wasm`, из сети CDN.</span><span class="sxs-lookup"><span data-stu-id="c4515-355">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="c4515-356">Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.</span><span class="sxs-lookup"><span data-stu-id="c4515-356">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="c4515-357">Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.</span><span class="sxs-lookup"><span data-stu-id="c4515-357">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="c4515-358">Параметры `loadBootResource` приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="c4515-358">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="c4515-359">Параметр</span><span class="sxs-lookup"><span data-stu-id="c4515-359">Parameter</span></span>    | <span data-ttu-id="c4515-360">Описание</span><span class="sxs-lookup"><span data-stu-id="c4515-360">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="c4515-361">Тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="c4515-361">The type of the resource.</span></span> <span data-ttu-id="c4515-362">Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="c4515-362">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="c4515-363">Имя ресурса.</span><span class="sxs-lookup"><span data-stu-id="c4515-363">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="c4515-364">Относительный или абсолютный URI ресурса.</span><span class="sxs-lookup"><span data-stu-id="c4515-364">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="c4515-365">Строка целостности, представляющая ожидаемое содержимое в ответе.</span><span class="sxs-lookup"><span data-stu-id="c4515-365">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="c4515-366">`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.</span><span class="sxs-lookup"><span data-stu-id="c4515-366">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="c4515-367">Строка URI.</span><span class="sxs-lookup"><span data-stu-id="c4515-367">URI string.</span></span> <span data-ttu-id="c4515-368">В следующем примере (`wwwroot/index.html`) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="c4515-368">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="c4515-369">Данные часового пояса</span><span class="sxs-lookup"><span data-stu-id="c4515-369">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="c4515-370">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="c4515-370">`Promise<Response>`.</span></span> <span data-ttu-id="c4515-371">Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c4515-371">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="c4515-372">В следующем примере (`wwwroot/index.html`) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`:</span><span class="sxs-lookup"><span data-stu-id="c4515-372">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="c4515-373">`null`/`undefined`, что приводит к поведению загрузки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c4515-373">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="c4515-374">Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.</span><span class="sxs-lookup"><span data-stu-id="c4515-374">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="c4515-375">По умолчанию CDN обычно предоставляют необходимые заголовки.</span><span class="sxs-lookup"><span data-stu-id="c4515-375">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="c4515-376">Необходимо указать только типы для пользовательских поведений.</span><span class="sxs-lookup"><span data-stu-id="c4515-376">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="c4515-377">Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c4515-377">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="c4515-378">Изменение расширения DLL-файлов.</span><span class="sxs-lookup"><span data-stu-id="c4515-378">Change the filename extension of DLL files</span></span>

<span data-ttu-id="c4515-379">Если вам необходимо изменить расширения опубликованных `.dll`-файлов приложения, следуйте указаниям в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="c4515-379">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="c4515-380">После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования `.dll`-файлов для использования другого расширения файла.</span><span class="sxs-lookup"><span data-stu-id="c4515-380">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="c4515-381">Указывайте на `.dll`-файлы в каталоге `wwwroot` опубликованных выходных данных приложения (например, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="c4515-381">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="c4515-382">В следующих примерах `.dll`-файлы переименованы для использования расширения имени файла `.bin`.</span><span class="sxs-lookup"><span data-stu-id="c4515-382">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="c4515-383">Windows:</span><span class="sxs-lookup"><span data-stu-id="c4515-383">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="c4515-384">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c4515-384">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="c4515-385">В Linux или macOS.</span><span class="sxs-lookup"><span data-stu-id="c4515-385">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="c4515-386">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c4515-386">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="c4515-387">Чтобы использовать расширение имени файла, отличное от `.bin`, замените `.bin` в предыдущих командах.</span><span class="sxs-lookup"><span data-stu-id="c4515-387">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="c4515-388">Для сжатых файлов `blazor.boot.json.gz` и `blazor.boot.json.br` используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="c4515-388">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="c4515-389">Удалите сжатые файлы `blazor.boot.json.gz` и `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="c4515-389">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="c4515-390">Сжатие при таком подходе отключается.</span><span class="sxs-lookup"><span data-stu-id="c4515-390">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="c4515-391">Выполните повторное сжатие обновленного файла `blazor.boot.json`.</span><span class="sxs-lookup"><span data-stu-id="c4515-391">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="c4515-392">Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли.</span><span class="sxs-lookup"><span data-stu-id="c4515-392">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="c4515-393">Выполните удаление или повторное сжатие файлов `wwwroot/service-worker-assets.js.br` и `wwwroot/service-worker-assets.js.gz`.</span><span class="sxs-lookup"><span data-stu-id="c4515-393">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="c4515-394">иначе проверка целостности файлов в браузере завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="c4515-394">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="c4515-395">В следующем примере Windows используется скрипт PowerShell, размещенный в корне проекта.</span><span class="sxs-lookup"><span data-stu-id="c4515-395">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="c4515-396">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="c4515-396">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="c4515-397">Если ресурсы рабочей роли также используются, добавьте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c4515-397">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="c4515-398">В файле проекта скрипт запускается после публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="c4515-398">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="c4515-399">В случае переименования и отложенной загрузки тех же сборок см. инструкции в разделе <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span><span class="sxs-lookup"><span data-stu-id="c4515-399">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="c4515-400">Устранение проблем с проверкой целостности</span><span class="sxs-lookup"><span data-stu-id="c4515-400">Resolve integrity check failures</span></span>

<span data-ttu-id="c4515-401">Когда Blazor WebAssembly скачивает файлы запуска для приложения, браузеру получает инструкции выполнить проверку целостности для ответов.</span><span class="sxs-lookup"><span data-stu-id="c4515-401">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="c4515-402">На основе информации из файла `blazor.boot.json` предоставляются ожидаемые значения хэша SHA-256 для `.dll`, `.wasm` и других файлов.</span><span class="sxs-lookup"><span data-stu-id="c4515-402">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="c4515-403">Это полезно сразу по нескольким причинам:</span><span class="sxs-lookup"><span data-stu-id="c4515-403">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="c4515-404">Вы не рискуете скачать несогласованный набор файлов, например, если к веб-серверу применяется новое развертывание во время скачивания файлов приложения пользователем.</span><span class="sxs-lookup"><span data-stu-id="c4515-404">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="c4515-405">Несогласованные файлы могут привести к проблемам с неопределенным поведением.</span><span class="sxs-lookup"><span data-stu-id="c4515-405">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="c4515-406">Вы не рискуете поместить в кэш браузера несогласованные или недопустимые ответы, которые помешают успешно запустить приложение даже после обновления страницы вручную.</span><span class="sxs-lookup"><span data-stu-id="c4515-406">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="c4515-407">Ответы можно хранить в кэше, даже не проверяя изменения на стороне сервера, пока не изменятся ожидаемые хэши SHA-256. При этом последующие загрузки страниц будут выполняться гораздо быстрее и с меньшим числом запросов.</span><span class="sxs-lookup"><span data-stu-id="c4515-407">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="c4515-408">Если веб-сервер возвращает ответы, которые не соответствуют ожидаемым хэшам SHA-256, в консоли разработчика в браузере отобразятся такие сообщения об ошибке:</span><span class="sxs-lookup"><span data-stu-id="c4515-408">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="c4515-409">В большинстве случаев это *не* указывает на проблемы с проверкой целостности.</span><span class="sxs-lookup"><span data-stu-id="c4515-409">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="c4515-410">Скорее всего, существует другая проблема, а проверка целостности просто указывает на это.</span><span class="sxs-lookup"><span data-stu-id="c4515-410">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="c4515-411">Диагностика проблем целостности</span><span class="sxs-lookup"><span data-stu-id="c4515-411">Diagnosing integrity problems</span></span>

<span data-ttu-id="c4515-412">При сборке приложения создается манифест `blazor.boot.json`, который описывает хэши SHA-256 для ресурсов загрузки (`.dll`, `.wasm` и другие файлы) на момент создания выходных данных сборки.</span><span class="sxs-lookup"><span data-stu-id="c4515-412">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="c4515-413">Проверка целостности проходит успешно до тех пор, пока хэши SHA-256 в `blazor.boot.json` соответствуют переданным в браузер файлам.</span><span class="sxs-lookup"><span data-stu-id="c4515-413">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="c4515-414">Ниже приводятся типичные причины сбоев.</span><span class="sxs-lookup"><span data-stu-id="c4515-414">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="c4515-415">Веб-сервер возвращает не запрошенный браузером файл, а информацию об ошибке (например, *404 - Not Found* (не найдено) или *500 - Internal Server Error* (внутренняя ошибка сервера)).</span><span class="sxs-lookup"><span data-stu-id="c4515-415">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="c4515-416">В браузере этот ответ проявляется как ошибка проверки целостности, но не как ошибка ответа.</span><span class="sxs-lookup"><span data-stu-id="c4515-416">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="c4515-417">Содержимое файлов по какой-либо причине изменилось в период между их сборкой и доставкой в браузер.</span><span class="sxs-lookup"><span data-stu-id="c4515-417">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="c4515-418">Это может произойти:</span><span class="sxs-lookup"><span data-stu-id="c4515-418">This might happen:</span></span>
   * <span data-ttu-id="c4515-419">Если вы вручную или с помощью средств сборки изменили выходные данные сборки.</span><span class="sxs-lookup"><span data-stu-id="c4515-419">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="c4515-420">Если какие-то аспекты процесса развертывания изменили файлы.</span><span class="sxs-lookup"><span data-stu-id="c4515-420">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="c4515-421">Например, при использовании механизма развертывания на основе Git важно помнить, что Git прозрачно преобразует концы строк из стиля Windows в стиль UNIX, если вы фиксируете файлы из Windows, а затем получаете их в Linux.</span><span class="sxs-lookup"><span data-stu-id="c4515-421">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="c4515-422">Изменение концов строк в файле приводит к изменению хэша SHA-256.</span><span class="sxs-lookup"><span data-stu-id="c4515-422">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="c4515-423">Чтобы избежать этой проблемы, можно [применить `.gitattributes`, чтобы артефакты сборки воспринимались как файлы `binary`](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes);</span><span class="sxs-lookup"><span data-stu-id="c4515-423">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="c4515-424">Если веб-сервер изменяет содержимое файла при его обработке.</span><span class="sxs-lookup"><span data-stu-id="c4515-424">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="c4515-425">Например, некоторые сети доставки содержимого (CDN) автоматически пытаются [уменьшить размер](xref:client-side/bundling-and-minification#minification) HTML-файлов, неизбежно изменяя их.</span><span class="sxs-lookup"><span data-stu-id="c4515-425">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="c4515-426">Возможно, вам придется отключить эти функции.</span><span class="sxs-lookup"><span data-stu-id="c4515-426">You may need to disable such features.</span></span>

<span data-ttu-id="c4515-427">Чтобы определить причину для конкретной ситуации, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="c4515-427">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="c4515-428">Узнайте, какой файл вызывает ошибку, прочитав сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="c4515-428">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="c4515-429">Откройте средства разработчика в браузере и перейдите на вкладку *Network* (Сеть). Если потребуется, перезагрузите страницу и получите список запросов и ответов.</span><span class="sxs-lookup"><span data-stu-id="c4515-429">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="c4515-430">Найдите в этом списке файл, который вызвал ошибку.</span><span class="sxs-lookup"><span data-stu-id="c4515-430">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="c4515-431">Проверьте код состояния в ответе HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4515-431">Check the HTTP status code in the response.</span></span> <span data-ttu-id="c4515-432">Если сервер возвращает любой ответ, кроме *200 — OK* или другого кода из диапазона 2xx, значит проблему нужно диагностировать на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="c4515-432">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="c4515-433">Например, код состояния 403 означает проблему с авторизацией, а код состояния 500 — неопределенную ошибку в работе сервера.</span><span class="sxs-lookup"><span data-stu-id="c4515-433">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="c4515-434">Просмотрите журналы на стороне сервера, чтобы диагностировать и исправить приложение.</span><span class="sxs-lookup"><span data-stu-id="c4515-434">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="c4515-435">Если для ресурса возвращается код состояния *200 — ОK*, проверьте содержимое ответа с помощью средств разработчика в браузере и убедитесь, что это содержимое соответствует ожиданиям.</span><span class="sxs-lookup"><span data-stu-id="c4515-435">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="c4515-436">Например, довольно часто ошибка в настройке маршрутизации приводит к тому, что запросы возвращают страницу `index.html` даже при обращении к другим файлам.</span><span class="sxs-lookup"><span data-stu-id="c4515-436">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="c4515-437">Убедитесь, что в ответ на запросы `.wasm` возвращаются двоичные файлы WebAssembly, а в ответ на запросы `.dll` — двоичные файлы сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="c4515-437">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="c4515-438">В противном случае проблему нужно диагностировать на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="c4515-438">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="c4515-439">Если вы убедились, что сервер возвращает данные в предположительно правильном формате, значит что-то еще изменяет содержимое файлов в период между сборкой и доставкой.</span><span class="sxs-lookup"><span data-stu-id="c4515-439">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="c4515-440">Для анализа этой ситуации сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="c4515-440">To investigate this:</span></span>

 * <span data-ttu-id="c4515-441">Изучите цепочку инструментов сборки и механизм развертывания, чтобы исключить операции изменения файлов после сборки.</span><span class="sxs-lookup"><span data-stu-id="c4515-441">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="c4515-442">Выше описан пример таких изменений, связанный с преобразованием концов строк в файлах при работе с Git.</span><span class="sxs-lookup"><span data-stu-id="c4515-442">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="c4515-443">Изучите конфигурацию веб-сервера или сети доставки содержимого, чтобы исключить динамическое изменение ответов в них (например, для уменьшения размера HTML-файлов).</span><span class="sxs-lookup"><span data-stu-id="c4515-443">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="c4515-444">При этом сжатие HTTP (например, в формате `content-encoding: br` или `content-encoding: gzip`) на стороне веб-сервера считается допустимым, так как не влияет на результат после распаковки.</span><span class="sxs-lookup"><span data-stu-id="c4515-444">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="c4515-445">Но совершенно *недопустимы* изменения несжатых данных на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="c4515-445">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="c4515-446">Отключение проверки целостности для приложений, не относящихся к PWA</span><span class="sxs-lookup"><span data-stu-id="c4515-446">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="c4515-447">В большинстве случаев проверку целостности отключать не нужно.</span><span class="sxs-lookup"><span data-stu-id="c4515-447">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="c4515-448">Отключение проверки целостности не решает основную проблему, которая вызывает непредвиденные ответы, и приводит к потере описанных выше преимуществ.</span><span class="sxs-lookup"><span data-stu-id="c4515-448">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="c4515-449">Но возможны случаи, когда невозможно гарантировать стабильность ответов от веб-сервера, и тогда вам придется отключить проверку целостности.</span><span class="sxs-lookup"><span data-stu-id="c4515-449">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="c4515-450">Для этого добавьте следующий фрагмент в группу свойств в файле `.csproj` проекта Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="c4515-450">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="c4515-451">`BlazorCacheBootResources` также отключает Blazorповедение кэширования по умолчанию для `.dll`, `.wasm` и других файлов на основе хэшей SHA-256, так как это свойство запрещает доверять правильности хэшей SHA-256.</span><span class="sxs-lookup"><span data-stu-id="c4515-451">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="c4515-452">Даже при включенном параметре стандартный кэш HTTP браузера может кэшировать эти файлы, но этим поведением можно управлять через конфигурацию веб-сервера и заголовков `cache-control`, которые он возвращает.</span><span class="sxs-lookup"><span data-stu-id="c4515-452">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="c4515-453">Свойство `BlazorCacheBootResources` не отключает проверку целостности для [прогрессивных веб-приложений (PWA)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="c4515-453">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="c4515-454">Сведения об особенностях работы с PWA см. в разделе [Отключение проверки целостности для PWA](#disable-integrity-checking-for-pwas).</span><span class="sxs-lookup"><span data-stu-id="c4515-454">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="c4515-455">Отключение проверки целостности для PWA</span><span class="sxs-lookup"><span data-stu-id="c4515-455">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="c4515-456">Шаблон Blazor для прогрессивного веб-приложения (PWA) содержит рекомендуемый файл `service-worker.published.js`, который отвечает за получение и хранение файлов приложений для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="c4515-456">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="c4515-457">Этот процесс отличается от обычного механизма запуска приложения и использует собственную логику проверки целостности.</span><span class="sxs-lookup"><span data-stu-id="c4515-457">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="c4515-458">В файле `service-worker.published.js` есть следующая строка:</span><span class="sxs-lookup"><span data-stu-id="c4515-458">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="c4515-459">Чтобы отключить проверку целостности, удалите параметр `integrity` из этой строки, заменив ее следующим текстом:</span><span class="sxs-lookup"><span data-stu-id="c4515-459">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="c4515-460">И в этом случае отключение проверки целостности будет означать потерю гарантий безопасности, которые предоставляет этот механизм.</span><span class="sxs-lookup"><span data-stu-id="c4515-460">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="c4515-461">Например, появится риск кэширования приложения в браузере пользователя в тот момент, когда развертывается новая версия. При этом могут кэшироваться файлы одновременно из старого и нового развертываний.</span><span class="sxs-lookup"><span data-stu-id="c4515-461">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="c4515-462">В такой ситуации приложение окажется неработоспособным до развертывания следующего обновления.</span><span class="sxs-lookup"><span data-stu-id="c4515-462">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
