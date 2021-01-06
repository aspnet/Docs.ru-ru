---
title: Публикация приложения ASP.NET Core в Azure с помощью Visual Studio
author: rick-anderson
description: Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060226"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="afbc3-103">Публикация приложения ASP.NET Core в Azure с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afbc3-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="afbc3-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="afbc3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="afbc3-105">См. сведения о [публикации веб-приложения в Службе приложений Azure с помощью Visual Studio для Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019).</span><span class="sxs-lookup"><span data-stu-id="afbc3-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="afbc3-106">Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="afbc3-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="afbc3-107">Настройка</span><span class="sxs-lookup"><span data-stu-id="afbc3-107">Set up</span></span>

* <span data-ttu-id="afbc3-108">Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.</span><span class="sxs-lookup"><span data-stu-id="afbc3-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="afbc3-109">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="afbc3-109">Create a web app</span></span>

<span data-ttu-id="afbc3-110">На начальной странице Visual Studio последовательно выберите **Файл > Создать > Проект…**</span><span class="sxs-lookup"><span data-stu-id="afbc3-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![меню "Файл"](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="afbc3-112">В диалоговом окне **Создание проекта** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="afbc3-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="afbc3-113">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="afbc3-114">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-114">Select **Next**.</span></span>

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="afbc3-116">В диалоговом окне **Создание веб-приложения ASP.NET Core** сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="afbc3-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="afbc3-117">Выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-117">Select **Web Application**.</span></span>
* <span data-ttu-id="afbc3-118">Выберите **Изменить** в разделе способа проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="afbc3-118">Select **Change** under Authentication.</span></span>

![Диалоговое окно нового веб-приложения ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="afbc3-120">Появится диалоговое окно **Изменение способа проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="afbc3-121">Выберите **Учетные записи отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="afbc3-122">Нажмите кнопку **ОК**, чтобы вернуться на страницу **Создать веб-приложение ASP.NET Core**, и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Диалоговое окно "Новый способ веб-проверки подлинности ASP.NET Core"](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="afbc3-124">Visual Studio создает решение.</span><span class="sxs-lookup"><span data-stu-id="afbc3-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="afbc3-125">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="afbc3-125">Run the app</span></span>

* <span data-ttu-id="afbc3-126">Нажмите клавиши CTRL+F5, чтобы запустить проект.</span><span class="sxs-lookup"><span data-stu-id="afbc3-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="afbc3-127">Прочтите, что написано по ссылке **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-127">Test the **Privacy** link.</span></span>

![Веб-приложение откроется в localhost.](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="afbc3-129">Регистрация пользователя</span><span class="sxs-lookup"><span data-stu-id="afbc3-129">Register a user</span></span>

* <span data-ttu-id="afbc3-130">Выберите **Зарегистрироваться** и зарегистрируйте нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="afbc3-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="afbc3-131">Можно использовать вымышленный адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="afbc3-131">You can use a fictitious email address.</span></span> <span data-ttu-id="afbc3-132">После отправки на странице отображается следующая ошибка.</span><span class="sxs-lookup"><span data-stu-id="afbc3-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="afbc3-133">*Не удалось выполнить операцию базы данных при обработке запроса. Применение имеющихся миграций для контекста базы данных приложения, возможно, позволит решить проблему."*</span><span class="sxs-lookup"><span data-stu-id="afbc3-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="afbc3-134">Выберите **Применить миграции** и после обновления страницы перезагрузите ее.</span><span class="sxs-lookup"><span data-stu-id="afbc3-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![не удалось выполнить операцию базы данных при обработке запроса.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="afbc3-137">Приложение отобразит адрес электронной почты, который использовался для регистрации нового пользователя, и ссылку **Выйти**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Веб-приложение откроется в Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="afbc3-140">Развертывание приложения в Azure</span><span class="sxs-lookup"><span data-stu-id="afbc3-140">Deploy the app to Azure</span></span>

<span data-ttu-id="afbc3-141">В обозревателе решений щелкните правой кнопкой мыши проект и выберите команду **Опубликовать…**</span><span class="sxs-lookup"><span data-stu-id="afbc3-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="afbc3-143">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="afbc3-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="afbc3-144">Выберите **Azure**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-144">Select **Azure**.</span></span>
* <span data-ttu-id="afbc3-145">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-145">Select **Next**.</span></span>

![Диалоговое окно публикации](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="afbc3-147">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="afbc3-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="afbc3-148">Выберите **Служба приложений Azure (Linux)** .</span><span class="sxs-lookup"><span data-stu-id="afbc3-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="afbc3-149">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-149">Select **Next**.</span></span>

![Диалоговое окно публикации: выбор службы Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="afbc3-151">В диалоговом окне **Публикация** выберите вариант **Create a new Azure App Service...** (Создать экземпляр Службы приложений Azure).</span><span class="sxs-lookup"><span data-stu-id="afbc3-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Диалоговое окно публикации: выбор экземпляра службы Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="afbc3-153">Отображается диалоговое окно **Создание приложения службы**:</span><span class="sxs-lookup"><span data-stu-id="afbc3-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="afbc3-154">Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-154">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="afbc3-155">Вы можете сохранить эти имена или изменить их.</span><span class="sxs-lookup"><span data-stu-id="afbc3-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="afbc3-156">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-156">Select **Create**.</span></span>

![Диалоговое окно "Создание службы приложений"](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="afbc3-158">Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="afbc3-159">Созданный экземпляр выбирается автоматически.</span><span class="sxs-lookup"><span data-stu-id="afbc3-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="afbc3-160">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-160">Select **Finish**.</span></span>

![Диалоговое окно публикации: выбор экземпляра Службы приложений](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="afbc3-162">Затем отобразится страница **Сводка** для профиля публикации.</span><span class="sxs-lookup"><span data-stu-id="afbc3-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="afbc3-163">Программа Visual Studio обнаружила, что для этого приложения требуется база данных SQL Server, и предлагает вам настроить ее.</span><span class="sxs-lookup"><span data-stu-id="afbc3-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="afbc3-164">Нажмите кнопку **Настроить**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-164">Select **Configure**.</span></span>

![Страница со сводными данными о профиле публикации: настройка зависимости SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="afbc3-166">Откроется диалоговое окно **Configure dependency** (Настройка зависимостей).</span><span class="sxs-lookup"><span data-stu-id="afbc3-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="afbc3-167">Выберите **База данных SQL Azure**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="afbc3-168">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-168">Select **Next**.</span></span>

![Диалоговое окно для настройки зависимости SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="afbc3-170">В диалоговом окне **Configure Azure SQL Database** (Настройка Базы данных SQL Azure) выберите **Создать базу данных SQL**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Диалоговое окно настройки Базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="afbc3-172">Появится окно **Create Azure SQL Database** (Создание базы данных SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="afbc3-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="afbc3-173">В нем уже заполнены поля **Имя базы данных**, **Группа ресурсов**, **Сервер базы данных** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-173">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="afbc3-174">Вы можете сохранить эти значения или изменить их.</span><span class="sxs-lookup"><span data-stu-id="afbc3-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="afbc3-175">Заполните поля **Database administrator username** (Имя администратора базы данных) и **Пароль администратора базы данных** для выбранного параметра **Сервер базы данных** . (Обратите внимание, что у используемой учетной записи должны быть разрешения на создание базы данных SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="afbc3-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="afbc3-176">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-176">Select **Create**.</span></span>

![Диалоговое окно создания базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="afbc3-178">Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Configure Azure SQL Database** (Настройка базы данных SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="afbc3-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="afbc3-179">Созданный экземпляр выбирается автоматически.</span><span class="sxs-lookup"><span data-stu-id="afbc3-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="afbc3-180">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-180">Select **Next**.</span></span>

![Диалоговое окно настройки Базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="afbc3-182">На следующем шаге в диалоговом окне **Configure Azure SQL Database** (Настройка Базы данных SQL Azure) сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="afbc3-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="afbc3-183">Заполните поля **Database connection user name** (Имя пользователя для подключения к базе данных) и **Database connection password** (Пароль для подключения к базе данных).</span><span class="sxs-lookup"><span data-stu-id="afbc3-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="afbc3-184">Эти сведения ваше приложение будет использовать при подключении к базе данных во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="afbc3-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="afbc3-185">Рекомендуется не указывать такое же имя администратора и пароль, которые вы использовали на предыдущем шаге.</span><span class="sxs-lookup"><span data-stu-id="afbc3-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="afbc3-186">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-186">Select **Finish**.</span></span>

![Диалоговое окно с настройками Базы данных SQL Azure, сведения о строке подключения](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="afbc3-188">На странице **Сводка** для профиля публикации выберите **Параметры**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-188">In the **Publish Profile summary** page select **Settings**:</span></span>

![Страница со сводными данными о профиле публикации: выбор параметров](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="afbc3-190">На странице **Параметры** диалогового окна **Публикация**</span><span class="sxs-lookup"><span data-stu-id="afbc3-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="afbc3-191">Разверните раздел **Базы данных** и установите флажок **Использовать эту строку подключения во время выполнения**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="afbc3-192">Разверните раздел **Миграции Entity Framework** и установите флажок **Использовать эту миграцию при публикации**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="afbc3-193">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-193">Select **Save**.</span></span> <span data-ttu-id="afbc3-194">Visual Studio вернется в диалоговое окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="afbc3-196">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-196">Click **Publish**.</span></span> <span data-ttu-id="afbc3-197">Visual Studio публикует приложение в Azure.</span><span class="sxs-lookup"><span data-stu-id="afbc3-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="afbc3-198">По завершении развертывания приложение открывается в браузере.</span><span class="sxs-lookup"><span data-stu-id="afbc3-198">When the deployment completes, the app is opened in a browser.</span></span>

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="afbc3-200">Обновление приложения</span><span class="sxs-lookup"><span data-stu-id="afbc3-200">Update the app</span></span>

* <span data-ttu-id="afbc3-201">Измените страницу Razor *Pages/Index.cshtml* и ее содержимое.</span><span class="sxs-lookup"><span data-stu-id="afbc3-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="afbc3-202">Например, вы можете изменить абзац на "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="afbc3-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="afbc3-203">На странице **Сводка** для профиля публикации снова нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Страница со сводными данными о профиле публикации](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="afbc3-205">После публикации приложения убедитесь, что внесенные изменения доступны в Azure.</span><span class="sxs-lookup"><span data-stu-id="afbc3-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Убедитесь, что задача завершена.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="afbc3-207">Очистка</span><span class="sxs-lookup"><span data-stu-id="afbc3-207">Clean up</span></span>

<span data-ttu-id="afbc3-208">Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.</span><span class="sxs-lookup"><span data-stu-id="afbc3-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="afbc3-209">Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.</span><span class="sxs-lookup"><span data-stu-id="afbc3-209">Select **Resource groups**, then select the resource group you created.</span></span>

![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="afbc3-211">На странице **Группы ресурсов** выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-211">In the **Resource groups** page, select **Delete**.</span></span>

![Портал Azure: страница "Группы ресурсов"](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="afbc3-213">Введите имя группы ресурсов и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="afbc3-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="afbc3-214">Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.</span><span class="sxs-lookup"><span data-stu-id="afbc3-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="afbc3-215">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="afbc3-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="afbc3-216">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="afbc3-216">Additional resources</span></span>

* <span data-ttu-id="afbc3-217">Сведения о Visual Studio Code см. в разделе [Профили публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="afbc3-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* <span data-ttu-id="afbc3-218">[служба приложений Azure](/azure/app-service/app-service-web-overview);</span><span class="sxs-lookup"><span data-stu-id="afbc3-218">[Azure App Service](/azure/app-service/app-service-web-overview)</span></span>
* [<span data-ttu-id="afbc3-219">Группа ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="afbc3-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="afbc3-220">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="afbc3-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>