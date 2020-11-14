---
title: Часть 3. Добавление представления в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 3 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 56f39643ccd69c6508148374033208eb3b2f25ab
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422721"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="82523-103">Часть 3. Добавление представления в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82523-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="82523-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="82523-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82523-105">В этом разделе вы измените класс `HelloWorldController`, чтобы использовать файлы представления [Razor](xref:mvc/views/razor) для отчетливой инкапсуляции процесса создания HTML-ответов для клиента.</span><span class="sxs-lookup"><span data-stu-id="82523-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="82523-106">Файл шаблона представления создается с помощью Razor.</span><span class="sxs-lookup"><span data-stu-id="82523-106">You create a view template file using Razor.</span></span> <span data-ttu-id="82523-107">Шаблоны представления на основе Razor имеют расширение файла *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="82523-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="82523-108">Они реализуют удобный способ для создания выходных данных HTML с помощью C#.</span><span class="sxs-lookup"><span data-stu-id="82523-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="82523-109">Сейчас метод `Index` возвращает строку с сообщением, которое жестко задано в классе контроллера.</span><span class="sxs-lookup"><span data-stu-id="82523-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="82523-110">В классе `HelloWorldController` замените метод `Index` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="82523-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="82523-111">Предыдущий код вызывает метод <xref:Microsoft.AspNetCore.Mvc.Controller.View*> контроллера.</span><span class="sxs-lookup"><span data-stu-id="82523-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="82523-112">В нем используется шаблон представления для создания HTML-ответа.</span><span class="sxs-lookup"><span data-stu-id="82523-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="82523-113">Методы контроллера (также называются *методами действия* ), такие как приведенный выше метод `Index`, обычно возвращают <xref:Microsoft.AspNetCore.Mvc.IActionResult> (или производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult> класс), а не типы, такие как `string`.</span><span class="sxs-lookup"><span data-stu-id="82523-113">Controller methods (also known as *action methods* ), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="82523-114">Добавление представления</span><span class="sxs-lookup"><span data-stu-id="82523-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="82523-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82523-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="82523-116">Щелкните правой кнопкой мыши папку *Views* , а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="82523-117">Щелкните правой кнопкой мыши папку *Views/HelloWorld* , а затем выберите **Добавить > Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="82523-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="82523-118">В диалоговом окне **Добавление нового элемента MvcMovie** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="82523-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="82523-119">В поле поиска в правом верхнем углу введите *view* (представление).</span><span class="sxs-lookup"><span data-stu-id="82523-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="82523-120">Выберите **Представление Razor** .</span><span class="sxs-lookup"><span data-stu-id="82523-120">Select **Razor View**</span></span>

  * <span data-ttu-id="82523-121">В поле *Имя* оставьте значение **Index.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="82523-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="82523-122">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="82523-122">Select **Add**</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="82523-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="82523-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="82523-125">Добавьте представление `Index` для `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="82523-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="82523-126">Добавьте новую папку с именем *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="82523-127">Добавьте новый файл *Index.cshtml* в папку *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="82523-128">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="82523-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="82523-129">Щелкните правой кнопкой мыши папку *Views* , а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="82523-130">Щелкните правой кнопкой мыши папку *Views/HelloWorld* , а затем выберите **Добавить > Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="82523-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="82523-131">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="82523-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="82523-132">Выберите **ASP .NET Core** в левой области.</span><span class="sxs-lookup"><span data-stu-id="82523-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="82523-133">Выберите **страницу представления MVC** в центральной области.</span><span class="sxs-lookup"><span data-stu-id="82523-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="82523-134">В поле **Имя** введите *Index*.</span><span class="sxs-lookup"><span data-stu-id="82523-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="82523-135">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="82523-135">Select **New**.</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="82523-137">Замените содержимое файла представления Razor *Views/HelloWorld/Index.cshtml* следующим.</span><span class="sxs-lookup"><span data-stu-id="82523-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="82523-138">Перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="82523-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="82523-139">Метод `Index` в `HelloWorldController` сделал совсем мало. Он выполнил оператор `return View();`, который указал, что метод должен использовать файл шаблона представления для отображения ответа в браузере.</span><span class="sxs-lookup"><span data-stu-id="82523-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="82523-140">Так как имя файла шаблона представления не указано, MVC по умолчанию использует файл представления по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="82523-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="82523-141">Файл представления по умолчанию имеет имя, совпадающее с именем метода (`Index`), поэтому используется шаблон представления в файле */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="82523-142">На рисунке ниже показана строка "Hello from our View Template!",</span><span class="sxs-lookup"><span data-stu-id="82523-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="82523-143">которая жестко задана в представлении.</span><span class="sxs-lookup"><span data-stu-id="82523-143">hard-coded in the view.</span></span>

![Окно браузера](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="82523-145">Изменение представлений и страниц макета</span><span class="sxs-lookup"><span data-stu-id="82523-145">Change views and layout pages</span></span>

<span data-ttu-id="82523-146">Выберите ссылки в меню ( **MvcMovie** , **Домашняя страница** и **Конфиденциальность** ).</span><span class="sxs-lookup"><span data-stu-id="82523-146">Select the menu links ( **MvcMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="82523-147">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="82523-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="82523-148">Макет меню реализован в файле *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="82523-149">Откройте файл *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="82523-150">С помощью шаблонов [макета](xref:mvc/views/layout) можно в одном месте задать макет контейнера HTML для всего сайта и затем использовать его на разных страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="82523-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="82523-151">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="82523-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="82523-152">`RenderBody` — это заполнитель, в котором отображаются все создаваемые страницы для определенных представлений, *упакованные* на странице макета.</span><span class="sxs-lookup"><span data-stu-id="82523-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="82523-153">Например, если щелкнуть ссылку **Конфиденциальность** , представление **Views/Home/Privacy.cshtml** отображается в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="82523-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="82523-154">Изменение заголовка, нижнего колонтитула и ссылки меню в файле макета</span><span class="sxs-lookup"><span data-stu-id="82523-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="82523-155">Замените содержимое файла *Views/Shared/_Layout.cshtml* следующей разметкой.</span><span class="sxs-lookup"><span data-stu-id="82523-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="82523-156">Изменения выделены:</span><span class="sxs-lookup"><span data-stu-id="82523-156">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82523-157">Приведенная выше разметка вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="82523-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="82523-158">Замена трех вхождений `MvcMovie` на `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="82523-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="82523-159">Замена элемента привязки `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` на `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="82523-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="82523-160">В приведенной выше разметке [атрибут вспомогательной функции тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-area=""` и значение атрибута были опущены, так как это приложение не использует области ([area](xref:mvc/controllers/areas)).</span><span class="sxs-lookup"><span data-stu-id="82523-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="82523-161">**Примечание**. Контроллер `Movies` не был реализован.</span><span class="sxs-lookup"><span data-stu-id="82523-161">**Note** : The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="82523-162">На этом этапе ссылка `Movie App` не работает.</span><span class="sxs-lookup"><span data-stu-id="82523-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="82523-163">Сохраните изменения и нажмите на ссылку **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="82523-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="82523-164">Обратите внимание, что заголовок вкладки браузера изменяется на **Политика конфиденциальности — Movie App** вместо **Политика конфиденциальности — Mvc Movie** :</span><span class="sxs-lookup"><span data-stu-id="82523-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie** :</span></span>

![Вкладка "Конфиденциальность"](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="82523-166">Выберите ссылку **Домашняя страница** и убедитесь, что в заголовке и тексте привязки также отображается **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="82523-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="82523-167">Таким образом, внеся одно изменение в шаблон макета, мы изменили заголовок и текст ссылки на всех страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="82523-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="82523-168">Просмотрите файл *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="82523-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="82523-169">Файл *Views/_ViewStart.cshtml* предоставляет файл *Views/Shared/_Layout.cshtml* для каждого представления.</span><span class="sxs-lookup"><span data-stu-id="82523-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="82523-170">Свойство `Layout` может задавать другое представление макета или иметь значение `null`, при котором макет не используется.</span><span class="sxs-lookup"><span data-stu-id="82523-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="82523-171">Измените заголовок и элемент `<h2>` файла представления *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="82523-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="82523-172">Заголовок и элемент `<h2>` немного отличаются, чтобы вы видели, какой именно фрагмент кода изменяет отображение.</span><span class="sxs-lookup"><span data-stu-id="82523-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="82523-173">`ViewData["Title"] = "Movie List";` в приведенном выше коде присваивает свойству `Title` словаря `ViewData` значение "Movie List".</span><span class="sxs-lookup"><span data-stu-id="82523-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="82523-174">Свойство `Title` используется в элементе HTML `<title>` на странице макета:</span><span class="sxs-lookup"><span data-stu-id="82523-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="82523-175">Сохраните изменения и перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="82523-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="82523-176">Обратите внимание, что основной и дополнительный заголовки браузера изменились.</span><span class="sxs-lookup"><span data-stu-id="82523-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="82523-177">(Если изменения не отображаются, возможно, вы просматриваете кэшированное содержимое.</span><span class="sxs-lookup"><span data-stu-id="82523-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="82523-178">В этом случае нажмите в браузере клавиши CTRL+F5 для принудительной загрузки ответа сервера.) Заголовок браузера создается с помощью атрибута `ViewData["Title"]`, который задается в шаблоне представления *Index.cshtml* и дополнительной строки "- Movie App", добавляемой в файл макета.</span><span class="sxs-lookup"><span data-stu-id="82523-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="82523-179">Содержимое шаблона представления *Index.cshtml* объединяется с шаблоном представления *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="82523-180">В браузер отправляется один HTML-ответ.</span><span class="sxs-lookup"><span data-stu-id="82523-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="82523-181">Шаблоны макетов позволяют легко вносить изменения, применяемые ко всем страницам в приложении.</span><span class="sxs-lookup"><span data-stu-id="82523-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="82523-182">Дополнительные сведения см. в разделе [Макет](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="82523-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Представление списка фильмов](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="82523-184">В этом примере небольшой фрагмент данных (сообщение "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="82523-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="82523-185">) жестко задан в коде.</span><span class="sxs-lookup"><span data-stu-id="82523-185">message) is hard-coded, though.</span></span> <span data-ttu-id="82523-186">Приложение MVC предоставляет представление, вы реализуете контроллер, однако модели на данный момент еще нет.</span><span class="sxs-lookup"><span data-stu-id="82523-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="82523-187">Передача данных из контроллера в представление</span><span class="sxs-lookup"><span data-stu-id="82523-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="82523-188">Действия контроллера вызываются в ответ на входящий запрос URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="82523-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="82523-189">Код, обрабатывающий входящие запросы браузера, добавляется в класс контроллера.</span><span class="sxs-lookup"><span data-stu-id="82523-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="82523-190">Контроллер извлекает данные из источника данных и определяет тип ответа, который будет отправлен в браузер.</span><span class="sxs-lookup"><span data-stu-id="82523-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="82523-191">Контроллер может использовать шаблоны представлений для создания и форматирования ответа HTML, отправляемого браузеру.</span><span class="sxs-lookup"><span data-stu-id="82523-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="82523-192">Контроллер отвечает за предоставление данных, необходимых шаблону представления для отображения ответа.</span><span class="sxs-lookup"><span data-stu-id="82523-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="82523-193">Рекомендации: шаблоны представлений **не должны** выполнять бизнес-логику или напрямую взаимодействовать с базой данных.</span><span class="sxs-lookup"><span data-stu-id="82523-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="82523-194">Вместо этого они должны работать только с данными, которые им предоставляет контроллер.</span><span class="sxs-lookup"><span data-stu-id="82523-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="82523-195">Подобное разделение сфер ответственности позволяет обеспечить максимальную оптимизацию кода, а также удобство его тестирования и поддержки.</span><span class="sxs-lookup"><span data-stu-id="82523-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="82523-196">В настоящее время метод `Welcome` в классе `HelloWorldController` принимает параметры `name` и `ID`, после чего выводит значения непосредственно в браузер.</span><span class="sxs-lookup"><span data-stu-id="82523-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="82523-197">Вместо отображения ответа в виде строки настройте контроллер для использования шаблона представления.</span><span class="sxs-lookup"><span data-stu-id="82523-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="82523-198">Шаблон представления создает динамический ответ, для получения которого необходимо передать соответствующие фрагменты данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="82523-199">Для этого контроллер может поместить динамические данные (параметры), которые требуются шаблону представления, в словарь `ViewData`, к которому впоследствии будет обращаться этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="82523-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="82523-200">В файле *HelloWorldController.cs* измените метод `Welcome` так, чтобы добавлять значения `Message` и `NumTimes` в словарь `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="82523-200">In *HelloWorldController.cs* , change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="82523-201">Словарь `ViewData` является динамическим объектом, то есть можно использовать любой тип. Объект `ViewData` не имеет определенных свойств, пока в него не будут добавлены какие-либо данные.</span><span class="sxs-lookup"><span data-stu-id="82523-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="82523-202">[Система привязки модели MVC](xref:mvc/models/model-binding) автоматически сопоставляет именованные параметры (`name` и `numTimes`) из строк запроса в адресной строке с параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="82523-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="82523-203">Полный файл *HelloWorldController.cs* выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="82523-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="82523-204">Объект словаря `ViewData` содержит данные, которые будут передаваться в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="82523-205">Создайте шаблон представления экрана приветствия с именем *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="82523-206">В шаблоне представления *Welcome.cshtml* создайте цикл, который будет отображать строку "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="82523-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="82523-207">Замените содержимое файла *Views/HelloWorld/Welcome.cshtml* следующим:</span><span class="sxs-lookup"><span data-stu-id="82523-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="82523-208">Сохраните изменения и перейдите по следующему URL-адресу:</span><span class="sxs-lookup"><span data-stu-id="82523-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="82523-209">Данные извлекаются по URL-адресу и передаются в контроллер с помощью [средства привязки модели MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="82523-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="82523-210">Контроллер упаковывает данные в словарь `ViewData` и передает этот объект в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="82523-211">Представление отображает данные в формате HTML в браузере.</span><span class="sxs-lookup"><span data-stu-id="82523-211">The view then renders the data as HTML to the browser.</span></span>

![Представление конфиденциальности, в котором отображается приветственный заголовок и четыре раза выводится фраза Hello Rick.](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="82523-213">В примере выше мы использовали словарь `ViewData` для передачи данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="82523-214">Далее в этом руководстве для передачи данных из контроллера в представление мы будем использовать модель представления.</span><span class="sxs-lookup"><span data-stu-id="82523-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="82523-215">Подход к передаче данных на основе модели представления является предпочтительным относительно применения словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="82523-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="82523-216">Дополнительные сведения см. в статье об [использовании ViewBag, ViewData или TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="82523-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="82523-217">В следующем руководстве создается база данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="82523-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="82523-218">[Назад](adding-controller.md)
> [Вперед](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="82523-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="82523-219">В этом разделе вы измените класс `HelloWorldController`, чтобы использовать файлы представления [Razor](xref:mvc/views/razor) для отчетливой инкапсуляции процесса создания HTML-ответов для клиента.</span><span class="sxs-lookup"><span data-stu-id="82523-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="82523-220">Файл шаблона представления создается с помощью Razor.</span><span class="sxs-lookup"><span data-stu-id="82523-220">You create a view template file using Razor.</span></span> <span data-ttu-id="82523-221">Шаблоны представления на основе Razor имеют расширение файла *CSHTML*.</span><span class="sxs-lookup"><span data-stu-id="82523-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="82523-222">Они реализуют удобный способ для создания выходных данных HTML с помощью C#.</span><span class="sxs-lookup"><span data-stu-id="82523-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="82523-223">Сейчас метод `Index` возвращает строку с сообщением, которое жестко задано в классе контроллера.</span><span class="sxs-lookup"><span data-stu-id="82523-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="82523-224">В классе `HelloWorldController` замените метод `Index` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="82523-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="82523-225">Предыдущий код вызывает метод <xref:Microsoft.AspNetCore.Mvc.Controller.View*> контроллера.</span><span class="sxs-lookup"><span data-stu-id="82523-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="82523-226">В нем используется шаблон представления для создания HTML-ответа.</span><span class="sxs-lookup"><span data-stu-id="82523-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="82523-227">Методы контроллера (также называются *методами действия* ), такие как приведенный выше метод `Index`, обычно возвращают <xref:Microsoft.AspNetCore.Mvc.IActionResult> (или производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult> класс), а не типы, такие как `string`.</span><span class="sxs-lookup"><span data-stu-id="82523-227">Controller methods (also known as *action methods* ), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="82523-228">Добавление представления</span><span class="sxs-lookup"><span data-stu-id="82523-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="82523-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82523-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="82523-230">Щелкните правой кнопкой мыши папку *Views* , а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="82523-231">Щелкните правой кнопкой мыши папку *Views/HelloWorld* , а затем выберите **Добавить > Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="82523-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="82523-232">В диалоговом окне **Добавление нового элемента MvcMovie** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="82523-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="82523-233">В поле поиска в правом верхнем углу введите *view* (представление).</span><span class="sxs-lookup"><span data-stu-id="82523-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="82523-234">Выберите **Представление Razor** .</span><span class="sxs-lookup"><span data-stu-id="82523-234">Select **Razor View**</span></span>

  * <span data-ttu-id="82523-235">В поле *Имя* оставьте значение **Index.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="82523-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="82523-236">Нажмите **Добавить**</span><span class="sxs-lookup"><span data-stu-id="82523-236">Select **Add**</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="82523-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="82523-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="82523-239">Добавьте представление `Index` для `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="82523-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="82523-240">Добавьте новую папку с именем *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="82523-241">Добавьте новый файл *Index.cshtml* в папку *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="82523-242">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="82523-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="82523-243">Щелкните правой кнопкой мыши папку *Views* , а затем выберите **Добавить > Новая папка**. Назовите папку *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="82523-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="82523-244">Щелкните правой кнопкой мыши папку *Views/HelloWorld* , а затем выберите **Добавить > Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="82523-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="82523-245">В диалоговом окне **Новый файл** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="82523-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="82523-246">В левой области выберите **Веб**.</span><span class="sxs-lookup"><span data-stu-id="82523-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="82523-247">В центральной области выберите **Пустой HTML-файл**.</span><span class="sxs-lookup"><span data-stu-id="82523-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="82523-248">В поле **Имя** введите *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="82523-249">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="82523-249">Select **New**.</span></span>

![Диалоговое окно ''Добавление нового элемента''](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="82523-251">Замените содержимое файла представления Razor *Views/HelloWorld/Index.cshtml* следующим.</span><span class="sxs-lookup"><span data-stu-id="82523-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="82523-252">Перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="82523-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="82523-253">Метод `Index` в `HelloWorldController` сделал совсем мало. Он выполнил оператор `return View();`, который указал, что метод должен использовать файл шаблона представления для отображения ответа в браузере.</span><span class="sxs-lookup"><span data-stu-id="82523-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="82523-254">Так как имя файла шаблона представления не указано, MVC по умолчанию использует файл представления по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="82523-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="82523-255">Файл представления по умолчанию имеет имя, совпадающее с именем метода (`Index`), поэтому используется файл */Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="82523-256">На рисунке ниже показана строка "Hello from our View Template!",</span><span class="sxs-lookup"><span data-stu-id="82523-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="82523-257">которая жестко задана в представлении.</span><span class="sxs-lookup"><span data-stu-id="82523-257">hard-coded in the view.</span></span>

![Окно браузера](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="82523-259">Изменение представлений и страниц макета</span><span class="sxs-lookup"><span data-stu-id="82523-259">Change views and layout pages</span></span>

<span data-ttu-id="82523-260">Выберите ссылки в меню ( **MvcMovie** , **Домашняя страница** и **Конфиденциальность** ).</span><span class="sxs-lookup"><span data-stu-id="82523-260">Select the menu links ( **MvcMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="82523-261">Меню на каждой странице имеют одинаковый макет.</span><span class="sxs-lookup"><span data-stu-id="82523-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="82523-262">Макет меню реализован в файле *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="82523-263">Откройте файл *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="82523-264">С помощью шаблонов [макета](xref:mvc/views/layout) можно в одном месте задать макет контейнера HTML для всего сайта и затем использовать его на разных страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="82523-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="82523-265">Найдите строку `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="82523-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="82523-266">`RenderBody` — это заполнитель, в котором отображаются все создаваемые страницы для определенных представлений, *упакованные* на странице макета.</span><span class="sxs-lookup"><span data-stu-id="82523-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="82523-267">Например, если щелкнуть ссылку **Конфиденциальность** , представление **Views/Home/Privacy.cshtml** отображается в методе `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="82523-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="82523-268">Изменение заголовка, нижнего колонтитула и ссылки меню в файле макета</span><span class="sxs-lookup"><span data-stu-id="82523-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="82523-269">В элементах заголовка и нижнего колонтитула измените `MvcMovie` на `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="82523-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="82523-270">Измените элемент привязки `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` на `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="82523-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="82523-271">Эти изменения выделены в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="82523-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="82523-272">В приведенной выше разметке [атрибут вспомогательной функции тега привязки](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-area` был опущен, так как это приложение не использует [области](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="82523-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="82523-273">**Примечание**. Контроллер `Movies` не был реализован.</span><span class="sxs-lookup"><span data-stu-id="82523-273">**Note** : The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="82523-274">На этом этапе ссылка `Movie App` не работает.</span><span class="sxs-lookup"><span data-stu-id="82523-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="82523-275">Сохраните изменения и нажмите на ссылку **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="82523-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="82523-276">Обратите внимание, что заголовок вкладки браузера изменяется на **Политика конфиденциальности — Movie App** вместо **Политика конфиденциальности — Mvc Movie** :</span><span class="sxs-lookup"><span data-stu-id="82523-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie** :</span></span>

![Вкладка "Конфиденциальность"](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="82523-278">Выберите ссылку **Домашняя страница** и убедитесь, что в заголовке и тексте привязки также отображается **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="82523-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="82523-279">Таким образом, внеся одно изменение в шаблон макета, мы изменили заголовок и текст ссылки на всех страницах сайта.</span><span class="sxs-lookup"><span data-stu-id="82523-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="82523-280">Просмотрите файл *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="82523-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="82523-281">Файл *Views/_ViewStart.cshtml* предоставляет файл *Views/Shared/_Layout.cshtml* для каждого представления.</span><span class="sxs-lookup"><span data-stu-id="82523-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="82523-282">Свойство `Layout` может задавать другое представление макета или иметь значение `null`, при котором макет не используется.</span><span class="sxs-lookup"><span data-stu-id="82523-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="82523-283">Измените заголовок и элемент `<h2>` файла представления *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="82523-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="82523-284">Заголовок и элемент `<h2>` немного отличаются, чтобы вы видели, какой именно фрагмент кода изменяет отображение.</span><span class="sxs-lookup"><span data-stu-id="82523-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="82523-285">`ViewData["Title"] = "Movie List";` в приведенном выше коде присваивает свойству `Title` словаря `ViewData` значение "Movie List".</span><span class="sxs-lookup"><span data-stu-id="82523-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="82523-286">Свойство `Title` используется в элементе HTML `<title>` на странице макета:</span><span class="sxs-lookup"><span data-stu-id="82523-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="82523-287">Сохраните изменения и перейдите к `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="82523-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="82523-288">Обратите внимание, что основной и дополнительный заголовки браузера изменились.</span><span class="sxs-lookup"><span data-stu-id="82523-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="82523-289">(Если изменения не отображаются, возможно, вы просматриваете кэшированное содержимое.</span><span class="sxs-lookup"><span data-stu-id="82523-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="82523-290">В этом случае нажмите в браузере клавиши CTRL+F5 для принудительной загрузки ответа сервера.) Заголовок браузера создается с помощью атрибута `ViewData["Title"]`, который задается в шаблоне представления *Index.cshtml* и дополнительной строки "- Movie App", добавляемой в файл макета.</span><span class="sxs-lookup"><span data-stu-id="82523-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="82523-291">Также обратите внимание, что содержимое шаблона представления *Index.cshtml* объединяется с шаблоном представления *Views/Shared/_Layout.cshtml* , а в браузер отправляется один ответ HTML.</span><span class="sxs-lookup"><span data-stu-id="82523-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="82523-292">С помощью шаблонов макета можно легко вносить изменения, которые применяются ко всем страницам приложения.</span><span class="sxs-lookup"><span data-stu-id="82523-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="82523-293">Дополнительные сведения см. в разделе [Макет](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="82523-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Представление списка фильмов](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="82523-295">В этом примере небольшой фрагмент данных (сообщение "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="82523-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="82523-296">) жестко задан в коде.</span><span class="sxs-lookup"><span data-stu-id="82523-296">message) is hard-coded, though.</span></span> <span data-ttu-id="82523-297">Приложение MVC предоставляет представление, вы реализуете контроллер, однако модели на данный момент еще нет.</span><span class="sxs-lookup"><span data-stu-id="82523-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="82523-298">Передача данных из контроллера в представление</span><span class="sxs-lookup"><span data-stu-id="82523-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="82523-299">Действия контроллера вызываются в ответ на входящий запрос URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="82523-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="82523-300">Код, обрабатывающий входящие запросы браузера, добавляется в класс контроллера.</span><span class="sxs-lookup"><span data-stu-id="82523-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="82523-301">Контроллер извлекает данные из источника данных и определяет тип ответа, который будет отправлен в браузер.</span><span class="sxs-lookup"><span data-stu-id="82523-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="82523-302">Контроллер может использовать шаблоны представлений для создания и форматирования ответа HTML, отправляемого браузеру.</span><span class="sxs-lookup"><span data-stu-id="82523-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="82523-303">Контроллер отвечает за предоставление данных, необходимых шаблону представления для отображения ответа.</span><span class="sxs-lookup"><span data-stu-id="82523-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="82523-304">Рекомендации: шаблоны представлений **не должны** выполнять бизнес-логику или напрямую взаимодействовать с базой данных.</span><span class="sxs-lookup"><span data-stu-id="82523-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="82523-305">Вместо этого они должны работать только с данными, которые им предоставляет контроллер.</span><span class="sxs-lookup"><span data-stu-id="82523-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="82523-306">Подобное разделение сфер ответственности позволяет обеспечить максимальную оптимизацию кода, а также удобство его тестирования и поддержки.</span><span class="sxs-lookup"><span data-stu-id="82523-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="82523-307">В настоящее время метод `Welcome` в классе `HelloWorldController` принимает параметры `name` и `ID`, после чего выводит значения непосредственно в браузер.</span><span class="sxs-lookup"><span data-stu-id="82523-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="82523-308">Вместо отображения ответа в виде строки настройте контроллер для использования шаблона представления.</span><span class="sxs-lookup"><span data-stu-id="82523-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="82523-309">Шаблон представления создает динамический ответ, для получения которого необходимо передать соответствующие фрагменты данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="82523-310">Для этого контроллер может поместить динамические данные (параметры), которые требуются шаблону представления, в словарь `ViewData`, к которому впоследствии будет обращаться этот шаблон.</span><span class="sxs-lookup"><span data-stu-id="82523-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="82523-311">В файле *HelloWorldController.cs* измените метод `Welcome` так, чтобы добавлять значения `Message` и `NumTimes` в словарь `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="82523-311">In *HelloWorldController.cs* , change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="82523-312">Словарь `ViewData` является динамическим объектом, то есть можно использовать любой тип. Объект `ViewData` не имеет определенных свойств, пока в него не будут добавлены какие-либо данные.</span><span class="sxs-lookup"><span data-stu-id="82523-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="82523-313">[Система привязки модели MVC](xref:mvc/models/model-binding) автоматически сопоставляет именованные параметры (`name` и `numTimes`) из строк запроса в адресной строке с параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="82523-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="82523-314">Полный файл *HelloWorldController.cs* выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="82523-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="82523-315">Объект словаря `ViewData` содержит данные, которые будут передаваться в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="82523-316">Создайте шаблон представления экрана приветствия с именем *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82523-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="82523-317">В шаблоне представления *Welcome.cshtml* создайте цикл, который будет отображать строку "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="82523-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="82523-318">Замените содержимое файла *Views/HelloWorld/Welcome.cshtml* следующим:</span><span class="sxs-lookup"><span data-stu-id="82523-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="82523-319">Сохраните изменения и перейдите по следующему URL-адресу:</span><span class="sxs-lookup"><span data-stu-id="82523-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="82523-320">Данные извлекаются по URL-адресу и передаются в контроллер с помощью [средства привязки модели MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="82523-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="82523-321">Контроллер упаковывает данные в словарь `ViewData` и передает этот объект в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="82523-322">Представление отображает данные в формате HTML в браузере.</span><span class="sxs-lookup"><span data-stu-id="82523-322">The view then renders the data as HTML to the browser.</span></span>

![Представление конфиденциальности, в котором отображается приветственный заголовок и четыре раза выводится фраза Hello Rick.](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="82523-324">В примере выше мы использовали словарь `ViewData` для передачи данных из контроллера в представление.</span><span class="sxs-lookup"><span data-stu-id="82523-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="82523-325">Далее в этом руководстве для передачи данных из контроллера в представление мы будем использовать модель представления.</span><span class="sxs-lookup"><span data-stu-id="82523-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="82523-326">Подход к передаче данных на основе модели представления является предпочтительным относительно применения словаря `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="82523-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="82523-327">Дополнительные сведения см. в статье об [использовании ViewBag, ViewData или TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="82523-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="82523-328">В следующем руководстве создается база данных фильмов.</span><span class="sxs-lookup"><span data-stu-id="82523-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="82523-329">[Назад](adding-controller.md)
> [Вперед](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="82523-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
