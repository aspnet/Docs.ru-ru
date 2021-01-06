---
title: Вызов методов .NET из функций JavaScript в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать методы .NET из функций JavaScript в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: c1a97919cb41f42a93f28d9b5f1ecf6bd3e64da0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97592860"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="63c55-103">Вызов методов .NET из функций JavaScript в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="63c55-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="63c55-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Шашикант Рудравади](http://wisne.co) (Shashikant Rudrawadi) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="63c55-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="63c55-105">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="63c55-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="63c55-106">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="63c55-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="63c55-107">В этой статье рассматривается вызов методов .NET из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="63c55-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="63c55-108">Сведения о том, как вызывать функции JavaScript из .NET, см. в разделе <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="63c55-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="63c55-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63c55-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="63c55-110">Вызов статического метода .NET</span><span class="sxs-lookup"><span data-stu-id="63c55-110">Static .NET method call</span></span>

<span data-ttu-id="63c55-111">Чтобы вызвать статический метод .NET из JavaScript, используйте функции `DotNet.invokeMethod` или `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="63c55-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="63c55-112">Передайте идентификатор статического метода, который необходимо вызвать, имя сборки, содержащей функцию, и любые аргументы.</span><span class="sxs-lookup"><span data-stu-id="63c55-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="63c55-113">Асинхронная версия является предпочтительной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="63c55-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="63c55-114">Метод .NET должен быть открытым, статическим и иметь атрибут [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="63c55-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="63c55-115">Вызов открытых универсальных методов в настоящее время не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="63c55-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="63c55-116">Пример приложения включает метод C#, возвращающий массив `int`.</span><span class="sxs-lookup"><span data-stu-id="63c55-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="63c55-117">К методу применяется атрибут [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).</span><span class="sxs-lookup"><span data-stu-id="63c55-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="63c55-118">`Pages/JsInterop.razor`.</span><span class="sxs-lookup"><span data-stu-id="63c55-118">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="63c55-119">JavaScript, предоставляемый клиенту, вызывает метод C# .NET.</span><span class="sxs-lookup"><span data-stu-id="63c55-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="63c55-120">`wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="63c55-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="63c55-121">Если нажата кнопка **`Trigger .NET static method ReturnArrayAsync`** , проверьте выходные данные консоли в средствах веб-разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="63c55-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="63c55-122">Выходные данные консоли:</span><span class="sxs-lookup"><span data-stu-id="63c55-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="63c55-123">Четвертое значение массива помещается в массив (`data.push(4);`), возвращаемый методом `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="63c55-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="63c55-124">По умолчанию идентификатором метода является имя метода, но можно указать другой идентификатор с помощью атрибута конструктора [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):</span><span class="sxs-lookup"><span data-stu-id="63c55-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="63c55-125">В файле JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="63c55-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="63c55-126">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="63c55-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="63c55-127">Вызов метода экземпляра</span><span class="sxs-lookup"><span data-stu-id="63c55-127">Instance method call</span></span>

<span data-ttu-id="63c55-128">Кроме того, из JavaScript можно вызывать методы экземпляра .NET.</span><span class="sxs-lookup"><span data-stu-id="63c55-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="63c55-129">Для этого необходимо выполнить следующие действия.</span><span class="sxs-lookup"><span data-stu-id="63c55-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="63c55-130">Передайте в JavaScript экземпляр .NET по ссылке:</span><span class="sxs-lookup"><span data-stu-id="63c55-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="63c55-131">Выполните статический вызов <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="63c55-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="63c55-132">Заключите экземпляр в экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference> и вызовите метод <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> в экземпляре <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="63c55-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="63c55-133">Удалите объекты <xref:Microsoft.JSInterop.DotNetObjectReference> (пример приведен далее в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="63c55-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="63c55-134">Вызовите методы экземпляра .NET в экземпляре с помощью функций `invokeMethod` или `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="63c55-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="63c55-135">Экземпляр .NET можно также передать в качестве аргумента при вызове других методов .NET из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="63c55-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="63c55-136">Пример приложения записывает сообщения в консоль на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="63c55-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="63c55-137">В следующих примерах, продемонстрированных в учебном приложении, изучите выходные данные консоли браузера в средствах разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="63c55-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="63c55-138">Если нажата кнопка **`Trigger .NET instance method HelloHelper.SayHello`** , вызывается `ExampleJsInterop.CallHelloHelperSayHello` и в метод передается имя `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="63c55-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="63c55-139">`Pages/JsInterop.razor`.</span><span class="sxs-lookup"><span data-stu-id="63c55-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="63c55-140">Метод `CallHelloHelperSayHello` вызывает функцию JavaScript `sayHello` с новым экземпляром `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="63c55-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="63c55-141">`JsInteropClasses/ExampleJsInterop.cs`.</span><span class="sxs-lookup"><span data-stu-id="63c55-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="63c55-142">`wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="63c55-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="63c55-143">Имя передается в конструктор `HelloHelper`, который задает свойство `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="63c55-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="63c55-144">При выполнении функции JavaScript `sayHello` метод `HelloHelper.SayHello` возвращает сообщение `Hello, {Name}!`, которое записывается в консоль функцией JavaScript.</span><span class="sxs-lookup"><span data-stu-id="63c55-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="63c55-145">`JsInteropClasses/HelloHelper.cs`.</span><span class="sxs-lookup"><span data-stu-id="63c55-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="63c55-146">Выходные данные консоли в средствах веб-разработчика браузера:</span><span class="sxs-lookup"><span data-stu-id="63c55-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="63c55-147">Чтобы избежать утечки памяти и разрешить сборку мусора для компонента, который создает метод <xref:Microsoft.JSInterop.DotNetObjectReference>, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="63c55-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="63c55-148">Удалите объект в классе, который создал экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference>.</span><span class="sxs-lookup"><span data-stu-id="63c55-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="63c55-149">Предыдущий шаблон, показанный в классе `ExampleJsInterop`, также можно реализовать в компоненте:</span><span class="sxs-lookup"><span data-stu-id="63c55-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="63c55-150">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="63c55-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="63c55-151">Если компонент или класс не удаляет <xref:Microsoft.JSInterop.DotNetObjectReference>, удалите объект в клиенте, вызвав `.dispose()`.</span><span class="sxs-lookup"><span data-stu-id="63c55-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="63c55-152">Вызов метода экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="63c55-152">Component instance method call</span></span>

<span data-ttu-id="63c55-153">Порядок вызова методов .NET компонента</span><span class="sxs-lookup"><span data-stu-id="63c55-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="63c55-154">Используйте функцию `invokeMethod` или `invokeMethodAsync`, чтобы вызвать статический метод для компонента.</span><span class="sxs-lookup"><span data-stu-id="63c55-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="63c55-155">Статический метод компонента создает программу-оболочку вызова метода экземпляра в виде вызванного <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="63c55-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="63c55-156">Для приложений Blazor Server, в которых несколько пользователей могут одновременно использовать один и тот же компонент, используйте вспомогательный класс для вызова методов экземпляра.</span><span class="sxs-lookup"><span data-stu-id="63c55-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="63c55-157">Дополнительные сведения см. в разделе [Вспомогательный класс для метода экземпляра компонента](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="63c55-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="63c55-158">В JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="63c55-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="63c55-159">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="63c55-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="63c55-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="63c55-160">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="63c55-161">Для передачи аргументов в метод экземпляра выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="63c55-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="63c55-162">Добавьте параметры в вызов метода JS.</span><span class="sxs-lookup"><span data-stu-id="63c55-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="63c55-163">В приведенном ниже примере в метод передается имя.</span><span class="sxs-lookup"><span data-stu-id="63c55-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="63c55-164">При необходимости в список можно добавить дополнительные параметры.</span><span class="sxs-lookup"><span data-stu-id="63c55-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="63c55-165">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="63c55-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="63c55-166">Укажите правильные типы для параметров <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="63c55-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="63c55-167">Укажите список параметров для методов C#.</span><span class="sxs-lookup"><span data-stu-id="63c55-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="63c55-168">Вызовите <xref:System.Action> (`UpdateMessage`) с параметрами (`action.Invoke(name)`).</span><span class="sxs-lookup"><span data-stu-id="63c55-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="63c55-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="63c55-169">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="63c55-170">Вывод `message` при нажатии кнопки **Call JS Method**:</span><span class="sxs-lookup"><span data-stu-id="63c55-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="63c55-171">Вспомогательный класс для метода экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="63c55-171">Component instance method helper class</span></span>

<span data-ttu-id="63c55-172">Вспомогательный класс используется для вызова метода экземпляра в качестве <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="63c55-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="63c55-173">Вспомогательные классы полезны в следующих случаях.</span><span class="sxs-lookup"><span data-stu-id="63c55-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="63c55-174">На одной странице отображается несколько компонентов одного типа.</span><span class="sxs-lookup"><span data-stu-id="63c55-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="63c55-175">Используется приложение Blazor Server, в котором несколько пользователей могут одновременно использовать один компонент.</span><span class="sxs-lookup"><span data-stu-id="63c55-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="63c55-176">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="63c55-176">In the following example:</span></span>

* <span data-ttu-id="63c55-177">Компонент `JSInteropExample` содержит несколько компонентов `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="63c55-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="63c55-178">Каждый компонент `ListItem` состоит из сообщения и кнопки.</span><span class="sxs-lookup"><span data-stu-id="63c55-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="63c55-179">При выборе кнопки компонента `ListItem` метод `UpdateMessage` `ListItem`изменяет текст элемента списка и скрывает кнопку.</span><span class="sxs-lookup"><span data-stu-id="63c55-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="63c55-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="63c55-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="63c55-181">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="63c55-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="63c55-182">В JavaScript на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="63c55-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="63c55-183">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="63c55-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="63c55-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="63c55-184">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="63c55-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="63c55-185">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="63c55-186">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="63c55-186">Avoid circular object references</span></span>

<span data-ttu-id="63c55-187">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="63c55-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="63c55-188">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="63c55-188">.NET method calls.</span></span>
* <span data-ttu-id="63c55-189">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="63c55-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="63c55-190">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="63c55-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="63c55-191">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="63c55-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="63c55-192">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="63c55-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a><span data-ttu-id="63c55-193">Модули JS</span><span class="sxs-lookup"><span data-stu-id="63c55-193">JS modules</span></span>

<span data-ttu-id="63c55-194">Для изоляции JS взаимодействие с JS работает с поддержкой по умолчанию браузера для [модулей EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([спецификация ECMAScript](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="63c55-194">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63c55-195">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="63c55-195">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="63c55-196">Пример `InteropComponent.razor` (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="63c55-196">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
