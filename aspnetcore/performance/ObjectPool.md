---
title: Повторное использование объектов с Обжектпул в ASP.NET Core
author: rick-anderson
description: Советы по повышению производительности ASP.NET Core приложений с помощью Обжектпул.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/ObjectPool
ms.openlocfilehash: 3bbfde6f68a238131149e67ce72f0ddc68a9ea0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056911"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="a5aee-103">Повторное использование объектов с Обжектпул в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5aee-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="a5aee-104">По [Стив Гордон](https://twitter.com/stevejgordon), [Райан Nowak)](https://github.com/rynowak)и [гüнсер фоидл](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="a5aee-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="a5aee-105"><xref:Microsoft.Extensions.ObjectPool> является частью инфраструктуры ASP.NET Core, поддерживающей хранение группы объектов в памяти для повторного использования, а не разрешение сбора мусора для объектов.</span><span class="sxs-lookup"><span data-stu-id="a5aee-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="a5aee-106">Пул объектов может потребоваться, если управляемые объекты являются:</span><span class="sxs-lookup"><span data-stu-id="a5aee-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="a5aee-107">Затратно на выделение и инициализацию.</span><span class="sxs-lookup"><span data-stu-id="a5aee-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="a5aee-108">Представляет некоторый ограниченный ресурс.</span><span class="sxs-lookup"><span data-stu-id="a5aee-108">Represent some limited resource.</span></span>
- <span data-ttu-id="a5aee-109">Используется в качестве прогнозируемых и часто используемых.</span><span class="sxs-lookup"><span data-stu-id="a5aee-109">Used predictably and frequently.</span></span>

<span data-ttu-id="a5aee-110">Например, ASP.NET Core Framework использует пул объектов в некоторых местах для повторного использования <xref:System.Text.StringBuilder> экземпляров.</span><span class="sxs-lookup"><span data-stu-id="a5aee-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="a5aee-111">`StringBuilder` выделяет собственные буферы для хранения символьных данных и управляет ими.</span><span class="sxs-lookup"><span data-stu-id="a5aee-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="a5aee-112">ASP.NET Core регулярное использование `StringBuilder` для реализации функций и их повторное использование обеспечивает выигрыш в производительности.</span><span class="sxs-lookup"><span data-stu-id="a5aee-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="a5aee-113">Пул объектов не всегда повышает производительность:</span><span class="sxs-lookup"><span data-stu-id="a5aee-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="a5aee-114">Если затраты на инициализацию объекта не высоки, обычно бывает медленнее получить объект из пула.</span><span class="sxs-lookup"><span data-stu-id="a5aee-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="a5aee-115">Объекты, управляемые пулом, не выделяются, пока пул не будет освобожден.</span><span class="sxs-lookup"><span data-stu-id="a5aee-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="a5aee-116">Используйте пул объектов только после сбора данных о производительности с помощью реалистичных сценариев для приложения или библиотеки.</span><span class="sxs-lookup"><span data-stu-id="a5aee-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="a5aee-117">**Предупреждение: `ObjectPool` не реализует `IDisposable` . Мы не рекомендуем использовать его с типами, которые требуют реализации.**</span><span class="sxs-lookup"><span data-stu-id="a5aee-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="a5aee-118">`ObjectPool` в ASP.NET Core 3,0 и более поздних версий поддерживается `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="a5aee-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="a5aee-119">**Примечание. Обжектпул не устанавливает ограничение на количество объектов, которые он будет выделять, он ограничивает количество объектов, которое он будет хранить.**</span><span class="sxs-lookup"><span data-stu-id="a5aee-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="a5aee-120">Основные понятия</span><span class="sxs-lookup"><span data-stu-id="a5aee-120">Concepts</span></span>

<span data-ttu-id="a5aee-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> — абстракция базового пула объектов.</span><span class="sxs-lookup"><span data-stu-id="a5aee-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="a5aee-122">Используется для получения и возврата объектов.</span><span class="sxs-lookup"><span data-stu-id="a5aee-122">Used to get and return objects.</span></span>

<span data-ttu-id="a5aee-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> — Реализуйте его, чтобы настроить способ создания объекта и его *сброса* при возврате в пул.</span><span class="sxs-lookup"><span data-stu-id="a5aee-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="a5aee-124">Это можно передать в пул объектов, который создается напрямую... НИ</span><span class="sxs-lookup"><span data-stu-id="a5aee-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="a5aee-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> выступает в качестве фабрики для создания пулов объектов.</span><span class="sxs-lookup"><span data-stu-id="a5aee-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="a5aee-126">Обжектпул можно использовать в приложении несколькими способами:</span><span class="sxs-lookup"><span data-stu-id="a5aee-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="a5aee-127">Создание экземпляра пула.</span><span class="sxs-lookup"><span data-stu-id="a5aee-127">Instantiating a pool.</span></span>
* <span data-ttu-id="a5aee-128">Регистрация пула в [внедрении зависимостей](xref:fundamentals/dependency-injection) (DI) в качестве экземпляра.</span><span class="sxs-lookup"><span data-stu-id="a5aee-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="a5aee-129">Регистрация `ObjectPoolProvider<>` в di и использование его в качестве фабрики.</span><span class="sxs-lookup"><span data-stu-id="a5aee-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="a5aee-130">Как использовать Обжектпул</span><span class="sxs-lookup"><span data-stu-id="a5aee-130">How to use ObjectPool</span></span>

<span data-ttu-id="a5aee-131">Вызовите метод <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> , чтобы получить объект и <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> вернуть объект.</span><span class="sxs-lookup"><span data-stu-id="a5aee-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="a5aee-132">Нет необходимости возвращать каждый объект.</span><span class="sxs-lookup"><span data-stu-id="a5aee-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="a5aee-133">Если не вернуть объект, он будет удален сборщиком мусора.</span><span class="sxs-lookup"><span data-stu-id="a5aee-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="a5aee-134">При <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> использовании и `T` реализует `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="a5aee-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="a5aee-135">Элементы, которые \* **не** _ были возвращены в пул, будут удалены.</span><span class="sxs-lookup"><span data-stu-id="a5aee-135">Items that are \* **not** _ returned to the pool will be disposed.</span></span>
<span data-ttu-id="a5aee-136">_ При удалении пула с помощью DI все элементы в пуле уничтожаются.</span><span class="sxs-lookup"><span data-stu-id="a5aee-136">_ When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="a5aee-137">Примечание. После удаления пула:</span><span class="sxs-lookup"><span data-stu-id="a5aee-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="a5aee-138">Вызов `Get` создает исключение `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="a5aee-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="a5aee-139">`return` Удаляет заданный элемент.</span><span class="sxs-lookup"><span data-stu-id="a5aee-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="a5aee-140">Пример Обжектпул</span><span class="sxs-lookup"><span data-stu-id="a5aee-140">ObjectPool sample</span></span>

<span data-ttu-id="a5aee-141">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="a5aee-141">The following code:</span></span>

* <span data-ttu-id="a5aee-142">Добавляет `ObjectPoolProvider` в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="a5aee-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="a5aee-143">Добавляет и настраивает `ObjectPool<StringBuilder>` контейнер di.</span><span class="sxs-lookup"><span data-stu-id="a5aee-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="a5aee-144">Добавляет `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="a5aee-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="a5aee-145">Следующий код реализует `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="a5aee-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
