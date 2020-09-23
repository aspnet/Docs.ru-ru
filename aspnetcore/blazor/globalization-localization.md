---
title: Глобализация и локализация в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как сделать компоненты Razor доступными для пользователей с различными языковыми и региональными параметрами.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 2b8820acba564bdfb85f8338ed5482573960fbb4
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080281"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="5273b-103">Глобализация и локализация в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5273b-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="5273b-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="5273b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5273b-105">Компоненты Razor можно сделать доступными для пользователей с различными языковыми и региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="5273b-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="5273b-106">Доступны следующие сценарии глобализации и локализации .NET:</span><span class="sxs-lookup"><span data-stu-id="5273b-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="5273b-107">система ресурсов NET;</span><span class="sxs-lookup"><span data-stu-id="5273b-107">.NET's resources system</span></span>
* <span data-ttu-id="5273b-108">форматирование чисел и дат в зависимости от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="5273b-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="5273b-109">В настоящее время поддерживается ограниченный набор сценариев локализации ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5273b-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="5273b-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> и <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *поддерживаются* в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="5273b-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="5273b-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> и локализация заметок к данным являются сценариями MVC ASP.NET Core и **не поддерживается** в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="5273b-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="5273b-112">Для получения дополнительной информации см. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="5273b-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="5273b-113">Глобализация</span><span class="sxs-lookup"><span data-stu-id="5273b-113">Globalization</span></span>

<span data-ttu-id="5273b-114">Функция [`@bind`](xref:mvc/views/razor#bind) в Blazor выполняет форматирование и синтаксический анализ значений, отображаемых на основе текущего языка и региональных параметров пользователя.</span><span class="sxs-lookup"><span data-stu-id="5273b-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="5273b-115">Доступ к текущему языку и региональным параметрам можно получить из свойства <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="5273b-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="5273b-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> используется для следующих типов полей (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="5273b-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="5273b-117">Предыдущие типы полей:</span><span class="sxs-lookup"><span data-stu-id="5273b-117">The preceding field types:</span></span>

* <span data-ttu-id="5273b-118">отображаются с использованием соответствующих правил форматирования браузера;</span><span class="sxs-lookup"><span data-stu-id="5273b-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="5273b-119">не могут содержать текст в свободной форме;</span><span class="sxs-lookup"><span data-stu-id="5273b-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="5273b-120">предоставляют характеристики взаимодействия с пользователем в зависимости от реализации браузера.</span><span class="sxs-lookup"><span data-stu-id="5273b-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="5273b-121">Следующие типы полей имеют особые требования к форматированию, которые в настоящее время не поддерживаются в Blazor, так как они не поддерживаются всеми основными браузерами.</span><span class="sxs-lookup"><span data-stu-id="5273b-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="5273b-122">[`@bind`](xref:mvc/views/razor#bind) поддерживает параметр `@bind:culture`, чтобы предоставлять <xref:System.Globalization.CultureInfo?displayProperty=fullName> для анализа и форматирования значения.</span><span class="sxs-lookup"><span data-stu-id="5273b-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="5273b-123">Указание языка и региональных параметров не рекомендуется при использовании типов полей `date` и `number`.</span><span class="sxs-lookup"><span data-stu-id="5273b-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="5273b-124">Типы `date` и `number` имеют встроенную поддержку Blazor, которая обеспечивает поддержку языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="5273b-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="5273b-125">Локализация</span><span class="sxs-lookup"><span data-stu-id="5273b-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="5273b-126">Приложения Blazor WebAssembly задают язык и региональные параметры с помощью [предпочитаемого языка пользователя](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span><span class="sxs-lookup"><span data-stu-id="5273b-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="5273b-127">Чтобы явно настроить язык и региональные параметры, задайте <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> и <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="5273b-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5273b-128">По умолчанию Blazor WebAssembly содержит ресурсы глобализации, необходимые для отображения значений, таких как даты и денежные единицы, на языке пользователя и с его региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="5273b-128">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="5273b-129">Если приложению не требуется локализация, можно настроить поддержку инвариантных языка и региональных параметров, основанных на языке и региональных параметрах `en-US`:</span><span class="sxs-lookup"><span data-stu-id="5273b-129">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5273b-130">По умолчанию конфигурация компоновщика промежуточного языка (IL) для приложений Blazor WebAssembly исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов.</span><span class="sxs-lookup"><span data-stu-id="5273b-130">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="5273b-131">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="5273b-131">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="5273b-132">Хотя язык и региональные параметры, выбираемые Blazor по умолчанию, могут быть достаточными для большинства пользователей, рекомендуется предоставить пользователям возможность указать предпочитаемый языковой стандарт.</span><span class="sxs-lookup"><span data-stu-id="5273b-132">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="5273b-133">Пример приложения Blazor WebAssembly с выбором языка и региональных параметров см. в описании примера приложения локализации [`LocSample`](https://github.com/pranavkm/LocSample).</span><span class="sxs-lookup"><span data-stu-id="5273b-133">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="5273b-134">Приложения Blazor Server локализуются с помощью [промежуточного слоя локализации](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="5273b-134">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="5273b-135">ПО промежуточного слоя выбирает соответствующие языки и региональные параметры для пользователей, запрашивающих ресурсы из приложения.</span><span class="sxs-lookup"><span data-stu-id="5273b-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="5273b-136">Язык и региональные параметры можно задать с помощью одного из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="5273b-136">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="5273b-137">[Файлы Cookie](#cookies)</span><span class="sxs-lookup"><span data-stu-id="5273b-137">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="5273b-138">Предоставление пользовательского интерфейса для выбора языка и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="5273b-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="5273b-139">Дополнительные сведения и примеры см. в разделе <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="5273b-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="5273b-140">Cookies</span><span class="sxs-lookup"><span data-stu-id="5273b-140">Cookies</span></span>

<span data-ttu-id="5273b-141">Файл cookie локализации может запоминать язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="5273b-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="5273b-142">ПО промежуточного слоя локализации считывает файл cookie при последующих запросах, чтобы задать язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="5273b-142">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="5273b-143">Использование файла cookie гарантирует, что соединение WebSocket сможет правильно распространить языковые и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="5273b-143">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="5273b-144">Если схема локализации основана на URL-адресе или строке запроса, она может не поддерживать работу с WebSockets и не сможет сохранить язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="5273b-144">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="5273b-145">Поэтому рекомендуемым подходом для локализации является использование файла cookie языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="5273b-145">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="5273b-146">Если язык и региональные параметры сохраняются в файле cookie с локализацией, то можно использовать любой метод для назначения языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="5273b-146">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="5273b-147">Если у приложения уже есть установленная схема локализации для ASP.NET Core на стороне сервера, продолжайте использовать существующую инфраструктуру локализации приложения и задавайте файл cookie языка и региональных параметров для локализации в схеме приложения.</span><span class="sxs-lookup"><span data-stu-id="5273b-147">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="5273b-148">В следующем примере показано, как задать текущий язык и региональные параметры в файле cookie, который можно прочитать в ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="5273b-148">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="5273b-149">Создайте Razor выражение в файле `Pages/_Host.cshtml` непосредственно в открывающем теге `<body>`:</span><span class="sxs-lookup"><span data-stu-id="5273b-149">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="5273b-150">Локализация обрабатывается приложением в следующей последовательности событий.</span><span class="sxs-lookup"><span data-stu-id="5273b-150">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="5273b-151">Браузер отправляет первоначальный HTTP-запрос в приложение.</span><span class="sxs-lookup"><span data-stu-id="5273b-151">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="5273b-152">Язык и региональные параметры назначаются в ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="5273b-152">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="5273b-153">Выражение Razor на странице `_Host` (`_Host.cshtml`) сохраняет язык и региональные параметры в файле cookie как часть ответа.</span><span class="sxs-lookup"><span data-stu-id="5273b-153">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="5273b-154">Браузер открывает соединение WebSocket для создания интерактивного сеанса Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="5273b-154">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="5273b-155">ПО промежуточного слоя для локализации считывает файл cookie и назначает язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="5273b-155">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="5273b-156">Сеанс Blazor Server начинается с нужными языком и региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="5273b-156">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="5273b-157">Предоставление пользовательского интерфейса для выбора языка и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="5273b-157">Provide UI to choose the culture</span></span>

<span data-ttu-id="5273b-158">Чтобы предоставить пользовательский интерфейс, позволяющий пользователю выбрать язык и региональные параметры, рекомендуется *подход на основе перенаправления*.</span><span class="sxs-lookup"><span data-stu-id="5273b-158">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="5273b-159">Процесс аналогичен тому, что происходит в веб-приложении, когда пользователь пытается получить доступ к защищенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="5273b-159">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="5273b-160">Пользователь перенаправляется на страницу входа, а затем обратно к исходному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="5273b-160">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="5273b-161">Приложение сохраняет выбранный язык и региональные параметры пользователя с помощью перенаправления к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="5273b-161">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="5273b-162">Контроллер устанавливает выбранный язык и региональные параметры пользователя в файл cookie и перенаправляет пользователя обратно в исходный URI.</span><span class="sxs-lookup"><span data-stu-id="5273b-162">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="5273b-163">Установите конечную точку HTTP на сервере, чтобы задать выбранный язык и региональные параметры пользователя в файле cookie, и выполните перенаправление обратно в исходный URI:</span><span class="sxs-lookup"><span data-stu-id="5273b-163">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="5273b-164">Для предотвращения атак с открытым перенаправлением используйте результат действия <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>.</span><span class="sxs-lookup"><span data-stu-id="5273b-164">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="5273b-165">Для получения дополнительной информации см. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="5273b-165">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="5273b-166">Если приложение не настроено для обработки действий контроллера:</span><span class="sxs-lookup"><span data-stu-id="5273b-166">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="5273b-167">Добавьте службы MVC в коллекцию служб в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5273b-167">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="5273b-168">Добавьте маршрутизацию конечных точек контроллера в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5273b-168">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="5273b-169">В следующем компоненте показан пример выполнения начального перенаправления, когда пользователь выбирает язык и региональные параметры:</span><span class="sxs-lookup"><span data-stu-id="5273b-169">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="5273b-170">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5273b-170">Additional resources</span></span>

* <xref:fundamentals/localization>
