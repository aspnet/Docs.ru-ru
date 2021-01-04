---
title: Создание приложения Blazor со списком дел
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 87626ff30589de82a04c95634fc0dcbcf2eeac18
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507011"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="22c07-103">Создание приложения Blazor со списком дел</span><span class="sxs-lookup"><span data-stu-id="22c07-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="22c07-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="22c07-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="22c07-105">В этом учебнике показано, как создать и изменить приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="22c07-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="22c07-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="22c07-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="22c07-107">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="22c07-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="22c07-108">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="22c07-108">Modify Razor components</span></span>
> * <span data-ttu-id="22c07-109">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="22c07-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="22c07-110">Использование маршрутизации в приложении Blazor</span><span class="sxs-lookup"><span data-stu-id="22c07-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="22c07-111">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.</span><span class="sxs-lookup"><span data-stu-id="22c07-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="22c07-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="22c07-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="22c07-113">Создание приложения Blazor со списком задач</span><span class="sxs-lookup"><span data-stu-id="22c07-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="22c07-114">Создайте в командной строке новое приложение Blazor с именем `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="22c07-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="22c07-115">Предыдущая команда создает папку с именем `TodoList` и параметром `-o|--output` для хранения приложения.</span><span class="sxs-lookup"><span data-stu-id="22c07-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="22c07-116">Папка `TodoList` находится в *корневой папке* проекта.</span><span class="sxs-lookup"><span data-stu-id="22c07-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="22c07-117">Перейдите в папку `TodoList` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="22c07-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="22c07-118">Добавьте в приложение новый компонент Razor `Todo` с помощью следующей команды.</span><span class="sxs-lookup"><span data-stu-id="22c07-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="22c07-119">Параметр `-n|--name` в предыдущей команде задает имя нового компонента Razor.</span><span class="sxs-lookup"><span data-stu-id="22c07-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="22c07-120">Новый компонент создается в папке `Pages` проекта с параметром `-o|--output`.</span><span class="sxs-lookup"><span data-stu-id="22c07-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="22c07-121">Имена файлов компонентов Razor должны начинаться с прописной буквы.</span><span class="sxs-lookup"><span data-stu-id="22c07-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="22c07-122">Откройте папку `Pages` и убедитесь, что имя файла компонента `Todo` начинается с заглавной буквы `T`.</span><span class="sxs-lookup"><span data-stu-id="22c07-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="22c07-123">Имя файла должно быть `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="22c07-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="22c07-124">Откройте компонент `Todo` в любом редакторе файлов и добавьте директиву `@page` Razor в начало файла с относительным URL-адресом `/todo`.</span><span class="sxs-lookup"><span data-stu-id="22c07-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="22c07-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="22c07-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo0.razor?highlight=1)]

   <span data-ttu-id="22c07-126">Сохраните файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="22c07-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="22c07-127">Добавьте компонент `Todo` на панель навигации.</span><span class="sxs-lookup"><span data-stu-id="22c07-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="22c07-128">Компонент `NavMenu` используется в макете этого приложения.</span><span class="sxs-lookup"><span data-stu-id="22c07-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="22c07-129">Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.</span><span class="sxs-lookup"><span data-stu-id="22c07-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="22c07-130">Когда в приложении загружается URL-адрес компонента, компонент `NavLink` предоставляет соответствующее указание в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="22c07-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="22c07-131">В неупорядоченном списке (`<ul>...</ul>`) компонента `NavMenu` добавьте указанный ниже элемент списка (`<li>...</li>`) и компонент `NavLink` для компонента `Todo`.</span><span class="sxs-lookup"><span data-stu-id="22c07-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="22c07-132">В `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="22c07-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="22c07-133">Сохраните файл `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="22c07-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="22c07-134">Скомпилируйте и запустите приложение, выполнив команду [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) в командной оболочке из папки `TodoList`.</span><span class="sxs-lookup"><span data-stu-id="22c07-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="22c07-135">После запуска приложения перейдите на страницу списка дел, щелкнув ссылку **`Todo`** на панели навигации приложения. В результате будет загружена страница по адресу `/todo`.</span><span class="sxs-lookup"><span data-stu-id="22c07-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="22c07-136">Оставьте приложение выполняться в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="22c07-136">Leave the app running the command shell.</span></span> <span data-ttu-id="22c07-137">При каждом сохранении файла приложение автоматически перестраивается.</span><span class="sxs-lookup"><span data-stu-id="22c07-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="22c07-138">Браузер временно утрачивает подключение к приложению на время компиляции и перезапуска.</span><span class="sxs-lookup"><span data-stu-id="22c07-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="22c07-139">При восстановлении подключения страница в браузере автоматически перезагружается.</span><span class="sxs-lookup"><span data-stu-id="22c07-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="22c07-140">Добавьте в корень проекта (папка `TodoList`) файл `TodoItem.cs`, который будет содержать класс для элемента списка дел.</span><span class="sxs-lookup"><span data-stu-id="22c07-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="22c07-141">Используйте следующий код C# для класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="22c07-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="22c07-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="22c07-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="22c07-143">Вернитесь к компоненту `Todo` и выполните указанные ниже задачи.</span><span class="sxs-lookup"><span data-stu-id="22c07-143">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="22c07-144">Добавьте поле для элементов списка дел в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="22c07-144">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="22c07-145">Компонент `Todo` использует это поле для сохранения состояния списка дел.</span><span class="sxs-lookup"><span data-stu-id="22c07-145">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="22c07-146">Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="22c07-146">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="22c07-147">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="22c07-147">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="22c07-148">Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.</span><span class="sxs-lookup"><span data-stu-id="22c07-148">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="22c07-149">Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="22c07-149">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="22c07-150">Сохраните файл `TodoItem.cs` и обновленный файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="22c07-150">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="22c07-151">При сохранении файлов в командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="22c07-151">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="22c07-152">Браузер временно утратит подключение к приложению, но когда подключение будет восстановлено, браузер перезагрузит страницу.</span><span class="sxs-lookup"><span data-stu-id="22c07-152">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="22c07-153">При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не подключен обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="22c07-153">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="22c07-154">Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="22c07-154">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="22c07-155">Теперь при нажатии кнопки вызывается метод C# `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="22c07-155">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="22c07-156">Чтобы получить заголовок нового элемента списка дел, добавьте строковое поле `newTodo` в начало блока `@code`.</span><span class="sxs-lookup"><span data-stu-id="22c07-156">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   <span data-ttu-id="22c07-157">Измените текстовый элемент `<input>`, чтобы привязать `newTodo` с помощью атрибута `@bind`.</span><span class="sxs-lookup"><span data-stu-id="22c07-157">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="22c07-158">Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список.</span><span class="sxs-lookup"><span data-stu-id="22c07-158">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="22c07-159">Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.</span><span class="sxs-lookup"><span data-stu-id="22c07-159">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="22c07-160">Сохраните файл `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="22c07-160">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="22c07-161">В командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="22c07-161">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="22c07-162">После повторного подключения браузера к приложению страница в браузере перезагрузится.</span><span class="sxs-lookup"><span data-stu-id="22c07-162">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="22c07-163">Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач.</span><span class="sxs-lookup"><span data-stu-id="22c07-163">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="22c07-164">Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="22c07-164">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="22c07-165">Замените `@todo.Title` элементом `<input>`, который привязан к `todo.Title` с помощью `@bind`.</span><span class="sxs-lookup"><span data-stu-id="22c07-165">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=4-7)]

1. <span data-ttu-id="22c07-166">Обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).</span><span class="sxs-lookup"><span data-stu-id="22c07-166">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="22c07-167">Готовый компонент `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="22c07-167">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="22c07-168">Сохраните файл `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="22c07-168">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="22c07-169">В командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="22c07-169">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="22c07-170">После повторного подключения браузера к приложению страница в браузере перезагрузится.</span><span class="sxs-lookup"><span data-stu-id="22c07-170">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="22c07-171">Добавьте элементы, измените их и пометьте элементы списка дел как завершенные, чтобы протестировать компонент.</span><span class="sxs-lookup"><span data-stu-id="22c07-171">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="22c07-172">По окончании завершите работу приложения в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="22c07-172">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="22c07-173">Многие командные оболочки принимают команду клавиатуры <kbd>CTRL</kbd>+<kbd>C</kbd> для остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="22c07-173">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="22c07-174">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="22c07-174">Next steps</span></span>

<span data-ttu-id="22c07-175">В этом руководстве вы узнали, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="22c07-175">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="22c07-176">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="22c07-176">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="22c07-177">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="22c07-177">Modify Razor components</span></span>
> * <span data-ttu-id="22c07-178">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="22c07-178">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="22c07-179">Использование маршрутизации в приложении Blazor</span><span class="sxs-lookup"><span data-stu-id="22c07-179">Use routing in a Blazor app</span></span>

<span data-ttu-id="22c07-180">Подробные сведения об инструментах для ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="22c07-180">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
