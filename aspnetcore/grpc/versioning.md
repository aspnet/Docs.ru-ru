---
title: Управление версиями gRPC Services
author: jamesnk
description: Узнайте, как управлять версиями служб gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058705"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="8d0a7-103">Управление версиями gRPC Services</span><span class="sxs-lookup"><span data-stu-id="8d0a7-103">Versioning gRPC services</span></span>

<span data-ttu-id="8d0a7-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="8d0a7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="8d0a7-105">Новые функции, добавленные в приложение, могут требовать изменения служб gRPC, предоставляемых клиентам, и иногда это может приводить к непредвиденному поведению и сбоям.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="8d0a7-106">При изменении служб gRPC:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-106">When gRPC services change:</span></span>

* <span data-ttu-id="8d0a7-107">Следует учитывать, как изменения влияют на клиентов.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="8d0a7-108">Должна быть реализована стратегия управления версиями для поддержки изменений.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="8d0a7-109">Обратная совместимость</span><span class="sxs-lookup"><span data-stu-id="8d0a7-109">Backwards compatibility</span></span>

<span data-ttu-id="8d0a7-110">Протокол gRPC предназначен для поддержки служб, которые изменяются со временем.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="8d0a7-111">Как правило, дополнения к службам и методам gRPC не являются критическими.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="8d0a7-112">Некритические изменения позволяют существующим клиентам продолжать работу без изменений.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="8d0a7-113">Изменение или удаление служб gRPC являются критическими изменениями.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="8d0a7-114">Когда в службах gRPC возникают критические изменения, клиенты, использующие эту службу, необходимо обновить и повторно развернуть.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="8d0a7-115">Внесение некритических изменений в службу имеет ряд преимуществ:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="8d0a7-116">Существующие клиенты продолжают работать.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="8d0a7-117">Не приходится уведомлять клиентов о критических изменениях и обновлять их.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="8d0a7-118">Только одна версия службы должна документироваться и поддерживаться.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="8d0a7-119">Некритические изменения</span><span class="sxs-lookup"><span data-stu-id="8d0a7-119">Non-breaking changes</span></span>

<span data-ttu-id="8d0a7-120">Эти изменения не нарушают уровень протокола gRPC и двоичный уровень .NET.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="8d0a7-121">**Добавление новой службы**</span><span class="sxs-lookup"><span data-stu-id="8d0a7-121">**Adding a new service**</span></span>
* <span data-ttu-id="8d0a7-122">**Добавление нового метода в службу**</span><span class="sxs-lookup"><span data-stu-id="8d0a7-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="8d0a7-123">**Добавление поля в сообщение запроса**  — поля, добавленные в сообщение запроса, десериализуются со [значением по умолчанию](https://developers.google.com/protocol-buffers/docs/proto3#default) на сервере, если не задано значение.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="8d0a7-124">Чтобы изменение было некритическим, служба должна выполняться, если новое поле не задается старыми клиентами.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="8d0a7-125">**Добавление поля в ответное сообщение**  — поля, добавленные в ответное сообщение, десериализуются в коллекцию [неизвестных полей](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) сообщения на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="8d0a7-126">**Добавление значения в перечисление**  — перечисления сериализуются как числовое значение.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="8d0a7-127">Новые значения перечисления десериализуются на клиенте в значение перечисления без имени перечисления.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="8d0a7-128">Чтобы изменение было некритическим, более старые клиенты должны правильно работать при получении нового значения перечисления.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="8d0a7-129">Критические изменения двоичного кода</span><span class="sxs-lookup"><span data-stu-id="8d0a7-129">Binary breaking changes</span></span>

<span data-ttu-id="8d0a7-130">Следующие изменения являются некритическими на уровне протокола gRPC, но клиент необходимо обновить, если обновление происходит до последней версии контракта *PROTO* или клиентской сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="8d0a7-131">Совместимость двоичного кода важна, если вы планируете публиковать библиотеку gRPC в NuGet.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="8d0a7-132">**Удаление поля**  — значения из удаленного поля десериализуются в [неизвестные поля](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) сообщения.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="8d0a7-133">Это не является критическим изменением протокола gRPC, но клиент должен быть обновлен, если выполняется обновление до последней версии контракта.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="8d0a7-134">Важно, чтобы номер удаленного поля случайно не использовался повторно в будущем.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="8d0a7-135">Чтобы этого не произошло, укажите удаленные номера и имена полей в сообщении с помощью ключевого слова Protobuf [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved).</span><span class="sxs-lookup"><span data-stu-id="8d0a7-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="8d0a7-136">**Переименование сообщения**  — имена сообщений обычно не отправляются по сети, поэтому это не является критическим изменением протокола gRPC.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="8d0a7-137">Клиент необходимо будет обновить, если выполняется обновление до последней версии контракта.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="8d0a7-138">Одна из ситуаций, в которой имена сообщений **отправляются** в сеть, — использование полей [Any](https://developers.google.com/protocol-buffers/docs/proto3#any), когда имя сообщения используется для распознавания типа сообщений.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="8d0a7-139">**Изменение csharp_namespace**  — изменение `csharp_namespace` приведет к изменению пространства имен созданных типов .NET.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="8d0a7-140">Это не является критическим изменением протокола gRPC, но клиент должен быть обновлен, если выполняется обновление до последней версии контракта.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="8d0a7-141">Критические изменения протокола</span><span class="sxs-lookup"><span data-stu-id="8d0a7-141">Protocol breaking changes</span></span>

<span data-ttu-id="8d0a7-142">Ниже перечислены критические изменения протокола и двоичного кода:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="8d0a7-143">**Переименование поля**  — при использовании содержимого Protobuf имена полей используются только в созданном коде.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="8d0a7-144">Номер поля используется для идентификации полей в сети.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="8d0a7-145">Переименование поля не является критическим изменением протокола для Protobuf.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="8d0a7-146">Однако, если сервер использует содержимое JSON, то переименование поля является критическим изменением.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="8d0a7-147">**Изменение типа данных поля**  — изменение типа данных поля на [несовместимый тип](https://developers.google.com/protocol-buffers/docs/proto3#updating) приведет к ошибкам при десериализации сообщения.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="8d0a7-148">Даже если новый тип данных совместим, скорее всего, клиент должен быть обновлен для поддержки нового типа, если выполняется обновление до последней версии контракта.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="8d0a7-149">**Изменение номера поля**  — с полезными данными Protobuf номер поля используется для идентификации полей в сети.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="8d0a7-150">**Переименование пакета, службы или метода**  — gRPC использует имя пакета, имя службы и имя метода для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="8d0a7-151">Клиент получает состояние *UNIMPLEMENTED* от сервера.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="8d0a7-152">**Удаление службы или метода**  — клиент получает состояние *UNIMPLEMENTED* от сервера при вызове удаленного метода.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="8d0a7-153">Критическое изменение поведения</span><span class="sxs-lookup"><span data-stu-id="8d0a7-153">Behavior breaking changes</span></span>

<span data-ttu-id="8d0a7-154">При внесении некритических изменений необходимо также определить, могут ли старые клиенты продолжать работу с новым поведением службы.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="8d0a7-155">Например, добавление нового поля в сообщение запроса:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="8d0a7-156">Не является критическим изменением протокола.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="8d0a7-157">Возврат состояния ошибки на сервере, если новое поле не задано, приводит к критическому изменению старых клиентов.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="8d0a7-158">Совместимость поведения определяется кодом конкретного приложения.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="8d0a7-159">Службы номера версии</span><span class="sxs-lookup"><span data-stu-id="8d0a7-159">Version number services</span></span>

<span data-ttu-id="8d0a7-160">Службы должны стремиться обеспечить обратную совместимость со старыми клиентами.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="8d0a7-161">В конечном итоге изменения в приложении могут потребовать критических изменений.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="8d0a7-162">Прерывание старых клиентов и их принудительное обновление вместе со службой — это неудачное решение.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="8d0a7-163">Для обеспечения обратной совместимости при внесении критических изменений необходимо опубликовать несколько версий службы.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="8d0a7-164">gRPC поддерживает необязательный описатель [пакета](https://developers.google.com/protocol-buffers/docs/proto3#packages), который во многом схож с пространством имен .NET.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="8d0a7-165">На самом деле `package` будет использоваться как пространство имен .NET для созданных типов .NET, если `option csharp_namespace` не задано в файле *PROTO*.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="8d0a7-166">Пакет можно использовать для указания номера версии службы и ее сообщений:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="8d0a7-167">Имя пакета объединяется с именем службы для идентификации адреса службы.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="8d0a7-168">Адрес службы позволяет размещать несколько версий службы параллельно:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="8d0a7-169">Реализации версии службы регистрируются в *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="8d0a7-169">Implementations of the versioned service are registered in *Startup.cs* :</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="8d0a7-170">Включение номера версии в имя пакета дает возможность публиковать версию службы *v2* с критическими изменениями, продолжая поддерживать более старые клиенты, вызывающие версию *v1*.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="8d0a7-171">После обновления клиентов для использования службы *v2* можно удалить старую версию.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="8d0a7-172">При планировании публикации нескольких версий службы:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="8d0a7-173">Избегайте критических изменений, если это оправданно.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="8d0a7-174">Не обновляйте номер версии, если не внесены критические изменения.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="8d0a7-175">Обновляйте номер версии, если внесены критические изменения.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="8d0a7-176">Публикация нескольких версий службы дублирует ее.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="8d0a7-177">Чтобы уменьшить дублирование, рассмотрите возможность перемещения бизнес-логики из реализаций службы в централизованное расположение, которое может быть повторно использовано старыми и новыми реализациями:</span><span class="sxs-lookup"><span data-stu-id="8d0a7-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="8d0a7-178">Службы и сообщения, созданные с разными именами пакетов, являются **различными типами .NET**.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="8d0a7-179">Для перемещения бизнес-логики в централизованное расположение требуется сопоставление сообщений с общими типами.</span><span class="sxs-lookup"><span data-stu-id="8d0a7-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d0a7-180">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8d0a7-180">Additional resources</span></span>

* <xref:grpc/protobuf>
