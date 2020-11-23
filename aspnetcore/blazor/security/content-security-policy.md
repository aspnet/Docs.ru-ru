---
title: Применение политики безопасности содержимого для ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать политику безопасности содержимого (CSP) с приложениями ASP.NET Core Blazor для защиты от атак с выполнением межсайтовых сценариев (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570124"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a><span data-ttu-id="6b527-103">Применение политики безопасности содержимого для ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6b527-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="6b527-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="6b527-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b527-105">[Выполнение межсайтовых сценариев (XSS)](xref:security/cross-site-scripting) является уязвимостью системы безопасности, когда злоумышленник помещает один или несколько вредоносных клиентских сценариев в преобразуемое для просмотра содержимое приложения.</span><span class="sxs-lookup"><span data-stu-id="6b527-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="6b527-106">Политика безопасности содержимого (CSP) помогает защититься от атак XSS путем формирования обозревателя допустимых:</span><span class="sxs-lookup"><span data-stu-id="6b527-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="6b527-107">источников загруженного содержимого, включая сценарии, таблицы стилей и изображения;</span><span class="sxs-lookup"><span data-stu-id="6b527-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="6b527-108">действий, выполняемых страницей, с указанием разрешенных целевых объектов URL-адресов форм;</span><span class="sxs-lookup"><span data-stu-id="6b527-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="6b527-109">подключаемых модулей, которые могут быть загружены.</span><span class="sxs-lookup"><span data-stu-id="6b527-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="6b527-110">Чтобы применить CSP к приложению, разработчик указывает несколько *директив* безопасности содержимого CSP в одном или нескольких заголовках `Content-Security-Policy` или тегах `<meta>`.</span><span class="sxs-lookup"><span data-stu-id="6b527-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="6b527-111">Политики оцениваются браузером во время загрузки страницы.</span><span class="sxs-lookup"><span data-stu-id="6b527-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="6b527-112">Браузер проверяет источники страницы и определяет, соответствуют ли они требованиям директив безопасности содержимого.</span><span class="sxs-lookup"><span data-stu-id="6b527-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="6b527-113">Если директивы политики не выполняются для ресурса, браузер не загружает ресурс.</span><span class="sxs-lookup"><span data-stu-id="6b527-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="6b527-114">Например, рассмотрим политику, которая не позволяет использовать сценарии сторонних разработчиков.</span><span class="sxs-lookup"><span data-stu-id="6b527-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="6b527-115">Если страница содержит тег `<script>`, в атрибуте `src` которого указан сторонний источник, браузер предотвращает загрузку сценария.</span><span class="sxs-lookup"><span data-stu-id="6b527-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="6b527-116">CSP поддерживается в большинстве современных браузеров для настольных ПК и мобильных устройств, включая Chrome, Microsoft Edge, Firefox, Opera и Safari.</span><span class="sxs-lookup"><span data-stu-id="6b527-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="6b527-117">CSP рекомендуется для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="6b527-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="6b527-118">Директивы политики</span><span class="sxs-lookup"><span data-stu-id="6b527-118">Policy directives</span></span>

<span data-ttu-id="6b527-119">Как минимум, укажите следующие директивы и источники для приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="6b527-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="6b527-120">При необходимости добавьте дополнительные директивы и источники.</span><span class="sxs-lookup"><span data-stu-id="6b527-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="6b527-121">Следующие директивы используются в разделе [Применение политики](#apply-the-policy) этой статьи, где указаны примеры политик безопасности для Blazor WebAssembly и Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="6b527-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="6b527-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): разрешает URL-адреса для тега `<base>` страницы.</span><span class="sxs-lookup"><span data-stu-id="6b527-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="6b527-123">Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="6b527-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="6b527-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): предотвращает загрузку смешанного содержимого HTTP и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6b527-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="6b527-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): указывает резервную форму для исходных директив, которые не задаются политикой явно.</span><span class="sxs-lookup"><span data-stu-id="6b527-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="6b527-126">Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="6b527-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="6b527-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): указывает допустимые источники для изображений.</span><span class="sxs-lookup"><span data-stu-id="6b527-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="6b527-128">Укажите `data:`, чтобы разрешить загрузку изображений с URL-адресов `data:`.</span><span class="sxs-lookup"><span data-stu-id="6b527-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="6b527-129">Укажите `https:`, чтобы разрешить загрузку изображений с конечных точек HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6b527-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="6b527-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): указывает допустимые источники для тегов `<object>`, `<embed>` и `<applet>`.</span><span class="sxs-lookup"><span data-stu-id="6b527-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="6b527-131">Укажите `none`, чтобы запретить все источники URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="6b527-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="6b527-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): указывает допустимые источники для сценариев.</span><span class="sxs-lookup"><span data-stu-id="6b527-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="6b527-133">Укажите источник узла `https://stackpath.bootstrapcdn.com/` для сценариев начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="6b527-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="6b527-134">Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="6b527-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="6b527-135">В приложении Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="6b527-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="6b527-136">Укажите хэши, чтобы разрешить загрузку необходимых сценариев.</span><span class="sxs-lookup"><span data-stu-id="6b527-136">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="6b527-137">Укажите `unsafe-eval`, чтобы использовать `eval()` и методы для создания кода из строк.</span><span class="sxs-lookup"><span data-stu-id="6b527-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="6b527-138">В приложении Blazor Server укажите хэши, чтобы разрешить загрузку необходимых сценариев.</span><span class="sxs-lookup"><span data-stu-id="6b527-138">In a Blazor Server app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="6b527-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): указывает допустимые источники таблиц стилей.</span><span class="sxs-lookup"><span data-stu-id="6b527-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="6b527-140">Укажите источник узла `https://stackpath.bootstrapcdn.com/` для таблиц начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="6b527-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="6b527-141">Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="6b527-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="6b527-142">Укажите `unsafe-inline`, чтобы разрешить использование встроенных стилей.</span><span class="sxs-lookup"><span data-stu-id="6b527-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="6b527-143">Для пользовательского интерфейса в приложениях Blazor Server требуется встроенное объявление для повторного подключения клиента и сервера после первоначального запроса.</span><span class="sxs-lookup"><span data-stu-id="6b527-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="6b527-144">В следующем выпуске встроенные стили могут быть удалены, поэтому `unsafe-inline` больше не требуется.</span><span class="sxs-lookup"><span data-stu-id="6b527-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="6b527-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): указывает, что URL-адреса содержимого из небезопасных (HTTP) источников должны быть безопасно получены по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6b527-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="6b527-146">Предыдущие директивы поддерживаются всеми браузерами, кроме Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="6b527-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="6b527-147">Порядок получения хэшей SHA для дополнительных встроенных сценариев:</span><span class="sxs-lookup"><span data-stu-id="6b527-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="6b527-148">Примените CSP, показанный в разделе [Применение политики](#apply-the-policy).</span><span class="sxs-lookup"><span data-stu-id="6b527-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="6b527-149">Доступ к консоли средств разработчика в браузере при локальном запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="6b527-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="6b527-150">Браузер вычисляет и отображает хэши для заблокированных сценариев при наличии заголовка CSP или тега `meta`.</span><span class="sxs-lookup"><span data-stu-id="6b527-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="6b527-151">Скопируйте хэши, предоставленные браузером, в источники `script-src`.</span><span class="sxs-lookup"><span data-stu-id="6b527-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="6b527-152">Заключите каждый хэш в одинарные кавычки.</span><span class="sxs-lookup"><span data-stu-id="6b527-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="6b527-153">Таблица поддержки браузерами политики безопасности содержимого уровня 2 приведена на сайте [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="6b527-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="6b527-154">Применение политики</span><span class="sxs-lookup"><span data-stu-id="6b527-154">Apply the policy</span></span>

<span data-ttu-id="6b527-155">Используйте тег `<meta>`, чтобы применить политику:</span><span class="sxs-lookup"><span data-stu-id="6b527-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="6b527-156">Присвойте атрибуту `http-equiv` значение `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="6b527-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="6b527-157">Поместите директивы в значение атрибута `content`.</span><span class="sxs-lookup"><span data-stu-id="6b527-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="6b527-158">Директивы следует разделять точкой с запятой (`;`).</span><span class="sxs-lookup"><span data-stu-id="6b527-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="6b527-159">Всегда помещайте тег `meta` в содержимое `<head>`.</span><span class="sxs-lookup"><span data-stu-id="6b527-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="6b527-160">В следующих разделах показаны примеры политик для Blazor WebAssembly и Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="6b527-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="6b527-161">Эти примеры приведены в этой статье с контролем версий для каждого выпуска Blazor.</span><span class="sxs-lookup"><span data-stu-id="6b527-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="6b527-162">Чтобы использовать версию, подходящую для вашего выпуска, выберите версию документа в раскрывающемся списке **Версия** на этой веб-странице.</span><span class="sxs-lookup"><span data-stu-id="6b527-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="6b527-163">В содержимом `<head>` страницы узла `wwwroot/index.html` примените директивы, описанные в разделе [Директивы политики](#policy-directives):</span><span class="sxs-lookup"><span data-stu-id="6b527-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="6b527-164">Добавьте дополнительные хэши `script-src` и `style-src`, необходимые этому приложению.</span><span class="sxs-lookup"><span data-stu-id="6b527-164">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="6b527-165">Во время разработки используйте интерактивное средство или средства разработчика браузера, которые будут вычислять хэши.</span><span class="sxs-lookup"><span data-stu-id="6b527-165">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="6b527-166">Например, следующая ошибка консоли средств браузера сообщает о том, что хэш для необходимого сценария не охвачен политикой:</span><span class="sxs-lookup"><span data-stu-id="6b527-166">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="6b527-167">Не удается выполнить встроенный сценарий, так как он нарушает следующую директиву политики безопасности содержимого: "…".</span><span class="sxs-lookup"><span data-stu-id="6b527-167">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="6b527-168">Для включения выполнения встроенного сценария требуется ключевое слово "unsafe-inline", хэш ("sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=") или nonce ("nonce-...").</span><span class="sxs-lookup"><span data-stu-id="6b527-168">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="6b527-169">Конкретный сценарий, связанный с ошибкой, отображается в консоли рядом с ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6b527-169">The particular script associated with the error is displayed in the console next to the error.</span></span>

### Blazor Server

<span data-ttu-id="6b527-170">В содержимом `<head>` страницы узла `Pages/_Host.cshtml` примените директивы, описанные в разделе [Директивы политики](#policy-directives):</span><span class="sxs-lookup"><span data-stu-id="6b527-170">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="6b527-171">Добавьте дополнительные хэши `script-src` и `style-src`, необходимые этому приложению.</span><span class="sxs-lookup"><span data-stu-id="6b527-171">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="6b527-172">Во время разработки используйте интерактивное средство или средства разработчика браузера, которые будут вычислять хэши.</span><span class="sxs-lookup"><span data-stu-id="6b527-172">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="6b527-173">Например, следующая ошибка консоли средств браузера сообщает о том, что хэш для необходимого сценария не охвачен политикой:</span><span class="sxs-lookup"><span data-stu-id="6b527-173">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="6b527-174">Не удается выполнить встроенный сценарий, так как он нарушает следующую директиву политики безопасности содержимого: "...".</span><span class="sxs-lookup"><span data-stu-id="6b527-174">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="6b527-175">Для включения выполнения встроенного сценария требуется ключевое слово "unsafe-inline", хэш ("sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=") или nonce ("nonce-...").</span><span class="sxs-lookup"><span data-stu-id="6b527-175">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="6b527-176">Конкретный сценарий, связанный с ошибкой, отображается в консоли рядом с ошибкой.</span><span class="sxs-lookup"><span data-stu-id="6b527-176">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="6b527-177">Ограничения метатегов</span><span class="sxs-lookup"><span data-stu-id="6b527-177">Meta tag limitations</span></span>

<span data-ttu-id="6b527-178">Политика тегов `<meta>` не поддерживает следующие директивы:</span><span class="sxs-lookup"><span data-stu-id="6b527-178">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="6b527-179">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="6b527-179">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="6b527-180">report-to</span><span class="sxs-lookup"><span data-stu-id="6b527-180">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="6b527-181">report-uri</span><span class="sxs-lookup"><span data-stu-id="6b527-181">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="6b527-182">sandbox</span><span class="sxs-lookup"><span data-stu-id="6b527-182">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="6b527-183">Для поддержки предыдущих директив используйте заголовок с именем `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="6b527-183">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="6b527-184">Строка директивы — это значение заголовка.</span><span class="sxs-lookup"><span data-stu-id="6b527-184">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="6b527-185">Тестирование политики и получение отчетов о нарушениях</span><span class="sxs-lookup"><span data-stu-id="6b527-185">Test a policy and receive violation reports</span></span>

<span data-ttu-id="6b527-186">Тестирование помогает убедиться в том, что сценарии сторонних разработчиков не блокируются непреднамеренно при создании начальной политики.</span><span class="sxs-lookup"><span data-stu-id="6b527-186">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="6b527-187">Чтобы протестировать политику в течение определенного периода времени без применения директив политики, задайте для атрибута`http-equiv` тега `<meta>` или заголовка политики на основе заголовков значение `Content-Security-Policy-Report-Only`.</span><span class="sxs-lookup"><span data-stu-id="6b527-187">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="6b527-188">Отчеты о сбоях отправляются как документы JSON по указанному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="6b527-188">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="6b527-189">Дополнительные сведения см. в [веб-документации MDN: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="6b527-189">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="6b527-190">Сведения о создании отчетов о нарушениях, когда политика активна, см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="6b527-190">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="6b527-191">report-to</span><span class="sxs-lookup"><span data-stu-id="6b527-191">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="6b527-192">report-uri</span><span class="sxs-lookup"><span data-stu-id="6b527-192">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="6b527-193">Хотя `report-uri` больше не рекомендуется использовать, обе директивы следует использовать до тех пор, пока `report-to` не будут поддерживаться всеми основными браузерами.</span><span class="sxs-lookup"><span data-stu-id="6b527-193">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="6b527-194">Не используйте исключительно `report-uri`, поскольку поддержка `report-uri` может быть удалена из браузеров *в любое время*.</span><span class="sxs-lookup"><span data-stu-id="6b527-194">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="6b527-195">Удалите поддержку `report-uri` в политиках, если `report-to` полностью поддерживается.</span><span class="sxs-lookup"><span data-stu-id="6b527-195">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="6b527-196">Сведения об отслеживании внедрения `report-to`см. на сайте [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="6b527-196">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="6b527-197">Протестируйте и обновляйте политику приложения в каждом выпуске.</span><span class="sxs-lookup"><span data-stu-id="6b527-197">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6b527-198">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="6b527-198">Troubleshoot</span></span>

* <span data-ttu-id="6b527-199">Ошибки отображаются в консоли средств разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="6b527-199">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="6b527-200">Браузеры предоставляют следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="6b527-200">Browsers provide information about:</span></span>
  * <span data-ttu-id="6b527-201">Элементы, которые не соответствуют политике.</span><span class="sxs-lookup"><span data-stu-id="6b527-201">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="6b527-202">Изменение политики для разрешения заблокированного элемента.</span><span class="sxs-lookup"><span data-stu-id="6b527-202">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="6b527-203">Политика полностью эффективна, только если браузер клиента поддерживает все включенные директивы.</span><span class="sxs-lookup"><span data-stu-id="6b527-203">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="6b527-204">Таблица со сведениями о поддержке браузеров см. на сайте [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="6b527-204">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b527-205">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6b527-205">Additional resources</span></span>

* [<span data-ttu-id="6b527-206">Веб-документы MDN: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="6b527-206">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="6b527-207">Политика безопасности содержимого уровня 2</span><span class="sxs-lookup"><span data-stu-id="6b527-207">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="6b527-208">Оценщик CSP Google</span><span class="sxs-lookup"><span data-stu-id="6b527-208">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
