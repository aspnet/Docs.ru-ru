---
title: 'Проверка подлинности и авторизация в ASP.NET Core Blazor'
author: guardrex
description: Сведения о проверке подлинности и авторизации в Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/index
ms.openlocfilehash: a333c189e81a9f44e94deb6b37097f1a8b19a0f9
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430930"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a><span data-ttu-id="d5d1a-103">Проверка подлинности и авторизация в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d5d1a-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="d5d1a-104">Авторы: [Стив Сандерсон (Steve Sanderson)](https://github.com/SteveSandersonMS) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d5d1a-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d5d1a-105">ASP.NET Core поддерживает настройку и администрирование средств безопасности в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="d5d1a-106">Сценарии безопасности для приложений Blazor Server и Blazor WebAssembly отличаются.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="d5d1a-107">Так как приложения Blazor Server выполняются на стороне сервера, проверки авторизации могут определить следующее:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="d5d1a-108">параметры пользовательского интерфейса, предоставленные пользователю (например, какие пункты меню доступны пользователю);</span><span class="sxs-lookup"><span data-stu-id="d5d1a-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="d5d1a-109">правила доступа для приложений и компонентов.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="d5d1a-110">Приложения Blazor WebAssembly выполняются на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="d5d1a-111">В этом случае авторизация используется *только* для определения отображаемых вариантов пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="d5d1a-112">Так как пользователь может изменить или обойти проверки на стороне клиента, приложение Blazor WebAssembly не может применять правила авторизации доступа.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="d5d1a-113">[Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization) не применяются к маршрутизируемым компонентам Razor.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="d5d1a-114">Если компонент Razor, не поддерживающий маршрутизацию, [встроен в страницу](xref:blazor/components/prerendering-and-integration), соглашения об авторизации страницы оказывают косвенное влияние на компонент Razor, а также на остальную часть содержимого страницы.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/prerendering-and-integration), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="d5d1a-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> и <xref:Microsoft.AspNetCore.Identity.UserManager%601> не поддерживаются в компонентах Razor.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="d5d1a-116">Проверка подлинности</span><span class="sxs-lookup"><span data-stu-id="d5d1a-116">Authentication</span></span>

<span data-ttu-id="d5d1a-117">Blazor использует существующие механизмы проверки подлинности ASP.NET Core для установления личности пользователя.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-117">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="d5d1a-118">Конкретный механизм зависит от того, как размещается приложение Blazor (Blazor WebAssembly или Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="d5d1a-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="no-locblazor-webassembly-authentication"></a><span data-ttu-id="d5d1a-119">Blazor WebAssembly аутентификация</span><span class="sxs-lookup"><span data-stu-id="d5d1a-119">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="d5d1a-120">В приложениях Blazor WebAssembly проверку подлинности можно обойти, так как пользователь может изменять весь код на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="d5d1a-121">Это же справедливо для всех технологий на стороне клиента, включая платформы одностраничного приложения JavaScript или собственных приложений для любой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="d5d1a-122">Добавьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-122">Add the following:</span></span>

* <span data-ttu-id="d5d1a-123">Ссылка на пакет для [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) для файла проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="d5d1a-124">Пространство имен `Microsoft.AspNetCore.Components.Authorization` для файла `_Imports.razor` приложения.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="d5d1a-125">Вопросы обработки проверки подлинности и использования встроенной или настраиваемой службы <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> рассматриваются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="d5d1a-126">Дополнительные сведения о создании и настройке приложений см. в статье <xref:blazor/security/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="no-locblazor-server-authentication"></a><span data-ttu-id="d5d1a-127">Blazor Server аутентификация</span><span class="sxs-lookup"><span data-stu-id="d5d1a-127">Blazor Server authentication</span></span>

<span data-ttu-id="d5d1a-128">Приложения Blazor Server работают через подключение в реальном времени, созданное с помощью SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-128">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="d5d1a-129">[Проверка подлинности в приложениях на основе SignalR](xref:signalr/authn-and-authz) выполняется при установлении подключения.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="d5d1a-130">Аутентификация может выполняться на основе cookie или других маркеров носителя.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="d5d1a-131">Встроенная служба <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> для приложений Blazor Server получает данные о состоянии проверки подлинности из `HttpContext.User` в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for Blazor Server apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="d5d1a-132">Так состояние проверки подлинности интегрируется с существующими соответствующими механизмами проверки подлинности ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="d5d1a-133">Дополнительные сведения о создании и настройке приложений см. в статье <xref:blazor/security/server/index>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="d5d1a-134">Служба AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="d5d1a-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="d5d1a-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> является базовой службой, которую компоненты <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> и <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> используют для получения состояния аутентификации.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="d5d1a-136">Обычно вы не используете <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> напрямую.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="d5d1a-137">Выберите подход с использованием [компонента `AuthorizeView`](#authorizeview-component) или [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter), как описано далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="d5d1a-138">Основной недостаток при использовании <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> напрямую заключается в том, что компонент не получает автоматического уведомления при изменении базовых данных о состоянии аутентификации.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="d5d1a-139">Служба <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> может предоставить данные <xref:System.Security.Claims.ClaimsPrincipal> о текущем пользователе, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="d5d1a-140">Если `user.Identity.IsAuthenticated` имеет значение `true`, а пользователь является <xref:System.Security.Claims.ClaimsPrincipal>, можно перечислить утверждения и оценить членство в ролях.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="d5d1a-141">Дополнительные сведения о внедрении зависимостей и службах см. в статьях <xref:blazor/fundamentals/dependency-injection> и <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="d5d1a-142">Реализация пользовательского AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="d5d1a-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="d5d1a-143">Если приложению требуется пользовательский поставщик, реализуйте <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> и переопределите `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="d5d1a-144">В приложении Blazor WebAssembly служба `CustomAuthStateProvider` регистрируется в `Main` `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="d5d1a-145">В приложении Blazor Server служба `CustomAuthStateProvider` регистрируется в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="d5d1a-146">С помощью `CustomAuthStateProvider` в предыдущем примере все пользователи проходят проверку подлинности с использованием имени пользователя `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="d5d1a-147">Предоставление состояния аутентификации в качестве каскадного параметра</span><span class="sxs-lookup"><span data-stu-id="d5d1a-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="d5d1a-148">Если процедурная логика требует данных о состоянии аутентификации, например при выполнении активируемых пользователем действий, для получения таких данных определите каскадный параметр типа `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="d5d1a-149">Если `user.Identity.IsAuthenticated` имеет значение `true`, можно перечислить утверждения и оценить членство в ролях.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="d5d1a-150">Настройте каскадный параметр `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` с помощью компонентов <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> и <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> в компоненте `App` (`App.razor`).</span><span class="sxs-lookup"><span data-stu-id="d5d1a-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="d5d1a-151">В приложении Blazor WebAssembly добавьте службы для параметров и авторизации в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="d5d1a-152">В приложении Blazor Server уже есть службы для параметров и авторизации, поэтому ничего делать не нужно.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="d5d1a-153">Авторизация</span><span class="sxs-lookup"><span data-stu-id="d5d1a-153">Authorization</span></span>

<span data-ttu-id="d5d1a-154">После аутентификации пользователя применяются правила *авторизации* , которые определяют доступные этому пользователю действия.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="d5d1a-155">Доступ обычно предоставляется или запрещается в зависимости от следующих аспектов:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="d5d1a-156">выполнена ли аутентификация (выполнен ли вход);</span><span class="sxs-lookup"><span data-stu-id="d5d1a-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="d5d1a-157">есть ли у пользователя определенная *роль* ;</span><span class="sxs-lookup"><span data-stu-id="d5d1a-157">A user is in a *role*.</span></span>
* <span data-ttu-id="d5d1a-158">есть ли у пользователя определенное *утверждение* ;</span><span class="sxs-lookup"><span data-stu-id="d5d1a-158">A user has a *claim*.</span></span>
* <span data-ttu-id="d5d1a-159">выполняются ли требования *политики*.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="d5d1a-160">Каждый из этих аспектов применяется здесь так же, как в приложениях ASP.NET Core MVC или Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="d5d1a-161">Дополнительные сведения о безопасности в ASP.NET Core вы найдете в статьях [о безопасности и Identity в ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="d5d1a-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="d5d1a-162">Компонент AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="d5d1a-162">AuthorizeView component</span></span>

<span data-ttu-id="d5d1a-163">Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> избирательно демонстрирует элементы пользовательского интерфейса в зависимости от того, имеет ли пользователь права на доступ к этим элементам.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="d5d1a-164">Этот подход полезен, если вам нужно просто *отображать* пользователю данные, и не использовать удостоверение пользователя в процедурной логике.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="d5d1a-165">Этот компонент представляет переменную `context` типа <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, которую можно использовать для доступа к сведениям о пользователе, выполнившем вход:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="d5d1a-166">Также вы можете указать другое содержимое, которое будет отображаться, если пользователь не выполнил аутентификацию:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="d5d1a-167">Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> можно использовать в компоненте `NavMenu` (`Shared/NavMenu.razor`) для отображения элемента списка (`<li>...</li>`) для [компонента `NavLink`](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), но следует учитывать, что при использовании этого подхода удаляется только элемент списка из отображаемых выходных данных.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="d5d1a-168">Пользователь по-прежнему может переходить к компоненту.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="d5d1a-169">Содержимое тегов `<Authorized>` и `<NotAuthorized>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="d5d1a-170">Условия авторизации, такие как роли или правила для выбора вариантов пользовательского интерфейса и доступа к ним, описаны в разделе [об авторизации](#authorization).</span><span class="sxs-lookup"><span data-stu-id="d5d1a-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="d5d1a-171">Если условия авторизации не указаны, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> использует политику по умолчанию со следующими правилами:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="d5d1a-172">выполнившие аутентификацию (выполнившие вход) пользователи считаются авторизованными;</span><span class="sxs-lookup"><span data-stu-id="d5d1a-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="d5d1a-173">не выполнившие аутентификацию (не выполнившие вход) пользователи считаются не авторизованными.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="d5d1a-174">Авторизация на основе ролей и политик</span><span class="sxs-lookup"><span data-stu-id="d5d1a-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="d5d1a-175">Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> поддерживает авторизацию на основе *ролей* или *политик*.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="d5d1a-176">Для авторизации на основе ролей примените параметр <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="d5d1a-177">Для получения дополнительной информации см. <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="d5d1a-178">Для авторизации на основе политик примените параметр <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="d5d1a-179">Авторизация на основе утверждений считается особым случаем авторизации на основе политик.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="d5d1a-180">Например, вы можете определить политику, которая требует наличия определенного утверждения у пользователя.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="d5d1a-181">Для получения дополнительной информации см. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="d5d1a-182">Эти API-интерфейсы можно использовать в приложениях Blazor Server или Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="d5d1a-183">Если не указано ни <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>, ни <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> использует политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="d5d1a-184">Содержимое, отображаемое при асинхронной аутентификации</span><span class="sxs-lookup"><span data-stu-id="d5d1a-184">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="d5d1a-185">Blazor позволяет *асинхронно* определять состояние проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-185">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="d5d1a-186">Основной сценарий для такого подхода — приложение Blazor WebAssembly, которое направляет запрос на проверку подлинности во внешнюю конечную точку.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="d5d1a-187">Пока аутентификация выполняется, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> по умолчанию не отображает содержимое.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="d5d1a-188">Для отображения содержимого в ходе проверки подлинности используйте тег `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-188">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="d5d1a-189">Такой подход обычно не применим к приложениям Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-189">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="d5d1a-190">Приложения Blazor Server узнают состояние проверки подлинности, как только оно устанавливается.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-190">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="d5d1a-191">Содержимое <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> можно указать в компоненте <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> для приложения Blazor Server, но это содержимое никогда не отображается.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="d5d1a-192">Атрибут [Authorize]</span><span class="sxs-lookup"><span data-stu-id="d5d1a-192">[Authorize] attribute</span></span>

<span data-ttu-id="d5d1a-193">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) можно использовать в компонентах Razor:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="d5d1a-194">Используйте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) только для компонентов `@page`, полученных через маршрутизатор Blazor.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="d5d1a-195">Авторизация выполняется только как аспект маршрутизации и *не* для дочерних компонентов, которые отображаются на странице.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="d5d1a-196">Чтобы разрешить отображение конкретных частей на странице, используйте вместо этого <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="d5d1a-197">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) также поддерживает авторизацию на основе ролей или политик.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="d5d1a-198">Для авторизации на основе ролей примените параметр <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="d5d1a-199">Для авторизации на основе политик примените параметр <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="d5d1a-200">Если не указано ни <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>, ни <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) использует политику по умолчанию со следующими правилами:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="d5d1a-201">выполнившие аутентификацию (выполнившие вход) пользователи считаются авторизованными;</span><span class="sxs-lookup"><span data-stu-id="d5d1a-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="d5d1a-202">не выполнившие аутентификацию (не выполнившие вход) пользователи считаются не авторизованными.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="d5d1a-203">Настройка несанкционированного содержимого для компонента маршрутизатора</span><span class="sxs-lookup"><span data-stu-id="d5d1a-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="d5d1a-204">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе с компонентом <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> позволяет приложению указать пользовательское содержимое для следующих ситуаций:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="d5d1a-205">содержимое не найдено;</span><span class="sxs-lookup"><span data-stu-id="d5d1a-205">Content isn't found.</span></span>
* <span data-ttu-id="d5d1a-206">пользователь не удовлетворяет условию [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), которое применено к компоненту</span><span class="sxs-lookup"><span data-stu-id="d5d1a-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="d5d1a-207">(атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) описан в разделе [Атрибут `[Authorize]`](#authorize-attribute));</span><span class="sxs-lookup"><span data-stu-id="d5d1a-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="d5d1a-208">выполняется асинхронная аутентификация.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="d5d1a-209">В стандартном шаблоне проекта приложения Blazor Server есть компонент `App` (`App.razor`) для настройки пользовательского содержимого.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-209">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="d5d1a-210">Содержимое тегов `<NotFound>`, `<NotAuthorized>` и `<Authorizing>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="d5d1a-211">Если тег `<NotAuthorized>` не указан, то <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> использует следующее резервное сообщение:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-211">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="d5d1a-212">Уведомление об изменении состояния аутентификации</span><span class="sxs-lookup"><span data-stu-id="d5d1a-212">Notification about authentication state changes</span></span>

<span data-ttu-id="d5d1a-213">Если приложение определяет, что базовые данные о состоянии аутентификации изменились (например, пользователь вышел из системы или другой пользователь изменил роль), [ пользовательский компонент `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) может вызвать необязательный метод <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> в базовом классе <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="d5d1a-214">Это действие информирует потребителей данных состояния аутентификации (таких, как <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) о необходимости повторно отображения с учетом новых данных.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="d5d1a-215">Процедурная логика</span><span class="sxs-lookup"><span data-stu-id="d5d1a-215">Procedural logic</span></span>

<span data-ttu-id="d5d1a-216">Если приложению нужно проверять правила авторизации в составе процедурной логики, используйте каскадный параметр с типом `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, чтобы получить <xref:System.Security.Claims.ClaimsPrincipal> пользователя.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="d5d1a-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` можно комбинировать для оценки политик с другими службами, например `IAuthorizationService`.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="d5d1a-218">В компоненте приложения Blazor WebAssembly добавьте пространства имен <xref:Microsoft.AspNetCore.Authorization> и <xref:Microsoft.AspNetCore.Components.Authorization>:</span><span class="sxs-lookup"><span data-stu-id="d5d1a-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="d5d1a-219">Эти пространства имен можно предоставить глобально, добавив их в файл `_Imports.razor` приложения.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="d5d1a-220">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="d5d1a-220">Troubleshoot errors</span></span>

<span data-ttu-id="d5d1a-221">Распространенные ошибки</span><span class="sxs-lookup"><span data-stu-id="d5d1a-221">Common errors:</span></span>

* <span data-ttu-id="d5d1a-222">**Для авторизации требуется каскадный параметр с типом `Task\<AuthenticationState>`. Чтобы предоставить его, попробуйте использовать `CascadingAuthenticationState`.**</span><span class="sxs-lookup"><span data-stu-id="d5d1a-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="d5d1a-223">**Для `authenticationStateTask` возвращается значение `null`**</span><span class="sxs-lookup"><span data-stu-id="d5d1a-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="d5d1a-224">Вполне вероятно, что проект не был создан на основе шаблона приложения Blazor Server с включенной проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="d5d1a-225">Примените `<CascadingAuthenticationState>` в качестве оболочки определенной части дерева пользовательского интерфейса, например в компоненте `App` (`App.razor`), следующим образом.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="d5d1a-226"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> предоставляет каскадный параметр `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, который он, в свою очередь, получает из базовой службы внедрения зависимостей <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>.</span><span class="sxs-lookup"><span data-stu-id="d5d1a-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5d1a-227">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d5d1a-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="d5d1a-228">[Awesome Blazor: проверка подлинности](https://github.com/AdrienTorris/awesome-blazor#authentication) — ссылки на примеры от сообщества</span><span class="sxs-lookup"><span data-stu-id="d5d1a-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
