---
title: Конфигурация Blazor ASP.NET Core
author: guardrex
description: Сведения о настройке приложений Blazor, включая параметры приложений, проверку подлинности и конфигурацию ведения журнала.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 1607a80ea12cedd82b5996cf1b09e02e1aada2fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628811"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="1166c-103">Конфигурация Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1166c-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="1166c-104">Эта статья относится к Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1166c-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="1166c-105">Общие рекомендации по настройке приложений ASP.NET Core см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1166c-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="1166c-106">Blazor WebAssembly загружает конфигурацию из файлов параметров приложения по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="1166c-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="1166c-107">Другие поставщики конфигурации, зарегистрированные приложением, также могут предоставлять конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="1166c-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="1166c-108">Не все поставщики или функции поставщиков подходят для приложений Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="1166c-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="1166c-109">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration): поставщик не поддерживается для сценариев управляемого удостоверения и идентификатора приложения (идентификатор клиента) с секретом клиента.</span><span class="sxs-lookup"><span data-stu-id="1166c-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="1166c-110">Идентификатор приложения с секретом клиента не рекомендуется для приложений ASP.NET Core, особенно приложений Blazor WebAssembly, так как секрет клиента невозможно защитить на стороне клиента для доступа к службе.</span><span class="sxs-lookup"><span data-stu-id="1166c-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="1166c-111">[Поставщик конфигурации приложения Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): поставщик не подходит для приложений Blazor WebAssembly, так как приложения Blazor WebAssembly не выполняются на сервере в Azure.</span><span class="sxs-lookup"><span data-stu-id="1166c-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="1166c-112">Конфигурация в приложении Blazor WebAssembly видна пользователям.</span><span class="sxs-lookup"><span data-stu-id="1166c-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="1166c-113">**Не храните учетные данные или секреты приложения в конфигурации.**</span><span class="sxs-lookup"><span data-stu-id="1166c-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="1166c-114">Дополнительные сведения о поставщиках конфигурации см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1166c-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="1166c-115">Конфигурация параметров приложения</span><span class="sxs-lookup"><span data-stu-id="1166c-115">App settings configuration</span></span>

<span data-ttu-id="1166c-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="1166c-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="1166c-117">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="1166c-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="1166c-118">Поставщик пользовательской конфигурации с EF Core</span><span class="sxs-lookup"><span data-stu-id="1166c-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="1166c-119">Поставщик пользовательской конфигурации с EF Core, работа которого продемонстрирована в разделе <xref:fundamentals/configuration/index#custom-configuration-provider>, работает с приложениями Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1166c-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="1166c-120">Добавьте поставщика конфигурации из примера с помощью следующего кода в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="1166c-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="1166c-121">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="1166c-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="1166c-122">Источник конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="1166c-122">Memory Configuration Source</span></span>

<span data-ttu-id="1166c-123">В следующем примере используется <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> для предоставления дополнительной конфигурации.</span><span class="sxs-lookup"><span data-stu-id="1166c-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="1166c-124">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="1166c-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="1166c-125">Внедрите экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> в компонент для доступа к данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="1166c-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="1166c-126">Для чтения других файлов конфигурации из папки `wwwroot` в конфигурацию используйте <xref:System.Net.Http.HttpClient> для получения содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="1166c-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="1166c-127">При использовании этого подхода существующая регистрация службы <xref:System.Net.Http.HttpClient> может использовать локальный клиент, созданный для чтения файла, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="1166c-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="1166c-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="1166c-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="1166c-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1166c-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="1166c-130">Конфигурация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="1166c-130">Authentication configuration</span></span>

<span data-ttu-id="1166c-131">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="1166c-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="1166c-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1166c-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="1166c-133">Конфигурация ведения журнала</span><span class="sxs-lookup"><span data-stu-id="1166c-133">Logging configuration</span></span>

<span data-ttu-id="1166c-134">Добавьте ссылку на пакет для [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span><span class="sxs-lookup"><span data-stu-id="1166c-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="1166c-135">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span><span class="sxs-lookup"><span data-stu-id="1166c-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="1166c-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="1166c-136">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="1166c-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1166c-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="1166c-138">Конфигурация построителя узлов</span><span class="sxs-lookup"><span data-stu-id="1166c-138">Host builder configuration</span></span>

<span data-ttu-id="1166c-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1166c-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="1166c-140">Кэшированная конфигурация</span><span class="sxs-lookup"><span data-stu-id="1166c-140">Cached configuration</span></span>

<span data-ttu-id="1166c-141">Файлы конфигурации кэшируются для автономного использования.</span><span class="sxs-lookup"><span data-stu-id="1166c-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="1166c-142">При использовании [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app) файлы конфигурации можно изменять только при создании развертывания.</span><span class="sxs-lookup"><span data-stu-id="1166c-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="1166c-143">Изменение файлов конфигурации между развертываниями не работает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="1166c-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="1166c-144">У пользователей есть кэшированные версии файлов, которые они продолжают использовать.</span><span class="sxs-lookup"><span data-stu-id="1166c-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="1166c-145">Файлы `service-worker.js` и `service-worker-assets.js` PWA должны быть перестроены при компиляции, что при следующем входе пользователя в сеть сообщает приложению о том, что приложение было развернуто повторно.</span><span class="sxs-lookup"><span data-stu-id="1166c-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="1166c-146">Дополнительные сведения об обработке фоновых изменений с помощью PWA см. на странице <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="1166c-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
