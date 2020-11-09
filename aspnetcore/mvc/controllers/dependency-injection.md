---
title: Внедрение зависимостей в контроллеры в ASP.NET Core
author: ardalis
description: Узнайте, как контроллеры MVC ASP.NET Core явным образом запрашивают зависимости с помощью конструкторов с внедрением зависимостей в ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 1282cd984584be423fba755e64e5d2f1afd2af89
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060616"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="c3102-103">Внедрение зависимостей в контроллеры в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3102-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3102-104">Авторы: [Shadi Namrouti](https://github.com/shadinamrouti) (Шади Намрути), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://github.com/ardalis) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="c3102-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="c3102-105">Контроллеры ASP.NET Core MVC запрашивают зависимости явно с помощью конструкторов.</span><span class="sxs-lookup"><span data-stu-id="c3102-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="c3102-106">ASP.NET Core имеет встроенную поддержку [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c3102-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c3102-107">Внедрение зависимостей упрощает тестирование и поддержку приложений.</span><span class="sxs-lookup"><span data-stu-id="c3102-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="c3102-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3102-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c3102-109">Внедрение через конструктор</span><span class="sxs-lookup"><span data-stu-id="c3102-109">Constructor Injection</span></span>

<span data-ttu-id="c3102-110">Службы добавляются в качестве параметра конструктора, и среда выполнения разрешает службу из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="c3102-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="c3102-111">Службы обычно задаются с помощью интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="c3102-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="c3102-112">Например, рассмотрим приложение, которому требуется текущее время.</span><span class="sxs-lookup"><span data-stu-id="c3102-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="c3102-113">Следующие интерфейсы предоставляют службу `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c3102-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="c3102-114">В следующем коде реализуется интерфейс `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c3102-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="c3102-115">Добавьте службу в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="c3102-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="c3102-116">Дополнительные сведения о <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, см. в разделе [Время существования службы внедрения зависимости](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="c3102-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="c3102-117">Следующий код отображает приветствие пользователю в зависимости от времени дня:</span><span class="sxs-lookup"><span data-stu-id="c3102-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="c3102-118">Запустите приложение, и в зависимости от времени отобразится сообщение.</span><span class="sxs-lookup"><span data-stu-id="c3102-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c3102-119">Внедрение действий с помощью FromServices</span><span class="sxs-lookup"><span data-stu-id="c3102-119">Action injection with FromServices</span></span>

<span data-ttu-id="c3102-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> позволяет внедрять службу непосредственно в метод действия без использования внедрения через конструктор:</span><span class="sxs-lookup"><span data-stu-id="c3102-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="c3102-121">Доступ к параметрам из контроллера</span><span class="sxs-lookup"><span data-stu-id="c3102-121">Access settings from a controller</span></span>

<span data-ttu-id="c3102-122">Доступ к параметрам приложения или конфигурации из контроллера относится к типичным шаблонам.</span><span class="sxs-lookup"><span data-stu-id="c3102-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c3102-123">*Шаблон параметров* , описанный в разделе <xref:fundamentals/configuration/options>, является предпочтительным подходом для управления параметрами.</span><span class="sxs-lookup"><span data-stu-id="c3102-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="c3102-124">Как правило, не следует напрямую внедрять <xref:Microsoft.Extensions.Configuration.IConfiguration> в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c3102-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="c3102-125">Создайте класс, представляющий параметры.</span><span class="sxs-lookup"><span data-stu-id="c3102-125">Create a class that represents the options.</span></span> <span data-ttu-id="c3102-126">Пример:</span><span class="sxs-lookup"><span data-stu-id="c3102-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="c3102-127">Добавьте класс конфигурации в коллекцию служб:</span><span class="sxs-lookup"><span data-stu-id="c3102-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="c3102-128">Настройте приложение для чтения параметров из файла формата JSON:</span><span class="sxs-lookup"><span data-stu-id="c3102-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="c3102-129">Следующий код запрашивает параметры `IOptions<SampleWebSettings>` из контейнера служб и использует их в методе `Index`:</span><span class="sxs-lookup"><span data-stu-id="c3102-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c3102-130">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c3102-130">Additional resources</span></span>

* <span data-ttu-id="c3102-131">См. раздел <xref:mvc/controllers/testing>, чтобы узнать, как упростить тестирование кода, явно запросив зависимости у контроллеров.</span><span class="sxs-lookup"><span data-stu-id="c3102-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="c3102-132">[Замените контейнер внедрения зависимостей по умолчанию на стороннюю реализацию](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="c3102-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3102-133">Авторы: [Shadi Namrouti](https://github.com/shadinamrouti) (Шади Намрути), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://github.com/ardalis) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="c3102-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="c3102-134">Контроллеры ASP.NET Core MVC запрашивают зависимости явно с помощью конструкторов.</span><span class="sxs-lookup"><span data-stu-id="c3102-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="c3102-135">ASP.NET Core имеет встроенную поддержку [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c3102-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c3102-136">Внедрение зависимостей упрощает тестирование и поддержку приложений.</span><span class="sxs-lookup"><span data-stu-id="c3102-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="c3102-137">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3102-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="c3102-138">Внедрение через конструктор</span><span class="sxs-lookup"><span data-stu-id="c3102-138">Constructor Injection</span></span>

<span data-ttu-id="c3102-139">Службы добавляются в качестве параметра конструктора, и среда выполнения разрешает службу из контейнера служб.</span><span class="sxs-lookup"><span data-stu-id="c3102-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="c3102-140">Службы обычно задаются с помощью интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="c3102-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="c3102-141">Например, рассмотрим приложение, которому требуется текущее время.</span><span class="sxs-lookup"><span data-stu-id="c3102-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="c3102-142">Следующие интерфейсы предоставляют службу `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c3102-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="c3102-143">В следующем коде реализуется интерфейс `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="c3102-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="c3102-144">Добавьте службу в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="c3102-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="c3102-145">Дополнительные сведения о <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, см. в разделе [Время существования службы внедрения зависимости](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="c3102-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="c3102-146">Следующий код отображает приветствие пользователю в зависимости от времени дня:</span><span class="sxs-lookup"><span data-stu-id="c3102-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="c3102-147">Запустите приложение, и в зависимости от времени отобразится сообщение.</span><span class="sxs-lookup"><span data-stu-id="c3102-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="c3102-148">Внедрение действий с помощью FromServices</span><span class="sxs-lookup"><span data-stu-id="c3102-148">Action injection with FromServices</span></span>

<span data-ttu-id="c3102-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> позволяет внедрять службу непосредственно в метод действия без использования внедрения через конструктор:</span><span class="sxs-lookup"><span data-stu-id="c3102-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="c3102-150">Доступ к параметрам из контроллера</span><span class="sxs-lookup"><span data-stu-id="c3102-150">Access settings from a controller</span></span>

<span data-ttu-id="c3102-151">Доступ к параметрам приложения или конфигурации из контроллера относится к типичным шаблонам.</span><span class="sxs-lookup"><span data-stu-id="c3102-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="c3102-152">*Шаблон параметров* , описанный в разделе <xref:fundamentals/configuration/options>, является предпочтительным подходом для управления параметрами.</span><span class="sxs-lookup"><span data-stu-id="c3102-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="c3102-153">Как правило, не следует напрямую внедрять <xref:Microsoft.Extensions.Configuration.IConfiguration> в контроллер.</span><span class="sxs-lookup"><span data-stu-id="c3102-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="c3102-154">Создайте класс, представляющий параметры.</span><span class="sxs-lookup"><span data-stu-id="c3102-154">Create a class that represents the options.</span></span> <span data-ttu-id="c3102-155">Пример:</span><span class="sxs-lookup"><span data-stu-id="c3102-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="c3102-156">Добавьте класс конфигурации в коллекцию служб:</span><span class="sxs-lookup"><span data-stu-id="c3102-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="c3102-157">Настройте приложение для чтения параметров из файла формата JSON:</span><span class="sxs-lookup"><span data-stu-id="c3102-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="c3102-158">Следующий код запрашивает параметры `IOptions<SampleWebSettings>` из контейнера служб и использует их в методе `Index`:</span><span class="sxs-lookup"><span data-stu-id="c3102-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c3102-159">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c3102-159">Additional resources</span></span>

* <span data-ttu-id="c3102-160">См. раздел <xref:mvc/controllers/testing>, чтобы узнать, как упростить тестирование кода, явно запросив зависимости у контроллеров.</span><span class="sxs-lookup"><span data-stu-id="c3102-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="c3102-161">[Замените контейнер внедрения зависимостей по умолчанию на стороннюю реализацию](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="c3102-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end