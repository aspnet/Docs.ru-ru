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
ms.openlocfilehash: c4ab82490a2b16cdb5fef691cfb1ca038f1dca00
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570072"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>Вызов методов .NET из функций JavaScript в ASP.NET Core Blazor

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Шашикант Рудравади](http://wisne.co) (Shashikant Rudrawadi) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript. Такой подход называется *взаимодействием с JavaScript* (*JS*).

В этой статье рассматривается вызов методов .NET из JavaScript. Сведения о том, как вызывать функции JavaScript из .NET, см. в разделе <xref:blazor/call-javascript-from-dotnet>.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Вызов статического метода .NET

Чтобы вызвать статический метод .NET из JavaScript, используйте функции `DotNet.invokeMethod` или `DotNet.invokeMethodAsync`. Передайте идентификатор статического метода, который необходимо вызвать, имя сборки, содержащей функцию, и любые аргументы. Асинхронная версия является предпочтительной для поддержки сценариев Blazor Server. Метод .NET должен быть открытым, статическим и иметь атрибут [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute). Вызов открытых универсальных методов в настоящее время не поддерживается.

Пример приложения включает метод C#, возвращающий массив `int`. К методу применяется атрибут [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute).

`Pages/JsInterop.razor`.

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

JavaScript, предоставляемый клиенту, вызывает метод C# .NET.

`wwwroot/exampleJsInterop.js`.

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Если нажата кнопка **`Trigger .NET static method ReturnArrayAsync`** , проверьте выходные данные консоли в средствах веб-разработчика браузера.

Выходные данные консоли:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Четвертое значение массива помещается в массив (`data.push(4);`), возвращаемый методом `ReturnArrayAsync`.

По умолчанию идентификатором метода является имя метода, но можно указать другой идентификатор с помощью атрибута конструктора [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

В файле JavaScript на стороне клиента:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

## <a name="instance-method-call"></a>Вызов метода экземпляра

Кроме того, из JavaScript можно вызывать методы экземпляра .NET. Для этого необходимо выполнить следующие действия.

* Передайте в JavaScript экземпляр .NET по ссылке:
  * Выполните статический вызов <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.
  * Заключите экземпляр в экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference> и вызовите метод <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> в экземпляре <xref:Microsoft.JSInterop.DotNetObjectReference>. Удалите объекты <xref:Microsoft.JSInterop.DotNetObjectReference> (пример приведен далее в этом разделе).
* Вызовите методы экземпляра .NET в экземпляре с помощью функций `invokeMethod` или `invokeMethodAsync`. Экземпляр .NET можно также передать в качестве аргумента при вызове других методов .NET из JavaScript.

> [!NOTE]
> Пример приложения записывает сообщения в консоль на стороне клиента. В следующих примерах, продемонстрированных в учебном приложении, изучите выходные данные консоли браузера в средствах разработчика браузера.

Если нажата кнопка **`Trigger .NET instance method HelloHelper.SayHello`** , вызывается `ExampleJsInterop.CallHelloHelperSayHello` и в метод передается имя `Blazor`.

`Pages/JsInterop.razor`.

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

Метод `CallHelloHelperSayHello` вызывает функцию JavaScript `sayHello` с новым экземпляром `HelloHelper`.

`JsInteropClasses/ExampleJsInterop.cs`.

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`.

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Имя передается в конструктор `HelloHelper`, который задает свойство `HelloHelper.Name`. При выполнении функции JavaScript `sayHello` метод `HelloHelper.SayHello` возвращает сообщение `Hello, {Name}!`, которое записывается в консоль функцией JavaScript.

`JsInteropClasses/HelloHelper.cs`.

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Выходные данные консоли в средствах веб-разработчика браузера:

```console
Hello, Blazor!
```

Чтобы избежать утечки памяти и разрешить сборку мусора для компонента, который создает метод <xref:Microsoft.JSInterop.DotNetObjectReference>, используйте один из следующих подходов.

* Удалите объект в классе, который создал экземпляр <xref:Microsoft.JSInterop.DotNetObjectReference>.

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

  Предыдущий шаблон, показанный в классе `ExampleJsInterop`, также можно реализовать в компоненте:

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
  
  Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

* Если компонент или класс не удаляет <xref:Microsoft.JSInterop.DotNetObjectReference>, удалите объект в клиенте, вызвав `.dispose()`.

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Вызов метода экземпляра компонента

Порядок вызова методов .NET компонента

* Используйте функцию `invokeMethod` или `invokeMethodAsync`, чтобы вызвать статический метод для компонента.
* Статический метод компонента создает программу-оболочку вызова метода экземпляра в виде вызванного <xref:System.Action>.

> [!NOTE]
> Для приложений Blazor Server, в которых несколько пользователей могут одновременно использовать один и тот же компонент, используйте вспомогательный класс для вызова методов экземпляра.
>
> Дополнительные сведения см. в разделе [Вспомогательный класс для метода экземпляра компонента](#component-instance-method-helper-class).

В JavaScript на стороне клиента:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

`Pages/JSInteropComponent.razor`:

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

Для передачи аргументов в метод экземпляра выполните следующие действия.

* Добавьте параметры в вызов метода JS. В приведенном ниже примере в метод передается имя. При необходимости в список можно добавить дополнительные параметры.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

* Укажите правильные типы для параметров <xref:System.Action>. Укажите список параметров для методов C#. Вызовите <xref:System.Action> (`UpdateMessage`) с параметрами (`action.Invoke(name)`).

  `Pages/JSInteropComponent.razor`:

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

  Вывод `message` при нажатии кнопки **Call JS Method**:

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Вспомогательный класс для метода экземпляра компонента

Вспомогательный класс используется для вызова метода экземпляра в качестве <xref:System.Action>. Вспомогательные классы полезны в следующих случаях.

* На одной странице отображается несколько компонентов одного типа.
* Используется приложение Blazor Server, в котором несколько пользователей могут одновременно использовать один компонент.

В следующем примере:

* Компонент `JSInteropExample` содержит несколько компонентов `ListItem`.
* Каждый компонент `ListItem` состоит из сообщения и кнопки.
* При выборе кнопки компонента `ListItem` метод `UpdateMessage` `ListItem`изменяет текст элемента списка и скрывает кнопку.

`MessageUpdateInvokeHelper.cs`:

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

В JavaScript на стороне клиента:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

`Shared/ListItem.razor`:

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

`Pages/JSInteropExample.razor`:

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Исключение циклических ссылок на объекты

Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:

* вызовов метода .NET.
* Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.

Дополнительные сведения см. на следующих страницах:

* [Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a>Модули JS

Для изоляции JS взаимодействие с JS работает с поддержкой по умолчанию браузера для [модулей EcmaScript (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([спецификация ECMAScript](https://tc39.es/ecma262/#sec-modules)).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/call-javascript-from-dotnet>
* [Пример `InteropComponent.razor` (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
