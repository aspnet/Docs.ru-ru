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
ms.openlocfilehash: c789928252417ef1cf95c60deb7edef24d58126e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056001"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a><span data-ttu-id="648ee-103">Обработка ошибок в приложениях ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="648ee-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="648ee-104">Автор: [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)</span><span class="sxs-lookup"><span data-stu-id="648ee-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="648ee-105">В этой статье описывается, как Blazor управляет необработанными исключениями и как разрабатывать приложения, которые обнаруживают и обрабатывают ошибки.</span><span class="sxs-lookup"><span data-stu-id="648ee-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="648ee-106">Подробные сведения об ошибках во время разработки</span><span class="sxs-lookup"><span data-stu-id="648ee-106">Detailed errors during development</span></span>

<span data-ttu-id="648ee-107">Если во время разработки приложение Blazor работает неправильно, подробные сведения об ошибках в приложении могут помочь в устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="648ee-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="648ee-108">При возникновении ошибки в приложении Blazor в нижней части экрана отображается золотистая полоска.</span><span class="sxs-lookup"><span data-stu-id="648ee-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="648ee-109">Во время разработки из этой полоски можно перейти в консоль браузера, где можно просмотреть исключение.</span><span class="sxs-lookup"><span data-stu-id="648ee-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="648ee-110">В рабочей среде эта полоска уведомляет пользователя о том, что произошла ошибка, и рекомендует обновить содержимое окна браузера.</span><span class="sxs-lookup"><span data-stu-id="648ee-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="648ee-111">Пользовательский интерфейс для этого процесса обработки ошибок входит в состав шаблонов проектов Blazor.</span><span class="sxs-lookup"><span data-stu-id="648ee-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="648ee-112">В приложении Blazor WebAssembly настройте интерфейс в файле `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="648ee-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="648ee-113">В приложении Blazor Server настройте интерфейс в файле `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="648ee-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

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

<span data-ttu-id="648ee-114">Элемент `blazor-error-ui` скрыт стилями, включенными в шаблоны Blazor (`wwwroot/css/app.css` или `wwwroot/css/site.css`), а затем отображается при возникновении ошибки:</span><span class="sxs-lookup"><span data-stu-id="648ee-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

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

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="648ee-115">Как приложение Blazor Server реагирует на необработанные исключения</span><span class="sxs-lookup"><span data-stu-id="648ee-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="648ee-116">Blazor Server — это платформа с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="648ee-116">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="648ee-117">Когда пользователи взаимодействуют с приложением, они поддерживают подключение к серверу, которое называется *каналом*.</span><span class="sxs-lookup"><span data-stu-id="648ee-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="648ee-118">Канал содержит экземпляры активных компонентов, а также многие другие аспекты состояния, например:</span><span class="sxs-lookup"><span data-stu-id="648ee-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="648ee-119">Последние отображаемые выходные данные компонентов.</span><span class="sxs-lookup"><span data-stu-id="648ee-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="648ee-120">Текущий набор делегатов обработки событий, которые могут вызываться событиями на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="648ee-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="648ee-121">Если пользователь открывает приложение на нескольких вкладках браузера, у него есть несколько независимых каналов.</span><span class="sxs-lookup"><span data-stu-id="648ee-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="648ee-122">Blazor обрабатывает большинство необработанных исключений как неустранимые для канала, где они происходят.</span><span class="sxs-lookup"><span data-stu-id="648ee-122">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="648ee-123">Если канал завершается из-за необработанного исключения, пользователь может продолжить взаимодействие с приложением только путем перезагрузки страницы для создания нового канала.</span><span class="sxs-lookup"><span data-stu-id="648ee-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="648ee-124">Это не влияет на остальные каналы для других пользователей или других вкладок браузера.</span><span class="sxs-lookup"><span data-stu-id="648ee-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="648ee-125">Этот сценарий аналогичен сценарию с настольным приложением, которое аварийно завершает работу.</span><span class="sxs-lookup"><span data-stu-id="648ee-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="648ee-126">Такое приложение необходимо перезапустить, но остальные приложения это не затрагивает.</span><span class="sxs-lookup"><span data-stu-id="648ee-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="648ee-127">Канал завершается, когда необработанное исключение возникает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="648ee-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="648ee-128">Необработанное исключение часто оставляет канал в неопределенном состоянии.</span><span class="sxs-lookup"><span data-stu-id="648ee-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="648ee-129">После необработанного исключения нормальную работу приложения невозможно гарантировать.</span><span class="sxs-lookup"><span data-stu-id="648ee-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="648ee-130">Дальнейшая работа канала может стать причиной уязвимостей системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="648ee-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="648ee-131">Управление необработанными исключениями в коде разработчика</span><span class="sxs-lookup"><span data-stu-id="648ee-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="648ee-132">Чтобы приложение продолжало работу после возникновения ошибки, в нем должна быть логика обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="648ee-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="648ee-133">В последующих разделах этой статьи описываются потенциальные источники необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="648ee-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="648ee-134">В рабочей среде не следует отображать сообщения об исключениях платформы или трассировки стека в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="648ee-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="648ee-135">Отображение сообщений об исключениях или трассировок стека может:</span><span class="sxs-lookup"><span data-stu-id="648ee-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="648ee-136">Раскрыть конфиденциальные сведения конечным пользователям.</span><span class="sxs-lookup"><span data-stu-id="648ee-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="648ee-137">Помочь злоумышленнику обнаружить слабые места в приложении, что может нарушить безопасность приложения, сервера или сети.</span><span class="sxs-lookup"><span data-stu-id="648ee-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="648ee-138">Регистрация ошибок с помощью постоянного поставщика</span><span class="sxs-lookup"><span data-stu-id="648ee-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="648ee-139">При возникновении необработанного исключения оно заносится в журнал экземпляров <xref:Microsoft.Extensions.Logging.ILogger>, настроенных в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="648ee-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="648ee-140">По умолчанию приложения Blazor записываются в выходные данные консоли с помощью поставщика ведения журнала консоли.</span><span class="sxs-lookup"><span data-stu-id="648ee-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="648ee-141">Возможно, следует сохранять записи журнала в более постоянное расположение с помощью поставщика, который управляет размером журнала и ротацией журналов.</span><span class="sxs-lookup"><span data-stu-id="648ee-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="648ee-142">Для получения дополнительной информации см. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="648ee-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="648ee-143">Во время разработки Blazor обычно отправляет полные сведения об исключениях в консоль браузера для поддержки отладки.</span><span class="sxs-lookup"><span data-stu-id="648ee-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="648ee-144">В рабочей среде подробные ошибки в консоли браузера по умолчанию отключены, то есть ошибки не отправляются клиентам, но все сведения об исключении по-прежнему регистрируются на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="648ee-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="648ee-145">Для получения дополнительной информации см. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="648ee-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="648ee-146">Необходимо решить, какие инциденты следует заносить в журнал, и выбрать уровень серьезности регистрируемых инцидентов.</span><span class="sxs-lookup"><span data-stu-id="648ee-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="648ee-147">Злоумышленники могут попытаться вызывать ошибки намеренно.</span><span class="sxs-lookup"><span data-stu-id="648ee-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="648ee-148">Например, не заносите в журнал инцидент в результате ошибки, когда в URL-адресе компонента, отображающего сведения о продукте, указан неизвестный `ProductId`.</span><span class="sxs-lookup"><span data-stu-id="648ee-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="648ee-149">Не все ошибки следует рассматривать как инциденты с высоким уровнем серьезности для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="648ee-150">Для получения дополнительной информации см. <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="648ee-150">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="648ee-151">Места, где могут возникнуть ошибки</span><span class="sxs-lookup"><span data-stu-id="648ee-151">Places where errors may occur</span></span>

<span data-ttu-id="648ee-152">Код платформы и приложения может вызывать необработанные исключения в любом из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="648ee-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="648ee-153">Экземпляр компонента</span><span class="sxs-lookup"><span data-stu-id="648ee-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="648ee-154">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="648ee-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="648ee-155">Логика отрисовки</span><span class="sxs-lookup"><span data-stu-id="648ee-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="648ee-156">Обработчики событий</span><span class="sxs-lookup"><span data-stu-id="648ee-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="648ee-157">Удаление компонентов</span><span class="sxs-lookup"><span data-stu-id="648ee-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="648ee-158">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="648ee-158">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="648ee-159">Отрисовка Blazor Server</span><span class="sxs-lookup"><span data-stu-id="648ee-159">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="648ee-160">Предыдущие необработанные исключения описаны в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="648ee-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="648ee-161">Экземпляр компонента</span><span class="sxs-lookup"><span data-stu-id="648ee-161">Component instantiation</span></span>

<span data-ttu-id="648ee-162">Когда Blazor создает экземпляр компонента:</span><span class="sxs-lookup"><span data-stu-id="648ee-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="648ee-163">Вызывается конструктор компонента.</span><span class="sxs-lookup"><span data-stu-id="648ee-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="648ee-164">Вызываются конструкторы любых не отдельных служб DI, которые передаются в конструктор компонента с помощью директивы [`@inject`](xref:mvc/views/razor#inject) или атрибута [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="648ee-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="648ee-165">Сбой канала Blazor Server происходит, когда любой выполненный конструктор или метод задания для любого свойства `[Inject]` выдает необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="648ee-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="648ee-166">Исключение является неустранимым, так как платформе не удается создать экземпляр компонента.</span><span class="sxs-lookup"><span data-stu-id="648ee-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="648ee-167">Если логика конструктора может вызывать исключения, приложение должно выполнить перехват исключений с помощью инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="648ee-168">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="648ee-168">Lifecycle methods</span></span>

<span data-ttu-id="648ee-169">В течение времени существования компонента Blazor вызывает следующие [методы жизненного цикла](xref:blazor/components/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="648ee-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="648ee-170">Если какой-либо метод жизненного цикла вызывает исключение (синхронно или асинхронно), такое исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="648ee-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="648ee-171">Для компонентов, обрабатывающих ошибки в методах жизненного цикла, добавьте логику обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="648ee-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="648ee-172">В следующем примере <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> вызывает метод для получения продукта:</span><span class="sxs-lookup"><span data-stu-id="648ee-172">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="648ee-173">Исключение, вызванное в методе `ProductRepository.GetProductByIdAsync`, обрабатывается инструкцией [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="648ee-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="648ee-174">При выполнении блока `catch`:</span><span class="sxs-lookup"><span data-stu-id="648ee-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="648ee-175">`loadFailed` получает значение `true`, которое используется для вывода сообщения об ошибке пользователю.</span><span class="sxs-lookup"><span data-stu-id="648ee-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="648ee-176">Ошибка регистрируется в журнале.</span><span class="sxs-lookup"><span data-stu-id="648ee-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="648ee-177">Логика отрисовки</span><span class="sxs-lookup"><span data-stu-id="648ee-177">Rendering logic</span></span>

<span data-ttu-id="648ee-178">Декларативная разметка в файле компонента `.razor` компилируется в метод C# с именем <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span><span class="sxs-lookup"><span data-stu-id="648ee-178">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="648ee-179">Когда компонент отрисовывается, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> выполняет и создает структуру данных, описывающую элементы, текст и дочерние компоненты отрисованного компонента.</span><span class="sxs-lookup"><span data-stu-id="648ee-179">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="648ee-180">Логика отрисовки может вызывать исключение.</span><span class="sxs-lookup"><span data-stu-id="648ee-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="648ee-181">Пример этого сценария происходит, когда `@someObject.PropertyName` вычисляется, но `@someObject` имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="648ee-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="648ee-182">Необработанное исключение, созданное логикой отрисовки, является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="648ee-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="648ee-183">Чтобы предотвратить исключение пустой ссылки в логике отрисовки, проверьте объект `null` перед доступом к его членам.</span><span class="sxs-lookup"><span data-stu-id="648ee-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="648ee-184">В следующем примере свойства `person.Address` недоступны, если `person.Address` имеет значение `null`:</span><span class="sxs-lookup"><span data-stu-id="648ee-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="648ee-185">В приведенном выше коде предполагается, что `person` не имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="648ee-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="648ee-186">Часто структура кода гарантирует, что объект существует на момент отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="648ee-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="648ee-187">В таких случаях нет необходимости проверять наличие `null` в логике отрисовки.</span><span class="sxs-lookup"><span data-stu-id="648ee-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="648ee-188">В предыдущем примере `person` может гарантированно существовать, поскольку `person` создается при создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="648ee-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="648ee-189">Обработчики событий</span><span class="sxs-lookup"><span data-stu-id="648ee-189">Event handlers</span></span>

<span data-ttu-id="648ee-190">Код на стороне клиента активирует вызовы кода C# при создании обработчиков событий с помощью:</span><span class="sxs-lookup"><span data-stu-id="648ee-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="648ee-191">Другие атрибуты `@on...`</span><span class="sxs-lookup"><span data-stu-id="648ee-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="648ee-192">В этих сценариях код обработчика событий может вызывать необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="648ee-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="648ee-193">Если обработчик событий создает необработанное исключение (например, запрос к базе данных завершается ошибкой), такое исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="648ee-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="648ee-194">Если приложение вызывает код, который может завершиться ошибкой по внешним причинам, перехватите исключения с помощью инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-194">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="648ee-195">Если пользовательский код не выполняет перехват и обработку исключения, платформа регистрирует исключение и завершает канал.</span><span class="sxs-lookup"><span data-stu-id="648ee-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="648ee-196">Удаление компонентов</span><span class="sxs-lookup"><span data-stu-id="648ee-196">Component disposal</span></span>

<span data-ttu-id="648ee-197">Компонент может быть удален из пользовательского интерфейса, например, потому, что пользователь перешел на другую страницу.</span><span class="sxs-lookup"><span data-stu-id="648ee-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="648ee-198">Когда компонент, реализующий <xref:System.IDisposable?displayProperty=fullName>, удаляется из пользовательского интерфейса, платформа вызывает метод <xref:System.IDisposable.Dispose%2A> компонента.</span><span class="sxs-lookup"><span data-stu-id="648ee-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="648ee-199">Если метод `Dispose` компонента создает необработанное исключение, такое исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="648ee-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="648ee-200">Если логика удаления может вызывать исключения, приложение должно выполнить перехват исключений с помощью инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="648ee-201">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="648ee-201">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="648ee-202">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="648ee-202">JavaScript interop</span></span>

<span data-ttu-id="648ee-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> позволяет коду .NET выполнять асинхронные вызовы в среде выполнения JavaScript в браузере пользователя.</span><span class="sxs-lookup"><span data-stu-id="648ee-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="648ee-204">К обработке ошибок с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> применяются следующие условия:</span><span class="sxs-lookup"><span data-stu-id="648ee-204">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="648ee-205">Если вызов к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> выполняется синхронно, возникает исключение .NET.</span><span class="sxs-lookup"><span data-stu-id="648ee-205">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="648ee-206">Вызов к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> может завершиться ошибкой, например, поскольку передаваемые аргументы не могут быть сериализованы.</span><span class="sxs-lookup"><span data-stu-id="648ee-206">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="648ee-207">Код разработчика должен перехватить исключение.</span><span class="sxs-lookup"><span data-stu-id="648ee-207">Developer code must catch the exception.</span></span> <span data-ttu-id="648ee-208">Если код приложения в обработчике событий или методе жизненного цикла компонента не обрабатывает исключение, полученное исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="648ee-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="648ee-209">Если вызов <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> асинхронно завершается сбоем, .NET <xref:System.Threading.Tasks.Task> также завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="648ee-209">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="648ee-210">Вызов к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> может завершиться ошибкой, например, потому, что код на стороне JavaScript вызывает исключение или возвращает `Promise`, который был завершен как `rejected`.</span><span class="sxs-lookup"><span data-stu-id="648ee-210">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="648ee-211">Код разработчика должен перехватить исключение.</span><span class="sxs-lookup"><span data-stu-id="648ee-211">Developer code must catch the exception.</span></span> <span data-ttu-id="648ee-212">При использовании оператора [`await`](/dotnet/csharp/language-reference/keywords/await)рекомендуется заключить вызов метода в инструкцию [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-212">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="648ee-213">В противном случае сбой кода приводит к возникновению необработанного исключения, которое является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="648ee-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="648ee-214">По умолчанию вызовы к <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> должны завершаться в течение определенного периода или время ожидания вызова истекает. Период ожидания по умолчанию составляет одну минуту.</span><span class="sxs-lookup"><span data-stu-id="648ee-214">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="648ee-215">Время ожидания защищает код от потери сетевого подключения или кода JavaScript, который никогда не отправляет обратно сообщение о завершении.</span><span class="sxs-lookup"><span data-stu-id="648ee-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="648ee-216">Если время ожидания вызова истекает, полученный элемент <xref:System.Threading.Tasks> завершается с исключением <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="648ee-216">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="648ee-217">Перехватите и обработайте исключение с ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="648ee-218">Аналогичным образом код JavaScript может инициировать вызовы методов .NET, указанных в атрибуте [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="648ee-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="648ee-219">Если эти методы .NET создают необработанное исключение:</span><span class="sxs-lookup"><span data-stu-id="648ee-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="648ee-220">исключение не рассматривается как неустранимое для канала Blazor Server;</span><span class="sxs-lookup"><span data-stu-id="648ee-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="648ee-221">`Promise` на стороне JavaScript отклоняется.</span><span class="sxs-lookup"><span data-stu-id="648ee-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="648ee-222">Вы можете использовать код обработки ошибок либо на стороне .NET, либо на стороне JavaScript вызова метода.</span><span class="sxs-lookup"><span data-stu-id="648ee-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="648ee-223">Дополнительные сведения см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="648ee-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a><span data-ttu-id="648ee-224">Предварительная отрисовка Blazor Server</span><span class="sxs-lookup"><span data-stu-id="648ee-224">Blazor Server prerendering</span></span>

<span data-ttu-id="648ee-225">Компоненты Blazor можно предварительно отрисовать с помощью [вспомогательной функции тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper), чтобы их отрисованная разметка HTML возвращалась как часть исходного HTTP-запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="648ee-225">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="648ee-226">Это происходит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="648ee-226">This works by:</span></span>

* <span data-ttu-id="648ee-227">Создается новый канал для всех предварительно отрисованных компонентов, которые являются частью одной страницы.</span><span class="sxs-lookup"><span data-stu-id="648ee-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="648ee-228">Создается исходный HTML-код.</span><span class="sxs-lookup"><span data-stu-id="648ee-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="648ee-229">Канал рассматривается как `disconnected`, пока браузер пользователя не установит подключение SignalR к тому же серверу.</span><span class="sxs-lookup"><span data-stu-id="648ee-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="648ee-230">Если соединение установлено, взаимодействие с каналом возобновляется, а HTML-разметка компонентов обновляется.</span><span class="sxs-lookup"><span data-stu-id="648ee-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="648ee-231">Если какой-либо компонент создает необработанное исключение во время предварительной отрисовки, например во время метода жизненного цикла или в логике отрисовки:</span><span class="sxs-lookup"><span data-stu-id="648ee-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="648ee-232">Исключение является неустранимым для канала.</span><span class="sxs-lookup"><span data-stu-id="648ee-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="648ee-233">Исключение вызывается в стеке вызовов из вспомогательной функции тега <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="648ee-233">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="648ee-234">Таким образом, весь HTTP-запрос завершается ошибкой, если только исключение явно не перехвачено кодом разработчика.</span><span class="sxs-lookup"><span data-stu-id="648ee-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="648ee-235">В обычных обстоятельствах при сбое предварительной отрисовки продолжение сборки и отрисовки компонента не имеет смысла, так как невозможно отрисовать рабочий компонент.</span><span class="sxs-lookup"><span data-stu-id="648ee-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="648ee-236">Чтобы допускать ошибки, которые могут возникнуть во время предварительной отрисовки, логика обработки ошибок должна размещаться внутри компонента, который может вызывать исключения.</span><span class="sxs-lookup"><span data-stu-id="648ee-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="648ee-237">Используйте инструкции [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="648ee-237">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="648ee-238">Вместо того чтобы упаковывать вспомогательную функцию тега <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> в инструкцию [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch), поместите логику обработки ошибок в компонент, отрисовываемый вспомогательной функцией тега <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="648ee-238">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="648ee-239">Сложные сценарии</span><span class="sxs-lookup"><span data-stu-id="648ee-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="648ee-240">Рекурсивная отрисовка</span><span class="sxs-lookup"><span data-stu-id="648ee-240">Recursive rendering</span></span>

<span data-ttu-id="648ee-241">Компоненты можно вкладывать друг в друга рекурсивно.</span><span class="sxs-lookup"><span data-stu-id="648ee-241">Components can be nested recursively.</span></span> <span data-ttu-id="648ee-242">Это полезно для представления рекурсивных структур данных.</span><span class="sxs-lookup"><span data-stu-id="648ee-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="648ee-243">Например, компонент `TreeNode` может отрисовывать другие компоненты `TreeNode` для каждого из дочерних элементов узла.</span><span class="sxs-lookup"><span data-stu-id="648ee-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="648ee-244">При рекурсивной отрисовке избегайте шаблонов кодирования, которые приводят к бесконечной рекурсии:</span><span class="sxs-lookup"><span data-stu-id="648ee-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="648ee-245">Не следует рекурсивно отрисовывать структуру данных, содержащую цикл.</span><span class="sxs-lookup"><span data-stu-id="648ee-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="648ee-246">Например, не следует отрисовывать узел дерева, дочерние элементы которого содержат сами себя.</span><span class="sxs-lookup"><span data-stu-id="648ee-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="648ee-247">Не создавайте цепочку макетов, содержащих цикл.</span><span class="sxs-lookup"><span data-stu-id="648ee-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="648ee-248">Например, не создавайте макет, макетом которого является он сам.</span><span class="sxs-lookup"><span data-stu-id="648ee-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="648ee-249">Не разрешайте пользователю нарушать инварианты рекурсии (правила) с помощью ввода вредоносных данных или вызовов взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="648ee-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="648ee-250">Бесконечные циклы во время отрисовки:</span><span class="sxs-lookup"><span data-stu-id="648ee-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="648ee-251">Приводят к тому, что процесс отрисовки будет выполняться бесконечно.</span><span class="sxs-lookup"><span data-stu-id="648ee-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="648ee-252">Эквивалентны созданию незавершенного цикла.</span><span class="sxs-lookup"><span data-stu-id="648ee-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="648ee-253">В этих сценариях происходит сбой канала Blazor Server, и поток обычно пытается сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="648ee-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="648ee-254">Использовать столько процессорных ресурсов, сколько разрешено операционной системой, в неограниченном объеме.</span><span class="sxs-lookup"><span data-stu-id="648ee-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="648ee-255">Использовать неограниченный объема памяти сервера.</span><span class="sxs-lookup"><span data-stu-id="648ee-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="648ee-256">Использование неограниченного объема памяти эквивалентно сценарию, в котором незавершенный цикл добавляет записи в коллекцию при каждой итерации.</span><span class="sxs-lookup"><span data-stu-id="648ee-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="648ee-257">Чтобы избежать бесконечного шаблона рекурсии, убедитесь, что рекурсивный код отрисовки содержит подходящие условия остановки.</span><span class="sxs-lookup"><span data-stu-id="648ee-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="648ee-258">Пользовательская логика дерева отрисовки</span><span class="sxs-lookup"><span data-stu-id="648ee-258">Custom render tree logic</span></span>

<span data-ttu-id="648ee-259">Большинство компонентов Blazor реализуются как файлы `.razor` и компилируются для создания логики, которая работает в <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для отрисовки выходных данных.</span><span class="sxs-lookup"><span data-stu-id="648ee-259">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="648ee-260">Разработчик может вручную реализовать логику <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> с помощью процедурного кода C#.</span><span class="sxs-lookup"><span data-stu-id="648ee-260">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="648ee-261">Для получения дополнительной информации см. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="648ee-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="648ee-262">Использование выполняемой вручную логики построителя дерева считается сложным и небезопасным сценарием, не рекомендуемым для обычной разработки компонентов.</span><span class="sxs-lookup"><span data-stu-id="648ee-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="648ee-263">При написании кода <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> разработчик должен гарантировать правильность кода.</span><span class="sxs-lookup"><span data-stu-id="648ee-263">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="648ee-264">Например, разработчик должен убедиться в том, что:</span><span class="sxs-lookup"><span data-stu-id="648ee-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="648ee-265">Вызовы к <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> и <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> правильно сбалансированы.</span><span class="sxs-lookup"><span data-stu-id="648ee-265">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="648ee-266">Атрибуты добавляются только в нужные места.</span><span class="sxs-lookup"><span data-stu-id="648ee-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="648ee-267">Неправильная логика конструктора выполняемой вручную логики построителя дерева отрисовки может вызвать произвольное неопределенное поведение, включая сбои, зависания сервера и уязвимости системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="648ee-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="648ee-268">Считайте, что выполняемая вручную логика построителя дерева отрисовки имеет тот же уровень сложности и *опасности*, что и написание кода сборки или инструкций MSIL вручную.</span><span class="sxs-lookup"><span data-stu-id="648ee-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
