---
title: Отладка в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: d4fd0d501ff14e37bb55b78bb6493ad43f9e5a87
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805574"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Отладка в ASP.NET Core Blazor WebAssembly

[Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Microsoft Edge или Chrome). Отлаживать приложение можно также с помощью следующих интегрированных сред разработки (IDE).

* Visual Studio
* Visual Studio для Mac
* Visual Studio Code

Поддерживаются следующие сценарии:

* Установка и удаление точек останова.
* Запуск приложения с поддержкой отладки в IDE.
* Пошаговое прохождение кода.
* Возобновление выполнения кода с помощью сочетания клавиш в IDE.
* Просмотр значений локальных переменных в окне *Локальные переменные*.
* Просмотр стека вызовов, включая цепочки вызовов между JavaScript и .NET.

Сейчас вам *недоступны* следующие функции:

* Останов при необработанных исключениях.
* Попадание в точки останова во время запуска приложения перед запуском прокси-сервера отладки. Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.

## <a name="prerequisites"></a>Предварительные требования

Для отладки требуется один из следующих браузеров:

* Google Chrome (версии 70 и более поздних) (по умолчанию)
* Microsoft Edge (версии 80 и более поздних)

Для Visual Studio для Mac требуется версия 8.8 (сборка 1532) или более поздняя.

1. Установите последнюю версию Visual Studio для Mac, нажав кнопку **Скачать Visual Studio для Mac** на странице [Microsoft: Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).
1. Выберите в Visual Studio канал *Предварительная версия*. Дополнительные сведения см. в статье [Установка предварительной версии Visual Studio для Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> В настоящее время Apple Safari в macOS не поддерживается.

## <a name="enable-debugging"></a>Включение отладки

Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл `launchSettings.json` в запускаемом проекте, добавив в него следующее свойство `inspectUri` в каждом профиле запуска:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

После изменения файл `launchSettings.json` должен выглядеть следующим образом:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Свойство `inspectUri`:

* Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.
* Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.

Значения заполнителей для протокола WebSocket (`wsProtocol`), узла (`url.hostname`), порта (`url.port`) и URI инспектора в запущенном браузере (`browserInspectUri`) предоставляются платформой.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:

1. Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.
1. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.

   > [!NOTE]
   > **Запуск без отладки** (<kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается. При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.

1. В *клиентском* приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.
1. В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me**, чтобы попасть в точку останова.
1. В Visual Studio просмотрите значение поля `currentCount` в окне **Локальные переменные**.
1. Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.

При отладке приложения Blazor WebAssembly можно также отлаживать серверный код.

1. Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Установите точку останова в `WeatherForecastController` в методе действия `Get`.
1. Перейдите на страницу `Fetch Data`, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер.
1. Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите в точку останова на сервере в `WeatherForecastController`.
1. Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды в браузере.

> [!NOTE]
> Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки. Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.

Если приложение находится по [базовому пути](xref:blazor/host-and-deploy/index#app-base-path), отличному от `/`, измените следующие свойства в `Properties/launchSettings.json` в соответствии с базовым путем приложения:

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` каждого профиля:

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

Заполнители в указанных выше параметрах:

* `{INSECURE PORT}` — незащищенный порт; по умолчанию предоставляется случайное значение, но допускается пользовательский порт;
* `{APP BASE PATH}` — базовый путь приложения;
* `{SECURE PORT}` — защищенный порт; по умолчанию предоставляется случайное значение, но допускается пользовательский порт;
* `{PROFILE 1, 2, ... N}` — профили параметров запуска. Как правило, в приложении по умолчанию указывается более одного профиля (например, профиль для IIS Express и профиль проекта, который используется сервером Kestrel).

В следующих примерах приложение размещается в `/OAT` с базовым путем, настроенным в `wwwroot/index.html` как `<base href="/OAT/">`:

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Сведения об использовании пользовательского базового пути для приложений Blazor WebAssembly см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Отладка автономного приложения Blazor WebAssembly</h2>

1. Откройте автономное приложение Blazor WebAssembly в VS Code.

   Вы можете получить уведомление о том, что для включения отладки требуется дополнительная настройка.

   > Для отладки приложений Blazor WebAssembly требуется дополнительная настройка.

   Если вы получили уведомление:

   * Убедитесь, что установлена актуальная версия [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp). Чтобы проверить установленные расширения, откройте раздел **Вид** > **Расширения** в строке меню или выберите значок **Расширения** на боковой панели **Действия**.
   * Убедитесь, что отладчик JavaScript включен. Откройте файл параметров в строке меню (**Файл** > **Настройки** > **Параметры**). Выполните поиск по ключевым словам `debug preview`. В результатах поиска убедитесь, что флажок **Отладка > JavaScript: Use Preview (JavaScript: использовать предварительную версию)** установлен. Если параметр включения отладки предварительной версии отсутствует, обновите VS Code до актуальной версии или установите [расширение отладчика JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code версии 1.46 или более ранней).
   * Перезагрузите окно.

1. Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.

   > [!NOTE]
   > **Запуск без отладки** (<kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается. При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.

1. При появлении запроса выберите вариант **Blazor WebAssembly Debug** (Отладка Blazor WebAssembly), чтобы начать отладку.

1. Автономное приложение запустится и откроется браузер отладки.

1. В *клиентском* приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.

1. В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me**, чтобы попасть в точку останова.

> [!NOTE]
> Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки. Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Отладка размещенного приложения Blazor WebAssembly

1. Откройте папку решения размещенного приложения Blazor WebAssembly в VS Code.

1. Если для проекта не задана конфигурация запуска, появится следующее уведомление. Выберите ответ **Да**.

   > В {ИМЯ ПРИЛОЖЕНИЯ} отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?

1. В палитре команд вверху окна выберите проект *Server* в размещенном решении.

При этом будет создан файл `launch.json` с конфигурацией запуска для отладчика.

## <a name="attach-to-an-existing-debugging-session"></a>Подключение к существующему сеансу отладки

Чтобы подключиться к выполняющемуся приложению Blazor, создайте файл `launch.json` со следующей конфигурацией:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Подключение к сеансу отладки поддерживается только для автономных приложений. Чтобы использовать отладку полного стека, необходимо запустить приложение из VS Code.

## <a name="launch-configuration-options"></a>Параметры конфигурации запуска

Для отладки типа `blazorwasm` (`.vscode/launch.json`) поддерживаются следующие параметры конфигурации запуска.

| Параметр    | Описание |
| --------- | ----------- |
| `request` | Используйте `launch`, чтобы запустить и подключить сеанс отладки к приложению Blazor WebAssembly, или `attach`, чтобы подключить сеанс отладки к уже работающему приложению. |
| `url`     | URL-адрес для открытия в браузере при отладке. По умолчанию — `https://localhost:5001`. |
| `browser` | Браузер для запуска сеанса отладки. Задайте `edge` или `chrome`. По умолчанию — `chrome`. |
| `trace`   | Используется для создания журналов из отладчика JS. Задано значение `true`, чтобы создавались журналы. |
| `hosted`  | При запуске и отладке размещенного приложения Blazor WebAssembly необходимо задать значение `true`. |
| `webRoot` | Указывает полный путь к веб-серверу. Если приложение обслуживается с использованием подмаршрута, необходимо указать значение для этого параметра. |
| `timeout` | Количество миллисекунд ожидания до подключения сеанса отладки. По умолчанию — 30 000 миллисекунд (30 секунд). |
| `program` | Ссылка на исполняемый файл для запуска сервера размещенного приложения. Если для `hosted` задано значение `true`, нужно указать значение для этого параметра. |
| `cwd`     | Рабочий каталог для запуска приложения. Если для `hosted` задано значение `true`, нужно указать значение для этого параметра. |
| `env`     | Переменные среды, которые должны быть предоставлены запущенному процессу. Применимо, только если для параметра `hosted` задано значение `true`. |

## <a name="example-launch-configurations"></a>Примеры конфигураций запуска

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Запуск и отладка автономного приложения Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Подключение к выполняющемуся приложению по указанному URL-адресу

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Запуск и отладка размещенного приложения Blazor WebAssembly с помощью Microsoft Edge

По умолчанию конфигурация браузера использует Google Chrome. При использовании Microsoft Edge для отладки задайте для параметра `browser` значение `edge`. Чтобы использовать Google Chrome, не задавайте параметр `browser` или задайте для него значение `chrome`.

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

В предыдущем примере `MyHostedApp.Server.dll` является сборкой приложения *Server*. Папка `.vscode` расположена в папке решения рядом с папками `Client`, `Server` и `Shared`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio для Mac, сделайте следующее.

1. Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.
1. Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы запустить приложение в отладчике.

   > [!NOTE]
   > **Запуск без отладки** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) не поддерживается. При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.

   > [!IMPORTANT]
   > Для сеанса отладки должен быть выбран браузер Google Chrome или Microsoft Edge.

1. В *клиентском* приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.
1. В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me**, чтобы попасть в точку останова.
1. В Visual Studio просмотрите значение поля `currentCount` в окне **Локальные переменные**.
1. Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы продолжить выполнение.

При отладке приложения Blazor WebAssembly можно также отлаживать серверный код.

1. Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Установите точку останова в `WeatherForecastController` в методе действия `Get`.
1. Перейдите на страницу `Fetch Data`, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер.
1. Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`.
1. Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды в браузере.

> [!NOTE]
> Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки. Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.

Дополнительные сведения см. в статье [Отладка с помощью Visual Studio для Mac](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Отладка в браузере

*Инструкции в этом разделе относятся к Google Chrome и Microsoft Edge в Windows.*

1. Запустите отладочную сборку приложения в среде разработки.

1. Запустите браузер и перейдите по URL-адресу приложения (например, `https://localhost:5001`).

1. В браузере попытайтесь запустить удаленную отладку, нажав клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

   Браузер должен работать с включенной удаленной отладкой (это не поведение по умолчанию). Если удаленная отладка отключена, отобразится страница с ошибкой **Не удается найти вкладку браузера для отладки** с инструкциями по запуску браузера с открытым портом отладки. Выполните инструкции в браузере, после чего откроется новое окно браузера. Закройте предыдущее окно.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш из предыдущего шага.

1. Через некоторое время на вкладке **Источники** отобразится список сборок .NET приложения в узле `file://`.

1. Точки остановка, заданные в коде компонента (файлы `.razor`) и в файлах с кодом C# (`.cs`), будут срабатывать при выполнении кода. После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).

Blazor предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET. При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер. Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).

## <a name="browser-source-maps"></a>Сопоставители с исходным кодом в браузере

Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента. Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM. Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.

## <a name="troubleshoot"></a>Диагностика

При возникновении ошибок воспользуйтесь следующими советами:

* На вкладке **Отладчик** откройте средства разработчика в браузере. В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.
* Убедитесь, что вы установили и сделали доверенным сертификат разработки HTTPS ASP.NET Core. Для получения дополнительной информации см. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* В Visual Studio должен быть включен параметр **Включить отладку JavaScript для ASP.NET (Chrome, Edge и IE)** в разделе **Инструменты** > **Параметры** > **Отладка** > **Общие**. Этот параметр включен в Visual Studio по умолчанию. Если отладка не работает, проверьте, выбран ли этот параметр.
* Если в среде используется прокси-сервер HTTP, необходимо включить `localhost` в параметры обхода прокси-сервера. Для этого можно задать переменную среды `NO_PROXY` в одном из двух мест:
  * файл `launchSettings.json` проекта;
  * на уровне пользовательских или системных переменных среды для применения ко всем приложениям. Перезапустите Visual Studio, чтобы изменение переменной среды вступило в силу.

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Точки останова в `OnInitialized{Async}` не срабатывают

Прокси-сервер платформы Blazor для отладки запускается не мгновенно, поэтому точки останова в[методе жизненного цикла `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) могут не срабатывать. Мы рекомендуем добавить задержку в начале тела метода, чтобы прокси-сервер отладки успел запуститься до срабатывания точки останова. Вы можете включить задержку на основе [директивы компилятора `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), чтобы обеспечить отсутствие задержки для конечной сборки приложения.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>истекло время ожидания Visual Studio (Windows)

Если Visual Studio вызывает исключение о том, что не удалось запустить адаптер отладки, указав, что истекло время ожидания, то можно настроить время ожидания с помощью параметра реестра.

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

Заполнитель `{TIMEOUT}` в предыдущей команде указывается в миллисекундах. Например, одной минуте соответствует значение `60000`.
