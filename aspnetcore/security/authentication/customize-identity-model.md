---
title: 'Identity Настройка модели в ASP.NET Core'
author: ajcvickers
description: 'В этой статье описывается, как настроить базовую модель данных Entity Framework Core для ASP.NET Core Identity .'
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052049"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="cb86d-103">Identity Настройка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb86d-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="cb86d-104">По [Артур Виккерс](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="cb86d-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="cb86d-105">ASP.NET Core Identity предоставляет платформу для управления и хранения учетных записей пользователей в ASP.NET Core приложениях.</span><span class="sxs-lookup"><span data-stu-id="cb86d-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="cb86d-106">Identity добавляется в проект, если в качестве механизма проверки подлинности выбраны **отдельные учетные записи пользователей** .</span><span class="sxs-lookup"><span data-stu-id="cb86d-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="cb86d-107">По умолчанию Identity использует модель данных ядра Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="cb86d-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="cb86d-108">В этой статье описывается настройка Identity модели.</span><span class="sxs-lookup"><span data-stu-id="cb86d-108">This article describes how to customize the Identity model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="cb86d-109">Identity и EF Core миграции</span><span class="sxs-lookup"><span data-stu-id="cb86d-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="cb86d-110">Перед изучением модели полезно понять, как Identity работает [миграция EF Core](/ef/core/managing-schemas/migrations/) для создания и обновления базы данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="cb86d-111">На верхнем уровне процесс:</span><span class="sxs-lookup"><span data-stu-id="cb86d-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="cb86d-112">Определение или обновление [модели данных в коде](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="cb86d-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="cb86d-113">Добавьте миграцию, чтобы преобразовать эту модель в изменения, которые могут быть применены к базе данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="cb86d-114">Убедитесь, что миграция правильно соответствует вашим намерения.</span><span class="sxs-lookup"><span data-stu-id="cb86d-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="cb86d-115">Примените миграцию, чтобы обновить базу данных для синхронизации с моделью.</span><span class="sxs-lookup"><span data-stu-id="cb86d-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="cb86d-116">Повторите шаги 1 – 4, чтобы уточнить модель и синхронизировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="cb86d-117">Для добавления и применения миграций используйте один из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="cb86d-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="cb86d-118">Окно **консоли диспетчера пакетов** (PMC) при использовании Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cb86d-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="cb86d-119">Дополнительные сведения см. в разделе [средства EF Core PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="cb86d-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="cb86d-120">.NET Core CLI, если используется Командная строка.</span><span class="sxs-lookup"><span data-stu-id="cb86d-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="cb86d-121">Дополнительные сведения см. в разделе [EF Core средства командной строки .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="cb86d-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="cb86d-122">При запуске приложения нажмите кнопку **Применить миграции** на странице ошибки.</span><span class="sxs-lookup"><span data-stu-id="cb86d-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="cb86d-123">В ASP.NET Core имеется обработчик страницы ошибок времени разработки.</span><span class="sxs-lookup"><span data-stu-id="cb86d-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="cb86d-124">Обработчик может применять миграции при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="cb86d-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="cb86d-125">Рабочие приложения обычно создают скрипты SQL из миграции и развертывают изменения базы данных в рамках управляемого приложения и развертывания базы данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="cb86d-126">При создании нового приложения Identity , созданного с помощью, шаги 1 и 2 уже выполнены.</span><span class="sxs-lookup"><span data-stu-id="cb86d-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="cb86d-127">Это значит, что начальная модель данных уже существует, а первоначальная миграция была добавлена в проект.</span><span class="sxs-lookup"><span data-stu-id="cb86d-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="cb86d-128">К базе данных все еще необходимо применить первоначальную миграцию.</span><span class="sxs-lookup"><span data-stu-id="cb86d-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="cb86d-129">Начальную миграцию можно применить с помощью одного из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="cb86d-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="cb86d-130">Запустите `Update-Database` в PMC.</span><span class="sxs-lookup"><span data-stu-id="cb86d-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="cb86d-131">Запустите `dotnet ef database update` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="cb86d-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="cb86d-132">При запуске приложения нажмите кнопку **Применить миграции** на странице ошибки.</span><span class="sxs-lookup"><span data-stu-id="cb86d-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="cb86d-133">Повторите предыдущие шаги по мере внесения изменений в модель.</span><span class="sxs-lookup"><span data-stu-id="cb86d-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="cb86d-134">IdentityМодель</span><span class="sxs-lookup"><span data-stu-id="cb86d-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="cb86d-135">Типы сущностей</span><span class="sxs-lookup"><span data-stu-id="cb86d-135">Entity types</span></span>

<span data-ttu-id="cb86d-136">IdentityМодель состоит из следующих типов сущностей.</span><span class="sxs-lookup"><span data-stu-id="cb86d-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="cb86d-137">Тип сущности</span><span class="sxs-lookup"><span data-stu-id="cb86d-137">Entity type</span></span>|<span data-ttu-id="cb86d-138">Описание</span><span class="sxs-lookup"><span data-stu-id="cb86d-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="cb86d-139">Представляет пользователя.</span><span class="sxs-lookup"><span data-stu-id="cb86d-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="cb86d-140">Представляет роль.</span><span class="sxs-lookup"><span data-stu-id="cb86d-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="cb86d-141">Представляет утверждение, которому владеет пользователь.</span><span class="sxs-lookup"><span data-stu-id="cb86d-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="cb86d-142">Представляет маркер проверки подлинности для пользователя.</span><span class="sxs-lookup"><span data-stu-id="cb86d-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="cb86d-143">Связывает пользователя с именем входа.</span><span class="sxs-lookup"><span data-stu-id="cb86d-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="cb86d-144">Представляет утверждение, которое предоставляется всем пользователям в роли.</span><span class="sxs-lookup"><span data-stu-id="cb86d-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="cb86d-145">Сущность JOIN, связывающая пользователей и роли.</span><span class="sxs-lookup"><span data-stu-id="cb86d-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="cb86d-146">Отношения типов сущностей</span><span class="sxs-lookup"><span data-stu-id="cb86d-146">Entity type relationships</span></span>

<span data-ttu-id="cb86d-147">[Типы сущностей](#entity-types) связаны друг с другом следующими способами.</span><span class="sxs-lookup"><span data-stu-id="cb86d-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="cb86d-148">Каждый `User` может иметь много `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="cb86d-149">Каждый `User` может иметь много `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="cb86d-150">Каждый `User` может иметь много `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="cb86d-151">У каждого из них `Role` может быть несколько связанных `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="cb86d-152">Каждый `User` может иметь много связанных `Roles` , и каждый из них `Role` может быть связан с множеством `Users` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="cb86d-153">Это отношение "многие ко многим", для которого требуется таблица соединений в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="cb86d-154">Таблица Join представлена `UserRole` сущностью.</span><span class="sxs-lookup"><span data-stu-id="cb86d-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="cb86d-155">Конфигурация модели по умолчанию</span><span class="sxs-lookup"><span data-stu-id="cb86d-155">Default model configuration</span></span>

<span data-ttu-id="cb86d-156">Identity Определяет множество *классов контекста* , которые наследуют от [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) для настройки и использования модели.</span><span class="sxs-lookup"><span data-stu-id="cb86d-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="cb86d-157">Эта конфигурация выполняется с помощью [интерфейса API EF Core Code First Fluent](/ef/core/modeling/) в методе [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) класса Context.</span><span class="sxs-lookup"><span data-stu-id="cb86d-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="cb86d-158">Конфигурация по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="cb86d-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="cb86d-159">Универсальные типы модели</span><span class="sxs-lookup"><span data-stu-id="cb86d-159">Model generic types</span></span>

<span data-ttu-id="cb86d-160">Identity Определяет типы [среды CLR по умолчанию](/dotnet/standard/glossary#clr) для каждого из перечисленных выше типов сущностей.</span><span class="sxs-lookup"><span data-stu-id="cb86d-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="cb86d-161">Все эти типы начинаются с префикса *Identity* :</span><span class="sxs-lookup"><span data-stu-id="cb86d-161">These types are all prefixed with *Identity* :</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="cb86d-162">Вместо того чтобы использовать эти типы напрямую, эти типы можно использовать в качестве базовых классов для собственных типов приложения.</span><span class="sxs-lookup"><span data-stu-id="cb86d-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="cb86d-163">`DbContext`Классы, определенные с помощью Identity , являются универсальными, поэтому для одного или нескольких типов сущностей в модели можно использовать различные типы CLR.</span><span class="sxs-lookup"><span data-stu-id="cb86d-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="cb86d-164">Эти универсальные типы также позволяют `User` изменять тип данных первичного ключа (PK).</span><span class="sxs-lookup"><span data-stu-id="cb86d-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="cb86d-165">При использовании Identity с поддержкой ролей <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> следует использовать класс.</span><span class="sxs-lookup"><span data-stu-id="cb86d-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="cb86d-166">Пример:</span><span class="sxs-lookup"><span data-stu-id="cb86d-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="cb86d-167">Также можно использовать Identity без ролей (только утверждения). в этом случае <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> следует использовать класс:</span><span class="sxs-lookup"><span data-stu-id="cb86d-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="cb86d-168">Настройка модели</span><span class="sxs-lookup"><span data-stu-id="cb86d-168">Customize the model</span></span>

<span data-ttu-id="cb86d-169">Начальной точкой настройки модели является наследование от соответствующего типа контекста.</span><span class="sxs-lookup"><span data-stu-id="cb86d-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="cb86d-170">См. раздел [универсальные типы модели](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="cb86d-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="cb86d-171">Этот тип контекста является `ApplicationDbContext` шаблонным и создается с помощью шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb86d-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="cb86d-172">Контекст используется для настройки модели двумя способами.</span><span class="sxs-lookup"><span data-stu-id="cb86d-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="cb86d-173">Предоставление типов сущностей и ключей для параметров универсального типа.</span><span class="sxs-lookup"><span data-stu-id="cb86d-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="cb86d-174">Переопределение `OnModelCreating` для изменения сопоставления этих типов.</span><span class="sxs-lookup"><span data-stu-id="cb86d-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="cb86d-175">При переопределении `OnModelCreating` `base.OnModelCreating` метод должен вызываться первыми; переопределяющая конфигурация должна вызываться следующим.</span><span class="sxs-lookup"><span data-stu-id="cb86d-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="cb86d-176">EF Core обычно имеет политику последнего применения WINS для настройки.</span><span class="sxs-lookup"><span data-stu-id="cb86d-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="cb86d-177">Например, если `ToTable` метод для типа сущности вызывается сначала с одним именем таблицы, а затем снова с другим именем таблицы, то используется имя таблицы во втором вызове.</span><span class="sxs-lookup"><span data-stu-id="cb86d-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="cb86d-178">Пользовательские данные пользователя</span><span class="sxs-lookup"><span data-stu-id="cb86d-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="cb86d-179">[Пользовательские данные пользователя](xref:security/authentication/add-user-data) поддерживаются путем наследования от `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="cb86d-180">Этот тип можно присвоить этому типу `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="cb86d-181">Используйте `ApplicationUser` тип в качестве универсального аргумента для контекста:</span><span class="sxs-lookup"><span data-stu-id="cb86d-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="cb86d-182">Переопределение в классе не требуется `OnModelCreating` `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="cb86d-183">EF Core сопоставляет `CustomTag` свойство по соглашению.</span><span class="sxs-lookup"><span data-stu-id="cb86d-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="cb86d-184">Однако базу данных необходимо обновить для создания нового `CustomTag` столбца.</span><span class="sxs-lookup"><span data-stu-id="cb86d-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="cb86d-185">Чтобы создать столбец, добавьте миграцию, а затем обновите базу данных, как описано в [ Identity и EF Core миграции](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="cb86d-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="cb86d-186">Обновите *страницы/Shared/_LoginPartial. cshtml* и замените `IdentityUser` на `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="cb86d-187">Обновите *области/ Identity / Identity HostingStartup.CS* или `Startup.ConfigureServices` замените `IdentityUser` на `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="cb86d-188">В ASP.NET Core 2,1 или более поздней версии Identity предоставляется в виде Razor библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="cb86d-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="cb86d-189">Для получения дополнительной информации см. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="cb86d-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="cb86d-190">Следовательно, приведенный выше код требует вызова <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="cb86d-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="cb86d-191">Если для Identity добавления файлов в проект использовался механизм формирования шаблонов Identity , удалите вызов `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="cb86d-192">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="cb86d-192">For more information, see:</span></span>

* [<span data-ttu-id="cb86d-193">Шаблона Identity</span><span class="sxs-lookup"><span data-stu-id="cb86d-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="cb86d-194">Добавление, скачивание и удаление настраиваемых данных пользователя в Identity</span><span class="sxs-lookup"><span data-stu-id="cb86d-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="cb86d-195">Изменение типа первичного ключа</span><span class="sxs-lookup"><span data-stu-id="cb86d-195">Change the primary key type</span></span>

<span data-ttu-id="cb86d-196">Изменение типа данных столбца PK после создания базы данных является проблематичным для многих систем баз.</span><span class="sxs-lookup"><span data-stu-id="cb86d-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="cb86d-197">Изменение ПЕРВИЧного ключа обычно подразумевает удаление и повторное создание таблицы.</span><span class="sxs-lookup"><span data-stu-id="cb86d-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="cb86d-198">Поэтому при создании базы данных необходимо указать типы ключей в первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="cb86d-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="cb86d-199">Чтобы изменить тип ПЕРВИЧного ключа, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="cb86d-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="cb86d-200">Если база данных была создана до изменения ПЕРВИЧного ключа, выполните команду `Drop-Database` (PMC) или `dotnet ef database drop` (.NET Core CLI), чтобы удалить ее.</span><span class="sxs-lookup"><span data-stu-id="cb86d-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="cb86d-201">После подтверждения удаления базы данных удалите первоначальную миграцию с помощью `Remove-Migration` (PMC) или `dotnet ef migrations remove` (.NET Core CLI).</span><span class="sxs-lookup"><span data-stu-id="cb86d-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="cb86d-202">Обновите `ApplicationDbContext` класс, производный от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="cb86d-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="cb86d-203">Укажите новый тип ключа для `TKey` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="cb86d-204">Например, чтобы использовать `Guid` тип ключа:</span><span class="sxs-lookup"><span data-stu-id="cb86d-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="cb86d-205">В приведенном выше коде <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> для использования нового типа ключа необходимо указать универсальные классы и.</span><span class="sxs-lookup"><span data-stu-id="cb86d-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="cb86d-206">В приведенном выше коде <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> для использования нового типа ключа необходимо указать универсальные классы и.</span><span class="sxs-lookup"><span data-stu-id="cb86d-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="cb86d-207">`Startup.ConfigureServices` необходимо обновить для использования универсального пользователя:</span><span class="sxs-lookup"><span data-stu-id="cb86d-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="cb86d-208">Если используется пользовательский `ApplicationUser` класс, обновите класс, чтобы он наследовал от `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="cb86d-209">Пример:</span><span class="sxs-lookup"><span data-stu-id="cb86d-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="cb86d-210">Обновите `ApplicationDbContext` для ссылки на пользовательский `ApplicationUser` класс:</span><span class="sxs-lookup"><span data-stu-id="cb86d-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="cb86d-211">Зарегистрируйте класс контекста пользовательской базы данных при добавлении Identity службы в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="cb86d-212">Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="cb86d-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="cb86d-213">В ASP.NET Core 2,1 или более поздней версии Identity предоставляется в виде Razor библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="cb86d-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="cb86d-214">Для получения дополнительной информации см. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="cb86d-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="cb86d-215">Следовательно, приведенный выше код требует вызова <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="cb86d-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="cb86d-216">Если для Identity добавления файлов в проект использовался механизм формирования шаблонов Identity , удалите вызов `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="cb86d-217">Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="cb86d-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="cb86d-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Метод принимает `TKey` тип, указывающий тип данных первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="cb86d-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="cb86d-219">Если используется пользовательский `ApplicationRole` класс, обновите класс, чтобы он наследовал от `IdentityRole<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="cb86d-220">Пример:</span><span class="sxs-lookup"><span data-stu-id="cb86d-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="cb86d-221">Обновите `ApplicationDbContext` для ссылки на пользовательский `ApplicationRole` класс.</span><span class="sxs-lookup"><span data-stu-id="cb86d-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="cb86d-222">Например, следующий класс ссылается на пользовательский `ApplicationUser` и настраиваемый `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="cb86d-223">Зарегистрируйте класс контекста пользовательской базы данных при добавлении Identity службы в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="cb86d-224">Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="cb86d-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="cb86d-225">В ASP.NET Core 2,1 или более поздней версии Identity предоставляется в виде Razor библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="cb86d-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="cb86d-226">Для получения дополнительной информации см. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="cb86d-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="cb86d-227">Следовательно, приведенный выше код требует вызова <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="cb86d-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="cb86d-228">Если для Identity добавления файлов в проект использовался механизм формирования шаблонов Identity , удалите вызов `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="cb86d-229">Зарегистрируйте класс контекста пользовательской базы данных при добавлении Identity службы в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="cb86d-230">Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="cb86d-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="cb86d-231">Зарегистрируйте класс контекста пользовательской базы данных при добавлении Identity службы в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="cb86d-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Метод принимает `TKey` тип, указывающий тип данных первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="cb86d-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="cb86d-233">Добавление свойств навигации</span><span class="sxs-lookup"><span data-stu-id="cb86d-233">Add navigation properties</span></span>

<span data-ttu-id="cb86d-234">Изменение конфигурации модели для связей может быть более сложным, чем внесение других изменений.</span><span class="sxs-lookup"><span data-stu-id="cb86d-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="cb86d-235">Необходимо соблюдать осторожность, чтобы заменить существующие связи, а не создавать новые, дополнительные связи.</span><span class="sxs-lookup"><span data-stu-id="cb86d-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="cb86d-236">В частности, измененная связь должна указывать то же свойство внешнего ключа (FK), что и существующая связь.</span><span class="sxs-lookup"><span data-stu-id="cb86d-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="cb86d-237">Например, связь между `Users` и `UserClaims` имеет значение по умолчанию, указанное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="cb86d-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="cb86d-238">FK для этой связи указывается в качестве `UserClaim.UserId` Свойства.</span><span class="sxs-lookup"><span data-stu-id="cb86d-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="cb86d-239">`HasMany` и `WithOne` вызываются без аргументов для создания связи без свойств навигации.</span><span class="sxs-lookup"><span data-stu-id="cb86d-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="cb86d-240">Добавьте в это свойство навигации `ApplicationUser` , которое позволяет `UserClaims` обращаться к связанным пользователям.</span><span class="sxs-lookup"><span data-stu-id="cb86d-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="cb86d-241">`TKey`Для `IdentityUserClaim<TKey>` — это тип, указанный для PK пользователей.</span><span class="sxs-lookup"><span data-stu-id="cb86d-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="cb86d-242">В этом случае имеет значение, `TKey` `string` поскольку используются значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cb86d-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="cb86d-243">Это **не** тип первичного ключа для `UserClaim` типа сущности.</span><span class="sxs-lookup"><span data-stu-id="cb86d-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="cb86d-244">Теперь, когда свойство навигации существует, оно должно быть настроено в `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="cb86d-245">Обратите внимание, что связь настраивается точно так же, как и ранее, только со свойством навигации, указанным в вызове `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="cb86d-246">Свойства навигации существуют только в модели EF, а не в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="cb86d-247">Поскольку тип внешнего ключа для связи не изменился, изменение этой модели не требует обновления базы данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="cb86d-248">Это можно проверить, добавив миграцию после внесения изменений.</span><span class="sxs-lookup"><span data-stu-id="cb86d-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="cb86d-249">`Up`Методы и `Down` пусты.</span><span class="sxs-lookup"><span data-stu-id="cb86d-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="cb86d-250">Добавить все свойства навигации пользователя</span><span class="sxs-lookup"><span data-stu-id="cb86d-250">Add all User navigation properties</span></span>

<span data-ttu-id="cb86d-251">С помощью приведенного выше раздела в качестве руководства в следующем примере настраивается однонаправленные свойства навигации для всех связей пользователя:</span><span class="sxs-lookup"><span data-stu-id="cb86d-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="cb86d-252">Добавление свойств навигации для пользователей и ролей</span><span class="sxs-lookup"><span data-stu-id="cb86d-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="cb86d-253">В приведенном выше разделе в качестве руководства в следующем примере настраиваются свойства навигации для всех связей пользователя и роли:</span><span class="sxs-lookup"><span data-stu-id="cb86d-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="cb86d-254">Примечания.</span><span class="sxs-lookup"><span data-stu-id="cb86d-254">Notes:</span></span>

* <span data-ttu-id="cb86d-255">Этот пример также включает `UserRole` сущность JOIN, необходимую для навигации по связям «многие ко многим» от пользователей к ролям.</span><span class="sxs-lookup"><span data-stu-id="cb86d-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="cb86d-256">Не забудьте изменить типы свойств навигации, чтобы они отражали, что эти `Application{...}` типы теперь используются вместо `Identity{...}` типов.</span><span class="sxs-lookup"><span data-stu-id="cb86d-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `Identity{...}` types.</span></span>
* <span data-ttu-id="cb86d-257">Не забудьте использовать `Application{...}` в универсальном `ApplicationContext` определении.</span><span class="sxs-lookup"><span data-stu-id="cb86d-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="cb86d-258">Добавить все свойства навигации</span><span class="sxs-lookup"><span data-stu-id="cb86d-258">Add all navigation properties</span></span>

<span data-ttu-id="cb86d-259">В приведенном выше разделе в качестве руководства в следующем примере настраиваются свойства навигации для всех связей на всех типах сущностей:</span><span class="sxs-lookup"><span data-stu-id="cb86d-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="cb86d-260">Использование составных ключей</span><span class="sxs-lookup"><span data-stu-id="cb86d-260">Use composite keys</span></span>

<span data-ttu-id="cb86d-261">В предыдущих разделах было продемонстрировано изменение типа ключа, используемого в Identity модели.</span><span class="sxs-lookup"><span data-stu-id="cb86d-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="cb86d-262">Изменение Identity ключевой модели на использование составных ключей не поддерживается или не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="cb86d-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="cb86d-263">Использование составного ключа с Identity включает изменение того, как Identity код менеджера взаимодействует с моделью.</span><span class="sxs-lookup"><span data-stu-id="cb86d-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="cb86d-264">Эта настройка выходит за рамки данного документа.</span><span class="sxs-lookup"><span data-stu-id="cb86d-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="cb86d-265">Изменение имен таблиц и столбцов и аспектов</span><span class="sxs-lookup"><span data-stu-id="cb86d-265">Change table/column names and facets</span></span>

<span data-ttu-id="cb86d-266">Чтобы изменить имена таблиц и столбцов, вызовите `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="cb86d-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="cb86d-267">Затем добавьте конфигурацию, чтобы переопределить любые значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cb86d-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="cb86d-268">Например, чтобы изменить имена всех Identity таблиц:</span><span class="sxs-lookup"><span data-stu-id="cb86d-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="cb86d-269">В этих примерах используются типы по умолчанию Identity .</span><span class="sxs-lookup"><span data-stu-id="cb86d-269">These examples use the default Identity types.</span></span> <span data-ttu-id="cb86d-270">Если используется тип приложения `ApplicationUser` , например, настройте этот тип вместо типа по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cb86d-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="cb86d-271">В следующем примере изменяются некоторые имена столбцов:</span><span class="sxs-lookup"><span data-stu-id="cb86d-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="cb86d-272">Некоторые типы столбцов базы данных можно настроить с помощью определенных *аспектов* (например, максимально `string` допустимой длины).</span><span class="sxs-lookup"><span data-stu-id="cb86d-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="cb86d-273">В следующем примере задается максимальная длина столбца для нескольких `string` свойств в модели:</span><span class="sxs-lookup"><span data-stu-id="cb86d-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="cb86d-274">Сопоставьте с другой схемой</span><span class="sxs-lookup"><span data-stu-id="cb86d-274">Map to a different schema</span></span>

<span data-ttu-id="cb86d-275">Схемы могут вести себя по-разному для поставщиков баз данных.</span><span class="sxs-lookup"><span data-stu-id="cb86d-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="cb86d-276">Для SQL Server значение по умолчанию — создать все таблицы в схеме *dbo* .</span><span class="sxs-lookup"><span data-stu-id="cb86d-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="cb86d-277">Таблицы могут быть созданы в другой схеме.</span><span class="sxs-lookup"><span data-stu-id="cb86d-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="cb86d-278">Пример:</span><span class="sxs-lookup"><span data-stu-id="cb86d-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="cb86d-279">Отложенная загрузка</span><span class="sxs-lookup"><span data-stu-id="cb86d-279">Lazy loading</span></span>

<span data-ttu-id="cb86d-280">В этом разделе добавляется поддержка прокси-серверов с отложенной загрузкой в Identity модели.</span><span class="sxs-lookup"><span data-stu-id="cb86d-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="cb86d-281">Отложенная загрузка полезна, так как позволяет использовать свойства навигации без предварительного подтверждения их загрузки.</span><span class="sxs-lookup"><span data-stu-id="cb86d-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="cb86d-282">Типы сущностей можно сделать доступными для отложенной загрузки несколькими способами, как описано в документации по [EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="cb86d-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="cb86d-283">Для простоты используйте прокси-серверы с отложенной загрузкой, для которых требуется:</span><span class="sxs-lookup"><span data-stu-id="cb86d-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="cb86d-284">Установка пакета [Microsoft. EntityFrameworkCore. прокси](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="cb86d-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="cb86d-285">Вызов <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> внутри [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="cb86d-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="cb86d-286">Открытые типы сущностей с `public virtual` свойствами навигации.</span><span class="sxs-lookup"><span data-stu-id="cb86d-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="cb86d-287">В следующем примере демонстрируется вызов `UseLazyLoadingProxies` в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cb86d-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="cb86d-288">Инструкции по добавлению свойств навигации в типы сущностей см. в предыдущих примерах.</span><span class="sxs-lookup"><span data-stu-id="cb86d-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb86d-289">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="cb86d-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
