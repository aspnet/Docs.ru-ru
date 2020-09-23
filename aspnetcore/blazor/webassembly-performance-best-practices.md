---
title: Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly
author: pranavkm
description: Советы по повышению производительности приложений ASP.NET Core Blazor WebAssembly и избежанию распространенных проблем с производительностью.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 5d3cd1480dd37f437b2d6d5a89af0a842286be95
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080268"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="8d160-103">Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8d160-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="8d160-104">Автор: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)</span><span class="sxs-lookup"><span data-stu-id="8d160-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="8d160-105">В этой статье приводятся рекомендации по обеспечению высокой производительности ASP.NET Core Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8d160-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="8d160-106">Устранение лишних отрисовок компонентов</span><span class="sxs-lookup"><span data-stu-id="8d160-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="8d160-107">Алгоритм сравнения Blazor позволяет избежать повторной отрисовки компонентов, которые не изменялись.</span><span class="sxs-lookup"><span data-stu-id="8d160-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="8d160-108">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> для точного управления отрисовкой компонентов.</span><span class="sxs-lookup"><span data-stu-id="8d160-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="8d160-109">При создании компонента, который используется только в пользовательском интерфейсе и не изменяется после первоначальной отрисовки, настройте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> так, чтобы он возвращал значение `false`:</span><span class="sxs-lookup"><span data-stu-id="8d160-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="8d160-110">Большинству приложений не требуется детализированный контроль, но метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> можно использовать для выборочной отрисовки компонентов, отвечающих на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="8d160-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="8d160-111">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> также может потребоваться в сценариях с отрисовкой большого числа компонентов.</span><span class="sxs-lookup"><span data-stu-id="8d160-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="8d160-112">Например, рассмотрим сетку, в которой использование <xref:Microsoft.AspNetCore.Components.EventCallback> в одном компоненте одной ячейки сетки вызывает метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для всей сетки.</span><span class="sxs-lookup"><span data-stu-id="8d160-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="8d160-113">При вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> повторно отрисовываются все дочерние компоненты.</span><span class="sxs-lookup"><span data-stu-id="8d160-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="8d160-114">Если нужно повторно отрисовать только несколько ячеек, используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы не снижать производительность из-за ненужных операций.</span><span class="sxs-lookup"><span data-stu-id="8d160-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="8d160-115">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="8d160-115">In the following example:</span></span>

* <span data-ttu-id="8d160-116">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> переопределяется, и ему присваивается значение поля <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, которое при загрузке компонента изначально имеет значение `false`.</span><span class="sxs-lookup"><span data-stu-id="8d160-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="8d160-117">При нажатии кнопки методу <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> присваивается значение `true`, что приводит к повторной отрисовке компонента с обновленным `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="8d160-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="8d160-118">Сразу после повторной отрисовки <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> снова устанавливает для <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> значение `false`, чтобы предотвратить дальнейшую повторную отрисовку до тех пор, пока кнопка не будет нажата еще раз.</span><span class="sxs-lookup"><span data-stu-id="8d160-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="8d160-119">Для получения дополнительной информации см. <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="8d160-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="8d160-120">Виртуализация повторно используемых фрагментов</span><span class="sxs-lookup"><span data-stu-id="8d160-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="8d160-121">Компоненты обеспечивают удобный подход для создания многократно используемых фрагментов кода и разметки.</span><span class="sxs-lookup"><span data-stu-id="8d160-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="8d160-122">Как правило, рекомендуется создавать отдельные компоненты, которые наилучшим образом согласовываются с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="8d160-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="8d160-123">Одним из недостатков является то, что каждый дополнительный дочерний компонент увеличивает общее время, необходимое для отрисовки родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="8d160-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="8d160-124">Для большинства приложений этими дополнительными накладными расходами можно пренебречь.</span><span class="sxs-lookup"><span data-stu-id="8d160-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="8d160-125">Однако в случае с приложениями, которые создают большое количество компонентов, следует предусмотреть стратегии по снижению затрат на обработку, например путем ограничения количества отрисовываемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="8d160-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="8d160-126">Так, отрисовка сетки или списка с сотнями строк, содержащих компоненты, является ресурсоемкой задачей.</span><span class="sxs-lookup"><span data-stu-id="8d160-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="8d160-127">Вы можете виртуализировать макет сетки или списка, чтобы в любой момент времени отрисовывалось только подмножество компонентов.</span><span class="sxs-lookup"><span data-stu-id="8d160-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="8d160-128">Примером отрисовки подмножества компонентов могут служить следующие компоненты в [примере приложения `Virtualization` (в репозитории GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="8d160-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="8d160-129">Компонент `Virtualize` ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): компонент на C#, который реализует <xref:Microsoft.AspNetCore.Components.ComponentBase> для отрисовки набора строк данных по погоде при выполнении прокрутки пользователем;</span><span class="sxs-lookup"><span data-stu-id="8d160-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="8d160-130">Компонент `FetchData` ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): использует компонент `Virtualize` для вывода 25 строк данных по погоде за раз.</span><span class="sxs-lookup"><span data-stu-id="8d160-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="8d160-131">Избегайте использования взаимодействия с JavaScript для маршалирования данных</span><span class="sxs-lookup"><span data-stu-id="8d160-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="8d160-132">В Blazor WebAssembly вызов взаимодействия с JavaScript (JS) должен пересекать границу между WebAssembly и JS.</span><span class="sxs-lookup"><span data-stu-id="8d160-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="8d160-133">Сериализация и десериализация содержимого в двух контекстах вызывают дополнительные накладные расходы на обработку в приложении.</span><span class="sxs-lookup"><span data-stu-id="8d160-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="8d160-134">Частые вызовы взаимодействия с JS часто негативно влияют на производительность.</span><span class="sxs-lookup"><span data-stu-id="8d160-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="8d160-135">Чтобы уменьшить объем маршалирования данных через границу, определите, может ли приложение объединить множество небольших фрагментов полезных данных в один большой фрагмент. Это позволит избежать частых переключений контекста между WebAssembly и JS.</span><span class="sxs-lookup"><span data-stu-id="8d160-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="8d160-136">Использование System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="8d160-136">Use System.Text.Json</span></span>

<span data-ttu-id="8d160-137">Реализация взаимодействия с JS в Blazor основана на <xref:System.Text.Json>, высокопроизводительной библиотеке сериализации JSON, занимающей мало места в памяти.</span><span class="sxs-lookup"><span data-stu-id="8d160-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="8d160-138">Использование <xref:System.Text.Json> не приводит к увеличению размера полезных данных в приложении, в отличие от добавления одной или нескольких альтернативных библиотек JSON.</span><span class="sxs-lookup"><span data-stu-id="8d160-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="8d160-139">Руководство по миграции см. в статье [Миграция с `Newtonsoft.Json` на `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="8d160-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="8d160-140">Использование синхронных и немаршалируемых интерфейсов API взаимодействия с JS, где они применимы</span><span class="sxs-lookup"><span data-stu-id="8d160-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="8d160-141">Blazor WebAssembly предлагает две дополнительные версии <xref:Microsoft.JSInterop.IJSRuntime>, помимо версии, доступной для приложений Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="8d160-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="8d160-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> позволяет синхронно совершать вызовы взаимодействия с JS, что требует меньше ресурсов, чем асинхронные вызовы:</span><span class="sxs-lookup"><span data-stu-id="8d160-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="8d160-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает немаршалируемые вызовы взаимодействия с JS:</span><span class="sxs-lookup"><span data-stu-id="8d160-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="8d160-144">Хотя использование <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает наименьшие издержки по сравнению с другими подходами к взаимодействию с JS, интерфейсы API JavaScript, необходимые для взаимодействия с этими интерфейсами API, в настоящее время не документированы и могут подвернуться критическим изменениям в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="8d160-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="8d160-145">Уменьшение размера приложения</span><span class="sxs-lookup"><span data-stu-id="8d160-145">Reduce app size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="8d160-146">Обрезка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="8d160-146">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="8d160-147">[Обрезка неиспользуемых сборок в приложении Blazor WebAssembly](xref:blazor/host-and-deploy/configure-trimmer) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="8d160-147">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="8d160-148">По умолчанию средство обрезки выполняется при публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="8d160-148">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="8d160-149">Чтобы воспользоваться обрезкой, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="8d160-149">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="8d160-150">Компоновка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="8d160-150">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="8d160-151">[Компоновка приложения Blazor WebAssembly](xref:blazor/host-and-deploy/configure-linker) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="8d160-151">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="8d160-152">По умолчанию компоновщик промежуточного языка (IL) включается только при сборке в конфигурации `Release`.</span><span class="sxs-lookup"><span data-stu-id="8d160-152">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="8d160-153">Чтобы воспользоваться этой возможностью, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="8d160-153">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="8d160-154">Сборки с отложенной загрузкой</span><span class="sxs-lookup"><span data-stu-id="8d160-154">Lazy load assemblies</span></span>

<span data-ttu-id="8d160-155">Загрузка сборок во время выполнения на том этапе, когда они необходимы для маршрута.</span><span class="sxs-lookup"><span data-stu-id="8d160-155">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="8d160-156">Для получения дополнительной информации см. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="8d160-156">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="8d160-157">Сжатие</span><span class="sxs-lookup"><span data-stu-id="8d160-157">Compression</span></span>

<span data-ttu-id="8d160-158">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="8d160-158">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="8d160-159">Blazor использует сервер для согласования содержимого и предоставления статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="8d160-159">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="8d160-160">После развертывания приложения убедитесь в том, что приложение предоставляет сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="8d160-160">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="8d160-161">В браузере откройте вкладку "Сеть" в Средствах для разработчиков и убедитесь в том, что файлы предоставляются с `Content-Encoding: br` или `Content-Encoding: gz`.</span><span class="sxs-lookup"><span data-stu-id="8d160-161">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="8d160-162">Если узел не предоставляет сжатые файлы, выполните инструкции в разделе <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="8d160-162">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="8d160-163">Отключение неиспользуемых функций</span><span class="sxs-lookup"><span data-stu-id="8d160-163">Disable unused features</span></span>

<span data-ttu-id="8d160-164">Среда выполнения Blazor WebAssembly включает в себя следующие функции .NET, которые можно отключить, если они не требуются приложению, для уменьшения размера полезных данных:</span><span class="sxs-lookup"><span data-stu-id="8d160-164">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="8d160-165">Для обеспечения правильности сведений о часовом поясе включается файл данных.</span><span class="sxs-lookup"><span data-stu-id="8d160-165">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="8d160-166">Если приложению не нужна эта функция, ее можно отключить, присвоив свойству MSBuild `BlazorEnableTimeZoneSupport` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="8d160-166">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="8d160-167">По умолчанию Blazor WebAssembly содержит ресурсы глобализации, необходимые для отображения значений, таких как даты и денежные единицы, на языке пользователя и с его региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="8d160-167">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="8d160-168">Если приложению не требуется локализация, можно [настроить поддержку инвариантных языка и региональных параметров](xref:blazor/globalization-localization), основанных на языке и региональных параметрах `en-US`:</span><span class="sxs-lookup"><span data-stu-id="8d160-168">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="8d160-169">Для правильной работы таких интерфейсов API, как <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>, включаются сведения о параметрах сортировки.</span><span class="sxs-lookup"><span data-stu-id="8d160-169">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="8d160-170">Если вы уверены, что приложению не нужны сведения о параметрах сортировки, эту функцию можно отключить, присвоив свойству MSBuild `BlazorWebAssemblyPreserveCollationData` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="8d160-170">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
