---
title: Настройка внешнего входа Google в ASP.NET Core
author: rick-anderson
description: В этом руководстве демонстрируется Интеграция проверки подлинности пользователя учетной записи Google с существующим ASP.NET Core приложением.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: 111ea7c972778dfd5296d0401c16563aeaa36a63
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060317"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Настройка внешнего входа Google в ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом руководстве показано, как разрешить пользователям входить в систему с помощью учетной записи Google, используя проект ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Создание проекта консоли Google API и идентификатора клиента

* Установите [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Перейдите к [разделу интеграция Google Sign-In в веб-приложение](https://developers.google.com/identity/sign-in/web/sign-in) и выберите **Настройка проекта** .
* В диалоговом окне **Настройка клиента OAuth** выберите **веб-сервер** .
* В текстовом поле " **зарегистрированные URI перенаправления** " задайте URI перенаправления. Например `https://localhost:44312/signin-google`.
* Сохраните **идентификатор клиента** и **секрет клиента** .
* При развертывании сайта зарегистрируйте новый общедоступный URL-адрес из **консоли Google** .

## <a name="store-the-google-client-id-and-secret"></a>Хранение идентификатора и секрета клиента Google

Храните конфиденциальные параметры, такие как идентификатор клиента Google и секретные значения, с помощью [диспетчера секретов](xref:security/app-secrets). Для этого примера выполните следующие действия.

1. Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).
1. Храните конфиденциальные параметры в локальном хранилище секретов с секретными ключами `Authentication:Google:ClientId` и `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Вы можете управлять учетными данными и использованием API в [консоли API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Настройка проверки подлинности Google

Добавьте службу Google в `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Вход с учетными данными Google

* Запустите приложение и нажмите кнопку **войти** . Появится возможность войти в систему с помощью Google.
* Нажмите кнопку **Google** , которая перенаправляет на Google для проверки подлинности.
* После ввода учетных данных Google вы будете перенаправлены обратно на веб-сайт.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности Google, см. в справочнике по API. Это можно использовать для запроса различных сведений о пользователе.

## <a name="change-the-default-callback-uri"></a>Изменение URI обратного вызова по умолчанию

Сегмент URI `/signin-google` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Google. Вы можете изменить URI обратного вызова по умолчанию при настройке по промежуточного слоя для проверки подлинности Google с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [гуглеоптионс](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .

## <a name="troubleshooting"></a>Устранение неполадок

* Если вход не работает и вы не получаете никаких ошибок, переключитесь в режим разработки, чтобы упростить отладку.
* Если Identity не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности результатов в *ArgumentException: необходимо указать параметр "сигнинсчеме"* . Шаблон проекта, используемый в этом руководстве, гарантирует, что это будет сделано.
* Если база данных сайта не была создана путем применения первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* . Выберите **Применить миграции** , чтобы создать базу данных, и обновите страницу, чтобы продолжить работу после ошибки.

## <a name="next-steps"></a>Дальнейшие действия

* В этой статье показано, как можно пройти проверку подлинности в Google. Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).
* После публикации приложения в Azure сбросьте его `ClientSecret` в консоли Google API.
* Задайте `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Параметры приложения и в портал Azure. Система конфигурации настроена на чтение ключей из переменных среды.
