---
title: Приступая к работе с API защиты данных в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать ASP.NET Core API-интерфейсы защиты данных для защиты и снятия защиты данных в приложении.
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051659"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Приступая к работе с API защиты данных в ASP.NET Core

<a name="security-data-protection-getting-started"></a>

В самом простейшем случае защита данных состоит из следующих этапов.

1. Создайте средство защиты данных от поставщика защиты данных.

2. Вызовите `Protect` метод с данными, которые необходимо защитить.

3. Вызовите `Unprotect` метод с данными, которые необходимо вернуть в обычный текст.

Большинство платформ и моделей приложений, таких как ASP.NET Core или SignalR , уже настраивают систему защиты данных и добавляют их в контейнер службы, доступ к которому осуществляется посредством внедрения зависимостей. В следующем примере демонстрируется настройка контейнера службы для внедрения зависимостей и регистрация стека защиты данных, получение поставщика защиты данных через DI, создание предохранителя и защита, а также снятие защиты данных.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

При создании предохранителя необходимо указать одну или несколько [строк назначения](xref:security/data-protection/consumer-apis/purpose-strings). Строка назначения обеспечивает изоляцию между потребителями. Например, средство защиты, созданное с помощью строки назначения "Green", не сможет снять защиту данных, предоставляемых предохранителем, с целью "фиолетовый".

>[!TIP]
> Экземпляры `IDataProtectionProvider` и `IDataProtector` являются потокобезопасными для нескольких вызывающих объектов. Предполагается, что после того, как компонент получает ссылку на объект `IDataProtector` через вызов `CreateProtector` , он будет использовать эту ссылку для нескольких вызовов функций `Protect` и `Unprotect` .
>
>Вызов метода вызовет `Unprotect` исключение CryptographicException, если защищенные полезные данные не могут быть проверены или расшифрованы. Некоторым компонентам может потребоваться пропускать ошибки во время операций снятия защиты. компонент, считывающий проверку подлинности cookie , может обработать эту ошибку и обрабатывать запрос так, как если бы он имел вообще не cookie запрашивать ошибку. Компоненты, которые должны использовать это поведение, должны специально перехватывать CryptographicException, а не проглатывание все исключения.
