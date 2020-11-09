---
title: 'Публикация ASP.NET Core SignalR приложения в службе приложений Azure'
author: bradygaster
description: 'Узнайте, как опубликовать приложение ASP.NET Core SignalR в службе приложений Azure.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234518"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="834ab-103">Публикация ASP.NET Core SignalR приложения в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="834ab-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="834ab-104">По [Брейди Гастер](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="834ab-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="834ab-105">[Служба приложений Azure](/azure/app-service/app-service-web-overview) — это служба платформы [облачных вычислений Майкрософт](https://azure.microsoft.com/) для размещения веб-приложений, включая ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="834ab-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="834ab-106">В этой статье описывается публикация ASP.NET Core SignalR приложения из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="834ab-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="834ab-107">Дополнительные сведения см. в статье [ SignalR служба для Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="834ab-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="834ab-108">Публикация приложения</span><span class="sxs-lookup"><span data-stu-id="834ab-108">Publish the app</span></span>

<span data-ttu-id="834ab-109">В этой статье описывается публикация с помощью средств в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="834ab-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="834ab-110">Visual Studio Code пользователи могут использовать команды [Azure CLI](/cli/azure) для публикации приложений в Azure.</span><span class="sxs-lookup"><span data-stu-id="834ab-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="834ab-111">Дополнительные сведения см. в статье [публикация ASP.NET Core приложения в Azure с помощью средств командной строки](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="834ab-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="834ab-112">В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать** .</span><span class="sxs-lookup"><span data-stu-id="834ab-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="834ab-113">Убедитесь, что в диалоговом окне **Выбор целевого объекта публикации** выбран параметр **служба приложений** и **создать новые** .</span><span class="sxs-lookup"><span data-stu-id="834ab-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="834ab-114">Выберите **создать профиль** из раскрывающегося списка кнопка " **опубликовать** ".</span><span class="sxs-lookup"><span data-stu-id="834ab-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="834ab-115">Введите сведения, описанные в следующей таблице в диалоговом окне **Создание службы приложений** , и выберите **создать** .</span><span class="sxs-lookup"><span data-stu-id="834ab-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="834ab-116">Элемент</span><span class="sxs-lookup"><span data-stu-id="834ab-116">Item</span></span>               | <span data-ttu-id="834ab-117">Описание</span><span class="sxs-lookup"><span data-stu-id="834ab-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="834ab-118">**имя** ;</span><span class="sxs-lookup"><span data-stu-id="834ab-118">**Name**</span></span>           | <span data-ttu-id="834ab-119">Уникальное имя приложения.</span><span class="sxs-lookup"><span data-stu-id="834ab-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="834ab-120">**Подписка**</span><span class="sxs-lookup"><span data-stu-id="834ab-120">**Subscription**</span></span>   | <span data-ttu-id="834ab-121">Подписка Azure, используемая приложением.</span><span class="sxs-lookup"><span data-stu-id="834ab-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="834ab-122">**Группа ресурсов**</span><span class="sxs-lookup"><span data-stu-id="834ab-122">**Resource Group**</span></span> | <span data-ttu-id="834ab-123">Группа связанных ресурсов, к которым принадлежит приложение.</span><span class="sxs-lookup"><span data-stu-id="834ab-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="834ab-124">**План размещения**</span><span class="sxs-lookup"><span data-stu-id="834ab-124">**Hosting Plan**</span></span>   | <span data-ttu-id="834ab-125">Тарифный план для веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="834ab-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="834ab-126">В разделе **Service Dependencies (зависимости служб** ) выберите **SignalR служба Azure** .</span><span class="sxs-lookup"><span data-stu-id="834ab-126">Select **Azure SignalR Service** in the **Service Dependencies** section.</span></span> <span data-ttu-id="834ab-127">Нажмите **+** кнопку:</span><span class="sxs-lookup"><span data-stu-id="834ab-127">Select the **+** button:</span></span>

   ![Область зависимостей, отображающая выбор Azure::: No-Loc (SignalR)::: Service в раскрывающемся списке "Добавить"](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="834ab-129">В диалоговом окне **SignalR службы Azure** выберите **создать новый SignalR экземпляр службы Azure** .</span><span class="sxs-lookup"><span data-stu-id="834ab-129">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance** .</span></span>

1. <span data-ttu-id="834ab-130">Укажите **имя** , **группу ресурсов** и **Расположение** .</span><span class="sxs-lookup"><span data-stu-id="834ab-130">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="834ab-131">Вернитесь в диалоговое окно **SignalR службы Azure** и нажмите кнопку **добавить** .</span><span class="sxs-lookup"><span data-stu-id="834ab-131">Return to the **Azure SignalR Service** dialog and select **Add** .</span></span>

<span data-ttu-id="834ab-132">Visual Studio выполняет следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="834ab-132">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="834ab-133">Создает профиль публикации, содержащий параметры публикации.</span><span class="sxs-lookup"><span data-stu-id="834ab-133">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="834ab-134">Создает *веб-приложение Azure* с предоставленными сведениями.</span><span class="sxs-lookup"><span data-stu-id="834ab-134">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="834ab-135">Публикует приложение.</span><span class="sxs-lookup"><span data-stu-id="834ab-135">Publishes the app.</span></span>
* <span data-ttu-id="834ab-136">Запускает браузер, который загружает веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="834ab-136">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="834ab-137">URL-адрес приложения имеет формат `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="834ab-137">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="834ab-138">Например, приложение с именем `SignalRChatApp` имеет URL-адрес `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="834ab-138">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="834ab-139">Если при развертывании приложения, предназначенного для предварительной версии .NET Core, возникает ошибка HTTP *502,2-Bad Gateway* , см. статью [развертывание ASP.NET Core предварительной версии в службе приложений Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) .</span><span class="sxs-lookup"><span data-stu-id="834ab-139">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="834ab-140">Настройка приложения в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="834ab-140">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="834ab-141">*Этот раздел относится только к приложениям, которые не используют SignalR службу Azure.*</span><span class="sxs-lookup"><span data-stu-id="834ab-141">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="834ab-142">Если приложение использует SignalR службу Azure, служба приложений не требует настройки сходства маршрутизации запросов приложений (ARR) и веб-сокетов, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="834ab-142">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="834ab-143">Клиенты соединяют свои веб-сокеты со SignalR службой Azure, а не напрямую с приложением.</span><span class="sxs-lookup"><span data-stu-id="834ab-143">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="834ab-144">Для приложений, размещенных без SignalR службы Azure, включите:</span><span class="sxs-lookup"><span data-stu-id="834ab-144">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="834ab-145">[Соответствие ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(ARR- cookie ) -for-Azure-web-apps.html) для маршрутизации запросов от пользователя к тому же экземпляру службы приложений.</span><span class="sxs-lookup"><span data-stu-id="834ab-145">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="834ab-146">Значение по умолчанию — **On** .</span><span class="sxs-lookup"><span data-stu-id="834ab-146">The default setting is **On** .</span></span>
* <span data-ttu-id="834ab-147">[Веб-сокеты](xref:fundamentals/websockets) для обеспечения функционирования транспорта веб-сокетов.</span><span class="sxs-lookup"><span data-stu-id="834ab-147">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="834ab-148">Значение по умолчанию — **Off** .</span><span class="sxs-lookup"><span data-stu-id="834ab-148">The default setting is **Off** .</span></span>

1. <span data-ttu-id="834ab-149">В портал Azure перейдите к веб-приложению в **службах приложений** .</span><span class="sxs-lookup"><span data-stu-id="834ab-149">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="834ab-150">Откройте **Configuration**  >  **Общие параметры** конфигурации.</span><span class="sxs-lookup"><span data-stu-id="834ab-150">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="834ab-151">Установите для **веб-сокетов** значение **вкл** .</span><span class="sxs-lookup"><span data-stu-id="834ab-151">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="834ab-152">Убедитесь, что для параметра **сходство arr** установлено значение **вкл** .</span><span class="sxs-lookup"><span data-stu-id="834ab-152">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="834ab-153">Ограничения плана службы приложений</span><span class="sxs-lookup"><span data-stu-id="834ab-153">App Service Plan limits</span></span>

<span data-ttu-id="834ab-154">Веб-сокеты и другие транспорты ограничены в зависимости от выбранного плана службы приложений.</span><span class="sxs-lookup"><span data-stu-id="834ab-154">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="834ab-155">Дополнительные сведения см. в разделе *ограничения облачных служб Azure* и *ограничения службы приложений* статьи [Подписка Azure и ограничения службы, квоты и ограничения](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="834ab-155">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="834ab-156">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="834ab-156">Additional resources</span></span>

* [<span data-ttu-id="834ab-157">Что такое служба SignalR Azure?</span><span class="sxs-lookup"><span data-stu-id="834ab-157">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="834ab-158">Публикация приложения ASP.NET Core в Azure с использованием средств командной строки</span><span class="sxs-lookup"><span data-stu-id="834ab-158">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="834ab-159">Размещение и развертывание приложений ASP.NET Core Preview в Azure</span><span class="sxs-lookup"><span data-stu-id="834ab-159">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
