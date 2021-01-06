---
title: Использование ASP.NET Core SignalR с TypeScript и webpack
author: ssougnez
description: В рамках этого учебника вы настроите средство webpack для создания пакета и сборки веб-приложения ASP.NET Core SignalR, клиент которого написан на языке TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056846"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="741fe-103">Использование ASP.NET Core SignalR с TypeScript и webpack</span><span class="sxs-lookup"><span data-stu-id="741fe-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="741fe-104">Авторы: [Себастьен Сунье (Sébastien Sougnez)](https://twitter.com/ssougnez) и [Скотт Эдди (Scott Addie)](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="741fe-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="741fe-105">С помощью средства [Webpack](https://webpack.js.org/) разработчики могут создавать пакеты и выполнять сборку ресурсов на стороне клиента для веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="741fe-106">В этом учебнике демонстрируется использование средства webpack для создания пакета и сборки веб-приложения ASP.NET Core SignalR, клиент которого написан на языке [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="741fe-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="741fe-107">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="741fe-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="741fe-108">сформировать шаблон для начального приложения ASP.NET Core SignalR;</span><span class="sxs-lookup"><span data-stu-id="741fe-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="741fe-109">настроить клиент TypeScript SignalR;</span><span class="sxs-lookup"><span data-stu-id="741fe-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="741fe-110">Настроить конвейер сборки с использованием Webpack</span><span class="sxs-lookup"><span data-stu-id="741fe-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="741fe-111">настроить сервер SignalR.</span><span class="sxs-lookup"><span data-stu-id="741fe-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="741fe-112">Обеспечить взаимодействие между клиентом и сервером</span><span class="sxs-lookup"><span data-stu-id="741fe-112">Enable communication between client and server</span></span>

<span data-ttu-id="741fe-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="741fe-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="741fe-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="741fe-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fe-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fe-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="741fe-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="741fe-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="741fe-117">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="741fe-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="741fe-118">[Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="741fe-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="741fe-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="741fe-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="741fe-121">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="741fe-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="741fe-122">[C# для Visual Studio Code версии 1.17.1 или более поздней](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="741fe-122">[C# for Visual Studio Code version 1.17.1 or later](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)</span></span>
* <span data-ttu-id="741fe-123">[Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="741fe-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="741fe-124">Создание веб-приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="741fe-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fe-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fe-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="741fe-126">Настройте Visual Studio на поиск npm в переменной среды *PATH*.</span><span class="sxs-lookup"><span data-stu-id="741fe-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="741fe-127">По умолчанию Visual Studio использует версию npm, которая находится в его каталоге установки.</span><span class="sxs-lookup"><span data-stu-id="741fe-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="741fe-128">В Visual Studio выполните следующие инструкции:</span><span class="sxs-lookup"><span data-stu-id="741fe-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="741fe-129">Запустите Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="741fe-129">Launch Visual Studio.</span></span> <span data-ttu-id="741fe-130">В начальном окне выберите **Продолжить без кода**.</span><span class="sxs-lookup"><span data-stu-id="741fe-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="741fe-131">Щелкните **Сервис** > **Параметры** > **Проекты и решения** > **Управление веб-пакетами** > **Внешние веб-инструменты**.</span><span class="sxs-lookup"><span data-stu-id="741fe-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="741fe-132">Выберите элемент *$(PATH)* в списке.</span><span class="sxs-lookup"><span data-stu-id="741fe-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="741fe-133">Щелкните стрелку вверх, чтобы переместить этот элемент на вторую позицию в списке, и нажмите **OK**.</span><span class="sxs-lookup"><span data-stu-id="741fe-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Конфигурация Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="741fe-135">Настройка Visual Studio завершена.</span><span class="sxs-lookup"><span data-stu-id="741fe-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="741fe-136">Перейдите в меню **Файл** > **Создать** > **Проект** и выберите шаблон **Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="741fe-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="741fe-137">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="741fe-137">Select **Next**.</span></span>
1. <span data-ttu-id="741fe-138">Присвойте проекту имя *SignalRWebPack* и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="741fe-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="741fe-139">Выберите *.NET Core* в раскрывающемся списке целевых платформ и *ASP.NET Core 3.1* в раскрывающемся списке версий платформ.</span><span class="sxs-lookup"><span data-stu-id="741fe-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="741fe-140">Выберите шаблон **Пустой** и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="741fe-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="741fe-141">Добавить пакет `Microsoft.TypeScript.MSBuild` в проект:</span><span class="sxs-lookup"><span data-stu-id="741fe-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="741fe-142">В **обозревателе решений** (справа) щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="741fe-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="741fe-143">На вкладке **Обзор** найдите `Microsoft.TypeScript.MSBuild`, а затем нажмите кнопку **Установить** справа, чтобы установить пакет.</span><span class="sxs-lookup"><span data-stu-id="741fe-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="741fe-144">Visual Studio добавляет пакет NuGet в узел **Зависимости** в **обозревателе решений**, разрешая компиляцию TypeScript в проекте.</span><span class="sxs-lookup"><span data-stu-id="741fe-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="741fe-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="741fe-146">Во **встроенном терминале** выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="741fe-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="741fe-147">Команда `dotnet new` создает пустое веб-приложение ASP.NET Core в каталоге *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="741fe-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="741fe-148">Команда `code` открывает папку *SignalRWebPack* в текущем экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="741fe-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="741fe-149">Во **встроенном терминале** выполните следующую команду .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="741fe-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="741fe-150">Предыдущая команда добавляет пакет [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/), разрешая компиляцию TypeScript в проекте.</span><span class="sxs-lookup"><span data-stu-id="741fe-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="741fe-151">Настройка Webpack и TypeScript</span><span class="sxs-lookup"><span data-stu-id="741fe-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="741fe-152">Далее следует настроить преобразование TypeScript в JavaScript и создание пакета ресурсов на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="741fe-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="741fe-153">В корневом элементе проекта выполните следующую команду, чтобы создать файл *package.json*:</span><span class="sxs-lookup"><span data-stu-id="741fe-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="741fe-154">Добавьте выделенное свойство в файл *package.json* и сохраните изменения:</span><span class="sxs-lookup"><span data-stu-id="741fe-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="741fe-155">Если свойству `private` присвоено значение `true`, на следующем шаге не будут отображаться предупреждения об установке пакета.</span><span class="sxs-lookup"><span data-stu-id="741fe-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="741fe-156">Установите необходимые пакеты npm.</span><span class="sxs-lookup"><span data-stu-id="741fe-156">Install the required npm packages.</span></span> <span data-ttu-id="741fe-157">Выполните следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="741fe-158">Сведения о команде, на которые следует обратить внимание:</span><span class="sxs-lookup"><span data-stu-id="741fe-158">Some command details to note:</span></span>

    * <span data-ttu-id="741fe-159">Для каждого имени пакета номер версии указывается после знака `@`.</span><span class="sxs-lookup"><span data-stu-id="741fe-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="741fe-160">npm устанавливает указанные версии пакета.</span><span class="sxs-lookup"><span data-stu-id="741fe-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="741fe-161">Использование параметра `-E` позволяет отключить установленное по умолчанию поведение npm, предусматривающее запись операторов диапазона [семантического управления версиями](https://semver.org/) в файл *package.json*.</span><span class="sxs-lookup"><span data-stu-id="741fe-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="741fe-162">Например, `"webpack": "4.41.5"` используется вместо `"webpack": "^4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="741fe-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="741fe-163">Этот параметр позволяет исключить непреднамеренное обновление до более новых версий пакета.</span><span class="sxs-lookup"><span data-stu-id="741fe-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="741fe-164">Дополнительные сведения см. в документации по [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="741fe-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="741fe-165">Замените свойство `scripts` в файле *package.json* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="741fe-166">Некоторые пояснения к скриптам:</span><span class="sxs-lookup"><span data-stu-id="741fe-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="741fe-167">`build`. Создает пакет ваших ресурсов на стороне клиента в режиме разработки и отслеживает изменения.</span><span class="sxs-lookup"><span data-stu-id="741fe-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="741fe-168">Наблюдатель за файлами выполняет повторное создание пакета при каждом изменении файла проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="741fe-169">Параметр `mode` позволяет отключить оптимизации в рабочей среде, такие как встряхивание дерева и минификация.</span><span class="sxs-lookup"><span data-stu-id="741fe-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="741fe-170">В среде разработки следует использовать только `build`.</span><span class="sxs-lookup"><span data-stu-id="741fe-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="741fe-171">`release`. Создает пакет ресурсов на стороне клиента в рабочем режиме.</span><span class="sxs-lookup"><span data-stu-id="741fe-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="741fe-172">`publish`. запускает скрипт `release` для создания пакета ресурсов на стороне клиента в рабочем режиме.</span><span class="sxs-lookup"><span data-stu-id="741fe-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="741fe-173">Этот скрипт вызывает команду [publish](/dotnet/core/tools/dotnet-publish) интерфейса командной строки .NET Core для публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="741fe-174">Создайте в корневом элементе проекта файл *webpack.config.js* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="741fe-175">Приведенный выше файл определяет конфигурацию компиляции Webpack.</span><span class="sxs-lookup"><span data-stu-id="741fe-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="741fe-176">Сведения о конфигурации, на которые следует обратить внимание:</span><span class="sxs-lookup"><span data-stu-id="741fe-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="741fe-177">Свойство `output` переопределяет значение по умолчанию для *dist*.</span><span class="sxs-lookup"><span data-stu-id="741fe-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="741fe-178">Вместо этого пакет выводится в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="741fe-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="741fe-179">Массив `resolve.extensions` содержит элемент *.js* для импорта кода JavaScript клиента SignalR.</span><span class="sxs-lookup"><span data-stu-id="741fe-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="741fe-180">Создайте новый каталог *src* в корневом каталоге проекта, чтобы сохранить клиентские ресурсы проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="741fe-181">Создайте файл *src/index.html* со следующими исправлениями.</span><span class="sxs-lookup"><span data-stu-id="741fe-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="741fe-182">Приведенный выше HTML-файл определяет стереотипную разметку главной страницы.</span><span class="sxs-lookup"><span data-stu-id="741fe-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="741fe-183">Создайте каталог *src/css*.</span><span class="sxs-lookup"><span data-stu-id="741fe-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="741fe-184">В нем будут храниться файлы *.css* проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="741fe-185">Создайте файл *src/css/main.css* со следующими CSS:</span><span class="sxs-lookup"><span data-stu-id="741fe-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="741fe-186">Приведенный выше файл *main.css* определяет стиль приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="741fe-187">Создайте файл *src/tsconfig.json* со следующим JSON:</span><span class="sxs-lookup"><span data-stu-id="741fe-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="741fe-188">Приведенный выше код определяет конфигурацию компилятора TypeScript для получения совместимого с [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 кода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="741fe-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="741fe-189">Создайте файл *src/index.ts* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="741fe-190">Приведенный выше код TypeScript извлекает ссылки на элементы модели DOM и присоединяет два обработчика событий:</span><span class="sxs-lookup"><span data-stu-id="741fe-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="741fe-191">`keyup`. Это событие возникает в том случае, если пользователь вводит текст текстовое поле `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="741fe-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="741fe-192">Функция `send` вызывается, когда пользователь нажимает клавишу **ВВОД**.</span><span class="sxs-lookup"><span data-stu-id="741fe-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="741fe-193">`click`. это событие возникает, когда пользователь нажимает кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="741fe-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="741fe-194">Вызывается функция `send`.</span><span class="sxs-lookup"><span data-stu-id="741fe-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="741fe-195">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="741fe-195">Configure the app</span></span>

1. <span data-ttu-id="741fe-196">В `Startup.Configure` добавьте вызовы к [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="741fe-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="741fe-197">Приведенный выше код позволяет серверу размещать и обслуживать файл *index.html*.</span><span class="sxs-lookup"><span data-stu-id="741fe-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="741fe-198">Файл обрабатывается независимо от того, вводит ли пользователь полный или корневой URL-адрес веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="741fe-199">В конце `Startup.Configure` сопоставьте маршрут */hub* с концентратором `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="741fe-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="741fe-200">Замените код, отображающий *Hello World!* ,</span><span class="sxs-lookup"><span data-stu-id="741fe-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="741fe-201">следующей строкой:</span><span class="sxs-lookup"><span data-stu-id="741fe-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="741fe-202">В `Startup.ConfigureServices` вызовите [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="741fe-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="741fe-203">Создайте новый каталог с именем *Hubs* в корневом каталоге проекта *SignalRWebPack/* для хранения концентратора SignalR.</span><span class="sxs-lookup"><span data-stu-id="741fe-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="741fe-204">Создайте концентратор *Hubs/ChatHub.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="741fe-205">Добавьте следующий оператор `using` в самое начало файла *Startup.cs*, чтобы разрешить ссылку `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="741fe-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="741fe-206">Обеспечение взаимодействия между клиентом и сервером</span><span class="sxs-lookup"><span data-stu-id="741fe-206">Enable client and server communication</span></span>

<span data-ttu-id="741fe-207">Приложение в настоящее время отображает простую форму для отправки сообщений, но еще не работает.</span><span class="sxs-lookup"><span data-stu-id="741fe-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="741fe-208">Сервер прослушивает конкретный маршрут, но ничего не делает с отправленными сообщениями.</span><span class="sxs-lookup"><span data-stu-id="741fe-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="741fe-209">Выполните следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="741fe-210">Предыдущая команда устанавливает:</span><span class="sxs-lookup"><span data-stu-id="741fe-210">The preceding command installs:</span></span>

     * <span data-ttu-id="741fe-211">[Клиент TypeScript SignalR](https://www.npmjs.com/package/@microsoft/signalr), который позволяет клиенту отправлять сообщения на сервер.</span><span class="sxs-lookup"><span data-stu-id="741fe-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="741fe-212">Определения типа TypeScript для Node.js, обеспечивающие проверку типов Node.js во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="741fe-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="741fe-213">Добавьте выделенный код в файл *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="741fe-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="741fe-214">Приведенный выше код поддерживает получение сообщений от сервера.</span><span class="sxs-lookup"><span data-stu-id="741fe-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="741fe-215">Класс `HubConnectionBuilder` создает новый построитель для настройки подключения к серверу.</span><span class="sxs-lookup"><span data-stu-id="741fe-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="741fe-216">Функция `withUrl` настраивает URL-адрес концентратора.</span><span class="sxs-lookup"><span data-stu-id="741fe-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="741fe-217">SignalR обеспечивает обмен сообщениями между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="741fe-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="741fe-218">Каждому сообщению присваивается определенное имя.</span><span class="sxs-lookup"><span data-stu-id="741fe-218">Each message has a specific name.</span></span> <span data-ttu-id="741fe-219">Например, сообщения с именем `messageReceived` могут выполнять логику отображения новых сообщений в соответствующей зоне.</span><span class="sxs-lookup"><span data-stu-id="741fe-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="741fe-220">Прослушивание определенных сообщений реализуется с помощью функции `on`.</span><span class="sxs-lookup"><span data-stu-id="741fe-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="741fe-221">Возможно прослушивание любого числа имен сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="741fe-222">Кроме того, можно передавать параметры сообщения, например имя его автора или содержимое полученного сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="741fe-223">После того как клиент получает сообщение, создается новый элемент `div`, в атрибуте `innerHTML` которого содержатся имя автора и содержимое сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="741fe-224">Этот элемент добавляется в основной элемент `div`, который используется для отображения сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="741fe-225">Теперь клиент может принимать сообщения. Далее следует настроить его для отправки сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="741fe-226">Добавьте выделенный код в файл *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="741fe-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="741fe-227">Для отправки сообщения через соединение WebSockets необходимо вызвать метод `send`.</span><span class="sxs-lookup"><span data-stu-id="741fe-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="741fe-228">Первый параметр этого метода содержит имя сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="741fe-229">Другие параметры заполняются данными сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="741fe-230">В этом примере на сервер отправляется сообщение, идентифицированное как `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="741fe-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="741fe-231">Это сообщение содержит имя пользователя, а также данные, введенные этим пользователем в текстовое поле.</span><span class="sxs-lookup"><span data-stu-id="741fe-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="741fe-232">Если отправка выполняется успешно, значение текстового поля очищается.</span><span class="sxs-lookup"><span data-stu-id="741fe-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="741fe-233">Добавьте метод `NewMessage` в класс `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="741fe-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="741fe-234">Приведенный выше код осуществляет широковещательную рассылку полученных сервером сообщений всем подключенным пользователям.</span><span class="sxs-lookup"><span data-stu-id="741fe-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="741fe-235">Универсальный метод `on` для получения всех сообщений не требуется.</span><span class="sxs-lookup"><span data-stu-id="741fe-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="741fe-236">Имя метода указывается после суффиксов имени сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="741fe-237">В этом примере клиент TypeScript отправляет сообщение, идентифицированное как `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="741fe-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="741fe-238">Метод C# `NewMessage` ожидает данные, отправленные клиентом.</span><span class="sxs-lookup"><span data-stu-id="741fe-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="741fe-239">Выполняется вызов метода [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) для [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="741fe-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="741fe-240">Полученные сообщения отправляются всем клиентам, подключенным к концентратору.</span><span class="sxs-lookup"><span data-stu-id="741fe-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="741fe-241">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="741fe-241">Test the app</span></span>

<span data-ttu-id="741fe-242">Чтобы проверить работоспособность приложения, выполните следующие шаги.</span><span class="sxs-lookup"><span data-stu-id="741fe-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fe-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fe-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="741fe-244">Запустите средство Webpack в режиме *release*.</span><span class="sxs-lookup"><span data-stu-id="741fe-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="741fe-245">Выполните следующие команды в окне **Консоль диспетчера пакетов** в корневом элементе проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="741fe-246">Если вы не в корневом каталоге проекта, введите перед этой командой `cd SignalRWebPack`.</span><span class="sxs-lookup"><span data-stu-id="741fe-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="741fe-247">Выберите **Отладка** > **Запуск без отладки**, чтобы запустить приложение в браузере, не присоединяя отладчик.</span><span class="sxs-lookup"><span data-stu-id="741fe-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="741fe-248">Файл *wwwroot/index.html* обрабатывается по адресу `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="741fe-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="741fe-249">Если во время компиляции возникают ошибки, попробуйте закрыть и снова открыть решение.</span><span class="sxs-lookup"><span data-stu-id="741fe-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="741fe-250">Откройте другой экземпляр любого браузера.</span><span class="sxs-lookup"><span data-stu-id="741fe-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="741fe-251">Вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="741fe-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="741fe-252">Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="741fe-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="741fe-253">На обеих страницах мгновенно отображаются имя пользователя и сообщение.</span><span class="sxs-lookup"><span data-stu-id="741fe-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="741fe-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="741fe-255">Запустите средство Webpack в режиме *release*, выполнив следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="741fe-256">Выполните построение и запустите приложение, выполнив следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="741fe-257">Веб-сервер запускает приложение и делает его доступным на узле localhost.</span><span class="sxs-lookup"><span data-stu-id="741fe-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="741fe-258">Откройте браузер с адресом `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="741fe-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="741fe-259">Обрабатывается файл *wwwroot/index.html*.</span><span class="sxs-lookup"><span data-stu-id="741fe-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="741fe-260">Скопируйте URL-адрес из адресной строки.</span><span class="sxs-lookup"><span data-stu-id="741fe-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="741fe-261">Откройте другой экземпляр любого браузера.</span><span class="sxs-lookup"><span data-stu-id="741fe-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="741fe-262">Вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="741fe-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="741fe-263">Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="741fe-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="741fe-264">На обеих страницах мгновенно отображаются имя пользователя и сообщение.</span><span class="sxs-lookup"><span data-stu-id="741fe-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Сообщение, отображаемое в обоих окнах браузера](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="741fe-266">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="741fe-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fe-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fe-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="741fe-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="741fe-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="741fe-269">Пакет SDK для .NET Core 2.2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="741fe-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="741fe-270">[Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="741fe-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="741fe-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="741fe-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="741fe-273">Пакет SDK для .NET Core 2.2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="741fe-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="741fe-274">[C# для Visual Studio Code версии 1.17.1 или более поздней](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="741fe-274">[C# for Visual Studio Code version 1.17.1 or later](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)</span></span>
* <span data-ttu-id="741fe-275">[Node.js](https://nodejs.org/) с [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="741fe-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="741fe-276">Создание веб-приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="741fe-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fe-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fe-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="741fe-278">Настройте Visual Studio на поиск npm в переменной среды *PATH*.</span><span class="sxs-lookup"><span data-stu-id="741fe-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="741fe-279">По умолчанию Visual Studio использует версию npm, которая находится в его каталоге установки.</span><span class="sxs-lookup"><span data-stu-id="741fe-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="741fe-280">В Visual Studio выполните следующие инструкции:</span><span class="sxs-lookup"><span data-stu-id="741fe-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="741fe-281">Щелкните **Сервис** > **Параметры** > **Проекты и решения** > **Управление веб-пакетами** > **Внешние веб-инструменты**.</span><span class="sxs-lookup"><span data-stu-id="741fe-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="741fe-282">Выберите элемент *$(PATH)* в списке.</span><span class="sxs-lookup"><span data-stu-id="741fe-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="741fe-283">Щелкните стрелку вверх, чтобы переместить этот элемент на вторую позицию в списке.</span><span class="sxs-lookup"><span data-stu-id="741fe-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Конфигурация Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="741fe-285">Настройка Visual Studio завершена.</span><span class="sxs-lookup"><span data-stu-id="741fe-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="741fe-286">Теперь следует создать проект.</span><span class="sxs-lookup"><span data-stu-id="741fe-286">It's time to create the project.</span></span>

1. <span data-ttu-id="741fe-287">Перейдите в меню **Файл** > **Создать** > **Проект** и выберите шаблон **Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="741fe-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="741fe-288">Присвойте проекту имя *SignalRWebPack* и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="741fe-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="741fe-289">Выберите *.NET Core* в раскрывающемся списке целевых платформ и выберите *ASP.NET Core 2.2* в раскрывающемся списке средства выбора платформы.</span><span class="sxs-lookup"><span data-stu-id="741fe-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="741fe-290">Выберите шаблон **Пустой** и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="741fe-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="741fe-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="741fe-292">Во **встроенном терминале** выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="741fe-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="741fe-293">В каталоге *SignalRWebPack* создается пустое веб-приложение ASP.NET Core, ориентированное на платформу .NET Core.</span><span class="sxs-lookup"><span data-stu-id="741fe-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="741fe-294">Настройка Webpack и TypeScript</span><span class="sxs-lookup"><span data-stu-id="741fe-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="741fe-295">Далее следует настроить преобразование TypeScript в JavaScript и создание пакета ресурсов на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="741fe-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="741fe-296">В корневом элементе проекта выполните следующую команду, чтобы создать файл *package.json*:</span><span class="sxs-lookup"><span data-stu-id="741fe-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="741fe-297">Добавьте выделенное свойство в файл *package.json*:</span><span class="sxs-lookup"><span data-stu-id="741fe-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="741fe-298">Если свойству `private` присвоено значение `true`, на следующем шаге не будут отображаться предупреждения об установке пакета.</span><span class="sxs-lookup"><span data-stu-id="741fe-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="741fe-299">Установите необходимые пакеты npm.</span><span class="sxs-lookup"><span data-stu-id="741fe-299">Install the required npm packages.</span></span> <span data-ttu-id="741fe-300">Выполните следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="741fe-301">Сведения о команде, на которые следует обратить внимание:</span><span class="sxs-lookup"><span data-stu-id="741fe-301">Some command details to note:</span></span>

    * <span data-ttu-id="741fe-302">Для каждого имени пакета номер версии указывается после знака `@`.</span><span class="sxs-lookup"><span data-stu-id="741fe-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="741fe-303">npm устанавливает указанные версии пакета.</span><span class="sxs-lookup"><span data-stu-id="741fe-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="741fe-304">Использование параметра `-E` позволяет отключить установленное по умолчанию поведение npm, предусматривающее запись операторов диапазона [семантического управления версиями](https://semver.org/) в файл *package.json*.</span><span class="sxs-lookup"><span data-stu-id="741fe-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="741fe-305">Например, `"webpack": "4.29.3"` используется вместо `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="741fe-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="741fe-306">Этот параметр позволяет исключить непреднамеренное обновление до более новых версий пакета.</span><span class="sxs-lookup"><span data-stu-id="741fe-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="741fe-307">Дополнительные сведения см. в документации по [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="741fe-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="741fe-308">Замените свойство `scripts` в файле *package.json* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="741fe-309">Некоторые пояснения к скриптам:</span><span class="sxs-lookup"><span data-stu-id="741fe-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="741fe-310">`build`. Создает пакет ваших ресурсов на стороне клиента в режиме разработки и отслеживает изменения.</span><span class="sxs-lookup"><span data-stu-id="741fe-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="741fe-311">Наблюдатель за файлами выполняет повторное создание пакета при каждом изменении файла проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="741fe-312">Параметр `mode` позволяет отключить оптимизации в рабочей среде, такие как встряхивание дерева и минификация.</span><span class="sxs-lookup"><span data-stu-id="741fe-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="741fe-313">В среде разработки следует использовать только `build`.</span><span class="sxs-lookup"><span data-stu-id="741fe-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="741fe-314">`release`. Создает пакет ресурсов на стороне клиента в рабочем режиме.</span><span class="sxs-lookup"><span data-stu-id="741fe-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="741fe-315">`publish`. запускает скрипт `release` для создания пакета ресурсов на стороне клиента в рабочем режиме.</span><span class="sxs-lookup"><span data-stu-id="741fe-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="741fe-316">Этот скрипт вызывает команду [publish](/dotnet/core/tools/dotnet-publish) интерфейса командной строки .NET Core для публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="741fe-317">Создайте в корневом элементе проекта файл *webpack.config.js* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="741fe-318">Приведенный выше файл определяет конфигурацию компиляции Webpack.</span><span class="sxs-lookup"><span data-stu-id="741fe-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="741fe-319">Сведения о конфигурации, на которые следует обратить внимание:</span><span class="sxs-lookup"><span data-stu-id="741fe-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="741fe-320">Свойство `output` переопределяет значение по умолчанию для *dist*.</span><span class="sxs-lookup"><span data-stu-id="741fe-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="741fe-321">Вместо этого пакет выводится в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="741fe-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="741fe-322">Массив `resolve.extensions` содержит элемент *.js* для импорта кода JavaScript клиента SignalR.</span><span class="sxs-lookup"><span data-stu-id="741fe-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="741fe-323">Создайте новый каталог *src* в корневом каталоге проекта, чтобы сохранить клиентские ресурсы проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="741fe-324">Создайте файл *src/index.html* со следующими исправлениями.</span><span class="sxs-lookup"><span data-stu-id="741fe-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="741fe-325">Приведенный выше HTML-файл определяет стереотипную разметку главной страницы.</span><span class="sxs-lookup"><span data-stu-id="741fe-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="741fe-326">Создайте каталог *src/css*.</span><span class="sxs-lookup"><span data-stu-id="741fe-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="741fe-327">В нем будут храниться файлы *.css* проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="741fe-328">Создайте файл *src/css/main.css* со следующими исправлениями:</span><span class="sxs-lookup"><span data-stu-id="741fe-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="741fe-329">Приведенный выше файл *main.css* определяет стиль приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="741fe-330">Создайте файл *src/tsconfig.json* со следующим JSON:</span><span class="sxs-lookup"><span data-stu-id="741fe-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="741fe-331">Приведенный выше код определяет конфигурацию компилятора TypeScript для получения совместимого с [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 кода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="741fe-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="741fe-332">Создайте файл *src/index.ts* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="741fe-333">Приведенный выше код TypeScript извлекает ссылки на элементы модели DOM и присоединяет два обработчика событий:</span><span class="sxs-lookup"><span data-stu-id="741fe-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="741fe-334">`keyup`. Это событие возникает в том случае, если пользователь вводит текст в текстовое поле `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="741fe-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="741fe-335">Функция `send` вызывается, когда пользователь нажимает клавишу **ВВОД**.</span><span class="sxs-lookup"><span data-stu-id="741fe-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="741fe-336">`click`. это событие возникает, когда пользователь нажимает кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="741fe-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="741fe-337">Вызывается функция `send`.</span><span class="sxs-lookup"><span data-stu-id="741fe-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="741fe-338">Настройка приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="741fe-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="741fe-339">Код в методе `Startup.Configure` отображает текст *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="741fe-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="741fe-340">Замените вызов метода `app.Run` вызовами методов [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="741fe-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="741fe-341">Приведенный выше код позволяет серверу обнаруживать и обрабатывать файл *index.html* независимо от того, вводит ли пользователь полный URL-адрес или URL-адрес корня веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="741fe-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="741fe-342">В `Startup.ConfigureServices` вызовите [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="741fe-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="741fe-343">Этот метод добавляет в проект службы SignalR.</span><span class="sxs-lookup"><span data-stu-id="741fe-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="741fe-344">Сопоставьте маршрут */hub* с концентратором `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="741fe-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="741fe-345">Добавьте в конец метода `Startup.Configure` следующие строки:</span><span class="sxs-lookup"><span data-stu-id="741fe-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="741fe-346">Создайте новый каталог *Hubs* в корневом элементе проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="741fe-347">Этот каталог служит для хранения концентратора SignalR, созданного на предыдущем шаге.</span><span class="sxs-lookup"><span data-stu-id="741fe-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="741fe-348">Создайте концентратор *Hubs/ChatHub.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="741fe-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="741fe-349">Добавьте следующий код в самое начало файла *Startup.cs*, чтобы разрешить ссылку `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="741fe-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="741fe-350">Обеспечение взаимодействия между клиентом и сервером</span><span class="sxs-lookup"><span data-stu-id="741fe-350">Enable client and server communication</span></span>

<span data-ttu-id="741fe-351">На данный момент приложение отображает простую форму для отправки сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="741fe-352">При попытке сделать это ничего не происходит.</span><span class="sxs-lookup"><span data-stu-id="741fe-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="741fe-353">Сервер прослушивает конкретный маршрут, но ничего не делает с отправленными сообщениями.</span><span class="sxs-lookup"><span data-stu-id="741fe-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="741fe-354">Выполните следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="741fe-355">Команда выше устанавливает [клиент TypeScript SignalR](https://www.npmjs.com/package/@microsoft/signalr), который позволяет клиенту отправлять сообщения на сервер.</span><span class="sxs-lookup"><span data-stu-id="741fe-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="741fe-356">Добавьте выделенный код в файл *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="741fe-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="741fe-357">Приведенный выше код поддерживает получение сообщений от сервера.</span><span class="sxs-lookup"><span data-stu-id="741fe-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="741fe-358">Класс `HubConnectionBuilder` создает новый построитель для настройки подключения к серверу.</span><span class="sxs-lookup"><span data-stu-id="741fe-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="741fe-359">Функция `withUrl` настраивает URL-адрес концентратора.</span><span class="sxs-lookup"><span data-stu-id="741fe-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="741fe-360">SignalR обеспечивает обмен сообщениями между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="741fe-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="741fe-361">Каждому сообщению присваивается определенное имя.</span><span class="sxs-lookup"><span data-stu-id="741fe-361">Each message has a specific name.</span></span> <span data-ttu-id="741fe-362">Например, сообщения с именем `messageReceived` могут выполнять логику отображения новых сообщений в соответствующей зоне.</span><span class="sxs-lookup"><span data-stu-id="741fe-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="741fe-363">Прослушивание определенных сообщений реализуется с помощью функции `on`.</span><span class="sxs-lookup"><span data-stu-id="741fe-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="741fe-364">Возможно прослушивание любого числа имен сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-364">You can listen to any number of message names.</span></span> <span data-ttu-id="741fe-365">Кроме того, можно передавать параметры сообщения, например имя его автора или содержимое полученного сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="741fe-366">После того как клиент получает сообщение, создается новый элемент `div`, в атрибуте `innerHTML` которого содержатся имя автора и содержимое сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="741fe-367">Новое сообщение добавляется в основной элемент `div`, который используется для отображения сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="741fe-368">Теперь клиент может принимать сообщения. Далее следует настроить его для отправки сообщений.</span><span class="sxs-lookup"><span data-stu-id="741fe-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="741fe-369">Добавьте выделенный код в файл *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="741fe-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="741fe-370">Для отправки сообщения через соединение WebSockets необходимо вызвать метод `send`.</span><span class="sxs-lookup"><span data-stu-id="741fe-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="741fe-371">Первый параметр этого метода содержит имя сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="741fe-372">Другие параметры заполняются данными сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="741fe-373">В этом примере на сервер отправляется сообщение, идентифицированное как `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="741fe-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="741fe-374">Это сообщение содержит имя пользователя, а также данные, введенные этим пользователем в текстовое поле.</span><span class="sxs-lookup"><span data-stu-id="741fe-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="741fe-375">Если отправка выполняется успешно, значение текстового поля очищается.</span><span class="sxs-lookup"><span data-stu-id="741fe-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="741fe-376">Добавьте метод `NewMessage` в класс `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="741fe-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="741fe-377">Приведенный выше код осуществляет широковещательную рассылку полученных сервером сообщений всем подключенным пользователям.</span><span class="sxs-lookup"><span data-stu-id="741fe-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="741fe-378">Универсальный метод `on` для получения всех сообщений не требуется.</span><span class="sxs-lookup"><span data-stu-id="741fe-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="741fe-379">Имя метода указывается после суффиксов имени сообщения.</span><span class="sxs-lookup"><span data-stu-id="741fe-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="741fe-380">В этом примере клиент TypeScript отправляет сообщение, идентифицированное как `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="741fe-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="741fe-381">Метод C# `NewMessage` ожидает данные, отправленные клиентом.</span><span class="sxs-lookup"><span data-stu-id="741fe-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="741fe-382">Выполняется вызов метода [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) для [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="741fe-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="741fe-383">Полученные сообщения отправляются всем клиентам, подключенным к концентратору.</span><span class="sxs-lookup"><span data-stu-id="741fe-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="741fe-384">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="741fe-384">Test the app</span></span>

<span data-ttu-id="741fe-385">Чтобы проверить работоспособность приложения, выполните следующие шаги.</span><span class="sxs-lookup"><span data-stu-id="741fe-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fe-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fe-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="741fe-387">Запустите средство Webpack в режиме *release*.</span><span class="sxs-lookup"><span data-stu-id="741fe-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="741fe-388">Выполните следующие команды в окне **Консоль диспетчера пакетов** в корневом элементе проекта.</span><span class="sxs-lookup"><span data-stu-id="741fe-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="741fe-389">Если вы не в корневом каталоге проекта, введите перед этой командой `cd SignalRWebPack`.</span><span class="sxs-lookup"><span data-stu-id="741fe-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="741fe-390">Выберите **Отладка** > **Запуск без отладки**, чтобы запустить приложение в браузере, не присоединяя отладчик.</span><span class="sxs-lookup"><span data-stu-id="741fe-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="741fe-391">Файл *wwwroot/index.html* обрабатывается по адресу `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="741fe-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="741fe-392">Откройте другой экземпляр любого браузера.</span><span class="sxs-lookup"><span data-stu-id="741fe-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="741fe-393">Вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="741fe-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="741fe-394">Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="741fe-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="741fe-395">На обеих страницах мгновенно отображаются имя пользователя и сообщение.</span><span class="sxs-lookup"><span data-stu-id="741fe-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="741fe-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="741fe-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="741fe-397">Запустите средство Webpack в режиме *release*, выполнив следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="741fe-398">Выполните построение и запустите приложение, выполнив следующую команду в корневом элементе проекта:</span><span class="sxs-lookup"><span data-stu-id="741fe-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="741fe-399">Веб-сервер запускает приложение и делает его доступным на узле localhost.</span><span class="sxs-lookup"><span data-stu-id="741fe-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="741fe-400">Откройте браузер с адресом `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="741fe-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="741fe-401">Обрабатывается файл *wwwroot/index.html*.</span><span class="sxs-lookup"><span data-stu-id="741fe-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="741fe-402">Скопируйте URL-адрес из адресной строки.</span><span class="sxs-lookup"><span data-stu-id="741fe-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="741fe-403">Откройте другой экземпляр любого браузера.</span><span class="sxs-lookup"><span data-stu-id="741fe-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="741fe-404">Вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="741fe-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="741fe-405">Выберите любой браузер, введите произвольный текст в поле **Сообщение** и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="741fe-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="741fe-406">На обеих страницах мгновенно отображаются имя пользователя и сообщение.</span><span class="sxs-lookup"><span data-stu-id="741fe-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Сообщение, отображаемое в обоих окнах браузера](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="741fe-408">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="741fe-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
