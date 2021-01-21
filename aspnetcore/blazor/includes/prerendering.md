<span data-ttu-id="8c76b-101">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено.</span><span class="sxs-lookup"><span data-stu-id="8c76b-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="8c76b-102">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="8c76b-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="8c76b-103">Чтобы отложить вызовы взаимодействия с JavaScript до установки подключения к браузеру, можно использовать [событие жизненного цикла компонента OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="8c76b-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="8c76b-104">Это событие вызывается только после полной отрисовки приложения и установки клиентского подключения.</span><span class="sxs-lookup"><span data-stu-id="8c76b-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="8c76b-105">Для предыдущего примера кода предоставьте функцию JavaScript `setElementText` внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="8c76b-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="8c76b-106">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="8c76b-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="8c76b-107">В предыдущем примере прямое изменение модели DOM показано только в демонстрационных целях.</span><span class="sxs-lookup"><span data-stu-id="8c76b-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="8c76b-108">В большинстве сценариев выполнять непосредственное изменение модели DOM с помощью JavaScript не рекомендуется, поскольку JavaScript может повлиять на отслеживание изменений в Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c76b-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="8c76b-109">В следующем компоненте показано, как использовать взаимодействие с JavaScript в составе логики инициализации компонента, совместимое с предварительной отрисовкой.</span><span class="sxs-lookup"><span data-stu-id="8c76b-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="8c76b-110">Компонент показывает, что обновление отрисовки можно активировать из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="8c76b-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="8c76b-111">В этом сценарии разработчику следует избегать создания бесконечного цикла.</span><span class="sxs-lookup"><span data-stu-id="8c76b-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="8c76b-112">При вызове <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> `ElementRef` используется только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, а не в предыдущем методе жизненного цикла, так как элемент JavaScript появляется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="8c76b-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="8c76b-113">Метод [StateHasChanged](xref:blazor/components/lifecycle#state-changes) вызывается для повторной отрисовки компонента с новым состоянием, полученным из вызова взаимодействия с JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8c76b-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="8c76b-114">Код не создает бесконечный цикл, так как метод `StateHasChanged` вызывается, только если `infoFromJs` имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="8c76b-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="8c76b-115">Для предыдущего примера кода предоставьте функцию JavaScript `setElementText` внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="8c76b-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="8c76b-116">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="8c76b-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="8c76b-117">В предыдущем примере прямое изменение модели DOM показано только в демонстрационных целях.</span><span class="sxs-lookup"><span data-stu-id="8c76b-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="8c76b-118">В большинстве сценариев выполнять непосредственное изменение модели DOM с помощью JavaScript не рекомендуется, поскольку JavaScript может повлиять на отслеживание изменений в Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c76b-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>