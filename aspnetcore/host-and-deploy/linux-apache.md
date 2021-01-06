---
title: Размещение ASP.NET Core в операционной системе Linux с Apache
author: rick-anderson
description: Процедура настройки Apache в качестве обратного прокси-сервера в CentOS для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0bae3f888a1b7a3c2860b85754779189c636d86f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057704"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="e1884-103">Размещение ASP.NET Core в операционной системе Linux с Apache</span><span class="sxs-lookup"><span data-stu-id="e1884-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="e1884-104">Автор: [Шейн Бойер (Shayne Boyer)](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="e1884-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="e1884-105">Из этого руководства вы узнаете, как настроить [Apache](https://httpd.apache.org/) в качестве обратного прокси-сервера в [CentOS 7](https://www.centos.org/) для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое на сервере [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="e1884-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="e1884-106">[Расширение mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) и связанные с ним модули создают обратный прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="e1884-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e1884-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="e1884-107">Prerequisites</span></span>

* <span data-ttu-id="e1884-108">Сервер под управлением CentOS 7 и учетная запись обычного пользователя с правами sudo.</span><span class="sxs-lookup"><span data-stu-id="e1884-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="e1884-109">Установите среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="e1884-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="e1884-110">Перейдите на [страницу скачивания .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="e1884-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="e1884-111">Выберите последнюю не предварительную версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1884-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="e1884-112">Скачайте последнюю не предварительную версию среды выполнения из таблицы под заголовком **Run apps — Runtime** (Выполнение приложений — среда выполнения).</span><span class="sxs-lookup"><span data-stu-id="e1884-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="e1884-113">Щелкните ссылку **Package manager instructions** (Инструкции диспетчера пакетов) для Linux и выполните инструкции для CentOS.</span><span class="sxs-lookup"><span data-stu-id="e1884-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="e1884-114">Существующее приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1884-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="e1884-115">Перезапустить приложения ASP.NET Core, размещенные на сервере, можно в любой момент после обновления общей платформы.</span><span class="sxs-lookup"><span data-stu-id="e1884-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="e1884-116">Публикация и копирование приложения</span><span class="sxs-lookup"><span data-stu-id="e1884-116">Publish and copy over the app</span></span>

<span data-ttu-id="e1884-117">Настройте приложение, чтобы [его развертывание зависело от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="e1884-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="e1884-118">Если приложение запускается локально и не настроено для безопасного подключения (HTTPS), следует применять один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="e1884-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="e1884-119">Настройка приложения для обработки безопасных локальных подключений.</span><span class="sxs-lookup"><span data-stu-id="e1884-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="e1884-120">Дополнительные сведения см. в разделе [Конфигурация HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="e1884-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="e1884-121">Удалите `https://localhost:5001` (при его наличии) из свойства `applicationUrl` в файле *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e1884-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="e1884-122">Запустите [dotnet publish](/dotnet/core/tools/dotnet-publish) в среде разработки, чтобы упаковать приложение в каталог (например, *bin/Release/&lt;target_framework_moniker&gt;/publish*), который может выполняться на сервере:</span><span class="sxs-lookup"><span data-stu-id="e1884-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="e1884-123">Приложение может быть опубликовано как [автономное развертывание](/dotnet/core/deploying/#self-contained-deployments-scd), если вы предпочитаете не сохранять среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="e1884-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="e1884-124">Скопируйте приложение ASP.NET Core на сервер с помощью инструмента, интегрированного в ваш рабочий процесс (например, SCP или SFTP).</span><span class="sxs-lookup"><span data-stu-id="e1884-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="e1884-125">Обычно веб-приложения находятся в каталоге *var* (например, *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="e1884-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="e1884-126">Если развертывание выполняется в рабочей среде, рабочий процесс непрерывной интеграции автоматически опубликует приложение и скопирует его ресурсы на сервер.</span><span class="sxs-lookup"><span data-stu-id="e1884-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="e1884-127">Настройка прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="e1884-127">Configure a proxy server</span></span>

<span data-ttu-id="e1884-128">Обратный прокси-сервер — это стандартный вариант настройки для обслуживания динамических веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="e1884-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="e1884-129">Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e1884-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="e1884-130">Прокси-сервер перенаправляет запросы клиента на другой сервер, а не обрабатывает их самостоятельно.</span><span class="sxs-lookup"><span data-stu-id="e1884-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="e1884-131">Обратный прокси-сервер перенаправляет запросы в фиксированное назначение обычно от имени клиентов.</span><span class="sxs-lookup"><span data-stu-id="e1884-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="e1884-132">При работе с этим руководством мы настроим Apache в качестве обратного прокси-сервера, который работает на том же сервере, где Kestrel предоставляет приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1884-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="e1884-133">Так как запросы перенаправляются обратным прокси-сервером, используйте [ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer), которое входит в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/).</span><span class="sxs-lookup"><span data-stu-id="e1884-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="e1884-134">Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.</span><span class="sxs-lookup"><span data-stu-id="e1884-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="e1884-135">Любой компонент, который зависит от схемы, например проверка подлинности, генерация ссылок, перенаправление и геолокация, должен находиться после вызова ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="e1884-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="e1884-136">Вызовите <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> метод наверху `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="e1884-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="e1884-137">В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="e1884-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="e1884-138">Если параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не задан для ПО промежуточного слоя, по умолчанию перенаправляются заголовки `None`.</span><span class="sxs-lookup"><span data-stu-id="e1884-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="e1884-139">Прокси-серверы под управлением адресов замыкания на себя (127.0.0.0/8, [:: 1]), включая стандартные адреса localhost (127.0.0.1), считаются доверенными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e1884-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="e1884-140">Если запросы между Интернетом и веб-сервером обрабатывают другие прокси-серверы или сети организации, добавьте их в список <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> или <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> с помощью <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="e1884-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="e1884-141">Следующий пример добавляет доверенный прокси-сервер с IP-адресом 10.0.0.100 в ПО промежуточного слоя для перенаправления заголовков `KnownProxies` в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e1884-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="e1884-142">Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="e1884-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="e1884-143">Установка Apache</span><span class="sxs-lookup"><span data-stu-id="e1884-143">Install Apache</span></span>

<span data-ttu-id="e1884-144">Обновите пакеты CentOS до последних стабильных версий:</span><span class="sxs-lookup"><span data-stu-id="e1884-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="e1884-145">Установите веб-сервер Apache на CentOS, выполнив одну команду `yum`:</span><span class="sxs-lookup"><span data-stu-id="e1884-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="e1884-146">Пример выходных данных этой команды:</span><span class="sxs-lookup"><span data-stu-id="e1884-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="e1884-147">В нашем примере выходные данные содержат строку httpd.86_64, так как используется 64-разрядная версия CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="e1884-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="e1884-148">Чтобы проверить, где установлен Apache, выполните `whereis httpd` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="e1884-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="e1884-149">Настройка Apache</span><span class="sxs-lookup"><span data-stu-id="e1884-149">Configure Apache</span></span>

<span data-ttu-id="e1884-150">Файлы конфигурации для Apache находятся в каталоге `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="e1884-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="e1884-151">В алфавитном порядке обрабатываются все файлы с расширением *.conf*, а также файлы конфигурации модуля из папки `/etc/httpd/conf.modules.d/`, где содержатся файлы конфигурации, необходимые для загрузки модулей.</span><span class="sxs-lookup"><span data-stu-id="e1884-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="e1884-152">Создайте для приложения файл конфигурации с именем *helloapp.conf*:</span><span class="sxs-lookup"><span data-stu-id="e1884-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="e1884-153">Блок `VirtualHost` может встречаться несколько раз в одном или нескольких файлах на сервере.</span><span class="sxs-lookup"><span data-stu-id="e1884-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="e1884-154">С представленным выше файлом конфигурации Apache принимает трафик от любого источника через порт 80.</span><span class="sxs-lookup"><span data-stu-id="e1884-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="e1884-155">Он обслуживает домен `www.example.com`, а псевдоним `*.example.com` указывает на тот же веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="e1884-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="e1884-156">Дополнительные сведения см. в статье [о поддержке виртуальных узлов на основе имен](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span><span class="sxs-lookup"><span data-stu-id="e1884-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="e1884-157">Запросы к корневому каталогу перенаправляются на порт 5000 того же сервера по адресу 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="e1884-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="e1884-158">Для двусторонней связи требуются `ProxyPass` и `ProxyPassReverse`.</span><span class="sxs-lookup"><span data-stu-id="e1884-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="e1884-159">Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="e1884-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="e1884-160">Если не удастся указать правильную [директиву ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) в блоке **VirtualHost**, приложение будет иметь значительные уязвимости.</span><span class="sxs-lookup"><span data-stu-id="e1884-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="e1884-161">Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.example.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="e1884-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e1884-162">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="e1884-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="e1884-163">Ведение журнала можно настроить отдельно для каждого `VirtualHost` с помощью директив `ErrorLog` и `CustomLog`.</span><span class="sxs-lookup"><span data-stu-id="e1884-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="e1884-164">Журналы ошибок сохраняются в расположение `ErrorLog`, а параметр `CustomLog` задает имя и формат для файла журнала.</span><span class="sxs-lookup"><span data-stu-id="e1884-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="e1884-165">В нашем примере здесь фиксируются сведения о запросах.</span><span class="sxs-lookup"><span data-stu-id="e1884-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="e1884-166">Для каждого запроса создается одна строка.</span><span class="sxs-lookup"><span data-stu-id="e1884-166">There's one line for each request.</span></span>

<span data-ttu-id="e1884-167">Сохраните файл и протестируйте конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="e1884-167">Save the file and test the configuration.</span></span> <span data-ttu-id="e1884-168">Если проверка выполнена успешно, ответ должен быть `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="e1884-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="e1884-169">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="e1884-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="e1884-170">Мониторинг приложения</span><span class="sxs-lookup"><span data-stu-id="e1884-170">Monitor the app</span></span>

<span data-ttu-id="e1884-171">Теперь Apache настроен на перенаправление запросов к `http://localhost:80` в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="e1884-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="e1884-172">Но Apache не настроен для управления процессом Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e1884-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="e1884-173">Для запуска и мониторинга базового веб-приложения используйте *systemd* и создайте файл службы.</span><span class="sxs-lookup"><span data-stu-id="e1884-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="e1884-174">*systemd* — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими.</span><span class="sxs-lookup"><span data-stu-id="e1884-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="e1884-175">Создание файла службы</span><span class="sxs-lookup"><span data-stu-id="e1884-175">Create the service file</span></span>

<span data-ttu-id="e1884-176">Создайте файл определения службы.</span><span class="sxs-lookup"><span data-stu-id="e1884-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="e1884-177">Пример файла службы для приложения:</span><span class="sxs-lookup"><span data-stu-id="e1884-177">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="e1884-178">В предыдущем примере управляющий службой пользователь задается с помощью параметра `User`.</span><span class="sxs-lookup"><span data-stu-id="e1884-178">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="e1884-179">Этот пользователь (`apache`) должен существовать и иметь права владельца в отношении файлов приложения.</span><span class="sxs-lookup"><span data-stu-id="e1884-179">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="e1884-180">Используйте `TimeoutStopSec`, чтобы настроить время ожидания до завершения работы приложения после начального сигнала прерывания.</span><span class="sxs-lookup"><span data-stu-id="e1884-180">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="e1884-181">Если приложение не завершит работу в течение этого периода, оно прерывается сигналом SIGKILL.</span><span class="sxs-lookup"><span data-stu-id="e1884-181">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="e1884-182">Укажите значение в секундах без единиц измерения (например, `150`), значение интервала (например, `2min 30s`) или значение `infinity`, которое отключает время ожидания.</span><span class="sxs-lookup"><span data-stu-id="e1884-182">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="e1884-183">По умолчанию `TimeoutStopSec` принимает значение `DefaultTimeoutStopSec` в файле конфигурации диспетчера (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="e1884-183">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="e1884-184">В большинстве дистрибутивов по умолчанию устанавливается время ожидания 90 секунд.</span><span class="sxs-lookup"><span data-stu-id="e1884-184">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="e1884-185">Некоторые значения (например, строки подключения SQL) необходимо экранировать, чтобы поставщики конфигурации могли считать переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e1884-185">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="e1884-186">Используйте следующую команду, чтобы создать правильно экранированное значение для файла конфигурации:</span><span class="sxs-lookup"><span data-stu-id="e1884-186">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1884-187">Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e1884-187">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="e1884-188">Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия.</span><span class="sxs-lookup"><span data-stu-id="e1884-188">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="e1884-189">[Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e1884-189">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="e1884-190">В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="e1884-190">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1884-191">Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e1884-191">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="e1884-192">Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия.</span><span class="sxs-lookup"><span data-stu-id="e1884-192">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="e1884-193">[Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e1884-193">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="e1884-194">В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="e1884-194">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="e1884-195">Сохраните файл и включите службу.</span><span class="sxs-lookup"><span data-stu-id="e1884-195">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="e1884-196">Запустите службу и убедитесь, что она работает.</span><span class="sxs-lookup"><span data-stu-id="e1884-196">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="e1884-197">Теперь, когда обратный прокси-сервер настроен и *systemd* управляет процессом Kestrel, веб-приложение можно считать полностью настроенным и вы можете обратиться к нему по адресу `http://localhost` из браузера на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="e1884-197">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="e1884-198">Заголовок **Server** в ответе подтверждает, что приложение ASP.NET Core обслуживается Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e1884-198">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="e1884-199">Просмотр журналов</span><span class="sxs-lookup"><span data-stu-id="e1884-199">View logs</span></span>

<span data-ttu-id="e1884-200">Так как веб-приложением, использующим Kestrel, управляет *systemd*, все события и процессы регистрируются в централизованном журнале.</span><span class="sxs-lookup"><span data-stu-id="e1884-200">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="e1884-201">Но этот журнал содержит все записи обо всех службах и процессах, управляемых *systemd*.</span><span class="sxs-lookup"><span data-stu-id="e1884-201">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="e1884-202">Чтобы просмотреть элементы, связанные с `kestrel-helloapp.service`, используйте следующую команду.</span><span class="sxs-lookup"><span data-stu-id="e1884-202">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="e1884-203">Чтобы отфильтровать элементы по времени, укажите в команде параметры времени.</span><span class="sxs-lookup"><span data-stu-id="e1884-203">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="e1884-204">Например, `--since today` позволяет отфильтровать данные за текущий день, а `--until 1 hour ago` — просмотреть записи за предыдущий час.</span><span class="sxs-lookup"><span data-stu-id="e1884-204">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="e1884-205">Дополнительные сведения см. на странице руководства о команде [journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="e1884-205">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="e1884-206">Защита данных</span><span class="sxs-lookup"><span data-stu-id="e1884-206">Data protection</span></span>

<span data-ttu-id="e1884-207">[Стек защиты данных в ASP.NET Core](xref:security/data-protection/introduction) используется определенным [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая промежуточное ПО для проверки подлинности (например, промежуточное ПО файлов cookie) и средствами защиты от подделки межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="e1884-207">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="e1884-208">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, необходимо настроить защиту данных для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="e1884-208">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e1884-209">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="e1884-209">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e1884-210">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="e1884-210">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e1884-211">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="e1884-211">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="e1884-212">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="e1884-212">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="e1884-213">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="e1884-213">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e1884-214">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="e1884-214">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e1884-215">Сведения о настройке защиты данных для хранения и шифрования набора ключей см. в приведенных ниже статьях.</span><span class="sxs-lookup"><span data-stu-id="e1884-215">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="e1884-216">Защита приложения</span><span class="sxs-lookup"><span data-stu-id="e1884-216">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="e1884-217">Настройка брандмауэра</span><span class="sxs-lookup"><span data-stu-id="e1884-217">Configure firewall</span></span>

<span data-ttu-id="e1884-218">*Firewalld* — это динамическая управляющая программа брандмауэра, которая поддерживает зоны сети.</span><span class="sxs-lookup"><span data-stu-id="e1884-218">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="e1884-219">Фильтрацию портов и пакетов можно по-прежнему осуществлять через iptables.</span><span class="sxs-lookup"><span data-stu-id="e1884-219">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="e1884-220">*Firewalld* обычно устанавливается в системе по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e1884-220">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="e1884-221">`yum` позволяет установить этот пакет или убедиться, что он установлен.</span><span class="sxs-lookup"><span data-stu-id="e1884-221">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="e1884-222">С помощью `firewalld` вы можете открыть только те порты, которые необходимые для работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e1884-222">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="e1884-223">В этом случае используются порты 80 и 443.</span><span class="sxs-lookup"><span data-stu-id="e1884-223">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="e1884-224">Следующие команды назначают порты 80 и 443 постоянно открытыми.</span><span class="sxs-lookup"><span data-stu-id="e1884-224">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="e1884-225">Обновите параметры брандмауэра.</span><span class="sxs-lookup"><span data-stu-id="e1884-225">Reload the firewall settings.</span></span> <span data-ttu-id="e1884-226">Проверьте, что доступные службы и порты находятся в зоне по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e1884-226">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="e1884-227">Эти параметры можно просмотреть с помощью `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="e1884-227">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="e1884-228">Конфигурация HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1884-228">HTTPS configuration</span></span>

<span data-ttu-id="e1884-229">**Настройка приложения для безопасных (HTTPS) локальных подключений**</span><span class="sxs-lookup"><span data-stu-id="e1884-229">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="e1884-230">Команда [dotnet run](/dotnet/core/tools/dotnet-run) использует файл приложения *Properties/launchSettings.json*, который настраивает приложение для прослушивания URL-адресов, заданных свойством `applicationUrl` (например, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="e1884-230">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="e1884-231">Настройте приложение для использования при разработке сертификата для команды `dotnet run` или среды разработки (F5 или CTRL + F5 в Visual Studio Code), используя один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="e1884-231">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="e1884-232">[Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel#configuration) (*рекомендуется*)</span><span class="sxs-lookup"><span data-stu-id="e1884-232">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="e1884-233">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="e1884-233">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="e1884-234">**Настройка обратного прокси-сервера для безопасного подключения клиентов (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="e1884-234">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="e1884-235">Apache для HTTPS настраивается с помощью модуля *mod_ssl*.</span><span class="sxs-lookup"><span data-stu-id="e1884-235">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="e1884-236">При установке модуля *httpd* автоматически добавляется и модуль *mod_ssl*.</span><span class="sxs-lookup"><span data-stu-id="e1884-236">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="e1884-237">Если он по каким-то причинам отсутствует, добавьте его в систему с помощью `yum`.</span><span class="sxs-lookup"><span data-stu-id="e1884-237">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="e1884-238">Чтобы принудительно использовать HTTPS, установите модуль `mod_rewrite` для перезаписи URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="e1884-238">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="e1884-239">Измените файл *helloapp.conf*, чтобы разрешить перезапись URL-адресов и безопасный обмен данными через порт 443:</span><span class="sxs-lookup"><span data-stu-id="e1884-239">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="e1884-240">В этом примере используется локально созданный сертификат.</span><span class="sxs-lookup"><span data-stu-id="e1884-240">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="e1884-241">В **SSLCertificateFile** должен быть указан основной файл сертификата для доменного имени.</span><span class="sxs-lookup"><span data-stu-id="e1884-241">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="e1884-242">В **SSLCertificateKeyFile** должен быть указан файл ключа, сформированный при создании CSR.</span><span class="sxs-lookup"><span data-stu-id="e1884-242">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="e1884-243">В **SSLCertificateChainFile** должен быть указан файл промежуточного сертификата (если он существует), предоставленный центром сертификации.</span><span class="sxs-lookup"><span data-stu-id="e1884-243">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="e1884-244">Сохраните файл и протестируйте конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="e1884-244">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="e1884-245">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="e1884-245">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="e1884-246">Дополнительные предложения Apache</span><span class="sxs-lookup"><span data-stu-id="e1884-246">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="e1884-247">Перезапуск приложений после обновления общей платформы</span><span class="sxs-lookup"><span data-stu-id="e1884-247">Restart apps with shared framework updates</span></span>

<span data-ttu-id="e1884-248">После обновления общей платформы на сервере перезапустите размещенные на нем приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1884-248">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="e1884-249">Дополнительные заголовки</span><span class="sxs-lookup"><span data-stu-id="e1884-249">Additional headers</span></span>

<span data-ttu-id="e1884-250">Для защиты от вредоносных атак следует изменить или добавить несколько заголовков.</span><span class="sxs-lookup"><span data-stu-id="e1884-250">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="e1884-251">Убедитесь, что модуль `mod_headers` установлен.</span><span class="sxs-lookup"><span data-stu-id="e1884-251">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="e1884-252">Защита Apache от атак кликджекинга</span><span class="sxs-lookup"><span data-stu-id="e1884-252">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="e1884-253">[Кликджекинг](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger) (или *атака с подменой пользовательского интерфейса*) является вредоносной атакой, при которой посетителя сайта обманным путем вынуждают щелкнуть ссылку или нажать кнопку не той страницы, на которой он находится.</span><span class="sxs-lookup"><span data-stu-id="e1884-253">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="e1884-254">Используйте `X-FRAME-OPTIONS` для защиты сайта.</span><span class="sxs-lookup"><span data-stu-id="e1884-254">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="e1884-255">Чтобы уменьшить риск атак кликджекинга, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="e1884-255">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="e1884-256">Измените файл *httpd.conf*.</span><span class="sxs-lookup"><span data-stu-id="e1884-256">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="e1884-257">Добавьте строку `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="e1884-257">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="e1884-258">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="e1884-258">Save the file.</span></span>
1. <span data-ttu-id="e1884-259">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="e1884-259">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="e1884-260">Сканирование типа MIME</span><span class="sxs-lookup"><span data-stu-id="e1884-260">MIME-type sniffing</span></span>

<span data-ttu-id="e1884-261">Заголовок `X-Content-Type-Options` защищает Internet Explorer от *сканирования MIME* (определения `Content-Type` для файла по его содержимому).</span><span class="sxs-lookup"><span data-stu-id="e1884-261">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="e1884-262">Если сервер задает заголовок `Content-Type` со значением `text/html`, и при этом установлен параметр `nosniff`, Internet Explorer отображает содержимое как `text/html` независимо от содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="e1884-262">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="e1884-263">Измените файл *httpd.conf*.</span><span class="sxs-lookup"><span data-stu-id="e1884-263">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="e1884-264">Добавьте строку `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="e1884-264">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="e1884-265">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="e1884-265">Save the file.</span></span> <span data-ttu-id="e1884-266">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="e1884-266">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="e1884-267">Балансировка нагрузки</span><span class="sxs-lookup"><span data-stu-id="e1884-267">Load Balancing</span></span>

<span data-ttu-id="e1884-268">В этом примере показано, как установить и настроить Apache в CentOS 7 и Kestrel на том же компьютере.</span><span class="sxs-lookup"><span data-stu-id="e1884-268">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="e1884-269">Чтобы устранить единую точку отказа, воспользуйтесь *mod_proxy_balancer* и измените значение **VirtualHost** для управления несколькими экземплярами веб-приложений за прокси-сервером Apache.</span><span class="sxs-lookup"><span data-stu-id="e1884-269">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="e1884-270">В представленном ниже файле конфигурации настроен дополнительный экземпляр `helloapp`, работающий на порте 5001.</span><span class="sxs-lookup"><span data-stu-id="e1884-270">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="e1884-271">В разделе *Proxy* настраивается конфигурация подсистемы балансировки нагрузки с двумя членами для распределения нагрузки методом *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="e1884-271">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="e1884-272">Ограничения скорости</span><span class="sxs-lookup"><span data-stu-id="e1884-272">Rate Limits</span></span>

<span data-ttu-id="e1884-273">С помощью элемента *mod_ratelimit*, который входит в модуль *httpd*, можно ограничить пропускную способность для клиентов:</span><span class="sxs-lookup"><span data-stu-id="e1884-273">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="e1884-274">В примере файла пропускная способность составляет 600 Кбит/с в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="e1884-274">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="e1884-275">Длинные поля заголовка запроса</span><span class="sxs-lookup"><span data-stu-id="e1884-275">Long request header fields</span></span>

<span data-ttu-id="e1884-276">Параметры прокси-сервера по умолчанию обычно ограничивают длину полей заголовка запроса значением 8190 байт.</span><span class="sxs-lookup"><span data-stu-id="e1884-276">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="e1884-277">Приложению могут потребоваться более длинные поля (например, приложениям, использующим [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="e1884-277">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="e1884-278">В этом случае требуется скорректировать директиву [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) для прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="e1884-278">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="e1884-279">Применяемое значение зависит от конкретного сценария.</span><span class="sxs-lookup"><span data-stu-id="e1884-279">The value to apply depends on the scenario.</span></span> <span data-ttu-id="e1884-280">Дополнительные сведения см. в документации сервера.</span><span class="sxs-lookup"><span data-stu-id="e1884-280">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="e1884-281">Не увеличивайте значение `LimitRequestFieldSize` по умолчанию, если это не требуется.</span><span class="sxs-lookup"><span data-stu-id="e1884-281">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="e1884-282">Увеличение значения повышает риск переполнения буфера и атак типа "отказ в обслуживании" (DoS) со стороны злоумышленников.</span><span class="sxs-lookup"><span data-stu-id="e1884-282">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1884-283">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e1884-283">Additional resources</span></span>

* [<span data-ttu-id="e1884-284">Необходимые компоненты для .NET Core в Linux</span><span class="sxs-lookup"><span data-stu-id="e1884-284">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
