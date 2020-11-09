---
title: Платформа удостоверений Майкрософт и Azure Active Directory с ASP.NET Core
author: rick-anderson
description: Ознакомьтесь с разделами, связанными с проверкой подлинности с помощью платформы удостоверений Майкрософт и Azure Active Directory для веб-приложений и API в ASP.NET Core.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061409"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="c166f-103">Azure Active Directory с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c166f-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="c166f-104">В этих учебниках и примерах демонстрируется проверка подлинности в ASP.NET Core с помощью платформы удостоверений Майкрософт и Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="c166f-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="c166f-105">Дополнительные учебники и примеры использования ASP.NET Core с Azure AD см. в статье [Платформа удостоверений Майкрософт](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="c166f-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="c166f-106">Сценарии приложений</span><span class="sxs-lookup"><span data-stu-id="c166f-106">Application Scenarios</span></span>

* [<span data-ttu-id="c166f-107">Краткое руководство. Добавление входа через Майкрософт в веб-приложение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c166f-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="c166f-108">Веб-приложение, которое реализует вход пользователей в систему</span><span class="sxs-lookup"><span data-stu-id="c166f-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="c166f-109">Веб-приложение, которое вызывает веб-API</span><span class="sxs-lookup"><span data-stu-id="c166f-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="c166f-110">Защищенный веб-API</span><span class="sxs-lookup"><span data-stu-id="c166f-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="c166f-111">Веб-API, вызывающий другие веб-API</span><span class="sxs-lookup"><span data-stu-id="c166f-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="c166f-112">Веб-приложение, которое реализует вход пользователей в систему с помощью Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="c166f-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="c166f-113">Примеры</span><span class="sxs-lookup"><span data-stu-id="c166f-113">Samples</span></span>

* <span data-ttu-id="c166f-114">[Разрешите приложению ASP.NET Core реализовать вход пользователей в систему и вызывать веб-API с помощью Azure AD v2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="c166f-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="c166f-115">Смотрите [ролик на эту тему](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="c166f-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="c166f-116">[Вызов веб-API ASP.NET Core 2.0 из приложения WPF с помощью Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="c166f-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="c166f-117">Смотрите [ролик на эту тему](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="c166f-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="c166f-118">ASP.NET Core с Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="c166f-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
