---
title: Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать функции JavaScript из методов .NET в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: c73de0e30b7b564915f30d75f754f89fecccdc78
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035727"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="b65b4-103">Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b65b4-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="b65b4-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниел Рот](https://github.com/danroth27) (Daniel Roth), [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="b65b4-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b65b4-105">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="b65b4-106">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="b65b4-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="b65b4-107">В этой статье рассматривается вызов функций JavaScript из .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="b65b4-108">Сведения о том, как вызывать методы .NET из JavaScript, см. в статье <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="b65b4-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b65b4-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b65b4-110">Для вызова JavaScript из .NET используйте абстракцию <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="b65b4-111">Чтобы выполнять вызовы взаимодействия с JS, внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент.</span><span class="sxs-lookup"><span data-stu-id="b65b4-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="b65b4-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> принимает идентификатор функции JavaScript, которую нужно вызвать, вместе с любым числом аргументов, сериализуемых в JSON.</span><span class="sxs-lookup"><span data-stu-id="b65b4-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="b65b4-113">Идентификатор функции задается относительно глобальной области (`window`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="b65b4-114">Если нужно вызвать функцию `window.someScope.someFunction`, идентификатором будет `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="b65b4-115">Регистрировать функцию перед ее вызовом не требуется.</span><span class="sxs-lookup"><span data-stu-id="b65b4-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="b65b4-116">Тип возвращаемого значения `T` также должен сериализоваться в JSON.</span><span class="sxs-lookup"><span data-stu-id="b65b4-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="b65b4-117">Тип `T` должен соответствовать типу .NET, который лучше всего соответствует возвращаемому типу JSON.</span><span class="sxs-lookup"><span data-stu-id="b65b4-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="b65b4-118">Функции JavaScript, возвращающие [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), вызываются с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="b65b4-119">`InvokeAsync` распаковывает обещание и возвращает значение, ожидаемое обещанием.</span><span class="sxs-lookup"><span data-stu-id="b65b4-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="b65b4-120">Для приложений Blazor Server с включенной предварительной обработкой вызвать JavaScript нельзя во время первоначальной предварительной обработки.</span><span class="sxs-lookup"><span data-stu-id="b65b4-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="b65b4-121">Вызовы взаимодействия с JavaScript должны быть отложены до тех пор, пока не будет установлено соединение с браузером.</span><span class="sxs-lookup"><span data-stu-id="b65b4-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="b65b4-122">См. раздел [Обнаружение предварительной обработки в приложении Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="b65b4-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="b65b4-123">Приведенный ниже пример основан на [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), декодере на базе JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="b65b4-124">В примере показано, как вызвать функцию JavaScript из метода C#, которая переносит требование из кода разработчика в существующий API JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="b65b4-125">Функция JavaScript принимает массив байтов из метода C#, декодирует его и возвращает компоненту текст для отображения.</span><span class="sxs-lookup"><span data-stu-id="b65b4-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="b65b4-126">Внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) предоставьте функцию JavaScript, которая использует `TextDecoder` для декодирования переданного массива и возвращения декодированного значения:</span><span class="sxs-lookup"><span data-stu-id="b65b4-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="b65b4-127">Код JavaScript, например из предыдущего примера, можно также загрузить из файла JavaScript (`.js`) со ссылкой на файл скрипта:</span><span class="sxs-lookup"><span data-stu-id="b65b4-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="b65b4-128">Приведенный ниже компонент выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b65b4-128">The following component:</span></span>

* <span data-ttu-id="b65b4-129">вызывает функцию JavaScript `convertArray` с помощью `JS` при нажатии кнопки компонента ( **`Convert Array`** );</span><span class="sxs-lookup"><span data-stu-id="b65b4-129">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="b65b4-130">после вызова функции JavaScript переданный массив преобразуется в строку.</span><span class="sxs-lookup"><span data-stu-id="b65b4-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="b65b4-131">Строка возвращается компоненту для отображения.</span><span class="sxs-lookup"><span data-stu-id="b65b4-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="b65b4-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="b65b4-132">IJSRuntime</span></span>

<span data-ttu-id="b65b4-133">Чтобы использовать абстракцию <xref:Microsoft.JSInterop.IJSRuntime>, можно выбрать один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="b65b4-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="b65b4-134">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="b65b4-135">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="b65b4-136">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="b65b4-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="b65b4-137">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в класс (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="b65b4-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="b65b4-138">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="b65b4-139">Функция вызывается с помощью метода `JS.InvokeAsync` и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="b65b4-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="b65b4-140">Для динамического создания содержимого с помощью [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) используйте атрибут `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="b65b4-141">В примере клиентского приложения, используемом в этой статье, приложению доступны две функции JavaScript, которые взаимодействуют с моделью DOM для получения вводимых пользователем данных и вывода приветственного сообщения:</span><span class="sxs-lookup"><span data-stu-id="b65b4-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="b65b4-142">`showPrompt`. Создает запрос на ввод пользователем данных (имени пользователя) и возвращает имя вызвавшему объекту.</span><span class="sxs-lookup"><span data-stu-id="b65b4-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="b65b4-143">`displayWelcome`. Назначает приветственное сообщение от вызывающего объекта объекту модели DOM со значением `id`, равным `welcome`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="b65b4-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="b65b4-145">Поместите тег `<script>` со ссылкой на файл JavaScript в файл `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="b65b4-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="b65b4-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="b65b4-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="b65b4-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="b65b4-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="b65b4-148">Не помещайте тег `<script>` в файл компонента, так как тег `<script>` не может изменяться динамически.</span><span class="sxs-lookup"><span data-stu-id="b65b4-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="b65b4-149">Методы .NET взаимодействуют с функциями JavaScript в файле `exampleJsInterop.js` путем вызова <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b65b4-150">Абстракция <xref:Microsoft.JSInterop.IJSRuntime> является асинхронной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b65b4-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="b65b4-151">Если вы используете приложение Blazor WebAssembly и вам нужно вызывать функцию JavaScript синхронно, выполните нисходящее приведение к <xref:Microsoft.JSInterop.IJSInProcessRuntime> и вызовите <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> вместо этого.</span><span class="sxs-lookup"><span data-stu-id="b65b4-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="b65b4-152">В большинстве библиотек взаимодействия с JS рекомендуется использовать асинхронные интерфейсы API, чтобы обеспечить доступность библиотек в любых сценариях.</span><span class="sxs-lookup"><span data-stu-id="b65b4-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="b65b4-153">Сведения о включении изоляции JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) см. в разделе об [изоляции JavaScript Blazor и ссылках на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="b65b4-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="b65b4-154">Пример приложения включает в себя компонент для демонстрации взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="b65b4-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="b65b4-155">Он выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b65b4-155">The component:</span></span>

* <span data-ttu-id="b65b4-156">принимает вводимые пользователем данные посредством запроса JavaScript;</span><span class="sxs-lookup"><span data-stu-id="b65b4-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="b65b4-157">возвращает текст компоненту для обработки;</span><span class="sxs-lookup"><span data-stu-id="b65b4-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="b65b4-158">вызывает еще одну функцию JavaScript, которая взаимодействует с моделью DOM для вывода приветственного сообщения.</span><span class="sxs-lookup"><span data-stu-id="b65b4-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="b65b4-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="b65b4-160">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="b65b4-161">Если функция `TriggerJsPrompt` выполняется при нажатии кнопки **`Trigger JavaScript Prompt`** , то вызывается функция JavaScript `showPrompt`, предоставленная в файле `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="b65b4-162">Функция `showPrompt` принимает введенные пользователем данные (имя пользователя), которые кодируются в HTML и возвращаются компоненту.</span><span class="sxs-lookup"><span data-stu-id="b65b4-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="b65b4-163">Компонент сохраняет имя пользователя в локальной переменной `name`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="b65b4-164">Строка, хранящаяся в `name`, включается в приветственное сообщение, которое передается в функцию JavaScript `displayWelcome`, визуализирующую приветственное сообщение в теге заголовка.</span><span class="sxs-lookup"><span data-stu-id="b65b4-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="b65b4-165">Вызов функции void JavaScript</span><span class="sxs-lookup"><span data-stu-id="b65b4-165">Call a void JavaScript function</span></span>

<span data-ttu-id="b65b4-166">Используйте <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="b65b4-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="b65b4-167">для функций JavaScript, возвращающих значение [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) или [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined);</span><span class="sxs-lookup"><span data-stu-id="b65b4-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="b65b4-168">если .NET не нужно считывать результат вызова JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="b65b4-169">Обнаружение предварительной отрисовки в приложении Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b65b4-169">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="b65b4-170">Получение ссылок на элементы</span><span class="sxs-lookup"><span data-stu-id="b65b4-170">Capture references to elements</span></span>

<span data-ttu-id="b65b4-171">В некоторых сценариях взаимодействия с JS требуются ссылки на элементы HTML.</span><span class="sxs-lookup"><span data-stu-id="b65b4-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="b65b4-172">Например, ссылка на элемент может требоваться библиотеке пользовательского интерфейса для инициализации либо необходимо вызывать командные интерфейсы API для элемента, такого как `focus` или `play`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="b65b4-173">Для получения ссылок на элементы HTML в компоненте используйте описанный ниже подход.</span><span class="sxs-lookup"><span data-stu-id="b65b4-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="b65b4-174">Добавьте атрибут `@ref` к элементу HTML.</span><span class="sxs-lookup"><span data-stu-id="b65b4-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="b65b4-175">Определите поле типа <xref:Microsoft.AspNetCore.Components.ElementReference>, имя которого совпадает со значением атрибута `@ref`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="b65b4-176">В следующем примере показано получение ссылки на элемент `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="b65b4-177">Ссылку на элемент следует использовать только для изменения содержимого пустого элемента, который не взаимодействует с Blazor.</span><span class="sxs-lookup"><span data-stu-id="b65b4-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="b65b4-178">Этот сценарий полезен, если сторонний интерфейс API предоставляет содержимое элементу.</span><span class="sxs-lookup"><span data-stu-id="b65b4-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="b65b4-179">Так как Blazor не взаимодействует с элементом, риск конфликта между представлением Blazor элемента и моделью DOM отсутствует.</span><span class="sxs-lookup"><span data-stu-id="b65b4-179">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="b65b4-180">В следующем примере изменять содержимое неупорядоченного списка (`ul`) *опасно*, так как Blazor взаимодействует с моделью DOM для заполнения элементов этого списка (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="b65b4-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="b65b4-181">Если при взаимодействии с JS содержимое элемента `MyList` изменяется и Blazor пытается применить изменения к элементу, эти изменения не будут соответствовать модели DOM.</span><span class="sxs-lookup"><span data-stu-id="b65b4-181">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="b65b4-182"><xref:Microsoft.AspNetCore.Components.ElementReference> передается в код JavaScript через JS-взаимодействие.</span><span class="sxs-lookup"><span data-stu-id="b65b4-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="b65b4-183">Код JavaScript получает экземпляр `HTMLElement`, который может использоваться с обычными интерфейсами API DOM.</span><span class="sxs-lookup"><span data-stu-id="b65b4-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="b65b4-184">Например, в приведенном ниже коде определяется метод расширения .NET, который позволяет отправить щелчок мыши в элемент.</span><span class="sxs-lookup"><span data-stu-id="b65b4-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="b65b4-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="b65b4-186">Чтобы установить фокус на элементе в коде C#, используйте [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element), который встроен в платформу Blazor и работает со ссылками на элементы.</span><span class="sxs-lookup"><span data-stu-id="b65b4-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="b65b4-187">Для вызова функции JavaScript, которая не возвращает значение, используйте метод <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b65b4-188">Следующий код активирует событие `Click` на стороне клиента, вызывая предыдущую функцию JavaScript с захваченным <xref:Microsoft.AspNetCore.Components.ElementReference>:</span><span class="sxs-lookup"><span data-stu-id="b65b4-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="b65b4-189">Чтобы использовать метод расширения, создайте статический метод расширения, который принимает экземпляр <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="b65b4-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="b65b4-190">Метод `clickElement` вызывается для объекта напрямую.</span><span class="sxs-lookup"><span data-stu-id="b65b4-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="b65b4-191">В следующем примере предполагается, что метод `TriggerClickEvent` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="b65b4-192">Переменная `exampleButton` заполняется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="b65b4-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="b65b4-193">Если в код JavaScript передается пустая ссылка <xref:Microsoft.AspNetCore.Components.ElementReference>, он получает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="b65b4-194">Для управления ссылками на элементы после завершения отрисовки компонента используйте [методы жизненного цикла компонента `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="b65b4-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="b65b4-195">При работе с универсальными типами и возврате значения используйте <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="b65b4-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="b65b4-196">Метод `GenericMethod` вызывается для объекта с типом напрямую.</span><span class="sxs-lookup"><span data-stu-id="b65b4-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="b65b4-197">В следующем примере предполагается, что метод `GenericMethod` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="b65b4-198">Ссылки на элементы между компонентами</span><span class="sxs-lookup"><span data-stu-id="b65b4-198">Reference elements across components</span></span>

<span data-ttu-id="b65b4-199"><xref:Microsoft.AspNetCore.Components.ElementReference> нельзя передать между компонентами, так как:</span><span class="sxs-lookup"><span data-stu-id="b65b4-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="b65b4-200">Экземпляр гарантированно существует только после визуализации компонента, то есть во время или после выполнения метода компонента <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="b65b4-201"><xref:Microsoft.AspNetCore.Components.ElementReference> имеет тип [`struct`](/csharp/language-reference/builtin-types/struct), который нельзя передать в качестве [параметра компонента](xref:blazor/components/index#component-parameters).</span><span class="sxs-lookup"><span data-stu-id="b65b4-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="b65b4-202">Чтобы сделать ссылку на элемент доступной для других компонентов, родительский компонент может:</span><span class="sxs-lookup"><span data-stu-id="b65b4-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="b65b4-203">разрешить дочерним компонентам регистрировать обратные вызовы;</span><span class="sxs-lookup"><span data-stu-id="b65b4-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="b65b4-204">вызывать зарегистрированные обратные вызовы во время события <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> с помощью переданной ссылки на элемент.</span><span class="sxs-lookup"><span data-stu-id="b65b4-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="b65b4-205">Такой подход позволяет дочерним компонентам взаимодействовать со ссылкой на элемент родительского компонента косвенным образом.</span><span class="sxs-lookup"><span data-stu-id="b65b4-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="b65b4-206">Этот подход показан в следующем примере Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b65b4-206">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="b65b4-207">В `<head>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="b65b4-208">В `<body>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="b65b4-209">`Pages/Index.razor` (родительский компонент):</span><span class="sxs-lookup"><span data-stu-id="b65b4-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="b65b4-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-210">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="b65b4-211">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="b65b4-212">`Shared/SurveyPrompt.razor` (дочерний компонент):</span><span class="sxs-lookup"><span data-stu-id="b65b4-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="b65b4-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="b65b4-214">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="b65b4-215">Повышение надежности вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="b65b4-215">Harden JS interop calls</span></span>

<span data-ttu-id="b65b4-216">Взаимодействие с JS может завершаться сбоем из-за ошибок сети и в этом случае должно считаться ненадежным.</span><span class="sxs-lookup"><span data-stu-id="b65b4-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="b65b4-217">По умолчанию в приложениях Blazor Server время ожидания вызовов взаимодействия с JS на сервере равно одной минуте.</span><span class="sxs-lookup"><span data-stu-id="b65b4-217">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="b65b4-218">Если для приложения допустимо более короткое время ожидания, установите его одним из указанных ниже способов.</span><span class="sxs-lookup"><span data-stu-id="b65b4-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="b65b4-219">Глобально в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="b65b4-220">Время ожидания можно указать для отдельного вызова в коде компонента:</span><span class="sxs-lookup"><span data-stu-id="b65b4-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="b65b4-221">Дополнительные сведения о нехватке ресурсов см. в статье <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="b65b4-222">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="b65b4-222">Avoid circular object references</span></span>

<span data-ttu-id="b65b4-223">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="b65b4-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="b65b4-224">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-224">.NET method calls.</span></span>
* <span data-ttu-id="b65b4-225">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="b65b4-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="b65b4-226">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="b65b4-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="b65b4-227">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="b65b4-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="b65b4-228">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="b65b4-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="b65b4-229">Изоляция Blazor JavaScript и ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="b65b4-229">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="b65b4-230">Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="b65b4-230">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="b65b4-231">Изоляция JavaScript обеспечивает следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="b65b4-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="b65b4-232">Импортированный JavaScript больше не засоряет глобальное пространство имен.</span><span class="sxs-lookup"><span data-stu-id="b65b4-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="b65b4-233">Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="b65b4-234">Например, следующий модуль JavaScript экспортирует функцию JavaScript для отображения запроса браузера:</span><span class="sxs-lookup"><span data-stu-id="b65b4-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="b65b4-235">Добавьте предыдущий модуль JavaScript в библиотеку .NET в виде статического веб-ресурса (`wwwroot/exampleJsInterop.js`), а затем импортируйте модуль в код .NET с помощью службы <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="b65b4-236">Служба внедряется как `js` (не показано) в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b65b4-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="b65b4-237">Идентификатором `import` в предыдущем примере является специальный идентификатор, используемый специально для импорта модуля JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="b65b4-238">Укажите модуль, используя путь к статическому стабильному веб-ресурсу: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="b65b4-239">Заполнитель `{LIBRARY NAME}` — это имя библиотеки.</span><span class="sxs-lookup"><span data-stu-id="b65b4-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="b65b4-240">Заполнитель `{PATH UNDER WWWROOT}` — это путь к скрипту в разделе `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="b65b4-241"><xref:Microsoft.JSInterop.IJSRuntime> импортирует модуль как `IJSObjectReference`, который представляет ссылку на объект JavaScript из кода .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="b65b4-242">Используйте `IJSObjectReference` для вызова экспортированных функций JavaScript из модуля:</span><span class="sxs-lookup"><span data-stu-id="b65b4-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="b65b4-243">`IJSInProcessObjectReference` представляет ссылку на объект JavaScript, функции которого могут вызываться синхронно.</span><span class="sxs-lookup"><span data-stu-id="b65b4-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="b65b4-244">Использование библиотек JavaScript, отображающих пользовательский интерфейс (элементы DOM)</span><span class="sxs-lookup"><span data-stu-id="b65b4-244">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="b65b4-245">Иногда может потребоваться использовать библиотеки JavaScript, которые создают видимые элементы пользовательского интерфейса в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="b65b4-245">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="b65b4-246">На первый взгляд, это может показаться затруднительным, так как система сравнения Blazor предполагает наличие контроля над деревом элементов DOM и в ней возникают ошибки, если какой-либо внешний код изменяет дерево DOM и механизм применения различий становится недействительным.</span><span class="sxs-lookup"><span data-stu-id="b65b4-246">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="b65b4-247">Это ограничение не относится лишь к Blazor.</span><span class="sxs-lookup"><span data-stu-id="b65b4-247">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="b65b4-248">Такая же проблема возникает при использовании любой платформы пользовательского интерфейса на основе сравнения.</span><span class="sxs-lookup"><span data-stu-id="b65b4-248">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="b65b4-249">К счастью, в пользовательский интерфейс компонента Blazor можно легко внедрить внешний пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="b65b4-249">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="b65b4-250">Рекомендуемым методом является создание пустого элемента кодом компонента (в файле `.razor`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-250">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="b65b4-251">С точки зрения системы сравнения Blazor элемент всегда пуст, поэтому отрисовщик не выполняет рекурсию в него и не обрабатывает его содержимое.</span><span class="sxs-lookup"><span data-stu-id="b65b4-251">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="b65b4-252">Это позволяет спокойно заполнить элемент произвольным содержимым, управляемым извне.</span><span class="sxs-lookup"><span data-stu-id="b65b4-252">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="b65b4-253">Данный принцип демонстрируется в приведенном ниже примере.</span><span class="sxs-lookup"><span data-stu-id="b65b4-253">The following example demonstrates the concept.</span></span> <span data-ttu-id="b65b4-254">В операторе `if`, когда `firstRender` имеет значение `true`, сделайте что-нибудь с `myElement`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-254">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="b65b4-255">Например, вызовите внешнюю библиотеку JavaScript для заполнения элемента.</span><span class="sxs-lookup"><span data-stu-id="b65b4-255">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="b65b4-256">Blazor оставляет содержимое элемента без изменений, пока сам компонент не будет удален.</span><span class="sxs-lookup"><span data-stu-id="b65b4-256">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="b65b4-257">При удалении компонента удаляется и все его поддерево DOM.</span><span class="sxs-lookup"><span data-stu-id="b65b4-257">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="b65b4-258">В качестве более подробного примера рассмотрим следующий компонент, который отрисовывает интерактивную карту с помощью [интерфейсов API Mapbox с открытым кодом](https://www.mapbox.com/).</span><span class="sxs-lookup"><span data-stu-id="b65b4-258">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="b65b4-259">Соответствующий модуль JavaScript, который следует поместить в `wwwroot/mapComponent.js`, выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="b65b4-259">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="b65b4-260">В предыдущем примере замените строку `{ACCESS TOKEN}` допустимым маркером доступа, который можно получить из https://account.mapbox.com.</span><span class="sxs-lookup"><span data-stu-id="b65b4-260">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="b65b4-261">Чтобы обеспечить правильный стиль, добавьте следующий тег таблицы стилей на страницу HTML узла (`index.html` или `_Host.cshtml`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-261">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="b65b4-262">В предыдущем примере создается интерактивный пользовательский интерфейс карты, в котором пользователь может выполнять следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b65b4-262">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="b65b4-263">прокручивать карту или изменять масштаб перетаскиванием;</span><span class="sxs-lookup"><span data-stu-id="b65b4-263">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="b65b4-264">переходить к заданным расположениям нажатием кнопок.</span><span class="sxs-lookup"><span data-stu-id="b65b4-264">Click buttons to jump to predefined locations.</span></span>

![Карта улиц Mapbox для Токио (Япония) с кнопками для выбора Бристоля (Великобритания) и Токио (Япония)](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="b65b4-266">Важном понимать следующие основные моменты.</span><span class="sxs-lookup"><span data-stu-id="b65b4-266">The key points to understand are:</span></span>

 * <span data-ttu-id="b65b4-267">В случае с Blazor `<div>` с `@ref="mapElement"` остается пустым.</span><span class="sxs-lookup"><span data-stu-id="b65b4-267">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="b65b4-268">Поэтому `mapbox-gl.js` может спокойно заполнять его и затем изменять его содержимое.</span><span class="sxs-lookup"><span data-stu-id="b65b4-268">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="b65b4-269">Этот метод можно использовать с любой библиотекой JavaScript, которая отрисовывает пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="b65b4-269">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="b65b4-270">В компоненты Blazor можно даже внедрять компоненты из сторонней платформы одностраничных приложений JavaScript, если они не пытаются изменить другие части страницы.</span><span class="sxs-lookup"><span data-stu-id="b65b4-270">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="b65b4-271">Ситуация, когда внешний код JavaScript изменяет элементы, которые Blazor не считает пустыми, *небезопасна*.</span><span class="sxs-lookup"><span data-stu-id="b65b4-271">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="b65b4-272">При использовании этого подхода необходимо учитывать то, как Blazor удерживает или уничтожает элементы DOM.</span><span class="sxs-lookup"><span data-stu-id="b65b4-272">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="b65b4-273">В предыдущем примере компонент безопасно обрабатывает события нажатия кнопок и обновляет существующий экземпляр карты, так как по умолчанию элементы DOM по возможности сохраняются без изменений.</span><span class="sxs-lookup"><span data-stu-id="b65b4-273">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="b65b4-274">При отрисовке списка элементов карты из цикла `@foreach` необходимо использовать `@key`, чтобы гарантировать сохранность экземпляров компонента.</span><span class="sxs-lookup"><span data-stu-id="b65b4-274">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="b65b4-275">В противном случае изменения в данных списка могут приводить к нежелательному сохранению состояния предыдущих экземпляров компонента.</span><span class="sxs-lookup"><span data-stu-id="b65b4-275">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="b65b4-276">Дополнительные сведения см. в разделе об [использовании @key для сохранения элементов и компонентов](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="b65b4-276">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="b65b4-277">Кроме того, в предыдущем примере показано, как можно инкапсулировать логику и зависимости JavaScript в модуле ES6 и динамически загружать его с помощью идентификатора `import`.</span><span class="sxs-lookup"><span data-stu-id="b65b4-277">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="b65b4-278">Дополнительные сведения см.в статье [Изоляция JavaScript и ссылки на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="b65b4-278">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="b65b4-279">Ограничения размера для вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="b65b4-279">Size limits on JS interop calls</span></span>

<span data-ttu-id="b65b4-280">При использовании Blazor WebAssembly платформа не накладывает ограничений на размер входных и выходных данных в вызовах взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="b65b4-280">In Blazor WebAssembly, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="b65b4-281">При использовании Blazor Server результат вызова взаимодействия с JS ограничен максимальным размером полезных данных, который настраивается в SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>) или имеет значение по умолчанию 32 КБ.</span><span class="sxs-lookup"><span data-stu-id="b65b4-281">In Blazor Server, the result of a JS interop call is limited by the maximum payload size enforced by SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="b65b4-282">Если приложение попытается ответить на вызов взаимодействия с JS, в котором размер полезных данных превышает значение <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>, возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="b65b4-282">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="b65b4-283">Можно увеличить предельный размер, изменив значение <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-283">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="b65b4-284">В следующем примере для размера получаемого сообщения устанавливается максимальный размер 64 КБ (64×1024×1024):</span><span class="sxs-lookup"><span data-stu-id="b65b4-284">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="b65b4-285">Увеличение предельного размера SignalR потребует больше серверных ресурсов для обработки, а также повысит уязвимость сервера к злонамеренным действиям пользователей.</span><span class="sxs-lookup"><span data-stu-id="b65b4-285">Increasing the SignalR limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="b65b4-286">Кроме того, считывание большого объема содержимого в память в виде строк или массивов байтов может привести к неудачным выделениям памяти, которые плохо обрабатываются сборщиком мусора, что дополнительно снизит производительность.</span><span class="sxs-lookup"><span data-stu-id="b65b4-286">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="b65b4-287">Чтобы считывать полезные данные большого размера, можно отправлять такое содержимое меньшими фрагментами и обрабатывать их как <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="b65b4-287">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="b65b4-288">Это удобно для чтения полезных данных большого объема в формате JSON или необработанных байтов из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-288">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="b65b4-289">Вы можете изучить отправку больших двоичных данных в Blazor Server с помощью методов, эквивалентных работе компонента `InputFile`, в [примере приложения с отправкой двоичных данных](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="b65b4-289">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="b65b4-290">При разработке кода, который передает данные большого объема между JavaScript и Blazor, учитывайте следующие рекомендации:</span><span class="sxs-lookup"><span data-stu-id="b65b4-290">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="b65b4-291">Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.</span><span class="sxs-lookup"><span data-stu-id="b65b4-291">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="b65b4-292">Не выделяйте большие объекты в коде C# и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-292">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="b65b4-293">Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.</span><span class="sxs-lookup"><span data-stu-id="b65b4-293">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="b65b4-294">Освободите занятую память при завершении или отмене процесса.</span><span class="sxs-lookup"><span data-stu-id="b65b4-294">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="b65b4-295">Применяйте следующие дополнительные требования в целях безопасности:</span><span class="sxs-lookup"><span data-stu-id="b65b4-295">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="b65b4-296">Объявите максимальный размер файла или данных, который может быть передан.</span><span class="sxs-lookup"><span data-stu-id="b65b4-296">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="b65b4-297">Объявите минимальную скорость передачи от клиента к серверу.</span><span class="sxs-lookup"><span data-stu-id="b65b4-297">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="b65b4-298">После получения данных сервером данные могут быть:</span><span class="sxs-lookup"><span data-stu-id="b65b4-298">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="b65b4-299">Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.</span><span class="sxs-lookup"><span data-stu-id="b65b4-299">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="b65b4-300">Использованы немедленно.</span><span class="sxs-lookup"><span data-stu-id="b65b4-300">Consumed immediately.</span></span> <span data-ttu-id="b65b4-301">Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.</span><span class="sxs-lookup"><span data-stu-id="b65b4-301">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="b65b4-302">Модули JS</span><span class="sxs-lookup"><span data-stu-id="b65b4-302">JS modules</span></span>

<span data-ttu-id="b65b4-303">Для изоляции JS взаимодействие с JS работает с поддержкой по умолчанию браузера для [модулей EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([спецификация ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="b65b4-303">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="b65b4-304">Демаршалированные вызовы взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="b65b4-304">Unmarshalled JS interop</span></span>

<span data-ttu-id="b65b4-305">Производительность компонентов Blazor WebAssembly может снизиться при сериализации объектов .NET для взаимодействия с JS и наличии одного из следующих условий:</span><span class="sxs-lookup"><span data-stu-id="b65b4-305">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="b65b4-306">Быстро сериализуется большой объем объектов .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-306">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="b65b4-307">Пример. Вызовы взаимодействия с JS выполняются на основе перемещения устройства ввода, например прокрутки колесика мыши.</span><span class="sxs-lookup"><span data-stu-id="b65b4-307">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="b65b4-308">Для взаимодействия с JS нужно сериализовать большие объекты .NET или много объектов .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-308">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="b65b4-309">Пример. Для вызовов взаимодействия с JS требуется сериализовать десятки файлов.</span><span class="sxs-lookup"><span data-stu-id="b65b4-309">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="b65b4-310"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> представляет ссылку на объект JavaScript, функции которого могут вызываться без дополнительных затрат, связанных с сериализацией данных .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-310"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="b65b4-311">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b65b4-311">In the following example:</span></span>

* <span data-ttu-id="b65b4-312">[Структура](/dotnet/csharp/language-reference/builtin-types/struct), содержащая строку и целое число, передается в JavaScript без сериализации.</span><span class="sxs-lookup"><span data-stu-id="b65b4-312">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="b65b4-313">Функции JavaScript обрабатывают данные и возвращают вызывающему объекту логическое значение или строку.</span><span class="sxs-lookup"><span data-stu-id="b65b4-313">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="b65b4-314">Строку JavaScript нельзя напрямую преобразовать в объект `string` .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-314">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="b65b4-315">Функция `unmarshalledFunctionReturnString` вызывает `BINDING.js_string_to_mono_string` для управления преобразованием строки JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-315">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="b65b4-316">Следующие примеры не являются типичными вариантами использования для этого сценария, так как [структура](/dotnet/csharp/language-reference/builtin-types/struct), передаваемая в JavaScript, не приводит к ухудшению производительности компонента.</span><span class="sxs-lookup"><span data-stu-id="b65b4-316">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="b65b4-317">В примере мы используем небольшой объект, только чтобы продемонстрировать концепцию передачи несериализованных данных .NET.</span><span class="sxs-lookup"><span data-stu-id="b65b4-317">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="b65b4-318">Содержимое блока `<script>` в `wwwroot/index.html` или во внешнем файле JavaScript, на который указывает ссылка `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="b65b4-318">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> <span data-ttu-id="b65b4-319">Возможно, в одном из будущих выпусков .NET. имя и поведение функции `js_string_to_mono_string` изменится либо она будет удалена.</span><span class="sxs-lookup"><span data-stu-id="b65b4-319">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="b65b4-320">Пример:</span><span class="sxs-lookup"><span data-stu-id="b65b4-320">For example:</span></span>
>
> * <span data-ttu-id="b65b4-321">Скорее всего, функция будет переименована.</span><span class="sxs-lookup"><span data-stu-id="b65b4-321">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="b65b4-322">Возможно, сама функция будет удалена, а вместо нее будет реализовано автоматическое преобразование строк платформой.</span><span class="sxs-lookup"><span data-stu-id="b65b4-322">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="b65b4-323">`Pages/UnmarshalledJSInterop.razor` (URL-адрес: `/unmarshalled-js-interop`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-323">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

<span data-ttu-id="b65b4-324">Если экземпляр `IJSUnmarshalledObjectReference` не удален в коде C#, он может быть удален в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b65b4-324">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="b65b4-325">Следующая функция `dispose` удаляет ссылку на объект при вызове из JavaScript:</span><span class="sxs-lookup"><span data-stu-id="b65b4-325">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

<span data-ttu-id="b65b4-326">Типы массивов можно преобразовать из объектов JavaScript в объекты .NET с помощью `js_typed_array_to_array`, но при этом массив JavaScript должен быть типизированным.</span><span class="sxs-lookup"><span data-stu-id="b65b4-326">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="b65b4-327">Массивы из JavaScript могут считываться в коде C# как массив объектов .NET (`object[]`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-327">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="b65b4-328">Можно преобразовать и другие типы данных, например массивы строк, но при этом нужно создать новый объект массива Mono (`mono_obj_array_new`) и задать его значение (`mono_obj_array_set`).</span><span class="sxs-lookup"><span data-stu-id="b65b4-328">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="b65b4-329">Возможно, в будущих выпусках .NET. функции JavaScript (такие как `js_typed_array_to_array`, `mono_obj_array_new` и `mono_obj_array_set`), предоставляемые платформой Blazor, будут удалены либо изменятся их имена или поведение.</span><span class="sxs-lookup"><span data-stu-id="b65b4-329">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b65b4-330">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b65b4-330">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="b65b4-331">Пример InteropComponent.razor (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="b65b4-331">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
