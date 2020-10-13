---
title: Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать функции JavaScript из методов .NET в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: d36140067ba6e75f2d00cb86ea488e40d28bd86f
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762169"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript. Такой подход называется *взаимодействием с JavaScript* (*JS*).

В этой статье рассматривается вызов функций JavaScript из .NET. Сведения о том, как вызывать методы .NET из JavaScript, см. в статье <xref:blazor/call-dotnet-from-javascript>.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Для вызова JavaScript из .NET используйте абстракцию <xref:Microsoft.JSInterop.IJSRuntime>. Чтобы выполнять вызовы взаимодействия с JS, внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> принимает идентификатор функции JavaScript, которую нужно вызвать, вместе с любым числом аргументов, сериализуемых в JSON. Идентификатор функции задается относительно глобальной области (`window`). Если нужно вызвать функцию `window.someScope.someFunction`, идентификатором будет `someScope.someFunction`. Регистрировать функцию перед ее вызовом не требуется. Тип возвращаемого значения `T` также должен сериализоваться в JSON. Тип `T` должен соответствовать типу .NET, который лучше всего соответствует возвращаемому типу JSON.

Функции JavaScript, возвращающие [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), вызываются с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>. `InvokeAsync` распаковывает обещание и возвращает значение, ожидаемое обещанием.

Для приложений Blazor Server с включенной предварительной обработкой вызвать JavaScript нельзя во время первоначальной предварительной обработки. Вызовы взаимодействия с JavaScript должны быть отложены до тех пор, пока не будет установлено соединение с браузером. См. раздел [Обнаружение предварительной обработки в приложении Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).

Приведенный ниже пример основан на [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), декодере на базе JavaScript. В примере показано, как вызвать функцию JavaScript из метода C#, которая переносит требование из кода разработчика в существующий API JavaScript. Функция JavaScript принимает массив байтов из метода C#, декодирует его и возвращает компоненту текст для отображения.

Внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) предоставьте функцию JavaScript, которая использует `TextDecoder` для декодирования переданного массива и возвращения декодированного значения:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Код JavaScript, например из предыдущего примера, можно также загрузить из файла JavaScript (`.js`) со ссылкой на файл скрипта:

```html
<script src="exampleJsInterop.js"></script>
```

Приведенный ниже компонент выполняет следующие действия:

* вызывает функцию JavaScript `convertArray` с помощью `JSRuntime` при нажатии кнопки компонента ( **`Convert Array`** );
* после вызова функции JavaScript переданный массив преобразуется в строку. Строка возвращается компоненту для отображения.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Чтобы использовать абстракцию <xref:Microsoft.JSInterop.IJSRuntime>, можно выбрать один из описанных ниже подходов.

* Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент Razor (`.razor`).

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`. Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в класс (`.cs`):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`. Функция вызывается с помощью метода `JSRuntime.InvokeAsync` и возвращает значение:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Для динамического создания содержимого с помощью [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) используйте атрибут `[Inject]`:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

В примере клиентского приложения, используемом в этой статье, приложению доступны две функции JavaScript, которые взаимодействуют с моделью DOM для получения вводимых пользователем данных и вывода приветственного сообщения:

* `showPrompt`. Создает запрос на ввод пользователем данных (имени пользователя) и возвращает имя вызвавшему объекту.
* `displayWelcome`. Назначает приветственное сообщение от вызывающего объекта объекту модели DOM со значением `id`, равным `welcome`.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Поместите тег `<script>` со ссылкой на файл JavaScript в файл `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Не помещайте тег `<script>` в файл компонента, так как тег `<script>` не может изменяться динамически.

Методы .NET взаимодействуют с функциями JavaScript в файле `exampleJsInterop.js` путем вызова <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.

Абстракция <xref:Microsoft.JSInterop.IJSRuntime> является асинхронной для поддержки сценариев Blazor Server. Если вы используете приложение Blazor WebAssembly и вам нужно вызывать функцию JavaScript синхронно, выполните нисходящее приведение к <xref:Microsoft.JSInterop.IJSInProcessRuntime> и вызовите <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> вместо этого. В большинстве библиотек взаимодействия с JS рекомендуется использовать асинхронные интерфейсы API, чтобы обеспечить доступность библиотек в любых сценариях.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Сведения о включении изоляции JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) см. в разделе об [изоляции JavaScript Blazor и ссылках на объекты](#blazor-javascript-isolation-and-object-references).

::: moniker-end

Пример приложения включает в себя компонент для демонстрации взаимодействия с JS. Он выполняет следующие действия:

* принимает вводимые пользователем данные посредством запроса JavaScript;
* возвращает текст компоненту для обработки;
* вызывает еще одну функцию JavaScript, которая взаимодействует с моделью DOM для вывода приветственного сообщения.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

1. Если функция `TriggerJsPrompt` выполняется при нажатии кнопки **`Trigger JavaScript Prompt`** , то вызывается функция JavaScript `showPrompt`, предоставленная в файле `wwwroot/exampleJsInterop.js`.
1. Функция `showPrompt` принимает введенные пользователем данные (имя пользователя), которые кодируются в HTML и возвращаются компоненту. Компонент сохраняет имя пользователя в локальной переменной `name`.
1. Строка, хранящаяся в `name`, включается в приветственное сообщение, которое передается в функцию JavaScript `displayWelcome`, визуализирующую приветственное сообщение в теге заголовка.

## <a name="call-a-void-javascript-function"></a>Вызов функции void JavaScript

Функции JavaScript, возвращающие значение [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) или [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined), вызываются с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Обнаружение предварительной отрисовки в приложении Blazor Server
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Получение ссылок на элементы

В некоторых сценариях взаимодействия с JS требуются ссылки на элементы HTML. Например, ссылка на элемент может требоваться библиотеке пользовательского интерфейса для инициализации либо необходимо вызывать командные интерфейсы API для элемента, такого как `focus` или `play`.

Для получения ссылок на элементы HTML в компоненте используйте описанный ниже подход.

* Добавьте атрибут `@ref` к элементу HTML.
* Определите поле типа <xref:Microsoft.AspNetCore.Components.ElementReference>, имя которого совпадает со значением атрибута `@ref`.

В следующем примере показано получение ссылки на элемент `username` `<input>`:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Ссылку на элемент следует использовать только для изменения содержимого пустого элемента, который не взаимодействует с Blazor. Этот сценарий полезен, если сторонний интерфейс API предоставляет содержимое элементу. Так как Blazor не взаимодействует с элементом, риск конфликта между представлением Blazor элемента и моделью DOM отсутствует.
>
> В следующем примере изменять содержимое неупорядоченного списка (`ul`) *опасно*, так как Blazor взаимодействует с моделью DOM для заполнения элементов этого списка (`<li>`):
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
> Если при взаимодействии с JS содержимое элемента `MyList` изменяется и Blazor пытается применить изменения к элементу, эти изменения не будут соответствовать модели DOM.

В случае с кодом .NET <xref:Microsoft.AspNetCore.Components.ElementReference> является непрозрачным дескриптором. *Единственное*, что можно сделать с <xref:Microsoft.AspNetCore.Components.ElementReference>, — передать в код JavaScript посредством взаимодействия с JS. При этом код на стороне JavaScript получает экземпляр `HTMLElement`, который может использоваться с обычными интерфейсами API DOM.

Например, в приведенном ниже коде определяется метод расширения .NET, который позволяет установить фокус на элемент.

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Для вызова функции JavaScript, которая не возвращает значение, используйте метод <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>. Следующий код устанавливает фокус на элементе для ввода имени пользователя, вызывая предыдущую функцию JavaScript с полученной ссылкой <xref:Microsoft.AspNetCore.Components.ElementReference>:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Чтобы использовать метод расширения, создайте статический метод расширения, который принимает экземпляр <xref:Microsoft.JSInterop.IJSRuntime>:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Метод `Focus` вызывается для объекта напрямую. В следующем примере предполагается, что метод `Focus` доступен из пространства имен `JsInteropClasses`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Переменная `username` заполняется только после отрисовки компонента. Если в код JavaScript передается пустая ссылка <xref:Microsoft.AspNetCore.Components.ElementReference>, он получает значение `null`. Для управления ссылками на элементы после завершения отрисовки компонента (для установки начального фокуса на элемент) используйте [метод жизненного цикла компонента `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).

При работе с универсальными типами и возврате значения используйте <xref:System.Threading.Tasks.ValueTask%601>:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

Метод `GenericMethod` вызывается для объекта с типом напрямую. В следующем примере предполагается, что метод `GenericMethod` доступен из пространства имен `JsInteropClasses`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Ссылки на элементы между компонентами

Ссылка <xref:Microsoft.AspNetCore.Components.ElementReference> гарантированно действует только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> компонента (причем ссылка на элемент является `struct`), поэтому ссылка на элемент не может передаваться между компонентами.

Чтобы сделать ссылку на элемент доступной для других компонентов, родительский компонент может:

* разрешить дочерним компонентам регистрировать обратные вызовы;
* вызывать зарегистрированные обратные вызовы во время события <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> с помощью переданной ссылки на элемент. Такой подход позволяет дочерним компонентам взаимодействовать со ссылкой на элемент родительского компонента косвенным образом.

Этот подход показан в следующем примере Blazor WebAssembly.

В `<head>` `wwwroot/index.html`:

```html
<style>
    .red { color: red }
</style>
```

В `<body>` `wwwroot/index.html`:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (родительский компонент):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

`Shared/SurveyPrompt.razor` (дочерний компонент):

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

`Shared/SurveyPrompt.razor.cs`:

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).

## <a name="harden-js-interop-calls"></a>Повышение надежности вызовов взаимодействия с JS

Взаимодействие с JS может завершаться сбоем из-за ошибок сети и в этом случае должно считаться ненадежным. По умолчанию в приложениях Blazor Server время ожидания вызовов взаимодействия с JS на сервере равно одной минуте. Если для приложения допустимо более короткое время ожидания, установите его одним из указанных ниже способов.

* Глобально в `Startup.ConfigureServices`:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Время ожидания можно указать для отдельного вызова в коде компонента:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Дополнительные сведения о нехватке ресурсов см. в статье <xref:blazor/security/server/threat-mitigation>.

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Исключение циклических ссылок на объекты

Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:

* вызовов метода .NET.
* Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.

Дополнительные сведения см. на следующих страницах:

* [Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Изоляция Blazor JavaScript и ссылки на объекты

Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Изоляция JavaScript обеспечивает следующие преимущества:

* Импортированный JavaScript больше не засоряет глобальное пространство имен.
* Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript.

Например, следующий модуль JavaScript экспортирует функцию JavaScript для отображения запроса браузера:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Добавьте предыдущий модуль JavaScript в библиотеку .NET в виде статического веб-ресурса (`wwwroot/exampleJsInterop.js`), а затем импортируйте модуль в код .NET с помощью службы <xref:Microsoft.JSInterop.IJSRuntime>. Служба внедряется как `jsRuntime` (не показано) в следующем примере:

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

Идентификатором `import` в предыдущем примере является специальный идентификатор, используемый специально для импорта модуля JavaScript. Укажите модуль, используя путь к статическому стабильному веб-ресурсу: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`. Заполнитель `{LIBRARY NAME}` — это имя библиотеки. Заполнитель `{PATH UNDER WWWROOT}` — это путь к скрипту в разделе `wwwroot`.

<xref:Microsoft.JSInterop.IJSRuntime> импортирует модуль как `IJSObjectReference`, который представляет ссылку на объект JavaScript из кода .NET. Используйте `IJSObjectReference` для вызова экспортированных функций JavaScript из модуля:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` представляет ссылку на объект JavaScript, функции которого могут вызываться синхронно.

`IJSUnmarshalledObjectReference` представляет ссылку на объект JavaScript, функции которого могут вызываться без дополнительных затрат, связанных с сериализацией данных .NET. Можно использовать в Blazor WebAssembly, когда важна производительность.

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Использование библиотек JavaScript, отображающих пользовательский интерфейс (элементы DOM)

Иногда может потребоваться использовать библиотеки JavaScript, которые создают видимые элементы пользовательского интерфейса в модели DOM браузера. На первый взгляд, это может показаться затруднительным, так как система сравнения Blazor предполагает наличие контроля над деревом элементов DOM и в ней возникают ошибки, если какой-либо внешний код изменяет дерево DOM и механизм применения различий становится недействительным. Это ограничение не относится лишь к Blazor. Такая же проблема возникает при использовании любой платформы пользовательского интерфейса на основе сравнения.

К счастью, в пользовательский интерфейс компонента Blazor можно легко внедрить внешний пользовательский интерфейс. Рекомендуемым методом является создание пустого элемента кодом компонента (в файле `.razor`). С точки зрения системы сравнения Blazor элемент всегда пуст, поэтому отрисовщик не выполняет рекурсию в него и не обрабатывает его содержимое. Это позволяет спокойно заполнить элемент произвольным содержимым, управляемым извне.

Данный принцип демонстрируется в приведенном ниже примере. В операторе `if`, когда `firstRender` имеет значение `true`, сделайте что-нибудь с `myElement`. Например, вызовите внешнюю библиотеку JavaScript для заполнения элемента. Blazor оставляет содержимое элемента без изменений, пока сам компонент не будет удален. При удалении компонента удаляется и все его поддерево DOM.

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

В качестве более подробного примера рассмотрим следующий компонент, который отрисовывает интерактивную карту с помощью [интерфейсов API Mapbox с открытым кодом](https://www.mapbox.com/).

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

Соответствующий модуль JavaScript, который следует поместить в `wwwroot/mapComponent.js`, выглядит следующим образом.

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

В предыдущем примере замените строку `{ACCESS TOKEN}` допустимым маркером доступа, который можно получить из https://account.mapbox.com.

Чтобы обеспечить правильный стиль, добавьте следующий тег таблицы стилей на страницу HTML узла (`index.html` или `_Host.cshtml`).

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

В предыдущем примере создается интерактивный пользовательский интерфейс карты, в котором пользователь может выполнять следующие действия:

* прокручивать карту или изменять масштаб перетаскиванием;
* переходить к заданным расположениям нажатием кнопок.

![Карта улиц Mapbox для Токио (Япония) с кнопками для выбора Бристоля (Великобритания) и Токио (Япония)](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Важном понимать следующие основные моменты.

 * В случае с Blazor `<div>` с `@ref="mapElement"` остается пустым. Поэтому `mapbox-gl.js` может спокойно заполнять его и затем изменять его содержимое. Этот метод можно использовать с любой библиотекой JavaScript, которая отрисовывает пользовательский интерфейс. В компоненты Blazor можно даже внедрять компоненты из сторонней платформы одностраничных приложений JavaScript, если они не пытаются изменить другие части страницы. Ситуация, когда внешний код JavaScript изменяет элементы, которые Blazor не считает пустыми, *небезопасна*.
 * При использовании этого подхода необходимо учитывать то, как Blazor удерживает или уничтожает элементы DOM. В предыдущем примере компонент безопасно обрабатывает события нажатия кнопок и обновляет существующий экземпляр карты, так как по умолчанию элементы DOM по возможности сохраняются без изменений. При отрисовке списка элементов карты из цикла `@foreach` необходимо использовать `@key`, чтобы гарантировать сохранность экземпляров компонента. В противном случае изменения в данных списка могут приводить к нежелательному сохранению состояния предыдущих экземпляров компонента. Дополнительные сведения см. в разделе об [использовании @key для сохранения элементов и компонентов](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Кроме того, в предыдущем примере показано, как можно инкапсулировать логику и зависимости JavaScript в модуле ES6 и динамически загружать его с помощью идентификатора `import`. Дополнительные сведения см.в статье [Изоляция JavaScript и ссылки на объекты](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/call-dotnet-from-javascript>
* [Пример InteropComponent.razor (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Выполнение передачи больших объемов данных в приложениях Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
