---
title: Повторное использование объектов с Обжектпул в ASP.NET Core
author: rick-anderson
description: Советы по повышению производительности ASP.NET Core приложений с помощью Обжектпул.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/ObjectPool
ms.openlocfilehash: 3bbfde6f68a238131149e67ce72f0ddc68a9ea0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056911"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Повторное использование объектов с Обжектпул в ASP.NET Core

По [Стив Гордон](https://twitter.com/stevejgordon), [Райан Nowak)](https://github.com/rynowak)и [гüнсер фоидл](https://github.com/gfoidl)

<xref:Microsoft.Extensions.ObjectPool> является частью инфраструктуры ASP.NET Core, поддерживающей хранение группы объектов в памяти для повторного использования, а не разрешение сбора мусора для объектов.

Пул объектов может потребоваться, если управляемые объекты являются:

- Затратно на выделение и инициализацию.
- Представляет некоторый ограниченный ресурс.
- Используется в качестве прогнозируемых и часто используемых.

Например, ASP.NET Core Framework использует пул объектов в некоторых местах для повторного использования <xref:System.Text.StringBuilder> экземпляров. `StringBuilder` выделяет собственные буферы для хранения символьных данных и управляет ими. ASP.NET Core регулярное использование `StringBuilder` для реализации функций и их повторное использование обеспечивает выигрыш в производительности.

Пул объектов не всегда повышает производительность:

- Если затраты на инициализацию объекта не высоки, обычно бывает медленнее получить объект из пула.
- Объекты, управляемые пулом, не выделяются, пока пул не будет освобожден.

Используйте пул объектов только после сбора данных о производительности с помощью реалистичных сценариев для приложения или библиотеки.

::: moniker range="< aspnetcore-3.0"
**Предупреждение: `ObjectPool` не реализует `IDisposable` . Мы не рекомендуем использовать его с типами, которые требуют реализации.** `ObjectPool` в ASP.NET Core 3,0 и более поздних версий поддерживается `IDisposable` .
::: moniker-end

**Примечание. Обжектпул не устанавливает ограничение на количество объектов, которые он будет выделять, он ограничивает количество объектов, которое он будет хранить.**

## <a name="concepts"></a>Основные понятия

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> — абстракция базового пула объектов. Используется для получения и возврата объектов.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> — Реализуйте его, чтобы настроить способ создания объекта и его *сброса* при возврате в пул. Это можно передать в пул объектов, который создается напрямую... НИ

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> выступает в качестве фабрики для создания пулов объектов.
<!-- REview, there is no ObjectPoolProvider<T> -->

Обжектпул можно использовать в приложении несколькими способами:

* Создание экземпляра пула.
* Регистрация пула в [внедрении зависимостей](xref:fundamentals/dependency-injection) (DI) в качестве экземпляра.
* Регистрация `ObjectPoolProvider<>` в di и использование его в качестве фабрики.

## <a name="how-to-use-objectpool"></a>Как использовать Обжектпул

Вызовите метод <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> , чтобы получить объект и <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> вернуть объект.  Нет необходимости возвращать каждый объект. Если не вернуть объект, он будет удален сборщиком мусора.

::: moniker range=">= aspnetcore-3.0"
При <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> использовании и `T` реализует `IDisposable` :

* Элементы, которые * **не** _ были возвращены в пул, будут удалены.
_ При удалении пула с помощью DI все элементы в пуле уничтожаются.

Примечание. После удаления пула:

* Вызов `Get` создает исключение `ObjectDisposedException` .
* `return` Удаляет заданный элемент.

::: moniker-end

## <a name="objectpool-sample"></a>Пример Обжектпул

В приведенном ниже коде

* Добавляет `ObjectPoolProvider` в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI).
* Добавляет и настраивает `ObjectPool<StringBuilder>` контейнер di.
* Добавляет `BirthdayMiddleware` .

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Следующий код реализует `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
