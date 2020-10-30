---
title: Приступая к работе с API защиты данных в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать ASP.NET Core API-интерфейсы защиты данных для защиты и снятия защиты данных в приложении.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051659"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="b3238-103">Приступая к работе с API защиты данных в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3238-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="b3238-104">В самом простейшем случае защита данных состоит из следующих этапов.</span><span class="sxs-lookup"><span data-stu-id="b3238-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="b3238-105">Создайте средство защиты данных от поставщика защиты данных.</span><span class="sxs-lookup"><span data-stu-id="b3238-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="b3238-106">Вызовите `Protect` метод с данными, которые необходимо защитить.</span><span class="sxs-lookup"><span data-stu-id="b3238-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="b3238-107">Вызовите `Unprotect` метод с данными, которые необходимо вернуть в обычный текст.</span><span class="sxs-lookup"><span data-stu-id="b3238-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="b3238-108">Большинство платформ и моделей приложений, таких как ASP.NET Core или :::no-loc(SignalR)::: , уже настраивают систему защиты данных и добавляют их в контейнер службы, доступ к которому осуществляется посредством внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b3238-108">Most frameworks and app models, such as ASP.NET Core or :::no-loc(SignalR):::, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="b3238-109">В следующем примере демонстрируется настройка контейнера службы для внедрения зависимостей и регистрация стека защиты данных, получение поставщика защиты данных через DI, создание предохранителя и защита, а также снятие защиты данных.</span><span class="sxs-lookup"><span data-stu-id="b3238-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="b3238-110">При создании предохранителя необходимо указать одну или несколько [строк назначения](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="b3238-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="b3238-111">Строка назначения обеспечивает изоляцию между потребителями.</span><span class="sxs-lookup"><span data-stu-id="b3238-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="b3238-112">Например, средство защиты, созданное с помощью строки назначения "Green", не сможет снять защиту данных, предоставляемых предохранителем, с целью "фиолетовый".</span><span class="sxs-lookup"><span data-stu-id="b3238-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="b3238-113">Экземпляры `IDataProtectionProvider` и `IDataProtector` являются потокобезопасными для нескольких вызывающих объектов.</span><span class="sxs-lookup"><span data-stu-id="b3238-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="b3238-114">Предполагается, что после того, как компонент получает ссылку на объект `IDataProtector` через вызов `CreateProtector` , он будет использовать эту ссылку для нескольких вызовов функций `Protect` и `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="b3238-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="b3238-115">Вызов метода вызовет `Unprotect` исключение CryptographicException, если защищенные полезные данные не могут быть проверены или расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="b3238-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="b3238-116">Некоторым компонентам может потребоваться пропускать ошибки во время операций снятия защиты. компонент, считывающий проверку подлинности :::no-loc(cookie)::: , может обработать эту ошибку и обрабатывать запрос так, как если бы он имел вообще не :::no-loc(cookie)::: запрашивать ошибку.</span><span class="sxs-lookup"><span data-stu-id="b3238-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication :::no-loc(cookie):::s might handle this error and treat the request as if it had no :::no-loc(cookie)::: at all rather than fail the request outright.</span></span> <span data-ttu-id="b3238-117">Компоненты, которые должны использовать это поведение, должны специально перехватывать CryptographicException, а не проглатывание все исключения.</span><span class="sxs-lookup"><span data-stu-id="b3238-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
