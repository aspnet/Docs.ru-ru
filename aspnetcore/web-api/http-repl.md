---
title: Тестирование веб-API с помощью HTTP REPL
author: scottaddie
description: Узнайте, как использовать глобальное средство .NET Core HTTP REPL для просмотра и тестирования веб-API ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 05/20/2020
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
uid: web-api/http-repl
ms.openlocfilehash: efd2208044ad6392131216266afc34187d738b78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058978"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="abbfb-103">Тестирование веб-API с помощью HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="abbfb-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="abbfb-104">Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="abbfb-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="abbfb-105">HTTP read-eval-print loop (REPL):</span><span class="sxs-lookup"><span data-stu-id="abbfb-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="abbfb-106">это кроссплатформенная программа командной строки, которая поддерживается везде, где поддерживается .NET Core;</span><span class="sxs-lookup"><span data-stu-id="abbfb-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="abbfb-107">служит для создания HTTP-запросов с целью тестирования веб-API ASP.NET Core (а также веб-API, не связанных с ASP.NET Core) и просмотра их результатов;</span><span class="sxs-lookup"><span data-stu-id="abbfb-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="abbfb-108">может использоваться для тестирования веб-API, размещенных в любой среде, включая localhost и Службу приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="abbfb-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="abbfb-109">Поддерживаются следующие [HTTP-команды](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods):</span><span class="sxs-lookup"><span data-stu-id="abbfb-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="abbfb-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="abbfb-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="abbfb-111">GET</span><span class="sxs-lookup"><span data-stu-id="abbfb-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="abbfb-112">Глава</span><span class="sxs-lookup"><span data-stu-id="abbfb-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="abbfb-113">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="abbfb-114">ЗАЩИТЫ</span><span class="sxs-lookup"><span data-stu-id="abbfb-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="abbfb-115">POST</span><span class="sxs-lookup"><span data-stu-id="abbfb-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="abbfb-116">ОТПРАВКА</span><span class="sxs-lookup"><span data-stu-id="abbfb-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="abbfb-117">Для выполнения дальнейших инструкций [просмотрите или скачайте пример веб-API ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="abbfb-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="abbfb-118">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="abbfb-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="abbfb-119">Установка</span><span class="sxs-lookup"><span data-stu-id="abbfb-119">Installation</span></span>

<span data-ttu-id="abbfb-120">Чтобы установить HTTP REPL, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="abbfb-121">[Глобальное средство .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) устанавливается из пакета NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="abbfb-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="abbfb-122">Использование</span><span class="sxs-lookup"><span data-stu-id="abbfb-122">Usage</span></span>

<span data-ttu-id="abbfb-123">После успешной установки HTTP REPL выполните следующую команду, чтобы запустить средство:</span><span class="sxs-lookup"><span data-stu-id="abbfb-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="abbfb-124">Чтобы просмотреть доступные команды HTTP REPL, выполните одну из следующих команд:</span><span class="sxs-lookup"><span data-stu-id="abbfb-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="abbfb-125">Выводится следующий результат.</span><span class="sxs-lookup"><span data-stu-id="abbfb-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="abbfb-126">В HTTP REPL поддерживается завершение команд.</span><span class="sxs-lookup"><span data-stu-id="abbfb-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="abbfb-127">Нажимая клавишу <kbd>TAB</kbd>, можно переходить по списку команд, которые начинаются с введенных символов или конечной точки API.</span><span class="sxs-lookup"><span data-stu-id="abbfb-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="abbfb-128">Доступные команды CLI описываются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="abbfb-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="abbfb-129">Подключение к веб-API</span><span class="sxs-lookup"><span data-stu-id="abbfb-129">Connect to the web API</span></span>

<span data-ttu-id="abbfb-130">Чтобы подключиться к веб-API, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="abbfb-131">`<ROOT URI>` — это базовый универсальный код ресурса (URI) для веб-API.</span><span class="sxs-lookup"><span data-stu-id="abbfb-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="abbfb-132">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="abbfb-133">Кроме того, когда программа HTTP REPL запущена, можно в любой момент выполнить следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="abbfb-134">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="abbfb-135">Создание ссылки на документ Swagger для веб-API вручную</span><span class="sxs-lookup"><span data-stu-id="abbfb-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="abbfb-136">Приведенная выше команда для подключения попытается автоматически найти документ Swagger.</span><span class="sxs-lookup"><span data-stu-id="abbfb-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="abbfb-137">Если по какой-то причине ей не удается это сделать, укажите для веб-API универсальный код ресурса (URI) для документа Swagger, используя параметр `--swagger`:</span><span class="sxs-lookup"><span data-stu-id="abbfb-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="abbfb-138">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="abbfb-139">Просмотр веб-API</span><span class="sxs-lookup"><span data-stu-id="abbfb-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="abbfb-140">Просмотр доступных конечных точек</span><span class="sxs-lookup"><span data-stu-id="abbfb-140">View available endpoints</span></span>

<span data-ttu-id="abbfb-141">Чтобы получить список конечных точек (контроллеров) по текущему пути веб-API, выполните команду `ls` или `dir`:</span><span class="sxs-lookup"><span data-stu-id="abbfb-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="abbfb-142">Будут выведены данные в следующем формате:</span><span class="sxs-lookup"><span data-stu-id="abbfb-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="abbfb-143">Из приведенных выше результатов видно, что доступно два контроллера: `Fruits` и `People`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="abbfb-144">Они оба поддерживают операции HTTP GET и POST без параметров.</span><span class="sxs-lookup"><span data-stu-id="abbfb-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="abbfb-145">Более подробную информацию можно получить, перейдя к определенному контроллеру.</span><span class="sxs-lookup"><span data-stu-id="abbfb-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="abbfb-146">Например, из выходных данных приведенной ниже команды видно, что контроллер `Fruits` также поддерживает операции HTTP GET, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="abbfb-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="abbfb-147">Каждая из этих операций принимает параметр `id` в маршруте:</span><span class="sxs-lookup"><span data-stu-id="abbfb-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="abbfb-148">Кроме того, можно выполнить команду `ui`, чтобы открыть страницу с пользовательским интерфейсом Swagger веб-API в браузере.</span><span class="sxs-lookup"><span data-stu-id="abbfb-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="abbfb-149">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="abbfb-150">Переход к конечной точке</span><span class="sxs-lookup"><span data-stu-id="abbfb-150">Navigate to an endpoint</span></span>

<span data-ttu-id="abbfb-151">Чтобы перейти к другой конечной точке веб-API, выполните команду `cd`:</span><span class="sxs-lookup"><span data-stu-id="abbfb-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="abbfb-152">В пути, указываемом после команды `cd`, регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="abbfb-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="abbfb-153">Будут выведены данные в следующем формате:</span><span class="sxs-lookup"><span data-stu-id="abbfb-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="abbfb-154">Настройка HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="abbfb-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="abbfb-155">[Цвета](#set-color-preferences), используемые в HTTP REPL по умолчанию, можно настроить.</span><span class="sxs-lookup"><span data-stu-id="abbfb-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="abbfb-156">Кроме того, можно определить [текстовый редактор по умолчанию](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="abbfb-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="abbfb-157">Настройки HTTP REPL сохраняются на протяжении текущего сеанса и учитываются при открытии следующих сеансов.</span><span class="sxs-lookup"><span data-stu-id="abbfb-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="abbfb-158">Измененные настройки хранятся в следующем файле:</span><span class="sxs-lookup"><span data-stu-id="abbfb-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="abbfb-159">Linux</span><span class="sxs-lookup"><span data-stu-id="abbfb-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="abbfb-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="abbfb-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="abbfb-161">macOS</span><span class="sxs-lookup"><span data-stu-id="abbfb-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="abbfb-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="abbfb-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="abbfb-163">Windows</span><span class="sxs-lookup"><span data-stu-id="abbfb-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="abbfb-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="abbfb-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="abbfb-165">Файл *HTTPREPLPREFS* загружается при запуске. Во время выполнения изменения в нем не отслеживаются.</span><span class="sxs-lookup"><span data-stu-id="abbfb-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="abbfb-166">Изменения, внесенные в него вручную, вступают в силу только после перезапуска средства.</span><span class="sxs-lookup"><span data-stu-id="abbfb-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="abbfb-167">Просмотр параметров</span><span class="sxs-lookup"><span data-stu-id="abbfb-167">View the settings</span></span>

<span data-ttu-id="abbfb-168">Чтобы просмотреть доступные параметры, выполните команду `pref get`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="abbfb-169">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="abbfb-170">В результате выполнения приведенной выше команды выводятся доступные пары "ключ-значение":</span><span class="sxs-lookup"><span data-stu-id="abbfb-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="abbfb-171">Настройки цветов</span><span class="sxs-lookup"><span data-stu-id="abbfb-171">Set color preferences</span></span>

<span data-ttu-id="abbfb-172">Раскрашивание ответов в настоящее время поддерживается только для JSON.</span><span class="sxs-lookup"><span data-stu-id="abbfb-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="abbfb-173">Чтобы настроить цвета по умолчанию в программе HTTP REPL, найдите ключ, соответствующий изменяемому цвету.</span><span class="sxs-lookup"><span data-stu-id="abbfb-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="abbfb-174">Инструкции по поиску ключей см. в разделе [Просмотр параметров](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="abbfb-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="abbfb-175">Например, измените значение ключа `colors.json` с `Green` на `White` следующим образом:</span><span class="sxs-lookup"><span data-stu-id="abbfb-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="abbfb-176">Можно использовать только [допустимые цвета](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs).</span><span class="sxs-lookup"><span data-stu-id="abbfb-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="abbfb-177">При выполнении последующих HTTP-запросов к выходным данным будут применяться новые цвета.</span><span class="sxs-lookup"><span data-stu-id="abbfb-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="abbfb-178">Если определенные цветовые ключи не заданы, используются более общие ключи.</span><span class="sxs-lookup"><span data-stu-id="abbfb-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="abbfb-179">Рассмотрим это поведение на следующем примере:</span><span class="sxs-lookup"><span data-stu-id="abbfb-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="abbfb-180">Если у `colors.json.name` нет значения, используется `colors.json.string`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="abbfb-181">Если у `colors.json.string` нет значения, используется `colors.json.literal`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="abbfb-182">Если у `colors.json.literal` нет значения, используется `colors.json`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="abbfb-183">Если у `colors.json` нет значения, используется текст цвета по умолчанию для командной оболочки (`AllowedColors.None`).</span><span class="sxs-lookup"><span data-stu-id="abbfb-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="abbfb-184">Установка размера отступа</span><span class="sxs-lookup"><span data-stu-id="abbfb-184">Set indentation size</span></span>

<span data-ttu-id="abbfb-185">Настройка размера отступа в ответах в настоящее время поддерживается только для JSON.</span><span class="sxs-lookup"><span data-stu-id="abbfb-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="abbfb-186">Размер по умолчанию — два пробела.</span><span class="sxs-lookup"><span data-stu-id="abbfb-186">The default size is two spaces.</span></span> <span data-ttu-id="abbfb-187">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="abbfb-188">Чтобы изменить размер по умолчанию, задайте ключ `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="abbfb-189">Например, чтобы использовались четыре пробела, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="abbfb-190">В последующих ответах будет применяться отступ в четыре пробела:</span><span class="sxs-lookup"><span data-stu-id="abbfb-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="abbfb-191">Установка текстового редактора по умолчанию</span><span class="sxs-lookup"><span data-stu-id="abbfb-191">Set the default text editor</span></span>

<span data-ttu-id="abbfb-192">По умолчанию текстовый редактор для HTTP REPL не настроен.</span><span class="sxs-lookup"><span data-stu-id="abbfb-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="abbfb-193">Для тестирования методов веб-API, требующих текста HTTP-запроса, необходимо задать текстовый редактор по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="abbfb-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="abbfb-194">Средство HTTP REPL запускает настроенный текстовый редактор исключительно в целях составления текста запроса.</span><span class="sxs-lookup"><span data-stu-id="abbfb-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="abbfb-195">Чтобы указать текстовый редактор по умолчанию, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="abbfb-196">В приведенной выше команде `<EXECUTABLE>` — это полный путь к исполняемому файлу текстового редактора.</span><span class="sxs-lookup"><span data-stu-id="abbfb-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="abbfb-197">Например, чтобы задать Visual Studio Code как текстовый редактор по умолчанию, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="abbfb-198">Linux</span><span class="sxs-lookup"><span data-stu-id="abbfb-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="abbfb-199">macOS</span><span class="sxs-lookup"><span data-stu-id="abbfb-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="abbfb-200">Windows</span><span class="sxs-lookup"><span data-stu-id="abbfb-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="abbfb-201">Чтобы текстовый редактор по умолчанию запускался с определенными аргументами CLI, задайте ключ `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="abbfb-202">Например, предположим, что Visual Studio Code — это текстовый редактор по умолчанию и необходимо, чтобы средство HTTP REPL всегда запускало сеанс Visual Studio Code с отключенными расширениями.</span><span class="sxs-lookup"><span data-stu-id="abbfb-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="abbfb-203">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="abbfb-204">Настройка путей поиска Swagger</span><span class="sxs-lookup"><span data-stu-id="abbfb-204">Set the Swagger search paths</span></span>

<span data-ttu-id="abbfb-205">По умолчанию REPL HTTP имеет набор относительных путей для поиска документа Swagger при выполнении команды `connect` без параметра `--swagger`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="abbfb-206">Эти относительные пути объединяются с корневыми и базовыми путями, указанными в команде `connect`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="abbfb-207">Относительные пути по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="abbfb-207">The default relative paths are:</span></span>

- <span data-ttu-id="abbfb-208">*swagger.jsна*</span><span class="sxs-lookup"><span data-stu-id="abbfb-208">*swagger.json*</span></span>
- <span data-ttu-id="abbfb-209">*Swagger/v1/swagger.jsв*</span><span class="sxs-lookup"><span data-stu-id="abbfb-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="abbfb-210">*/swagger.jsна*</span><span class="sxs-lookup"><span data-stu-id="abbfb-210">*/swagger.json*</span></span>
- <span data-ttu-id="abbfb-211">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="abbfb-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="abbfb-212">Чтобы использовать другой набор путей поиска в среде, используйте параметр `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="abbfb-213">Значение должно представлять собой список относительных путей, разделенных символом вертикальной черты.</span><span class="sxs-lookup"><span data-stu-id="abbfb-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="abbfb-214">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="abbfb-215">Тестирование HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="abbfb-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-216">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-217">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-218">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-219">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-219">Options</span></span>

<span data-ttu-id="abbfb-220">Для команды `get` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="abbfb-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="abbfb-221">Пример</span><span class="sxs-lookup"><span data-stu-id="abbfb-221">Example</span></span>

<span data-ttu-id="abbfb-222">Чтобы отправить HTTP-запрос GET, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="abbfb-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="abbfb-223">Выполните команду `get` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="abbfb-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="abbfb-224">Выходные данные приведенной выше команды имеют следующий формат:</span><span class="sxs-lookup"><span data-stu-id="abbfb-224">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="abbfb-225">Получите определенную запись, передав параметр в команду `get`:</span><span class="sxs-lookup"><span data-stu-id="abbfb-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="abbfb-226">Выходные данные приведенной выше команды имеют следующий формат:</span><span class="sxs-lookup"><span data-stu-id="abbfb-226">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="abbfb-227">Тестирование HTTP-запросов POST</span><span class="sxs-lookup"><span data-stu-id="abbfb-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-228">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-229">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-230">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-231">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="abbfb-232">Пример</span><span class="sxs-lookup"><span data-stu-id="abbfb-232">Example</span></span>

<span data-ttu-id="abbfb-233">Чтобы отправить HTTP-запрос POST, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="abbfb-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="abbfb-234">Выполните команду `post` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="abbfb-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="abbfb-235">В приведенной выше команде в заголовке HTTP-запроса `Content-Type` указан формат текста запроса JSON.</span><span class="sxs-lookup"><span data-stu-id="abbfb-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="abbfb-236">В текстовом редакторе по умолчанию открывается файл *TMP* с шаблоном JSON, представляющим текст HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="abbfb-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="abbfb-237">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="abbfb-238">Инструкции по определению текстового редактора по умолчанию см. в разделе [Установка текстового редактора по умолчанию](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="abbfb-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="abbfb-239">Измените шаблон JSON в соответствии с требованиями к проверке модели:</span><span class="sxs-lookup"><span data-stu-id="abbfb-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="abbfb-240">Сохраните файл *TMP* и закройте текстовый редактор.</span><span class="sxs-lookup"><span data-stu-id="abbfb-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="abbfb-241">В командной оболочке появятся следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="abbfb-241">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="abbfb-242">Тестирование HTTP-запросов PUT</span><span class="sxs-lookup"><span data-stu-id="abbfb-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-243">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-244">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-245">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-246">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="abbfb-247">Пример</span><span class="sxs-lookup"><span data-stu-id="abbfb-247">Example</span></span>

<span data-ttu-id="abbfb-248">Чтобы отправить HTTP-запрос PUT, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="abbfb-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="abbfb-249">*Необязательно* . выполните `get` команду, чтобы просмотреть данные перед их изменением:</span><span class="sxs-lookup"><span data-stu-id="abbfb-249">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="abbfb-250">Выполните команду `put` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="abbfb-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="abbfb-251">В приведенной выше команде в заголовке HTTP-запроса `Content-Type` указан формат текста запроса JSON.</span><span class="sxs-lookup"><span data-stu-id="abbfb-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="abbfb-252">В текстовом редакторе по умолчанию открывается файл *TMP* с шаблоном JSON, представляющим текст HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="abbfb-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="abbfb-253">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="abbfb-254">Инструкции по определению текстового редактора по умолчанию см. в разделе [Установка текстового редактора по умолчанию](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="abbfb-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="abbfb-255">Измените шаблон JSON в соответствии с требованиями к проверке модели:</span><span class="sxs-lookup"><span data-stu-id="abbfb-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="abbfb-256">Сохраните файл *TMP* и закройте текстовый редактор.</span><span class="sxs-lookup"><span data-stu-id="abbfb-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="abbfb-257">В командной оболочке появятся следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="abbfb-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="abbfb-258">*Необязательно* . выполните `get` команду, чтобы просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="abbfb-258">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="abbfb-259">Например, если вы ввели "Cherry" в текстовом редакторе, команда `get` вернет следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="abbfb-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="abbfb-260">Тестирование HTTP-запросов DELETE</span><span class="sxs-lookup"><span data-stu-id="abbfb-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-261">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-262">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-263">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-264">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="abbfb-265">Пример</span><span class="sxs-lookup"><span data-stu-id="abbfb-265">Example</span></span>

<span data-ttu-id="abbfb-266">Чтобы отправить HTTP-запрос DELETE, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="abbfb-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="abbfb-267">*Необязательно* . выполните `get` команду, чтобы просмотреть данные перед их изменением:</span><span class="sxs-lookup"><span data-stu-id="abbfb-267">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="abbfb-268">Выполните команду `delete` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="abbfb-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="abbfb-269">Выходные данные приведенной выше команды имеют следующий формат:</span><span class="sxs-lookup"><span data-stu-id="abbfb-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="abbfb-270">*Необязательно* . выполните `get` команду, чтобы просмотреть изменения.</span><span class="sxs-lookup"><span data-stu-id="abbfb-270">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="abbfb-271">В этом примере команда `get` возвращает следующие данные:</span><span class="sxs-lookup"><span data-stu-id="abbfb-271">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="abbfb-272">Тестирование HTTP-запросов PATCH</span><span class="sxs-lookup"><span data-stu-id="abbfb-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-273">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-274">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-275">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-276">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="abbfb-277">Тестирование HTTP-запросов HEAD</span><span class="sxs-lookup"><span data-stu-id="abbfb-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-278">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-279">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-280">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-281">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="abbfb-282">Тестирование HTTP-запросов OPTIONS</span><span class="sxs-lookup"><span data-stu-id="abbfb-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="abbfb-283">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="abbfb-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="abbfb-284">Аргументы</span><span class="sxs-lookup"><span data-stu-id="abbfb-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="abbfb-285">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="abbfb-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="abbfb-286">Параметры</span><span class="sxs-lookup"><span data-stu-id="abbfb-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="abbfb-287">Установка заголовков HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="abbfb-287">Set HTTP request headers</span></span>

<span data-ttu-id="abbfb-288">Задать заголовок HTTP-запроса можно одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="abbfb-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="abbfb-289">Внутри HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="abbfb-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="abbfb-290">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="abbfb-291">При таком подходе для каждого заголовка HTTP-запроса требуется собственный параметр `-h`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="abbfb-292">Перед отправкой HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="abbfb-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="abbfb-293">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="abbfb-294">Если заголовок задается перед отправкой запроса, он продолжает действовать на протяжении всего сеанса командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="abbfb-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="abbfb-295">Чтобы очистить заголовок, укажите пустое значение.</span><span class="sxs-lookup"><span data-stu-id="abbfb-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="abbfb-296">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="abbfb-297">Проверка защищенных конечных точек</span><span class="sxs-lookup"><span data-stu-id="abbfb-297">Test secured endpoints</span></span>

<span data-ttu-id="abbfb-298">HTTP REPL поддерживает тестирование защищенных конечных точек двумя способами: с помощью учетных данных пользователя, выполнившего вход в систему, или с помощью заголовков HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="abbfb-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="abbfb-299">Учетные данные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="abbfb-299">Default credentials</span></span>

<span data-ttu-id="abbfb-300">Рассмотрим ситуацию, в которой тестируемый веб-API размещается в IIS и защищен с помощью проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="abbfb-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="abbfb-301">Необходимо, чтобы учетные данные пользователя, запускающего средство, переводились в тестируемые конечные точки HTTP.</span><span class="sxs-lookup"><span data-stu-id="abbfb-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="abbfb-302">Чтобы передать учетные данные пользователя, выполнившего вход в систему, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="abbfb-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="abbfb-303">Задайте `httpClient.useDefaultCredentials` для параметра значение `true` :</span><span class="sxs-lookup"><span data-stu-id="abbfb-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="abbfb-304">Закройте и перезапустите инструмент перед отправкой другого запроса к веб-API.</span><span class="sxs-lookup"><span data-stu-id="abbfb-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="abbfb-305">Заголовки запросов HTTP</span><span class="sxs-lookup"><span data-stu-id="abbfb-305">HTTP request headers</span></span>

<span data-ttu-id="abbfb-306">Примеры поддерживаемых схем проверки подлинности и авторизации включают в себя обычную проверку подлинности, токены носителя JWT и дайджест-аутентификацию.</span><span class="sxs-lookup"><span data-stu-id="abbfb-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="abbfb-307">Например, можно отправить токен носителя в конечную точку с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="abbfb-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="abbfb-308">Для доступа к конечной точке, размещенной в Azure, или для использования [Azure REST API](/rest/api/azure/) необходим токен носителя.</span><span class="sxs-lookup"><span data-stu-id="abbfb-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="abbfb-309">Выполните следующие действия, чтобы получить токен носителя для подписки Azure с помощью [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="abbfb-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="abbfb-310">HTTP REPL задает токен носителя в заголовке HTTP-запроса и получает список веб-приложений Службы приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="abbfb-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="abbfb-311">Войдите в Azure:</span><span class="sxs-lookup"><span data-stu-id="abbfb-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="abbfb-312">Получите идентификатор подписки с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="abbfb-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="abbfb-313">Скопируйте идентификатор подписки и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="abbfb-314">Получите токен носителя с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="abbfb-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="abbfb-315">Подключитесь к Azure REST API с помощью HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="abbfb-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="abbfb-316">Задайте заголовок запроса HTTP `Authorization`:</span><span class="sxs-lookup"><span data-stu-id="abbfb-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="abbfb-317">Перейдите к подписке:</span><span class="sxs-lookup"><span data-stu-id="abbfb-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="abbfb-318">Получите список веб-приложений Службы приложений Azure для вашей подписки:</span><span class="sxs-lookup"><span data-stu-id="abbfb-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="abbfb-319">Появится следующий ответ:</span><span class="sxs-lookup"><span data-stu-id="abbfb-319">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="abbfb-320">Включение и отключение отображения HTTP-запроса</span><span class="sxs-lookup"><span data-stu-id="abbfb-320">Toggle HTTP request display</span></span>

<span data-ttu-id="abbfb-321">По умолчанию отправляемый HTTP-запрос не отображается.</span><span class="sxs-lookup"><span data-stu-id="abbfb-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="abbfb-322">Соответствующую настройку можно изменить на всю длительность сеанса командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="abbfb-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="abbfb-323">Включение отображения запроса</span><span class="sxs-lookup"><span data-stu-id="abbfb-323">Enable request display</span></span>

<span data-ttu-id="abbfb-324">Чтобы отправляемый HTTP-запрос отображался, выполните команду `echo on`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="abbfb-325">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="abbfb-326">При отправке последующих HTTP-запросов в рамках текущего сеанса заголовки запросов будут отображаться.</span><span class="sxs-lookup"><span data-stu-id="abbfb-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="abbfb-327">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-327">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="abbfb-328">Отключение отображения запроса</span><span class="sxs-lookup"><span data-stu-id="abbfb-328">Disable request display</span></span>

<span data-ttu-id="abbfb-329">Чтобы отправляемый HTTP-запрос не отображался, выполните команду `echo off`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="abbfb-330">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="abbfb-331">Выполнить сценарий</span><span class="sxs-lookup"><span data-stu-id="abbfb-331">Run a script</span></span>

<span data-ttu-id="abbfb-332">Если вы часто выполняете один и тот же набор команд HTTP REPL, их можно сохранить в текстовом файле.</span><span class="sxs-lookup"><span data-stu-id="abbfb-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="abbfb-333">Команды в файле имеют тот же формат, что и выполняемые вручную в командной строке.</span><span class="sxs-lookup"><span data-stu-id="abbfb-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="abbfb-334">Их можно выполнять в пакетном режиме с помощью команды `run`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="abbfb-335">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-335">For example:</span></span>

1. <span data-ttu-id="abbfb-336">Создайте текстовый файл с набором команд, разделенных символами новой строки.</span><span class="sxs-lookup"><span data-stu-id="abbfb-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="abbfb-337">Например, это может быть файл *people-script.txt* со следующими командами:</span><span class="sxs-lookup"><span data-stu-id="abbfb-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="abbfb-338">Выполните команду `run`, передав в нее путь к текстовому файлу.</span><span class="sxs-lookup"><span data-stu-id="abbfb-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="abbfb-339">Пример:</span><span class="sxs-lookup"><span data-stu-id="abbfb-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="abbfb-340">Отображаются следующие результаты:</span><span class="sxs-lookup"><span data-stu-id="abbfb-340">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="abbfb-341">Очистка выходных данных</span><span class="sxs-lookup"><span data-stu-id="abbfb-341">Clear the output</span></span>

<span data-ttu-id="abbfb-342">Чтобы удалить все выходные данные средства HTTP REPL из командной оболочки, выполните команду `clear` или `cls`.</span><span class="sxs-lookup"><span data-stu-id="abbfb-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="abbfb-343">Например, предположим, что в командной оболочке есть следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="abbfb-343">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="abbfb-344">Чтобы очистить их, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abbfb-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="abbfb-345">После выполнения приведенной выше команды командная оболочка содержит только следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="abbfb-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="abbfb-346">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="abbfb-346">Additional resources</span></span>

* [<span data-ttu-id="abbfb-347">Запросы к REST API</span><span class="sxs-lookup"><span data-stu-id="abbfb-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="abbfb-348">Репозиторий HTTP REPL в GitHub</span><span class="sxs-lookup"><span data-stu-id="abbfb-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
