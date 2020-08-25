---
title: Отладка в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/17/2020
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
ms.openlocfilehash: 5aeb333dc36ebc4c3a324b397793343e0335b1e1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628369"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Отладка в ASP.NET Core Blazor WebAssembly

[Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Microsoft Edge или Chrome). Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.

Поддерживаются следующие сценарии:

* Установка и удаление точек останова.
* Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).
* Выполните код пошагово (<kbd>F10</kbd>).
* Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.
* На экране *Локальные переменные* просмотрите значения локальных переменных.
* См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.

Сейчас вам *недоступны* следующие функции:

* Останов при необработанных исключениях.
* Попадание в точки останова во время запуска приложения перед запуском прокси-сервера отладки. Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.

Мы будем улучшать процесс отладки в будущих выпусках.

## <a name="prerequisites"></a>Предварительные требования

Для отладки требуется один из следующих браузеров:

* Google Chrome (версии 70 и более поздних) (по умолчанию)
* Microsoft Edge (версии 80 и более поздних)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Включение отладки для Visual Studio и Visual Studio Code

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

## <a name="visual-studio"></a>Visual Studio

Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:

1. Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.
1. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.

   > [!NOTE]
   > **Запуск без отладки** (<kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается. При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.

1. Установите точку останова в `Pages/Counter.razor` в методе `IncrementCount`.
1. Перейдите на вкладку **`Counter`** и нажмите кнопку для попадания в точку останова:

   ![Счетчик отладки](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Просмотрите значение поля `currentCount` в окне локальных переменных:

   ![Просмотр локальных переменных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.

При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:

1. Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Установите точку останова в `WeatherForecastController` в методе действия `Get`.
1. Перейдите на вкладку **`Fetch Data`** , чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:

   ![Отладка получения данных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:

   ![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Отладка автономного приложения Blazor WebAssembly

1. Откройте автономное приложение Blazor WebAssembly в VS Code.

   Вы можете получить следующее уведомление о том, что для включения отладки требуется дополнительная настройка:
   
   ![Требуется дополнительная настройка](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   Если вы получили уведомление:

   * Убедитесь, что установлена актуальная версия [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp). Чтобы проверить установленные расширения, откройте раздел **Вид** > **Расширения** в строке меню или выберите значок **Расширения** на боковой панели **Действия**.
   * Убедитесь, что отладчик JavaScript включен. Откройте файл параметров в строке меню (**Файл** > **Настройки** > **Параметры**). Выполните поиск по ключевым словам `debug preview`. В результатах поиска убедитесь, что флажок **Отладка > JavaScript: Use Preview (JavaScript: использовать предварительную версию)** установлен. Если параметр включения отладки предварительной версии отсутствует, обновите VS Code до актуальной версии или установите [расширение отладчика JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code версии 1.46 или более ранней).
   * Перезагрузите окно.

1. Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.

   > [!NOTE]
   > **Запуск без отладки** (<kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается. При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.

1. При появлении запроса выберите вариант **Blazor WebAssembly Debug** (Отладка Blazor WebAssembly), чтобы начать отладку.

   ![Список доступных вариантов отладки](index/_static/blazor-vscode-debugtypes.png)

1. Автономное приложение запустится и откроется браузер отладки.

1. Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Отладка размещенного приложения Blazor WebAssembly

1. Откройте папку решения размещенного приложения Blazor WebAssembly в VS Code.

1. Если для проекта не задана конфигурация запуска, появится следующее уведомление. Выберите ответ **Да**.

   ![Добавление необходимых ресурсов](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. В палитре команд вверху окна выберите проект *Server* в размещенном решении.

При этом будет создан файл `launch.json` с конфигурацией запуска для отладчика.

### <a name="attach-to-an-existing-debugging-session"></a>Подключение к существующему сеансу отладки

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

### <a name="launch-configuration-options"></a>Параметры конфигурации запуска

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

### <a name="example-launch-configurations"></a>Примеры конфигураций запуска

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Запуск и отладка автономного приложения Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Подключение к выполняющемуся приложению по указанному URL-адресу

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Запуск и отладка размещенного приложения Blazor WebAssembly с помощью Microsoft Edge

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

## <a name="debug-in-the-browser"></a>Отладка в браузере

1. Запустите отладочную сборку приложения в среде разработки.

1. Запустите браузер и перейдите по URL-адресу приложения (например, `https://localhost:5001`).

1. В браузере попытайтесь запустить удаленную отладку, нажав клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

   Браузер должен работать с включенной удаленной отладкой (это не поведение по умолчанию). Если удаленная отладка отключена, отобразится страница с ошибкой **Не удается найти вкладку браузера для отладки** с инструкциями по запуску браузера с открытым портом отладки. Выполните инструкции в браузере, после чего откроется новое окно браузера. Закройте предыдущее окно.

1. Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью клавиш <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

1. Через некоторое время на вкладке **Источники** отобразится список сборок .NET приложения в узле `file://`.

1. Точки остановка, заданные в коде компонента (файлы `.razor`) и в файлах с кодом C# (`.cs`), будут срабатывать при выполнении кода. После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).

Blazor предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET. При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер. Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).

## <a name="browser-source-maps"></a>Сопоставители с исходным кодом в браузере

Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента. Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM. Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.

## <a name="troubleshoot"></a>Устранение неполадок

При возникновении ошибок воспользуйтесь следующими советами:

* На вкладке **Отладчик** откройте средства разработчика в браузере. В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.
* Убедитесь, что вы установили и сделали доверенным сертификат разработки HTTPS ASP.NET Core. Для получения дополнительной информации см. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* В Visual Studio должен быть включен параметр **Включить отладку JavaScript для ASP.NET (Chrome, Edge и IE)** в разделе **Инструменты** > **Параметры** > **Отладка** > **Общие**. Этот параметр включен в Visual Studio по умолчанию. Если отладка не работает, проверьте, выбран ли этот параметр.

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

### <a name="visual-studio-timeout"></a>Время ожидания Visual Studio

Если Visual Studio вызывает исключение о том, что не удалось запустить адаптер отладки, указав, что истекло время ожидания, то можно настроить время ожидания с помощью параметра реестра.

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

Заполнитель `{TIMEOUT}` в предыдущей команде указывается в миллисекундах. Например, одной минуте соответствует значение `60000`.
