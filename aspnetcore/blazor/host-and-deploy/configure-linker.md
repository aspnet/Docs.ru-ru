---
title: 'Настройка компоновщика для ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 0c99056053356133e901d6cf468fec8034dfb845
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055832"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a><span data-ttu-id="fd4e0-103">Настройка компоновщика для ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="fd4e0-103">Configure the Linker for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="fd4e0-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="fd4e0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fd4e0-105">:::no-loc(Blazor WebAssembly)::: выполняет компоновку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) во время сборки, чтобы затем удалить ненужный IL из выходных сборок приложения.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-105">:::no-loc(Blazor WebAssembly)::: performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="fd4e0-106">Компоновщик отключен при сборке в конфигурации отладки.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="fd4e0-107">Для включения компоновщика приложения должны быть построены в конфигурации выпуска.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="fd4e0-108">Мы рекомендуем создавать выпуск при развертывании приложений :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-108">We recommend building in Release when deploying your :::no-loc(Blazor WebAssembly)::: apps.</span></span> 

<span data-ttu-id="fd4e0-109">Компоновка приложения оптимизируется в зависимости от размера, но это может иметь негативные последствия.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="fd4e0-110">Приложения, использующие отражение или связанные динамические функции, могут прерываться при усечении, так как компоновщик не знает об этом динамическом поведении и не может определить, какие типы необходимы для отражения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="fd4e0-111">Чтобы обрезать такие приложения, компоновщик должен быть уведомлен о любых типах, необходимых для отражения в коде и в пакетах или платформах, от которых зависит приложение.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="fd4e0-112">Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, важно часто тестировать сборки выпуска приложения при разработке.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="fd4e0-113">Компоновку приложений :::no-loc(Blazor)::: можно настроить с помощью следующих функций MSBuild:</span><span class="sxs-lookup"><span data-stu-id="fd4e0-113">Linking for :::no-loc(Blazor)::: apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="fd4e0-114">настройка компоновки глобально с помощью [свойства MSBuild](#control-linking-with-an-msbuild-property);</span><span class="sxs-lookup"><span data-stu-id="fd4e0-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="fd4e0-115">управлять компоновкой каждой сборки с помощью [файла конфигурации](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="fd4e0-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="fd4e0-116">Управление компоновкой с помощью свойства MSBuild</span><span class="sxs-lookup"><span data-stu-id="fd4e0-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="fd4e0-117">Компоновка включается, когда приложение собирается в конфигурации `Release`.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="fd4e0-118">Чтобы изменить это поведение, настройте свойство MSBuild `:::no-loc(Blazor):::WebAssemblyEnableLinking` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="fd4e0-118">To change this, configure the `:::no-loc(Blazor):::WebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyEnableLinking>false</:::no-loc(Blazor):::WebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="fd4e0-119">Управление компоновкой с помощью файла конфигурации</span><span class="sxs-lookup"><span data-stu-id="fd4e0-119">Control linking with a configuration file</span></span>

<span data-ttu-id="fd4e0-120">Чтобы управлять компоновкой каждой сборки, нужно предоставить XML-файл конфигурации и указать его как элемент MSBuild в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <:::no-loc(Blazor):::LinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="fd4e0-121">`LinkerConfig.xml`.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-121">`LinkerConfig.xml`:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or :::no-loc(Blazor)::: packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCool:::no-loc(Blazor):::App" />
</linker>
```

<span data-ttu-id="fd4e0-122">Дополнительные сведения см. в разделе [Форматы данных (репозиторий GitHub для mono/компоновщика)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="fd4e0-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="fd4e0-123">Добавление файла конфигурации компоновщика XML в библиотеку</span><span class="sxs-lookup"><span data-stu-id="fd4e0-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="fd4e0-124">Чтобы настроить компоновщик для определенной библиотеки, добавьте файл конфигурации компоновщика XML в библиотеку в качестве внедренного ресурса.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="fd4e0-125">Имя внедренного ресурса должно совпадать с именем сборки.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="fd4e0-126">В следующем примере файл `LinkerConfig.xml` указан как внедренный ресурс, имя которого совпадает с именем сборки библиотеки.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="fd4e0-127">Настройка компоновщика для интернационализации</span><span class="sxs-lookup"><span data-stu-id="fd4e0-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="fd4e0-128">По умолчанию конфигурация компоновщика :::no-loc(Blazor)::: для приложений :::no-loc(Blazor WebAssembly)::: исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-128">By default, :::no-loc(Blazor):::'s linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="fd4e0-129">Удаление этих сборок уменьшает размер приложения.</span><span class="sxs-lookup"><span data-stu-id="fd4e0-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="fd4e0-130">Чтобы указать, какие сборки I18N необходимо оставить, задайте свойство MSBuild `<:::no-loc(Blazor):::WebAssemblyI18NAssemblies>` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="fd4e0-130">To control which I18N assemblies are retained, set the `<:::no-loc(Blazor):::WebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</:::no-loc(Blazor):::WebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="fd4e0-131">Значение региона</span><span class="sxs-lookup"><span data-stu-id="fd4e0-131">Region Value</span></span>     | <span data-ttu-id="fd4e0-132">Сборка для одного региона</span><span class="sxs-lookup"><span data-stu-id="fd4e0-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="fd4e0-133">Включены все сборки</span><span class="sxs-lookup"><span data-stu-id="fd4e0-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="fd4e0-134">`none` (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="fd4e0-134">`none` (default)</span></span> | <span data-ttu-id="fd4e0-135">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="fd4e0-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="fd4e0-136">Для разделения нескольких значений используйте запятую (например, `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="fd4e0-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="fd4e0-137">Дополнительные сведения см. на странице [I18N: библиотека платформы интернационализации Pnetlib (репозиторий mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="fd4e0-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd4e0-138">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fd4e0-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
