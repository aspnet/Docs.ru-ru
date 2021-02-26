---
title: Обработка ошибок в приложениях ASP.NET Core Blazor
author: guardrex
description: Узнайте, как ASP.NET Core Blazor Blazor управляет необработанными исключениями и как разрабатывать приложения, которые обнаруживают и обрабатывают ошибки.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: f7cac477e2c5bca54e24ae3faeadff9b51bdcd0f
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101065"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="7c025-103">Обработка ошибок в приложениях ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7c025-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="7c025-104">В этой статье описывается, как Blazor управляет необработанными исключениями и как разрабатывать приложения, которые обнаруживают и обрабатывают ошибки.</span><span class="sxs-lookup"><span data-stu-id="7c025-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="7c025-105">Подробные сведения об ошибках во время разработки</span><span class="sxs-lookup"><span data-stu-id="7c025-105">Detailed errors during development</span></span>

<span data-ttu-id="7c025-106">Если во время разработки приложение Blazor работает неправильно, подробные сведения об ошибках в приложении могут помочь в устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="7c025-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="7c025-107">При возникновении ошибки в приложении Blazor в нижней части экрана отображается золотистая полоска.</span><span class="sxs-lookup"><span data-stu-id="7c025-107">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="7c025-108">Во время разработки из этой полоски можно перейти в консоль браузера, где можно просмотреть исключение.</span><span class="sxs-lookup"><span data-stu-id="7c025-108">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="7c025-109">В рабочей среде эта полоска уведомляет пользователя о том, что произошла ошибка, и рекомендует обновить содержимое окна браузера.</span><span class="sxs-lookup"><span data-stu-id="7c025-109">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="7c025-110">Пользовательский интерфейс для этого процесса обработки ошибок входит в состав шаблонов проектов Blazor.</span><span class="sxs-lookup"><span data-stu-id="7c025-110">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="7c025-111">В приложении Blazor WebAssembly настройте интерфейс в файле `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="7c025-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="7c025-112">В приложении Blazor Server настройте интерфейс в файле `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="7c025-112">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="7c025-113">Элемент `blazor-error-ui` скрыт стилями, включенными в шаблоны Blazor (`wwwroot/css/app.css` или `wwwroot/css/site.css`), а затем отображается при возникновении ошибки:</span><span class="sxs-lookup"><span data-stu-id="7c025-113">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="7c025-114">Подробное описание ошибок каналов в Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7c025-114">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="7c025-115">Ошибки на стороне клиента не включают стек вызовов и не предоставляют сведения о причине ошибки, но журналы сервера содержат такие сведения.</span><span class="sxs-lookup"><span data-stu-id="7c025-115">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="7c025-116">В целях разработки конфиденциальные сведения об ошибках каналов можно сделать доступными для клиента, включив подробное описание ошибок.</span><span class="sxs-lookup"><span data-stu-id="7c025-116">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="7c025-117">Включить подробное описание ошибок Blazor Server можно с помощью следующих способов:</span><span class="sxs-lookup"><span data-stu-id="7c025-117">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="7c025-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="7c025-118"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="7c025-119">Задайте для ключа конфигурации `DetailedErrors` значение `true` в файле параметров разработки приложения (`appsettings.Development.json`).</span><span class="sxs-lookup"><span data-stu-id="7c025-119">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="7c025-120">Ключ также можно задать с помощью переменной среды `ASPNETCORE_DETAILEDERRORS` со значением `true`.</span><span class="sxs-lookup"><span data-stu-id="7c025-120">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="7c025-121">Задайте для параметра [ведения журнала SignalR на стороне сервера](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) значение [Отладка](xref:Microsoft.Extensions.Logging.LogLevel) или [Трассировка](xref:Microsoft.Extensions.Logging.LogLevel), чтобы обеспечить подробное ведение журнала SignalR.</span><span class="sxs-lookup"><span data-stu-id="7c025-121">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="7c025-122">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="7c025-122">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> [!WARNING]
> <span data-ttu-id="7c025-123">Предоставление сведений об ошибках клиентам в Интернете является угрозой безопасности, которую всегда следует избегать.</span><span class="sxs-lookup"><span data-stu-id="7c025-123">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="7c025-124">Как приложение Blazor Server реагирует на необработанные исключения</span><span class="sxs-lookup"><span data-stu-id="7c025-124">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="7c025-125">Blazor Server — это платформа с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="7c025-125">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="7c025-126">Когда пользователи взаимодействуют с приложением, они поддерживают подключение к серверу, которое называется *каналом*.</span><span class="sxs-lookup"><span data-stu-id="7c025-126">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="7c025-127">Канал содержит экземпляры активных компонентов, а также многие другие аспекты состояния, например:</span><span class="sxs-lookup"><span data-stu-id="7c025-127">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="7c025-128">Последние отображаемые выходные данные компонентов.</span><span class="sxs-lookup"><span data-stu-id="7c025-128">The most recent rendered output of components.</span></span>
* <span data-ttu-id="7c025-129">Текущий набор делегатов обработки событий, которые могут вызываться событиями на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="7c025-129">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="7c025-130">Если пользователь открывает приложение на нескольких вкладках браузера, у него есть несколько независимых каналов.</span><span class="sxs-lookup"><span data-stu-id="7c025-130">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="7c025-131">Blazor обрабатывает большинство необработанных исключений как неустранимые для канала, где они происходят.</span><span class="sxs-lookup"><span data-stu-id="7c025-131">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="7c025-132">Если канал завершается из-за необработанного исключения, пользователь может продолжить взаимодействие с приложением только путем перезагрузки страницы для создания нового канала.</span><span class="sxs-lookup"><span data-stu-id="7c025-132">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="7c025-133">Это не влияет на остальные каналы для других пользователей или других вкладок браузера.</span><span class="sxs-lookup"><span data-stu-id="7c025-133">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="7c025-134">Этот сценарий аналогичен сценарию с настольным приложением, которое аварийно завершает работу.</span><span class="sxs-lookup"><span data-stu-id="7c025-134">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="7c025-135">Такое приложение необходимо перезапустить, но остальные приложения это не затрагивает.</span><span class="sxs-lookup"><span data-stu-id="7c025-135">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="7c025-136">Канал завершается, когда необработанное исключение возникает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="7c025-136">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="7c025-137">Необработанное исключение часто оставляет канал в неопределенном состоянии.</span><span class="sxs-lookup"><span data-stu-id="7c025-137">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="7c025-138">После необработанного исключения нормальную работу приложения невозможно гарантировать.</span><span class="sxs-lookup"><span data-stu-id="7c025-138">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="7c025-139">Дальнейшая работа канала может стать причиной уязвимостей системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="7c025-139">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="7c025-140">Управление необработанными исключениями в коде разработчика</span><span class="sxs-lookup"><span data-stu-id="7c025-140">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="7c025-141">Чтобы приложение продолжало работу после возникновения ошибки, в нем должна быть логика обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="7c025-141">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="7c025-142">В последующих разделах этой статьи описываются потенциальные источники необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="7c025-142">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="7c025-143">В рабочей среде не следует отображать сообщения об исключениях платформы или трассировки стека в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="7c025-143">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="7c025-144">Отображение сообщений об исключениях или трассировок стека может:</span><span class="sxs-lookup"><span data-stu-id="7c025-144">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="7c025-145">Раскрыть конфиденциальные сведения конечным пользователям.</span><span class="sxs-lookup"><span data-stu-id="7c025-145">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="7c025-146">Помочь злоумышленнику обнаружить слабые места в приложении, что может нарушить безопасность приложения, сервера или сети.</span><span class="sxs-lookup"><span data-stu-id="7c025-146">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="7c025-147">Регистрация ошибок с помощью постоянного поставщика</span><span class="sxs-lookup"><span data-stu-id="7c025-147">Log errors with a persistent provider</span></span>

<span data-ttu-id="7c025-148">При возникновении необработанного исключения оно заносится в журнал экземпляров <xref:Microsoft.Extensions.Logging.ILogger>, настроенных в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="7c025-148">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="7c025-149">По умолчанию приложения Blazor записываются в выходные данные консоли с помощью поставщика ведения журнала консоли.</span><span class="sxs-lookup"><span data-stu-id="7c025-149">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="7c025-150">Возможно, следует сохранять записи журнала в более постоянное расположение с помощью поставщика, который управляет размером журнала и ротацией журналов.</span><span class="sxs-lookup"><span data-stu-id="7c025-150">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="7c025-151">Для получения дополнительной информации см. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="7c025-151">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="7c025-152">Во время разработки Blazor обычно отправляет полные сведения об исключениях в консоль браузера для поддержки отладки.</span><span class="sxs-lookup"><span data-stu-id="7c025-152">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="7c025-153">В рабочей среде подробные ошибки в консоли браузера по умолчанию отключены, то есть ошибки не отправляются клиентам, но все сведения об исключении по-прежнему регистрируются на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="7c025-153">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="7c025-154">Для получения дополнительной информации см. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="7c025-154">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="7c025-155">Необходимо решить, какие инциденты следует заносить в журнал, и выбрать уровень серьезности регистрируемых инцидентов.</span><span class="sxs-lookup"><span data-stu-id="7c025-155">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="7c025-156">Злоумышленники могут попытаться вызывать ошибки намеренно.</span><span class="sxs-lookup"><span data-stu-id="7c025-156">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="7c025-157">Например, не заносите в журнал инцидент в результате ошибки, когда в URL-адресе компонента, отображающего сведения о продукте, указан неизвестный `ProductId`.</span><span class="sxs-lookup"><span data-stu-id="7c025-157">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="7c025-158">Не все ошибки следует рассматривать как инциденты с высоким уровнем серьезности для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-158">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="7c025-159">Для получения дополнительной информации см. <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="7c025-159">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="7c025-160">Места, где могут возникнуть ошибки</span><span class="sxs-lookup"><span data-stu-id="7c025-160">Places where errors may occur</span></span>

<span data-ttu-id="7c025-161">Код платформы и приложения может вызывать необработанные исключения в любом из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="7c025-161">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="7c025-162">Экземпляр компонента</span><span class="sxs-lookup"><span data-stu-id="7c025-162">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="7c025-163">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="7c025-163">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="7c025-164">Логика отрисовки</span><span class="sxs-lookup"><span data-stu-id="7c025-164">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="7c025-165">Обработчики событий</span><span class="sxs-lookup"><span data-stu-id="7c025-165">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="7c025-166">Удаление компонентов</span><span class="sxs-lookup"><span data-stu-id="7c025-166">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="7c025-167">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="7c025-167">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="7c025-168">Отрисовка Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7c025-168">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="7c025-169">Предыдущие необработанные исключения описаны в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="7c025-169">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="7c025-170">Экземпляр компонента</span><span class="sxs-lookup"><span data-stu-id="7c025-170">Component instantiation</span></span>

<span data-ttu-id="7c025-171">Когда Blazor создает экземпляр компонента:</span><span class="sxs-lookup"><span data-stu-id="7c025-171">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="7c025-172">Вызывается конструктор компонента.</span><span class="sxs-lookup"><span data-stu-id="7c025-172">The component's constructor is invoked.</span></span>
* <span data-ttu-id="7c025-173">Вызываются конструкторы любых не отдельных служб DI, которые передаются в конструктор компонента с помощью директивы [`@inject`](xref:mvc/views/razor#inject) или [атрибута `[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="7c025-173">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="7c025-174">Сбой канала Blazor Server происходит, когда любой выполненный конструктор или метод задания для любого свойства `[Inject]` выдает необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="7c025-174">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="7c025-175">Исключение является неустранимым, так как платформе не удается создать экземпляр компонента.</span><span class="sxs-lookup"><span data-stu-id="7c025-175">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="7c025-176">Если логика конструктора может вызывать исключения, приложение должно выполнить перехват исключений с помощью инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-176">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="7c025-177">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="7c025-177">Lifecycle methods</span></span>

<span data-ttu-id="7c025-178">В течение времени существования компонента Blazor вызывает следующие [методы жизненного цикла](xref:blazor/components/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="7c025-178">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="7c025-179">Если какой-либо метод жизненного цикла вызывает исключение (синхронно или асинхронно), такое исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c025-179">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="7c025-180">Для компонентов, обрабатывающих ошибки в методах жизненного цикла, добавьте логику обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="7c025-180">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="7c025-181">В следующем примере <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> вызывает метод для получения продукта:</span><span class="sxs-lookup"><span data-stu-id="7c025-181">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="7c025-182">Исключение, вызванное в методе `ProductRepository.GetProductByIdAsync`, обрабатывается инструкцией [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="7c025-182">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="7c025-183">При выполнении блока `catch`:</span><span class="sxs-lookup"><span data-stu-id="7c025-183">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="7c025-184">`loadFailed` получает значение `true`, которое используется для вывода сообщения об ошибке пользователю.</span><span class="sxs-lookup"><span data-stu-id="7c025-184">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="7c025-185">Ошибка регистрируется в журнале.</span><span class="sxs-lookup"><span data-stu-id="7c025-185">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

### <a name="rendering-logic"></a><span data-ttu-id="7c025-186">Логика отрисовки</span><span class="sxs-lookup"><span data-stu-id="7c025-186">Rendering logic</span></span>

<span data-ttu-id="7c025-187">Декларативная разметка в файле компонента `.razor` компилируется в метод C# с именем <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span><span class="sxs-lookup"><span data-stu-id="7c025-187">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="7c025-188">Когда компонент отрисовывается, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> выполняет и создает структуру данных, описывающую элементы, текст и дочерние компоненты отрисованного компонента.</span><span class="sxs-lookup"><span data-stu-id="7c025-188">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="7c025-189">Логика отрисовки может вызывать исключение.</span><span class="sxs-lookup"><span data-stu-id="7c025-189">Rendering logic can throw an exception.</span></span> <span data-ttu-id="7c025-190">Пример этого сценария происходит, когда `@someObject.PropertyName` вычисляется, но `@someObject` имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="7c025-190">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="7c025-191">Необработанное исключение, созданное логикой отрисовки, является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c025-191">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="7c025-192">Чтобы предотвратить исключение пустой ссылки в логике отрисовки, проверьте объект `null` перед доступом к его членам.</span><span class="sxs-lookup"><span data-stu-id="7c025-192">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="7c025-193">В следующем примере свойства `person.Address` недоступны, если `person.Address` имеет значение `null`:</span><span class="sxs-lookup"><span data-stu-id="7c025-193">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="7c025-194">В приведенном выше коде предполагается, что `person` не имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="7c025-194">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="7c025-195">Часто структура кода гарантирует, что объект существует на момент отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="7c025-195">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="7c025-196">В таких случаях нет необходимости проверять наличие `null` в логике отрисовки.</span><span class="sxs-lookup"><span data-stu-id="7c025-196">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="7c025-197">В предыдущем примере `person` может гарантированно существовать, поскольку `person` создается при создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="7c025-197">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="7c025-198">Обработчики событий</span><span class="sxs-lookup"><span data-stu-id="7c025-198">Event handlers</span></span>

<span data-ttu-id="7c025-199">Код на стороне клиента активирует вызовы кода C# при создании обработчиков событий с помощью:</span><span class="sxs-lookup"><span data-stu-id="7c025-199">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="7c025-200">Другие атрибуты `@on...`</span><span class="sxs-lookup"><span data-stu-id="7c025-200">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="7c025-201">В этих сценариях код обработчика событий может вызывать необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="7c025-201">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="7c025-202">Если обработчик событий создает необработанное исключение (например, запрос к базе данных завершается ошибкой), такое исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c025-202">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="7c025-203">Если приложение вызывает код, который может завершиться ошибкой по внешним причинам, перехватите исключения с помощью инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-203">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="7c025-204">Если пользовательский код не выполняет перехват и обработку исключения, платформа регистрирует исключение и завершает канал.</span><span class="sxs-lookup"><span data-stu-id="7c025-204">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="7c025-205">Удаление компонентов</span><span class="sxs-lookup"><span data-stu-id="7c025-205">Component disposal</span></span>

<span data-ttu-id="7c025-206">Компонент может быть удален из пользовательского интерфейса, например, потому, что пользователь перешел на другую страницу.</span><span class="sxs-lookup"><span data-stu-id="7c025-206">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="7c025-207">Когда компонент, реализующий <xref:System.IDisposable?displayProperty=fullName>, удаляется из пользовательского интерфейса, платформа вызывает метод <xref:System.IDisposable.Dispose%2A> компонента.</span><span class="sxs-lookup"><span data-stu-id="7c025-207">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="7c025-208">Если метод `Dispose` компонента создает необработанное исключение, такое исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c025-208">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="7c025-209">Если логика удаления может вызывать исключения, приложение должно выполнить перехват исключений с помощью инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-209">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="7c025-210">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="7c025-210">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="7c025-211">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="7c025-211">JavaScript interop</span></span>

<span data-ttu-id="7c025-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> позволяет коду .NET выполнять асинхронные вызовы в среде выполнения JavaScript в браузере пользователя.</span><span class="sxs-lookup"><span data-stu-id="7c025-212"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="7c025-213">К обработке ошибок с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> применяются следующие условия:</span><span class="sxs-lookup"><span data-stu-id="7c025-213">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="7c025-214">Если вызов к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> выполняется синхронно, возникает исключение .NET.</span><span class="sxs-lookup"><span data-stu-id="7c025-214">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="7c025-215">Вызов к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> может завершиться ошибкой, например, поскольку передаваемые аргументы не могут быть сериализованы.</span><span class="sxs-lookup"><span data-stu-id="7c025-215">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="7c025-216">Код разработчика должен перехватить исключение.</span><span class="sxs-lookup"><span data-stu-id="7c025-216">Developer code must catch the exception.</span></span> <span data-ttu-id="7c025-217">Если код приложения в обработчике событий или методе жизненного цикла компонента не обрабатывает исключение, полученное исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c025-217">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="7c025-218">Если вызов <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> асинхронно завершается сбоем, .NET <xref:System.Threading.Tasks.Task> также завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="7c025-218">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="7c025-219">Вызов к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> может завершиться ошибкой, например, потому, что код на стороне JavaScript вызывает исключение или возвращает `Promise`, который был завершен как `rejected`.</span><span class="sxs-lookup"><span data-stu-id="7c025-219">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="7c025-220">Код разработчика должен перехватить исключение.</span><span class="sxs-lookup"><span data-stu-id="7c025-220">Developer code must catch the exception.</span></span> <span data-ttu-id="7c025-221">При использовании оператора [`await`](/dotnet/csharp/language-reference/keywords/await)рекомендуется заключить вызов метода в инструкцию [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-221">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="7c025-222">В противном случае сбой кода приводит к возникновению необработанного исключения, которое является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c025-222">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="7c025-223">По умолчанию вызовы к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> должны завершаться в течение определенного периода или время ожидания вызова истекает. Период ожидания по умолчанию составляет одну минуту.</span><span class="sxs-lookup"><span data-stu-id="7c025-223">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="7c025-224">Время ожидания защищает код от потери сетевого подключения или кода JavaScript, который никогда не отправляет обратно сообщение о завершении.</span><span class="sxs-lookup"><span data-stu-id="7c025-224">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="7c025-225">Если время ожидания вызова истекает, полученный элемент <xref:System.Threading.Tasks> завершается с исключением <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="7c025-225">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="7c025-226">Перехватите и обработайте исключение с ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-226">Trap and process the exception with logging.</span></span>

<span data-ttu-id="7c025-227">Аналогичным образом код JavaScript может инициировать вызовы методов .NET, указанных в [атрибуте `[JSInvokable]`](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="7c025-227">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="7c025-228">Если эти методы .NET создают необработанное исключение:</span><span class="sxs-lookup"><span data-stu-id="7c025-228">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="7c025-229">исключение не рассматривается как неустранимое для канала Blazor Server;</span><span class="sxs-lookup"><span data-stu-id="7c025-229">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="7c025-230">`Promise` на стороне JavaScript отклоняется.</span><span class="sxs-lookup"><span data-stu-id="7c025-230">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="7c025-231">Вы можете использовать код обработки ошибок либо на стороне .NET, либо на стороне JavaScript вызова метода.</span><span class="sxs-lookup"><span data-stu-id="7c025-231">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="7c025-232">Дополнительные сведения см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="7c025-232">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a><span data-ttu-id="7c025-233">Предварительная отрисовка Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7c025-233">Blazor Server prerendering</span></span>

<span data-ttu-id="7c025-234">Компоненты Blazor можно предварительно отрисовать с помощью [вспомогательной функции тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper), чтобы их отрисованная разметка HTML возвращалась как часть исходного HTTP-запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="7c025-234">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="7c025-235">Это происходит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="7c025-235">This works by:</span></span>

* <span data-ttu-id="7c025-236">Создается новый канал для всех предварительно отрисованных компонентов, которые являются частью одной страницы.</span><span class="sxs-lookup"><span data-stu-id="7c025-236">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="7c025-237">Создается исходный HTML-код.</span><span class="sxs-lookup"><span data-stu-id="7c025-237">Generating the initial HTML.</span></span>
* <span data-ttu-id="7c025-238">Канал рассматривается как `disconnected`, пока браузер пользователя не установит подключение SignalR к тому же серверу.</span><span class="sxs-lookup"><span data-stu-id="7c025-238">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="7c025-239">Если соединение установлено, взаимодействие с каналом возобновляется, а HTML-разметка компонентов обновляется.</span><span class="sxs-lookup"><span data-stu-id="7c025-239">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="7c025-240">Если какой-либо компонент создает необработанное исключение во время предварительной отрисовки, например во время метода жизненного цикла или в логике отрисовки:</span><span class="sxs-lookup"><span data-stu-id="7c025-240">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="7c025-241">Исключение является неустранимым для канала.</span><span class="sxs-lookup"><span data-stu-id="7c025-241">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="7c025-242">Исключение вызывается в стеке вызовов из вспомогательной функции тега <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="7c025-242">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="7c025-243">Таким образом, весь HTTP-запрос завершается ошибкой, если только исключение явно не перехвачено кодом разработчика.</span><span class="sxs-lookup"><span data-stu-id="7c025-243">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="7c025-244">В обычных обстоятельствах при сбое предварительной отрисовки продолжение сборки и отрисовки компонента не имеет смысла, так как невозможно отрисовать рабочий компонент.</span><span class="sxs-lookup"><span data-stu-id="7c025-244">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="7c025-245">Чтобы допускать ошибки, которые могут возникнуть во время предварительной отрисовки, логика обработки ошибок должна размещаться внутри компонента, который может вызывать исключения.</span><span class="sxs-lookup"><span data-stu-id="7c025-245">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="7c025-246">Используйте инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7c025-246">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="7c025-247">Вместо того чтобы упаковывать вспомогательную функцию тега <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> в инструкцию [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch), поместите логику обработки ошибок в компонент, отрисовываемый вспомогательной функцией тега <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="7c025-247">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="7c025-248">Сложные сценарии</span><span class="sxs-lookup"><span data-stu-id="7c025-248">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="7c025-249">Рекурсивная отрисовка</span><span class="sxs-lookup"><span data-stu-id="7c025-249">Recursive rendering</span></span>

<span data-ttu-id="7c025-250">Компоненты можно вкладывать друг в друга рекурсивно.</span><span class="sxs-lookup"><span data-stu-id="7c025-250">Components can be nested recursively.</span></span> <span data-ttu-id="7c025-251">Это полезно для представления рекурсивных структур данных.</span><span class="sxs-lookup"><span data-stu-id="7c025-251">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="7c025-252">Например, компонент `TreeNode` может отрисовывать другие компоненты `TreeNode` для каждого из дочерних элементов узла.</span><span class="sxs-lookup"><span data-stu-id="7c025-252">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="7c025-253">При рекурсивной отрисовке избегайте шаблонов кодирования, которые приводят к бесконечной рекурсии:</span><span class="sxs-lookup"><span data-stu-id="7c025-253">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="7c025-254">Не следует рекурсивно отрисовывать структуру данных, содержащую цикл.</span><span class="sxs-lookup"><span data-stu-id="7c025-254">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="7c025-255">Например, не следует отрисовывать узел дерева, дочерние элементы которого содержат сами себя.</span><span class="sxs-lookup"><span data-stu-id="7c025-255">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="7c025-256">Не создавайте цепочку макетов, содержащих цикл.</span><span class="sxs-lookup"><span data-stu-id="7c025-256">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="7c025-257">Например, не создавайте макет, макетом которого является он сам.</span><span class="sxs-lookup"><span data-stu-id="7c025-257">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="7c025-258">Не разрешайте пользователю нарушать инварианты рекурсии (правила) с помощью ввода вредоносных данных или вызовов взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7c025-258">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="7c025-259">Бесконечные циклы во время отрисовки:</span><span class="sxs-lookup"><span data-stu-id="7c025-259">Infinite loops during rendering:</span></span>

* <span data-ttu-id="7c025-260">Приводят к тому, что процесс отрисовки будет выполняться бесконечно.</span><span class="sxs-lookup"><span data-stu-id="7c025-260">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="7c025-261">Эквивалентны созданию незавершенного цикла.</span><span class="sxs-lookup"><span data-stu-id="7c025-261">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="7c025-262">В этих сценариях происходит сбой канала Blazor Server, и поток обычно пытается сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="7c025-262">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="7c025-263">Использовать столько процессорных ресурсов, сколько разрешено операционной системой, в неограниченном объеме.</span><span class="sxs-lookup"><span data-stu-id="7c025-263">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="7c025-264">Использовать неограниченный объема памяти сервера.</span><span class="sxs-lookup"><span data-stu-id="7c025-264">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="7c025-265">Использование неограниченного объема памяти эквивалентно сценарию, в котором незавершенный цикл добавляет записи в коллекцию при каждой итерации.</span><span class="sxs-lookup"><span data-stu-id="7c025-265">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="7c025-266">Чтобы избежать бесконечного шаблона рекурсии, убедитесь, что рекурсивный код отрисовки содержит подходящие условия остановки.</span><span class="sxs-lookup"><span data-stu-id="7c025-266">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="7c025-267">Пользовательская логика дерева отрисовки</span><span class="sxs-lookup"><span data-stu-id="7c025-267">Custom render tree logic</span></span>

<span data-ttu-id="7c025-268">Большинство компонентов Blazor реализуются как файлы `.razor` и компилируются для создания логики, которая работает в <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для отрисовки выходных данных.</span><span class="sxs-lookup"><span data-stu-id="7c025-268">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="7c025-269">Разработчик может вручную реализовать логику <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> с помощью процедурного кода C#.</span><span class="sxs-lookup"><span data-stu-id="7c025-269">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="7c025-270">Для получения дополнительной информации см. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="7c025-270">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="7c025-271">Использование выполняемой вручную логики построителя дерева считается сложным и небезопасным сценарием, не рекомендуемым для обычной разработки компонентов.</span><span class="sxs-lookup"><span data-stu-id="7c025-271">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="7c025-272">При написании кода <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> разработчик должен гарантировать правильность кода.</span><span class="sxs-lookup"><span data-stu-id="7c025-272">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="7c025-273">Например, разработчик должен убедиться в том, что:</span><span class="sxs-lookup"><span data-stu-id="7c025-273">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="7c025-274">Вызовы к <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> и <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> правильно сбалансированы.</span><span class="sxs-lookup"><span data-stu-id="7c025-274">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="7c025-275">Атрибуты добавляются только в нужные места.</span><span class="sxs-lookup"><span data-stu-id="7c025-275">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="7c025-276">Неправильная логика конструктора выполняемой вручную логики построителя дерева отрисовки может вызвать произвольное неопределенное поведение, включая сбои, зависания сервера и уязвимости системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="7c025-276">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="7c025-277">Считайте, что выполняемая вручную логика построителя дерева отрисовки имеет тот же уровень сложности и *опасности*, что и написание кода сборки или инструкций MSIL вручную.</span><span class="sxs-lookup"><span data-stu-id="7c025-277">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
