---
title: Неизменность ключа и ключевые параметры в ASP.NET Core
author: rick-anderson
description: Сведения о реализации интерфейсов API неизменности ключа защиты данных ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061253"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="3ebd0-103">Неизменность ключа и ключевые параметры в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ebd0-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="3ebd0-104">После сохранения объекта в резервном хранилище его представление постоянно фиксируется.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="3ebd0-105">Новые данные могут быть добавлены в резервное хранилище, но существующие данные не могут изменяться.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="3ebd0-106">Основное назначение этого поведения заключается в предотвращении повреждения данных.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="3ebd0-107">Одним из следствием такого поведения является то, что после того, как ключ записывается в резервное хранилище, он становится неизменяемым.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="3ebd0-108">Даты создания, активации и истечения срока действия не могут быть изменены, хотя могут быть отозваны с помощью `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="3ebd0-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="3ebd0-109">Кроме того, его базовые сведения о алгоритме, основном материале для ключа и шифровании неактивных данных также являются неизменяемыми.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="3ebd0-110">Если разработчик изменяет любой параметр, влияющий на сохраняемость ключей, эти изменения не вступят в силу до следующего создания ключа, либо посредством явного вызова `IKeyManager.CreateNewKey` или посредством [автоматического создания ключа](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) в системе защиты данных.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="3ebd0-111">Ниже приведены параметры, влияющие на сохраняемость ключей.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="3ebd0-112">Время жизни ключа по умолчанию</span><span class="sxs-lookup"><span data-stu-id="3ebd0-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="3ebd0-113">Механизм неактивных ключей шифрования</span><span class="sxs-lookup"><span data-stu-id="3ebd0-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="3ebd0-114">Алгоритмическая информация, содержащаяся в ключе</span><span class="sxs-lookup"><span data-stu-id="3ebd0-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="3ebd0-115">Если необходимо, чтобы эти параметры запускались раньше, чем при следующем автоматическом создании ключа, рассмотрите возможность явного вызова для `IKeyManager.CreateNewKey` принудительного создания нового ключа.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="3ebd0-116">Не забудьте предоставить явную дату активации ({Now + 2 дня} — хорошее правило, чтобы разрешить распространение изменений) и дату истечения срока действия в вызове.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="3ebd0-117">Все приложения, затрагивающие репозиторий, должны указывать те же параметры с помощью `IDataProtectionBuilder` методов расширения.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="3ebd0-118">В противном случае свойства постоянного ключа будут зависеть от конкретного приложения, которое вызвало подпрограммы создания ключей.</span><span class="sxs-lookup"><span data-stu-id="3ebd0-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
