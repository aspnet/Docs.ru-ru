---
title: Размещение и развертывание ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и страниц GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: dadf6076e7f07c07381856aa225667a6eb38046a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080320"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>Размещение и развертывание ASP.NET Core Blazor WebAssembly

Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla).

При использовании [модели размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):

* Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно.
* Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.

Поддерживаются следующие стратегии развертывания:

* Приложение Blazor обслуживается приложением ASP.NET Core. Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).
* Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor. Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.

## <a name="compression"></a>Сжатие

При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения. Используются следующие алгоритмы сжатия:

* [Brotli](https://tools.ietf.org/html/rfc7932) (высший уровень)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Для обслуживания соответствующих сжатых файлов в Blazor используется ведущий проект. При использовании размещенного проекта ASP.NET Core ведущий проект может выполнять согласование содержимого и обслуживать статически сжатые файлы. При размещении автономного приложения Blazor WebAssembly, возможно, потребуется реализовать дополнительные действия для обслуживания статически сжатых файлов.

* Сведения о конфигурации сжатия `web.config` IIS см. в статье [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression). 
* При размещении в статических решениях размещения, которые не поддерживают согласование содержимого статически сжатых файлов, например на страницах GitHub, рассмотрите возможность настройки приложения для извлечения и декодирования сжатых файлов Brotli:

  * Получите декодер JavaScript Brotli из [репозитория GitHub google/brotli](https://github.com/google/brotli). По состоянию на июль 2020 г. файл декодера называется `decode.min.js` и находится в папке [`js` репозитория](https://github.com/google/brotli/tree/master/js).
  * обновите приложение для использования декодера. Измените разметку в закрывающем теге `<body>` в `wwwroot/index.html` следующим образом.
  
    ```html
    <script src="decode.min.js"></script>
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
 
Чтобы отключить сжатие, добавьте свойство `BlazorEnableCompression` MSBuild в файл проекта приложения и задайте для него значение `false`.

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

Свойство `BlazorEnableCompression` можно передать в команду [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с помощью следующего синтаксиса в командной оболочке.

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Переопределение URL-адресов для маршрутизации

Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к размещенному приложению Blazor Server. Рассмотрим сценарий с приложением Blazor WebAssembly с двумя компонентами:

* `Main.razor`. загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).
* `About.razor`: компонент `About`.

При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`):

1. Браузер отправляет запрос.
1. Возвращается страница по умолчанию; обычно это `index.html`.
1. Страница `index.html` обеспечивает начальную загрузку приложения.
1. Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.

На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`. Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете. Маршрутизатор обрабатывает запросы внутренним образом.

Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой. Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).

Так как браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы `index.html`. Когда `index.html` возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом.

При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом `web.config` приложения. Дополнительные сведения см. в описании [IIS](#iis).

## <a name="hosted-deployment-with-aspnet-core"></a>Размещенное развертывание с использованием ASP.NET Core

*Размещенное развертывание* обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.

Клиентское приложение Blazor WebAssembly будет опубликовано в папку `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` серверного приложения вместе со статическими веб-ресурсами серверного приложения. Оба приложения развертываются вместе. Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным. Для размещаемого развертывания Visual Studio включает шаблон проекта **Приложение Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [`dotnet new`](/dotnet/core/tools/dotnet-new)) с выбранным параметром **`Hosted`** (`-ho|--hosted` при использовании команды `dotnet new`).

Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.

Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Размещенное развертывание с несколькими приложениями Blazor WebAssembly

### <a name="app-configuration"></a>Конфигурация приложения

Чтобы настроить размещенное решение Blazor для обслуживания нескольких приложений Blazor WebAssembly:

* Используйте существующее размещенное решение Blazor или создайте новое решение из размещенного шаблона проекта Blazor.

* В файле проекта клиентского приложения добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `FirstApp`, чтобы задать базовый путь для статических ресурсов проекта.

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Добавьте в решение второе клиентское приложение:

  * Добавьте папку с именем `SecondClient` в папку решения.
  * Создайте приложение Blazor WebAssembly с именем `SecondBlazorApp.Client` в папке `SecondClient` из шаблона проекта Blazor WebAssembly.
  * В файле проекта приложения:

    * Добавьте свойство `<StaticWebAssetBasePath>` в `<PropertyGroup>` со значением `SecondApp`:

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Добавьте ссылку на проект в проект `Shared`:

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      Заполнитель `{SOLUTION NAME}` — это имя решения.

* В файле проекта серверного приложения создайте ссылку на проект для добавленного клиентского приложения:

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* В файле серверного приложения `Properties/launchSettings.json` настройте `applicationUrl` профиля Kestrel (`{SOLUTION NAME}.Server`), чтобы получить доступ к клиентским приложениям через порты 5001 и 5002:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* В методе `Startup.Configure` серверного приложения (`Startup.cs`) удалите эти строки, которые появляются после вызова <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:

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

  Добавьте ПО промежуточного слоя, которое сопоставляет запросы с клиентскими приложениями. В следующем примере выполняется настройка ПО промежуточного слоя для запуска в следующих случаях:

  * Порт запроса — 5001 для исходного клиентского приложения или 5002 для добавленного клиентского приложения.
  * Узел запроса — `firstapp.com` для исходного клиентского приложения или `secondapp.com` для добавленного клиентского приложения.

    > [!NOTE]
    > В примере, приведенном в этом разделе, требуется дополнительная настройка для:
    >
    > * Доступа к приложениям в примерах доменов узла `firstapp.com` и `secondapp.com`.
    > * Сертификатов для клиентских приложений, чтобы включить средства безопасности TLS (HTTPS).
    >
    > Описание полной необходимой конфигурации выходит за рамки этой статьи и зависит от того, как размещается решение. Дополнительные сведения см. в [статьях о размещении и развертывании](xref:host-and-deploy/index).

  Поместите следующий код, где строки были удалены ранее:

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

* В контроллере прогноза погоды серверного приложения (`Controllers/WeatherForecastController.cs`) замените существующий маршрут (`[Route("[controller]")]`) к `WeatherForecastController` следующими маршрутами:

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  ПО промежуточного слоя, добавленное в метод `Startup.Configure` серверного приложения ранее, изменяет входящие запросы к `/WeatherForecast` на `/FirstApp/WeatherForecast` или `/SecondApp/WeatherForecast` в зависимости от порта (5001/5002) или домена (`firstapp.com`/`secondapp.com`). Предыдущие маршруты контроллеров необходимы для возврата данных о погоде из серверного приложения в клиентские приложения.

### <a name="static-assets-and-class-libraries"></a>Статические ресурсы и библиотеки классов

Для статических ресурсов используйте следующие подходы.

* Если ресурс находится в папке `wwwroot` клиентского приложения, укажите пути к ним обычным образом:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Если ресурс находится в папке `wwwroot` [библиотеки классов (RCL) Razor](xref:blazor/components/class-libraries), ссылайтесь на статический ресурс в клиентском приложении, следуя инструкциям в статье об [RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом. Если какие-либо компоненты нуждаются в таблицах стилей или файлах JavaScript, используйте один из следующих подходов для получения статических ресурсов:

* Файл `wwwroot/index.html` клиентского приложения можно связать со статическими ресурсами (`<link>`).
* Компонент может использовать [компонент `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) платформы для получения статических ресурсов.

Приведенные выше подходы демонстрируются в следующих примерах.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

На компоненты, предоставляемые клиентскому приложению с помощью библиотеки классов, ссылаются обычным образом. Если какие либо компоненты нуждаются в таблицах стилей или файлах JavaScript, то файл `wwwroot/index.html` клиентского приложения должен содержать правильные ссылки на статические ресурсы. Эти подходы демонстрируются в следующих примерах.

::: moniker-end

Добавьте следующий компонент `Jeep` в одно из клиентских приложений. Компонент `Jeep` использует:

* изображение из папки `wwwroot` клиентского приложения (`jeep-cj.png`);
* изображение из папки `wwwroot` (`jeep-yj.png`) [добавленной библиотеки компонентов Razor](xref:blazor/components/class-libraries) (`JeepImage`).
* Пример компонента (`Component1`) создается автоматически шаблоном проекта RCL при добавлении библиотеки `JeepImage` в решение.

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
> **Не** публикуйте изображения автомобилей, если вы не являетесь владельцем этих изображений. В противном случае вы рискуете нарушить авторские права.

::: moniker range=">= aspnetcore-5.0"

Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки. Чтобы предоставить класс CSS `my-component` для страницы клиентского приложения, свяжите его с таблицей стилей библиотеки, используя [компонент `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) платформы:

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

Альтернативой использованию [компонента `Link`](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) является загрузка таблицы стилей из файла `wwwroot/index.html` клиентского приложения. Такой подход делает таблицу стилей доступной для всех компонентов в клиентском приложении:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Изображение `jeep-yj.png` из библиотеки также можно добавить в компонент `Component1` (`Component1.razor`) библиотеки.

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

Файл `wwwroot/index.html` клиентского приложения запрашивает таблицу стилей библиотеки со следующим добавленным тегом `<link>`:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

Добавьте навигацию в компонент `Jeep` в компоненте `NavMenu` клиентского приложения (`Shared/NavMenu.razor`):

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Дополнительные сведения об RCL см. здесь:

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Автономное развертывание

*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами. Любой статический файловый сервер способен обслуживать приложение Blazor.

Ресурсы автономного развертывания публикуются в папке `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.

### <a name="azure-app-service"></a>Служба приложений Azure

Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis).

Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается. Сейчас образ сервера Linux для размещения приложения недоступен. Ведется работа над реализацией этого сценария.

### <a name="iis"></a>IIS

IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor. Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Опубликованные ресурсы создаются в папке `/bin/Release/{TARGET FRAMEWORK}/publish`. Разместите содержимое папки `publish` на веб-сервере или в службе размещения.

#### <a name="webconfig"></a>web.config.

При публикации проекта Blazor создается файл `web.config` со следующей конфигурацией IIS:

* Типы MIME заданы для следующих расширений файлов:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Сжатие HTTP включено для следующих типов MIME:
  * `application/octet-stream`
  * `application/wasm`
* Задаются правила модуля переопределения URL-адресов:
  * Предоставление подкаталога для размещения статических ресурсов приложения (`wwwroot/{PATH REQUESTED}`).
  * Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (`wwwroot/index.html`).
  
#### <a name="use-a-custom-webconfig"></a>Использование пользовательского файла web.config

Чтобы применить пользовательский файл `web.config`, поместите пользовательский файл `web.config` в корневую папку проекта и опубликуйте проект.

#### <a name="install-the-url-rewrite-module"></a>Установка модуля переопределения URL-адресов

[Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса. Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS). Модуль необходимо скачать с веб-сайта IIS. Для его установки используйте установщик веб-платформы.

1. Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы. Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.
1. Скопируйте установщик на сервер. Запустите установщик. Нажмите кнопку **Установить** и примите условия лицензионного соглашения. Перезагрузка сервера после завершения установки не требуется.

#### <a name="configure-the-website"></a>Настройка веб-сайта

Установите для веб сайта **физический путь** к папке приложения. Папка содержит:

* Файл `web.config`, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.
* Папку статических ресурсов приложения.

#### <a name="host-as-an-iis-sub-app"></a>Размещение в качестве дочернего приложения IIS

Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:

* Отключите наследуемый обработчик модуля ASP.NET Core.

  Удалите обработчик в опубликованном файле `web.config` приложения Blazor, добавив раздел `<handlers>` в файл:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:

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

Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path). Задайте базовый путь приложения в файле `index.html` приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS.

#### <a name="brotli-and-gzip-compression"></a>Алгоритмы сжатия Brotli и Gzip

С помощью файла `web.config` можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритма Brotli или Gzip. Пример конфигурации см. по адресу [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Устранение неполадок

Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов. Если модуль не установлен, IIS не может проанализировать файл `web.config`. Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.

Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Хранилище Azure

Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor. Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS.

Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:

* Задайте для параметра **Имя документа индекса** значение `index.html`.
* Задайте для параметра **Путь к документу с ошибкой** значение `index.html`. Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов. При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**. При этом возвращается большой двоичный объект `index.html`, и маршрутизатор Blazor загружает и обрабатывает путь.

Если файлы не загружаются во время выполнения из-за недопустимых типов MIME в заголовках файлов `Content-Type`, выполните одно из следующих действий:

* Настройте средства для установки правильных типов MIME (заголовков `Content-Type`) при развертывании файлов.
* Измените типы MIME (заголовки `Content-Type`) для файлов после развертывания приложения.

  В Обозревателе службы хранилища (портал Azure) для каждого файла сделайте следующее:
  
  1. Щелкните правой кнопкой мыши файл и выберите **Свойства**.
  1. Укажите значение для параметра **ContentType** и нажмите кнопку **Сохранить**.

См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Следующий файл `nginx.conf` упрощен для демонстрации того, как настроить Nginx для отправки файла `index.html`, когда не удается найти соответствующий файл на диске.

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

При задании [ограничения пиковой скорости NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) с помощью [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) приложениям Blazor WebAssembly может потребоваться большое значение параметра `burst` с учетом сравнительно большого количества запросов, выполняемых приложением. Изначально задайте для этого параметра значение не менее 60.

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

Увеличьте значение, если средства разработчика в браузере или средство контроля сетевого трафика сообщают о том, что запросы возвращают код состояния *503, служба недоступна*.

Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx в Docker

Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine. Измените Dockerfile, скопировав файл `nginx.config` в контейнер.

Добавьте одну строку в Dockerfile, как показано в следующем примере:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:

1. Создайте файл конфигурации Apache. В следующем примере показан упрощенный файл конфигурации (`blazorapp.config`):

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

1. Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7.

1. Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации).

1. Перезапустите службу Apache.

Дополнительные сведения см. в разделах [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub Pages

Чтобы обеспечить переопределение URL-адресов, добавьте файл `wwwroot/404.html` со скриптом, который производит перенаправление запроса на страницу `index.html`. Пример см. в репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Пример веб-сайта](https://stevesandersonms.github.io/BlazorOnGitHubPages/)

При использовании сайта проекта вместо сайта организации следует обновить тег `<base>` в файле `wwwroot/index.html`. Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `/my-repository/`). В репозитории GitHub [SteveSandersonMS/BlazorOnGitHubPages ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)база `href` обновляется при публикации [файлом конфигурации `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).

> [!NOTE]
> [Репозиторий GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) не принадлежит, не обслуживается или не поддерживается .NET Foundation или корпорацией Майкрософт.

## <a name="host-configuration-values"></a>Значения конфигурации узла

[Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.

### <a name="content-root"></a>Корневой каталог содержимого

Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)). В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения.

* Передайте этот аргумент при локальном запуске приложения в командной строке. Перейдите в каталог приложения и выполните следующую команду:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**. Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**. Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Базовый путь

Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред). В следующих примерах `/relative-URL-path` — это базовый путь приложения. Дополнительные сведения см. в описании [базового пути приложения](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`. Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце).

* Передайте этот аргумент при локальном запуске приложения в командной строке. Перейдите в каталог приложения и выполните следующую команду:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**. Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**. Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL-адреса

Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы.

* Передайте этот аргумент при локальном запуске приложения в командной строке. Перейдите в каталог приложения и выполните следующую команду:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Добавьте запись в файл приложения `launchSettings.json` в профиле **IIS Express**. Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**. Задание аргумента на странице свойств Visual Studio добавляет его в файл `launchSettings.json`.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>Настройка средства обрезки

Blazor выполняет фильтрацию для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок. Дополнительные сведения см. в разделе <xref:blazor/host-and-deploy/configure-trimmer>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>Настройка компоновщика

Blazor выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок. Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker>.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>Загрузка пользовательских ресурсов загрузки

Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки. Используйте `loadBootResource` в следующих сценариях.

* Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или `dotnet.wasm`, из сети CDN.
* Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера.
* Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя.

Параметры `loadBootResource` приведены в следующей таблице.

| Параметр    | Описание |
| ------------ | ----------- |
| `type`       | Тип ресурса. Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` |
| `name`       | Имя ресурса. |
| `defaultUri` | Относительный или абсолютный URI ресурса. |
| `integrity`  | Строка целостности, представляющая ожидаемое содержимое в ответе. |

`loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.

* Строка URI. В следующем примере (`wwwroot/index.html`) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`:

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Данные часового пояса

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

* `Promise<Response>`. Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию.

  В следующем примере (`wwwroot/index.html`) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`:
  
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

* `null`/`undefined`, что приводит к поведению загрузки по умолчанию.

Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками. По умолчанию CDN обычно предоставляют необходимые заголовки.

Необходимо указать только типы для пользовательских поведений. Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.

## <a name="change-the-filename-extension-of-dll-files"></a>Изменение расширения DLL-файлов.

Если вам необходимо изменить расширения опубликованных `.dll`-файлов приложения, следуйте указаниям в этом разделе.

После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования `.dll`-файлов для использования другого расширения файла. Указывайте на `.dll`-файлы в каталоге `wwwroot` опубликованных выходных данных приложения (например, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).

В следующих примерах `.dll`-файлы переименованы для использования расширения имени файла `.bin`.

Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Если ресурсы рабочей роли также используются, добавьте следующую команду:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

В Linux или macOS.

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Если ресурсы рабочей роли также используются, добавьте следующую команду:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Чтобы использовать расширение имени файла, отличное от `.bin`, замените `.bin` в предыдущих командах.

Для сжатых файлов `blazor.boot.json.gz` и `blazor.boot.json.br` используйте один из следующих подходов:

* Удалите сжатые файлы `blazor.boot.json.gz` и `blazor.boot.json.br`. Сжатие при таком подходе отключается.
* Выполните повторное сжатие обновленного файла `blazor.boot.json`.

Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли. Выполните удаление или повторное сжатие файлов `wwwroot/service-worker-assets.js.br` и `wwwroot/service-worker-assets.js.gz`. иначе проверка целостности файлов в браузере завершится ошибкой.

В следующем примере Windows используется скрипт PowerShell, размещенный в корне проекта.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Если ресурсы рабочей роли также используются, добавьте следующую команду:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

В файле проекта скрипт запускается после публикации приложения.

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> В случае переименования и отложенной загрузки тех же сборок см. инструкции в разделе <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.

Чтобы оставить отзыв, перейдите на страницу [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).
