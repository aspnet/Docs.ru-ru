---
title: Тестирование веб-API с помощью HTTP REPL
author: scottaddie
description: Узнайте, как использовать глобальное средство .NET Core HTTP REPL для просмотра и тестирования веб-API ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 05/20/2020
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
uid: web-api/http-repl
ms.openlocfilehash: b12f4b10230f2631392011a6e443156bf9a2d843
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491448"
---
# <a name="test-web-apis-with-the-http-repl"></a>Тестирование веб-API с помощью HTTP REPL

Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)

HTTP read-eval-print loop (REPL):

* это кроссплатформенная программа командной строки, которая поддерживается везде, где поддерживается .NET Core;
* служит для создания HTTP-запросов с целью тестирования веб-API ASP.NET Core (а также веб-API, не связанных с ASP.NET Core) и просмотра их результатов;
* может использоваться для тестирования веб-API, размещенных в любой среде, включая localhost и Службу приложений Azure.

Поддерживаются следующие [HTTP-команды](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods):

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [Глава](#test-http-head-requests)
* [Параметры](#test-http-options-requests)
* [ЗАЩИТЫ](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

Для выполнения дальнейших инструкций [просмотрите или скачайте пример веб-API ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([как скачивать](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Предварительные требования

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Установка

Чтобы установить HTTP REPL, выполните следующую команду:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

[Глобальное средство .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) устанавливается из пакета NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).

## <a name="usage"></a>Использование

После успешной установки HTTP REPL выполните следующую команду, чтобы запустить средство:

```console
httprepl
```

Чтобы просмотреть доступные команды HTTP REPL, выполните одну из следующих команд:

```console
httprepl -h
```

```console
httprepl --help
```

Выводится следующий результат.

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

В HTTP REPL поддерживается завершение команд. Нажимая клавишу <kbd>TAB</kbd>, можно переходить по списку команд, которые начинаются с введенных символов или конечной точки API. Доступные команды CLI описываются в следующих разделах.

## <a name="connect-to-the-web-api"></a>Подключение к веб-API

Чтобы подключиться к веб-API, выполните следующую команду:

```console
httprepl <ROOT URI>
```

`<ROOT URI>` — это базовый универсальный код ресурса (URI) для веб-API. Например:

```console
httprepl https://localhost:5001
```

Кроме того, когда программа HTTP REPL запущена, можно в любой момент выполнить следующую команду:

```console
connect <ROOT URI>
```

Например:

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a>Создание ссылки на документ Swagger для веб-API вручную

Приведенная выше команда для подключения попытается автоматически найти документ Swagger. Если по какой-то причине ей не удается это сделать, укажите для веб-API универсальный код ресурса (URI) для документа Swagger, используя параметр `--swagger`:

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

Например:

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Просмотр веб-API

### <a name="view-available-endpoints"></a>Просмотр доступных конечных точек

Чтобы получить список конечных точек (контроллеров) по текущему пути веб-API, выполните команду `ls` или `dir`:

```console
https://localhot:5001/~ ls
```

Будут выведены данные в следующем формате:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Из приведенных выше результатов видно, что доступно два контроллера: `Fruits` и `People`. Они оба поддерживают операции HTTP GET и POST без параметров.

Более подробную информацию можно получить, перейдя к определенному контроллеру. Например, из выходных данных приведенной ниже команды видно, что контроллер `Fruits` также поддерживает операции HTTP GET, PUT и DELETE. Каждая из этих операций принимает параметр `id` в маршруте:

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

Кроме того, можно выполнить команду `ui`, чтобы открыть страницу с пользовательским интерфейсом Swagger веб-API в браузере. Например:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>Переход к конечной точке

Чтобы перейти к другой конечной точке веб-API, выполните команду `cd`:

```console
https://localhost:5001/~ cd people
```

В пути, указываемом после команды `cd`, регистр символов не учитывается. Будут выведены данные в следующем формате:

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>Настройка HTTP REPL

[Цвета](#set-color-preferences), используемые в HTTP REPL по умолчанию, можно настроить. Кроме того, можно определить [текстовый редактор по умолчанию](#set-the-default-text-editor). Настройки HTTP REPL сохраняются на протяжении текущего сеанса и учитываются при открытии следующих сеансов. Измененные настройки хранятся в следующем файле:

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

Файл *HTTPREPLPREFS* загружается при запуске. Во время выполнения изменения в нем не отслеживаются. Изменения, внесенные в него вручную, вступают в силу только после перезапуска средства.

### <a name="view-the-settings"></a>Просмотр параметров

Чтобы просмотреть доступные параметры, выполните команду `pref get`. Например:

```console
https://localhost:5001/~ pref get
```

В результате выполнения приведенной выше команды выводятся доступные пары "ключ-значение":

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

### <a name="set-color-preferences"></a>Настройки цветов

Раскрашивание ответов в настоящее время поддерживается только для JSON. Чтобы настроить цвета по умолчанию в программе HTTP REPL, найдите ключ, соответствующий изменяемому цвету. Инструкции по поиску ключей см. в разделе [Просмотр параметров](#view-the-settings). Например, измените значение ключа `colors.json` с `Green` на `White` следующим образом:

```console
https://localhost:5001/people~ pref set colors.json White
```

Можно использовать только [допустимые цвета](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs). При выполнении последующих HTTP-запросов к выходным данным будут применяться новые цвета.

Если определенные цветовые ключи не заданы, используются более общие ключи. Рассмотрим это поведение на следующем примере:

* Если у `colors.json.name` нет значения, используется `colors.json.string`.
* Если у `colors.json.string` нет значения, используется `colors.json.literal`.
* Если у `colors.json.literal` нет значения, используется `colors.json`. 
* Если у `colors.json` нет значения, используется текст цвета по умолчанию для командной оболочки (`AllowedColors.None`).

### <a name="set-indentation-size"></a>Установка размера отступа

Настройка размера отступа в ответах в настоящее время поддерживается только для JSON. Размер по умолчанию — два пробела. Например:

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

Чтобы изменить размер по умолчанию, задайте ключ `formatting.json.indentSize`. Например, чтобы использовались четыре пробела, выполните следующую команду:

```console
pref set formatting.json.indentSize 4
```

В последующих ответах будет применяться отступ в четыре пробела:

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

### <a name="set-the-default-text-editor"></a>Установка текстового редактора по умолчанию

По умолчанию текстовый редактор для HTTP REPL не настроен. Для тестирования методов веб-API, требующих текста HTTP-запроса, необходимо задать текстовый редактор по умолчанию. Средство HTTP REPL запускает настроенный текстовый редактор исключительно в целях составления текста запроса. Чтобы указать текстовый редактор по умолчанию, выполните следующую команду:

```console
pref set editor.command.default "<EXECUTABLE>"
```

В приведенной выше команде `<EXECUTABLE>` — это полный путь к исполняемому файлу текстового редактора. Например, чтобы задать Visual Studio Code как текстовый редактор по умолчанию, выполните следующую команду:

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Чтобы текстовый редактор по умолчанию запускался с определенными аргументами CLI, задайте ключ `editor.command.default.arguments`. Например, предположим, что Visual Studio Code — это текстовый редактор по умолчанию и необходимо, чтобы средство HTTP REPL всегда запускало сеанс Visual Studio Code с отключенными расширениями. Выполните следующую команду:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a>Настройка путей поиска Swagger

По умолчанию REPL HTTP имеет набор относительных путей для поиска документа Swagger при выполнении команды `connect` без параметра `--swagger`. Эти относительные пути объединяются с корневыми и базовыми путями, указанными в команде `connect`. Относительные пути по умолчанию:

- *swagger.jsна*
- *Swagger/v1/swagger.jsв*
- */swagger.jsна*
- */swagger/v1/swagger.json*

Чтобы использовать другой набор путей поиска в среде, используйте параметр `swagger.searchPaths`. Значение должно представлять собой список относительных путей, разделенных символом вертикальной черты. Например:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a>Тестирование HTTP-запросов GET

### <a name="synopsis"></a>Краткий обзор

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

Для команды `get` доступны следующие параметры:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Пример

Чтобы отправить HTTP-запрос GET, выполните указанные ниже действия.

1. Выполните команду `get` в конечной точке, которая поддерживает ее:

    ```console
    https://localhost:5001/people~ get
    ```

    Выходные данные приведенной выше команды имеют следующий формат:

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

1. Получите определенную запись, передав параметр в команду `get`:

    ```console
    https://localhost:5001/people~ get 2
    ```

    Выходные данные приведенной выше команды имеют следующий формат:

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

## <a name="test-http-post-requests"></a>Тестирование HTTP-запросов POST

### <a name="synopsis"></a>Краткий обзор

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Пример

Чтобы отправить HTTP-запрос POST, выполните указанные ниже действия.

1. Выполните команду `post` в конечной точке, которая поддерживает ее:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    В приведенной выше команде в заголовке HTTP-запроса `Content-Type` указан формат текста запроса JSON. В текстовом редакторе по умолчанию открывается файл *TMP* с шаблоном JSON, представляющим текст HTTP-запроса. Например:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Инструкции по определению текстового редактора по умолчанию см. в разделе [Установка текстового редактора по умолчанию](#set-the-default-text-editor).

1. Измените шаблон JSON в соответствии с требованиями к проверке модели:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Сохраните файл *TMP* и закройте текстовый редактор. В командной оболочке появятся следующие выходные данные:

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

## <a name="test-http-put-requests"></a>Тестирование HTTP-запросов PUT

### <a name="synopsis"></a>Краткий обзор

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Пример

Чтобы отправить HTTP-запрос PUT, выполните указанные ниже действия.

1. *Необязательно*. выполните `get` команду, чтобы просмотреть данные перед их изменением:

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

1. Выполните команду `put` в конечной точке, которая поддерживает ее:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    В приведенной выше команде в заголовке HTTP-запроса `Content-Type` указан формат текста запроса JSON. В текстовом редакторе по умолчанию открывается файл *TMP* с шаблоном JSON, представляющим текст HTTP-запроса. Например:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Инструкции по определению текстового редактора по умолчанию см. в разделе [Установка текстового редактора по умолчанию](#set-the-default-text-editor).

1. Измените шаблон JSON в соответствии с требованиями к проверке модели:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Сохраните файл *TMP* и закройте текстовый редактор. В командной оболочке появятся следующие выходные данные:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Необязательно*. выполните `get` команду, чтобы просмотреть изменения. Например, если вы ввели "Cherry" в текстовом редакторе, команда `get` вернет следующие выходные данные:

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

## <a name="test-http-delete-requests"></a>Тестирование HTTP-запросов DELETE

### <a name="synopsis"></a>Краткий обзор

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Пример

Чтобы отправить HTTP-запрос DELETE, выполните указанные ниже действия.

1. *Необязательно*. выполните `get` команду, чтобы просмотреть данные перед их изменением:

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

1. Выполните команду `delete` в конечной точке, которая поддерживает ее:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    Выходные данные приведенной выше команды имеют следующий формат:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Необязательно*. выполните `get` команду, чтобы просмотреть изменения. В этом примере команда `get` возвращает следующие данные:

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

## <a name="test-http-patch-requests"></a>Тестирование HTTP-запросов PATCH

### <a name="synopsis"></a>Краткий обзор

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Тестирование HTTP-запросов HEAD

### <a name="synopsis"></a>Краткий обзор

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Тестирование HTTP-запросов OPTIONS

### <a name="synopsis"></a>Краткий обзор

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Аргументы

`PARAMETER`

Параметр маршрута, который требуется методу действия соответствующего контроллера.

### <a name="options"></a>Параметры

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Установка заголовков HTTP-запросов

Задать заголовок HTTP-запроса можно одним из следующих способов.

* Внутри HTTP-запроса. Например:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    При таком подходе для каждого заголовка HTTP-запроса требуется собственный параметр `-h`.

* Перед отправкой HTTP-запроса. Например:

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    Если заголовок задается перед отправкой запроса, он продолжает действовать на протяжении всего сеанса командной оболочки. Чтобы очистить заголовок, укажите пустое значение. Например:
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Проверка защищенных конечных точек

HTTP REPL поддерживает тестирование защищенных конечных точек двумя способами: с помощью учетных данных пользователя, выполнившего вход в систему, или с помощью заголовков HTTP-запроса. 

### <a name="default-credentials"></a>Учетные данные по умолчанию

Рассмотрим ситуацию, в которой тестируемый веб-API размещается в IIS и защищен с помощью проверки подлинности Windows. Необходимо, чтобы учетные данные пользователя, запускающего средство, переводились в тестируемые конечные точки HTTP. Чтобы передать учетные данные пользователя, выполнившего вход в систему, выполните следующие действия.

1. Задайте `httpClient.useDefaultCredentials` для параметра значение `true` :

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Закройте и перезапустите инструмент перед отправкой другого запроса к веб-API.

### <a name="http-request-headers"></a>Заголовки запросов HTTP

Примеры поддерживаемых схем проверки подлинности и авторизации включают в себя обычную проверку подлинности, токены носителя JWT и дайджест-аутентификацию. Например, можно отправить токен носителя в конечную точку с помощью следующей команды:

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Для доступа к конечной точке, размещенной в Azure, или для использования [Azure REST API](/rest/api/azure/) необходим токен носителя. Выполните следующие действия, чтобы получить токен носителя для подписки Azure с помощью [Azure CLI](/cli/azure/). HTTP REPL задает токен носителя в заголовке HTTP-запроса и получает список веб-приложений Службы приложений Azure.

1. Войдите в Azure:

    ```azurecli
    az login
    ```

1. Получите идентификатор подписки с помощью следующей команды:

    ```azurecli
    az account show --query id
    ```

1. Скопируйте идентификатор подписки и выполните следующую команду:

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Получите токен носителя с помощью следующей команды:

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Подключитесь к Azure REST API с помощью HTTP REPL:

    ```console
    httprepl https://management.azure.com
    ```

1. Задайте заголовок запроса HTTP `Authorization`:

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Перейдите к подписке:

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Получите список веб-приложений Службы приложений Azure для вашей подписки:

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    Появится следующий ответ:

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

## <a name="toggle-http-request-display"></a>Включение и отключение отображения HTTP-запроса

По умолчанию отправляемый HTTP-запрос не отображается. Соответствующую настройку можно изменить на всю длительность сеанса командной оболочки.

### <a name="enable-request-display"></a>Включение отображения запроса

Чтобы отправляемый HTTP-запрос отображался, выполните команду `echo on`. Например:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

При отправке последующих HTTP-запросов в рамках текущего сеанса заголовки запросов будут отображаться. Например:

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

### <a name="disable-request-display"></a>Отключение отображения запроса

Чтобы отправляемый HTTP-запрос не отображался, выполните команду `echo off`. Например:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>Выполнить сценарий

Если вы часто выполняете один и тот же набор команд HTTP REPL, их можно сохранить в текстовом файле. Команды в файле имеют тот же формат, что и выполняемые вручную в командной строке. Их можно выполнять в пакетном режиме с помощью команды `run`. Например:

1. Создайте текстовый файл с набором команд, разделенных символами новой строки. Например, это может быть файл *people-script.txt* со следующими командами:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Выполните команду `run`, передав в нее путь к текстовому файлу. Например:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    Отображаются следующие результаты:

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

## <a name="clear-the-output"></a>Очистка выходных данных

Чтобы удалить все выходные данные средства HTTP REPL из командной оболочки, выполните команду `clear` или `cls`. Например, предположим, что в командной оболочке есть следующие выходные данные:

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

Чтобы очистить их, выполните следующую команду:

```console
https://localhost:5001/~ clear
```

После выполнения приведенной выше команды командная оболочка содержит только следующие выходные данные:

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Запросы к REST API](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Репозиторий HTTP REPL в GitHub](https://github.com/dotnet/HttpRepl)
