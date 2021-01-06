---
title: Макеты Blazor в ASP.NET Core
author: guardrex
description: Узнайте, как создавать многократно используемые компоненты макета для Blazor приложений.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: 3cb7c6184c13a003b4f4294f887d8938caa42f97
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506907"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="d71a2-103">Макеты Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d71a2-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="d71a2-104">Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="d71a2-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d71a2-105">Некоторые элементы приложения, такие как меню, сообщения об авторских правах и логотипы компании, обычно являются частью общего макета приложения и используются каждым компонентом в приложении.</span><span class="sxs-lookup"><span data-stu-id="d71a2-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="d71a2-106">Копирование кода этих элементов во все компоненты приложения не является эффективным подходом.</span><span class="sxs-lookup"><span data-stu-id="d71a2-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="d71a2-107">Каждый раз, когда одному из элементов требуется обновление, нужно обновлять каждый компонент.</span><span class="sxs-lookup"><span data-stu-id="d71a2-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="d71a2-108">Такое дублирование сложно поддерживать, и это может привести к несогласованному содержимому с течением времени.</span><span class="sxs-lookup"><span data-stu-id="d71a2-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="d71a2-109">Для решения этой проблемы используются *макеты*.</span><span class="sxs-lookup"><span data-stu-id="d71a2-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="d71a2-110">Технически макет представляет собой просто другой компонент.</span><span class="sxs-lookup"><span data-stu-id="d71a2-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="d71a2-111">Макет определяется в шаблоне Razor или в коде C# и может использовать [привязки данных](xref:blazor/components/data-binding), [внедрения зависимостей](xref:blazor/fundamentals/dependency-injection) и другие сценарии компонентов.</span><span class="sxs-lookup"><span data-stu-id="d71a2-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="d71a2-112">Чтобы преобразовать компонент в макет, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="d71a2-112">To convert a component into a layout:</span></span>

* <span data-ttu-id="d71a2-113">Создайте компонент, производный от <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="d71a2-113">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="d71a2-114"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase> определяет свойство <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> для отображаемого содержимого внутри макета.</span><span class="sxs-lookup"><span data-stu-id="d71a2-114">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="d71a2-115">Использует синтаксис `@Body` Razor для указания расположения в разметке макета, в которой отображается содержимое.</span><span class="sxs-lookup"><span data-stu-id="d71a2-115">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="d71a2-116">В следующем примере кода показан шаблон Razor компонента макета `MainLayout.razor`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-116">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="d71a2-117">Макет наследует <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> и задает `@Body` между панелью навигации и нижним колонтитулом:</span><span class="sxs-lookup"><span data-stu-id="d71a2-117">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="d71a2-118">`MainLayout`</span><span class="sxs-lookup"><span data-stu-id="d71a2-118">`MainLayout` component</span></span>

<span data-ttu-id="d71a2-119">В приложении на основе одного из шаблонов проектов Blazor компонент `MainLayout` (`MainLayout.razor`) находится в папке `Shared` приложения:</span><span class="sxs-lookup"><span data-stu-id="d71a2-119">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="d71a2-120">Макет по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d71a2-120">Default layout</span></span>

<span data-ttu-id="d71a2-121">Укажите макет приложения по умолчанию в компоненте <xref:Microsoft.AspNetCore.Components.Routing.Router> в файле `App.razor` приложения.</span><span class="sxs-lookup"><span data-stu-id="d71a2-121">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="d71a2-122">Следующий компонент <xref:Microsoft.AspNetCore.Components.Routing.Router>, предоставляемый шаблонами Blazor по умолчанию, задает для макета по умолчанию компонент `MainLayout`:</span><span class="sxs-lookup"><span data-stu-id="d71a2-122">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="d71a2-123">Чтобы предоставить макет по умолчанию для содержимого <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, укажите <xref:Microsoft.AspNetCore.Components.LayoutView> для содержимого <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>:</span><span class="sxs-lookup"><span data-stu-id="d71a2-123">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="d71a2-124">Дополнительные сведения о компоненте <xref:Microsoft.AspNetCore.Components.Routing.Router> см. в разделе <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d71a2-124">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="d71a2-125">Указание макета в качестве макета по умолчанию в маршрутизаторе достаточно полезно, так как в этом случае его можно переопределить отдельно для каждого компонента или папки.</span><span class="sxs-lookup"><span data-stu-id="d71a2-125">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="d71a2-126">Для настройки макета приложения по умолчанию предпочтительнее использовать маршрутизатор, поскольку это наиболее общий способ.</span><span class="sxs-lookup"><span data-stu-id="d71a2-126">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="d71a2-127">Указание макета в компоненте</span><span class="sxs-lookup"><span data-stu-id="d71a2-127">Specify a layout in a component</span></span>

<span data-ttu-id="d71a2-128">Используйте директиву Razor `@layout`, чтобы применить макет к компоненту.</span><span class="sxs-lookup"><span data-stu-id="d71a2-128">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="d71a2-129">Компилятор преобразует `@layout` в <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, который применяется к классу компонента.</span><span class="sxs-lookup"><span data-stu-id="d71a2-129">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="d71a2-130">Содержимое следующего компонента `MasterList` вставляется в `MasterLayout` в позиции `@Body`:</span><span class="sxs-lookup"><span data-stu-id="d71a2-130">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="d71a2-131">Указание макета непосредственно в компоненте переопределяет *макет по умолчанию*, заданный в маршрутизаторе, или директиву `@layout`, импортированную из файла `_Imports.razor`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-131">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="d71a2-132">Централизованный выбор макетов</span><span class="sxs-lookup"><span data-stu-id="d71a2-132">Centralized layout selection</span></span>

<span data-ttu-id="d71a2-133">В каждой папке приложения может дополнительно содержаться файл шаблона с именем `_Imports.razor`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-133">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="d71a2-134">Компилятор включает директивы, указанные в файле импорта, во все шаблоны Razor в одной и той же папке и рекурсивно во всех ее вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="d71a2-134">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="d71a2-135">Таким образом, файл `_Imports.razor`, содержащий `@layout MyCoolLayout`, гарантирует, что все компоненты в папке используют `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-135">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="d71a2-136">Нет необходимости многократно добавлять `@layout MyCoolLayout` во все файлы `.razor` в папке и вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="d71a2-136">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="d71a2-137">Директивы `@using` применяются к компонентам таким же образом.</span><span class="sxs-lookup"><span data-stu-id="d71a2-137">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="d71a2-138">Следующий файл `_Imports.razor` импортирует следующее:</span><span class="sxs-lookup"><span data-stu-id="d71a2-138">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="d71a2-139">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-139">`MyCoolLayout`.</span></span>
* <span data-ttu-id="d71a2-140">Все компоненты Razor в одной и той же папке и во всех вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="d71a2-140">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="d71a2-141">Пространство имен `BlazorApp1.Data` .</span><span class="sxs-lookup"><span data-stu-id="d71a2-141">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="d71a2-142">Файл `_Imports.razor` аналогичен файлу [_ViewImports.cshtml для представлений и страниц Razor](xref:mvc/views/layout#importing-shared-directives), однако он применяется специально к файлам компонентов Razor.</span><span class="sxs-lookup"><span data-stu-id="d71a2-142">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="d71a2-143">Указание макета в файле `_Imports.razor` переопределяет макет, указанный в качестве *макета по умолчанию* маршрутизатора.</span><span class="sxs-lookup"><span data-stu-id="d71a2-143">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="d71a2-144">**Не** добавляйте директиву Razor `@layout` к корневому файлу `_Imports.razor`, так как это приведет к формированию бесконечного цикла макетов в файле.</span><span class="sxs-lookup"><span data-stu-id="d71a2-144">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="d71a2-145">Чтобы управлять макетом приложения по умолчанию, укажите макет в компоненте `Router`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-145">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="d71a2-146">Дополнительные сведения см. в разделе [Макет по умолчанию](#default-layout).</span><span class="sxs-lookup"><span data-stu-id="d71a2-146">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="d71a2-147">Вложенные макеты</span><span class="sxs-lookup"><span data-stu-id="d71a2-147">Nested layouts</span></span>

<span data-ttu-id="d71a2-148">Приложения могут состоять из вложенных макетов.</span><span class="sxs-lookup"><span data-stu-id="d71a2-148">Apps can consist of nested layouts.</span></span> <span data-ttu-id="d71a2-149">Компонент может ссылаться на макет, который, в свою очередь, ссылается на другой макет.</span><span class="sxs-lookup"><span data-stu-id="d71a2-149">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="d71a2-150">Например, для создания структуры многоуровневого меню используются вложенные макеты.</span><span class="sxs-lookup"><span data-stu-id="d71a2-150">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="d71a2-151">В следующем примере показано использование вложенных макетов.</span><span class="sxs-lookup"><span data-stu-id="d71a2-151">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="d71a2-152">Файл `EpisodesComponent.razor` является отображаемым компонентом.</span><span class="sxs-lookup"><span data-stu-id="d71a2-152">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="d71a2-153">Компонент ссылается на `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="d71a2-153">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="d71a2-154">Файл `MasterListLayout.razor` предоставляет `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="d71a2-154">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="d71a2-155">Макет ссылается на другой макет, `MasterLayout`, где он преобразуется для просмотра.</span><span class="sxs-lookup"><span data-stu-id="d71a2-155">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="d71a2-156">`EpisodesComponent` отображается там, где находится `@Body`:</span><span class="sxs-lookup"><span data-stu-id="d71a2-156">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="d71a2-157">Наконец, `MasterLayout` в файле `MasterLayout.razor` содержит элементы макета верхнего уровня, такие как заголовок, главное меню и нижний колонтитул.</span><span class="sxs-lookup"><span data-stu-id="d71a2-157">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="d71a2-158">`MasterListLayout` с `EpisodesComponent` отображается там, где находится `@Body`:</span><span class="sxs-lookup"><span data-stu-id="d71a2-158">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="d71a2-159">Совместное использование макета Razor Pages с интегрированными компонентами</span><span class="sxs-lookup"><span data-stu-id="d71a2-159">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="d71a2-160">Если маршрутизируемые компоненты интегрированы в приложение Razor Pages, общий макет приложения можно использовать с компонентами.</span><span class="sxs-lookup"><span data-stu-id="d71a2-160">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="d71a2-161">Дополнительные сведения см. в разделе <xref:blazor/components/prerendering-and-integration>.</span><span class="sxs-lookup"><span data-stu-id="d71a2-161">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d71a2-162">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d71a2-162">Additional resources</span></span>

* <xref:mvc/views/layout>
