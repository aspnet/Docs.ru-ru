---
title: Высокопроизводительное ведение журналов с помощью LoggerMessage в ASP.NET Core
author: rick-anderson
description: Сведения о том, как использовать LoggerMessage для создания кэшируемых делегатов, которым нужно меньше выделений объектов в сценариях высокопроизводительного ведения журналов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 0224e768bd0e016eac5165dc4d9745f4b0867094
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060460"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="14564-103">Высокопроизводительное ведение журналов с помощью LoggerMessage в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14564-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14564-104">Функции <xref:Microsoft.Extensions.Logging.LoggerMessage> создают кэшируемые делегаты, которым нужно меньше выделений объектов и вычислительных ресурсов, чем [методам расширения для средства ведения журнала](xref:Microsoft.Extensions.Logging.LoggerExtensions), таким как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="14564-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="14564-105">Для сценариев высокопроизводительного ведения журналов используйте шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="14564-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="14564-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> предоставляет следующие преимущества производительности по сравнению с методами расширения для средства ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="14564-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="14564-107">Методы расширения для средства ведения журнала требуют "упаковку-преобразование" типов значений, таких как `int`, в `object`.</span><span class="sxs-lookup"><span data-stu-id="14564-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="14564-108">Шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage> позволяет избежать упаковки-преобразования за счет статических полей <xref:System.Action> и методов расширения со строго типизированными параметрами.</span><span class="sxs-lookup"><span data-stu-id="14564-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="14564-109">Методы расширения для средства ведения журнала должны анализировать шаблон сообщения (именованную строку формата) при каждой записи сообщения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="14564-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> требует анализировать шаблон всего один раз — при определении сообщения.</span><span class="sxs-lookup"><span data-stu-id="14564-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="14564-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14564-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="14564-112">Пример приложения показывает функции <xref:Microsoft.Extensions.Logging.LoggerMessage> с базовой системой отслеживания цитат.</span><span class="sxs-lookup"><span data-stu-id="14564-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="14564-113">Это приложение добавляет и удаляет цитаты, используя выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="14564-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="14564-114">По мере выполнения этих операций создаются сообщения журнала с помощью шаблона <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="14564-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="14564-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="14564-115">LoggerMessage.Define</span></span>

<span data-ttu-id="14564-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) создает делегат <xref:System.Action> для внесения сообщения в журнал.</span><span class="sxs-lookup"><span data-stu-id="14564-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="14564-117">Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> позволяют передать до шести параметров типа в именованную строку формата (шаблон).</span><span class="sxs-lookup"><span data-stu-id="14564-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="14564-118">Строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, является шаблоном, а не интерполированной строкой.</span><span class="sxs-lookup"><span data-stu-id="14564-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="14564-119">Заполнители заполняются в том порядке, в котором указаны типы.</span><span class="sxs-lookup"><span data-stu-id="14564-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="14564-120">Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="14564-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="14564-121">Они выступают в качестве имен свойств в структурированных данных журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="14564-122">Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей.</span><span class="sxs-lookup"><span data-stu-id="14564-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="14564-123">Например, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="14564-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="14564-124">Каждое сообщение журнала является <xref:System.Action>, хранящимся в статическом поле, созданном [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="14564-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="14564-125">Например, пример приложения создает поле для описания сообщения журнала для запроса GET для страницы индексов ( *Internal/LoggerExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="14564-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page ( *Internal/LoggerExtensions.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="14564-126">Для <xref:System.Action> укажите:</span><span class="sxs-lookup"><span data-stu-id="14564-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="14564-127">уровень ведения журнала;</span><span class="sxs-lookup"><span data-stu-id="14564-127">The log level.</span></span>
* <span data-ttu-id="14564-128">уникальный идентификатор события (<xref:Microsoft.Extensions.Logging.EventId>) с именем статического метода расширения;</span><span class="sxs-lookup"><span data-stu-id="14564-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="14564-129">шаблон сообщения (именованная строка формата).</span><span class="sxs-lookup"><span data-stu-id="14564-129">The message template (named format string).</span></span> 

<span data-ttu-id="14564-130">Запрос страницы индексов в примере приложения задает:</span><span class="sxs-lookup"><span data-stu-id="14564-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="14564-131">уровень ведения журнала — `Information`;</span><span class="sxs-lookup"><span data-stu-id="14564-131">Log level to `Information`.</span></span>
* <span data-ttu-id="14564-132">идентификатор события — `1` с именем метода `IndexPageRequested`;</span><span class="sxs-lookup"><span data-stu-id="14564-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="14564-133">шаблон сообщения (именованная строка формата) — строка.</span><span class="sxs-lookup"><span data-stu-id="14564-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="14564-134">Структурированные хранилища для ведения журнала могут использовать имя события, когда оно предоставляется вместе с идентификатором события, для улучшения ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="14564-135">Например, [Serilog](https://github.com/serilog/serilog-extensions-logging) использует имя события.</span><span class="sxs-lookup"><span data-stu-id="14564-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="14564-136"><xref:System.Action> вызывается с помощью строго типизированного метода расширения.</span><span class="sxs-lookup"><span data-stu-id="14564-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="14564-137">Метод `IndexPageRequested` заносит в журнал сообщение для запроса GET страницы индексов в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="14564-138">`IndexPageRequested` вызывается для средства ведения журнала в методе `OnGetAsync` в *Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="14564-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="14564-139">Изучите выходные данные консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="14564-140">Чтобы передать параметры в сообщение журнала, определите до шести типов при создании статического поля.</span><span class="sxs-lookup"><span data-stu-id="14564-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="14564-141">Пример приложения регистрирует строку при добавлении цитаты, определяя тип `string` для поля <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="14564-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="14564-142">Шаблон сообщения журнала делегата получает его значения заполнителей из предоставленных типов.</span><span class="sxs-lookup"><span data-stu-id="14564-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="14564-143">Пример приложения определяет делегат для добавления цитаты, где параметр квоты имеет значение`string`:</span><span class="sxs-lookup"><span data-stu-id="14564-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="14564-144">Статический метод расширения для добавления цитаты `QuoteAdded` получает значение аргумента цитаты и передает его в делегат <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="14564-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="14564-145">В страничной модели страницы индексов ( *Pages/Index.cshtml.cs* ) для регистрации сообщения в журнале вызывается `QuoteAdded`:</span><span class="sxs-lookup"><span data-stu-id="14564-145">In the Index page's page model ( *Pages/Index.cshtml.cs* ), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="14564-146">Изучите выходные данные консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="14564-147">Пример приложения реализует шаблон [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) для удаления цитаты.</span><span class="sxs-lookup"><span data-stu-id="14564-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="14564-148">Для успешной операции удаления регистрируется информационное сообщение.</span><span class="sxs-lookup"><span data-stu-id="14564-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="14564-149">Сообщение об ошибке регистрируется для операции удаления, когда возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="14564-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="14564-150">Сообщение журнала для неудачной операции удаления включает трассировку стека исключений ( *Internal/LoggerExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="14564-150">The log message for the unsuccessful delete operation includes the exception stack trace ( *Internal/LoggerExtensions.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="14564-151">Обратите внимание, как исключение передается делегату в `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="14564-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="14564-152">В страничной модели для страницы индексов успешная операция удаления цитаты вызывает метод `QuoteDeleted` для средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="14564-153">Если удаляемая цитата не найдена, возникает исключение <xref:System.ArgumentNullException>.</span><span class="sxs-lookup"><span data-stu-id="14564-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="14564-154">Это исключение перехватывается инструкцией [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) и регистрируется путем вызова метода `QuoteDeleteFailed` для средства ведения журнала в блоке [catch](/dotnet/csharp/language-reference/keywords/try-catch) ( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="14564-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="14564-155">При успешном удалении цитаты изучите выходные данные консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="14564-156">При неудачном удалении цитаты изучите выходные данные консоли приложения.</span><span class="sxs-lookup"><span data-stu-id="14564-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="14564-157">Обратите внимание, что исключение входит в состав сообщения журнала:</span><span class="sxs-lookup"><span data-stu-id="14564-157">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="14564-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="14564-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="14564-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) создает делегат <xref:System.Func%601> для определения [области журнала](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="14564-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="14564-160">Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> позволяют передать до трех параметров типа в именованную строку формата (шаблон).</span><span class="sxs-lookup"><span data-stu-id="14564-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="14564-161">Как и в случае с методом <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>, является шаблоном, а не интерполированной строкой.</span><span class="sxs-lookup"><span data-stu-id="14564-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="14564-162">Заполнители заполняются в том порядке, в котором указаны типы.</span><span class="sxs-lookup"><span data-stu-id="14564-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="14564-163">Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="14564-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="14564-164">Они выступают в качестве имен свойств в структурированных данных журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="14564-165">Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей.</span><span class="sxs-lookup"><span data-stu-id="14564-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="14564-166">Например, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="14564-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="14564-167">Определите [область журнала](xref:fundamentals/logging/index#log-scopes), чтобы применить последовательность сообщений журнала с помощью метода <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="14564-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="14564-168">Пример приложения имеет кнопку **Clear All** (Очистить все), позволяющую удалить все цитаты в базе данных.</span><span class="sxs-lookup"><span data-stu-id="14564-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="14564-169">Удаление цитат производится по одной за раз.</span><span class="sxs-lookup"><span data-stu-id="14564-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="14564-170">При каждом удалении цитаты для средства ведения журнала вызывается метод `QuoteDeleted`.</span><span class="sxs-lookup"><span data-stu-id="14564-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="14564-171">В эти сообщения журнала добавляется область журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="14564-172">Включите `IncludeScopes` в разделе средства ведения журнала в файле *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="14564-172">Enable `IncludeScopes` in the console logger section of *:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/:::no-loc(appsettings.json):::?highlight=3-5)]

<span data-ttu-id="14564-173">Для создания области журнала добавьте поле, содержащее делегат <xref:System.Func%601> для области.</span><span class="sxs-lookup"><span data-stu-id="14564-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="14564-174">Пример приложения создает поле `_allQuotesDeletedScope` ( *Internal/LoggerExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="14564-174">The sample app creates a field called `_allQuotesDeletedScope` ( *Internal/LoggerExtensions.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="14564-175">Используйте <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> для создания делегата.</span><span class="sxs-lookup"><span data-stu-id="14564-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="14564-176">Можно указать до трех типов для использования в качестве аргументов шаблона при вызове делегата.</span><span class="sxs-lookup"><span data-stu-id="14564-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="14564-177">Пример приложения использует шаблон сообщения, включающий в себя число удаленных цитат (тип `int`):</span><span class="sxs-lookup"><span data-stu-id="14564-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="14564-178">Предоставьте статический метод расширения для сообщения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="14564-179">Включите любые параметры типа для именованных свойств, отображаемых в шаблоне сообщений.</span><span class="sxs-lookup"><span data-stu-id="14564-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="14564-180">Пример приложения принимает число удаляемых цитат `count` и возвращает `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="14564-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="14564-181">Область создает оболочку для вызовов расширения ведения журнала в блоке [using](/dotnet/csharp/language-reference/keywords/using-statement).</span><span class="sxs-lookup"><span data-stu-id="14564-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="14564-182">Изучите сообщения журнала в выходных данных консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="14564-183">Следующий результат указывает три удаленных цитаты с включенным сообщением области журнала:</span><span class="sxs-lookup"><span data-stu-id="14564-183">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14564-184">Функции <xref:Microsoft.Extensions.Logging.LoggerMessage> создают кэшируемые делегаты, которым нужно меньше выделений объектов и вычислительных ресурсов, чем [методам расширения для средства ведения журнала](xref:Microsoft.Extensions.Logging.LoggerExtensions), таким как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="14564-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="14564-185">Для сценариев высокопроизводительного ведения журналов используйте шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="14564-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="14564-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> предоставляет следующие преимущества производительности по сравнению с методами расширения для средства ведения журнала:</span><span class="sxs-lookup"><span data-stu-id="14564-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="14564-187">Методы расширения для средства ведения журнала требуют "упаковку-преобразование" типов значений, таких как `int`, в `object`.</span><span class="sxs-lookup"><span data-stu-id="14564-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="14564-188">Шаблон <xref:Microsoft.Extensions.Logging.LoggerMessage> позволяет избежать упаковки-преобразования за счет статических полей <xref:System.Action> и методов расширения со строго типизированными параметрами.</span><span class="sxs-lookup"><span data-stu-id="14564-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="14564-189">Методы расширения для средства ведения журнала должны анализировать шаблон сообщения (именованную строку формата) при каждой записи сообщения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="14564-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> требует анализировать шаблон всего один раз — при определении сообщения.</span><span class="sxs-lookup"><span data-stu-id="14564-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="14564-191">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14564-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="14564-192">Пример приложения показывает функции <xref:Microsoft.Extensions.Logging.LoggerMessage> с базовой системой отслеживания цитат.</span><span class="sxs-lookup"><span data-stu-id="14564-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="14564-193">Это приложение добавляет и удаляет цитаты, используя выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="14564-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="14564-194">По мере выполнения этих операций создаются сообщения журнала с помощью шаблона <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="14564-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="14564-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="14564-195">LoggerMessage.Define</span></span>

<span data-ttu-id="14564-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) создает делегат <xref:System.Action> для внесения сообщения в журнал.</span><span class="sxs-lookup"><span data-stu-id="14564-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="14564-197">Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> позволяют передать до шести параметров типа в именованную строку формата (шаблон).</span><span class="sxs-lookup"><span data-stu-id="14564-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="14564-198">Строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, является шаблоном, а не интерполированной строкой.</span><span class="sxs-lookup"><span data-stu-id="14564-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="14564-199">Заполнители заполняются в том порядке, в котором указаны типы.</span><span class="sxs-lookup"><span data-stu-id="14564-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="14564-200">Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="14564-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="14564-201">Они выступают в качестве имен свойств в структурированных данных журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="14564-202">Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей.</span><span class="sxs-lookup"><span data-stu-id="14564-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="14564-203">Например, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="14564-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="14564-204">Каждое сообщение журнала является <xref:System.Action>, хранящимся в статическом поле, созданном [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="14564-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="14564-205">Например, пример приложения создает поле для описания сообщения журнала для запроса GET для страницы индексов ( *Internal/LoggerExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="14564-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page ( *Internal/LoggerExtensions.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="14564-206">Для <xref:System.Action> укажите:</span><span class="sxs-lookup"><span data-stu-id="14564-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="14564-207">уровень ведения журнала;</span><span class="sxs-lookup"><span data-stu-id="14564-207">The log level.</span></span>
* <span data-ttu-id="14564-208">уникальный идентификатор события (<xref:Microsoft.Extensions.Logging.EventId>) с именем статического метода расширения;</span><span class="sxs-lookup"><span data-stu-id="14564-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="14564-209">шаблон сообщения (именованная строка формата).</span><span class="sxs-lookup"><span data-stu-id="14564-209">The message template (named format string).</span></span> 

<span data-ttu-id="14564-210">Запрос страницы индексов в примере приложения задает:</span><span class="sxs-lookup"><span data-stu-id="14564-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="14564-211">уровень ведения журнала — `Information`;</span><span class="sxs-lookup"><span data-stu-id="14564-211">Log level to `Information`.</span></span>
* <span data-ttu-id="14564-212">идентификатор события — `1` с именем метода `IndexPageRequested`;</span><span class="sxs-lookup"><span data-stu-id="14564-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="14564-213">шаблон сообщения (именованная строка формата) — строка.</span><span class="sxs-lookup"><span data-stu-id="14564-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="14564-214">Структурированные хранилища для ведения журнала могут использовать имя события, когда оно предоставляется вместе с идентификатором события, для улучшения ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="14564-215">Например, [Serilog](https://github.com/serilog/serilog-extensions-logging) использует имя события.</span><span class="sxs-lookup"><span data-stu-id="14564-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="14564-216"><xref:System.Action> вызывается с помощью строго типизированного метода расширения.</span><span class="sxs-lookup"><span data-stu-id="14564-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="14564-217">Метод `IndexPageRequested` заносит в журнал сообщение для запроса GET страницы индексов в примере приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="14564-218">`IndexPageRequested` вызывается для средства ведения журнала в методе `OnGetAsync` в *Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="14564-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="14564-219">Изучите выходные данные консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="14564-220">Чтобы передать параметры в сообщение журнала, определите до шести типов при создании статического поля.</span><span class="sxs-lookup"><span data-stu-id="14564-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="14564-221">Пример приложения регистрирует строку при добавлении цитаты, определяя тип `string` для поля <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="14564-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="14564-222">Шаблон сообщения журнала делегата получает его значения заполнителей из предоставленных типов.</span><span class="sxs-lookup"><span data-stu-id="14564-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="14564-223">Пример приложения определяет делегат для добавления цитаты, где параметр квоты имеет значение`string`:</span><span class="sxs-lookup"><span data-stu-id="14564-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="14564-224">Статический метод расширения для добавления цитаты `QuoteAdded` получает значение аргумента цитаты и передает его в делегат <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="14564-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="14564-225">В страничной модели страницы индексов ( *Pages/Index.cshtml.cs* ) для регистрации сообщения в журнале вызывается `QuoteAdded`:</span><span class="sxs-lookup"><span data-stu-id="14564-225">In the Index page's page model ( *Pages/Index.cshtml.cs* ), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="14564-226">Изучите выходные данные консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="14564-227">Пример приложения реализует шаблон [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) для удаления цитаты.</span><span class="sxs-lookup"><span data-stu-id="14564-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="14564-228">Для успешной операции удаления регистрируется информационное сообщение.</span><span class="sxs-lookup"><span data-stu-id="14564-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="14564-229">Сообщение об ошибке регистрируется для операции удаления, когда возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="14564-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="14564-230">Сообщение журнала для неудачной операции удаления включает трассировку стека исключений ( *Internal/LoggerExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="14564-230">The log message for the unsuccessful delete operation includes the exception stack trace ( *Internal/LoggerExtensions.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="14564-231">Обратите внимание, как исключение передается делегату в `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="14564-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="14564-232">В страничной модели для страницы индексов успешная операция удаления цитаты вызывает метод `QuoteDeleted` для средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="14564-233">Если удаляемая цитата не найдена, возникает исключение <xref:System.ArgumentNullException>.</span><span class="sxs-lookup"><span data-stu-id="14564-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="14564-234">Это исключение перехватывается инструкцией [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) и регистрируется путем вызова метода `QuoteDeleteFailed` для средства ведения журнала в блоке [catch](/dotnet/csharp/language-reference/keywords/try-catch) ( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="14564-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="14564-235">При успешном удалении цитаты изучите выходные данные консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="14564-236">При неудачном удалении цитаты изучите выходные данные консоли приложения.</span><span class="sxs-lookup"><span data-stu-id="14564-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="14564-237">Обратите внимание, что исключение входит в состав сообщения журнала:</span><span class="sxs-lookup"><span data-stu-id="14564-237">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="14564-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="14564-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="14564-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) создает делегат <xref:System.Func%601> для определения [области журнала](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="14564-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="14564-240">Перегрузки <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> позволяют передать до трех параметров типа в именованную строку формата (шаблон).</span><span class="sxs-lookup"><span data-stu-id="14564-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="14564-241">Как и в случае с методом <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, строка, предоставляемая методу <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>, является шаблоном, а не интерполированной строкой.</span><span class="sxs-lookup"><span data-stu-id="14564-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="14564-242">Заполнители заполняются в том порядке, в котором указаны типы.</span><span class="sxs-lookup"><span data-stu-id="14564-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="14564-243">Имена заполнителей в шаблоне должны быть описательными и согласованными между разными шаблонами.</span><span class="sxs-lookup"><span data-stu-id="14564-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="14564-244">Они выступают в качестве имен свойств в структурированных данных журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="14564-245">Мы рекомендуем использовать [стиль Pascal ](/dotnet/standard/design-guidelines/capitalization-conventions) для имен заполнителей.</span><span class="sxs-lookup"><span data-stu-id="14564-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="14564-246">Например, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="14564-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="14564-247">Определите [область журнала](xref:fundamentals/logging/index#log-scopes), чтобы применить последовательность сообщений журнала с помощью метода <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="14564-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="14564-248">Пример приложения имеет кнопку **Clear All** (Очистить все), позволяющую удалить все цитаты в базе данных.</span><span class="sxs-lookup"><span data-stu-id="14564-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="14564-249">Удаление цитат производится по одной за раз.</span><span class="sxs-lookup"><span data-stu-id="14564-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="14564-250">При каждом удалении цитаты для средства ведения журнала вызывается метод `QuoteDeleted`.</span><span class="sxs-lookup"><span data-stu-id="14564-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="14564-251">В эти сообщения журнала добавляется область журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="14564-252">Включите `IncludeScopes` в разделе средства ведения журнала в файле *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="14564-252">Enable `IncludeScopes` in the console logger section of *:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/:::no-loc(appsettings.json):::?highlight=3-5)]

<span data-ttu-id="14564-253">Для создания области журнала добавьте поле, содержащее делегат <xref:System.Func%601> для области.</span><span class="sxs-lookup"><span data-stu-id="14564-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="14564-254">Пример приложения создает поле `_allQuotesDeletedScope` ( *Internal/LoggerExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="14564-254">The sample app creates a field called `_allQuotesDeletedScope` ( *Internal/LoggerExtensions.cs* ):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="14564-255">Используйте <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> для создания делегата.</span><span class="sxs-lookup"><span data-stu-id="14564-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="14564-256">Можно указать до трех типов для использования в качестве аргументов шаблона при вызове делегата.</span><span class="sxs-lookup"><span data-stu-id="14564-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="14564-257">Пример приложения использует шаблон сообщения, включающий в себя число удаленных цитат (тип `int`):</span><span class="sxs-lookup"><span data-stu-id="14564-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="14564-258">Предоставьте статический метод расширения для сообщения журнала.</span><span class="sxs-lookup"><span data-stu-id="14564-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="14564-259">Включите любые параметры типа для именованных свойств, отображаемых в шаблоне сообщений.</span><span class="sxs-lookup"><span data-stu-id="14564-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="14564-260">Пример приложения принимает число удаляемых цитат `count` и возвращает `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="14564-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="14564-261">Область создает оболочку для вызовов расширения ведения журнала в блоке [using](/dotnet/csharp/language-reference/keywords/using-statement).</span><span class="sxs-lookup"><span data-stu-id="14564-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="14564-262">Изучите сообщения журнала в выходных данных консоли приложения:</span><span class="sxs-lookup"><span data-stu-id="14564-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="14564-263">Следующий результат указывает три удаленных цитаты с включенным сообщением области журнала:</span><span class="sxs-lookup"><span data-stu-id="14564-263">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="14564-264">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="14564-264">Additional resources</span></span>

* [<span data-ttu-id="14564-265">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="14564-265">Logging</span></span>](xref:fundamentals/logging/index)
