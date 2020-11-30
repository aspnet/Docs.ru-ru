---
title: Формы и проверка ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать сценарии проверки форм и полей в Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: a8bbcbd6ac13ec064350a5b885423835baa4c4cc
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870377"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="e6e72-103">Формы и проверка ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e6e72-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e6e72-104">Авторы: [Дэниел Рот](https://github.com/danroth27) (Daniel Roth), [Реми Бургарель](https://remibou.github.io/) (Rémi Bourgarel) и [Люк Лэтем](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="e6e72-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e6e72-105">Формы и проверка поддерживаются в Blazor с помощью [заметок к данным](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="e6e72-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e6e72-106">Следующий тип `ExampleModel` определяет логику проверки с помощью заметок к данным:</span><span class="sxs-lookup"><span data-stu-id="e6e72-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e6e72-107">Форма определяется с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="e6e72-108">В следующей форме показаны типичные элементы, компоненты и код Razor:</span><span class="sxs-lookup"><span data-stu-id="e6e72-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="e6e72-109">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="e6e72-109">In the preceding example:</span></span>

* <span data-ttu-id="e6e72-110">Форма проверяет введенные пользователем данные в поле `name`, используя проверку, определенную в типе `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e6e72-111">Модель создается в блоке `@code` компонента и хранится в частном поле (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e6e72-112">Поле назначается атрибуту `Model` элемента `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e6e72-113">Элемент `@bind-Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText> привязывает:</span><span class="sxs-lookup"><span data-stu-id="e6e72-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="e6e72-114">Свойство модели (`exampleModel.Name`) к свойству `Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="e6e72-115">См. сведения о привязке свойств здесь: <xref:blazor/components/data-binding#binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-115">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="e6e72-116">Делегат события изменения к свойству `ValueChanged` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="e6e72-117">[Компонент проверяющего элемента управления](#validator-components) <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным.</span><span class="sxs-lookup"><span data-stu-id="e6e72-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e6e72-118">Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> обобщает сообщения проверки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="e6e72-119">`HandleValidSubmit` активируется, когда форма успешно отправляется (проходит проверку).</span><span class="sxs-lookup"><span data-stu-id="e6e72-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="e6e72-120">Встроенные компоненты форм</span><span class="sxs-lookup"><span data-stu-id="e6e72-120">Built-in forms components</span></span>

<span data-ttu-id="e6e72-121">Для получения и проверки вводимых пользователем данных доступен набор встроенных компонентов.</span><span class="sxs-lookup"><span data-stu-id="e6e72-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="e6e72-122">Входные данные проверяются при их изменении и отправке формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e6e72-123">В приведенной ниже таблице показаны доступные компоненты ввода.</span><span class="sxs-lookup"><span data-stu-id="e6e72-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="e6e72-124">Компонент ввода</span><span class="sxs-lookup"><span data-stu-id="e6e72-124">Input component</span></span> | <span data-ttu-id="e6e72-125">Отображается как &hellip;</span><span class="sxs-lookup"><span data-stu-id="e6e72-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="e6e72-126">Компонент ввода</span><span class="sxs-lookup"><span data-stu-id="e6e72-126">Input component</span></span> | <span data-ttu-id="e6e72-127">Отображается как &hellip;</span><span class="sxs-lookup"><span data-stu-id="e6e72-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="e6e72-128">Компоненты `InputRadio` и `InputRadioGroup` доступны в ASP.NET Core 5.0 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="e6e72-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="e6e72-129">Для получения дополнительных сведений выберите вариант этой статьи для версии 5.0 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="e6e72-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="e6e72-130">Все компоненты ввода, включая <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, поддерживают произвольные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="e6e72-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="e6e72-131">В отрисованный HTML-элемент добавляется любой атрибут, который не соответствует параметру компонента.</span><span class="sxs-lookup"><span data-stu-id="e6e72-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e6e72-132">Компоненты ввода обеспечивают поведение проверки по умолчанию при изменении поля, включая изменение класса CSS поля в соответствии с состоянием поля.</span><span class="sxs-lookup"><span data-stu-id="e6e72-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="e6e72-133">Некоторые компоненты включают в себя полезную логику синтаксического анализа.</span><span class="sxs-lookup"><span data-stu-id="e6e72-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="e6e72-134">Например, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> и <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> обрабатывают не анализируемые значения надлежащим образом, регистрируя их как ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="e6e72-135">Типы, которые могут принимать значения NULL, также поддерживают допустимость значений NULL для целевого поля (например, `int?`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e6e72-136">Следующий тип `Starship` определяет логику проверки, используя более широкий набор свойств и заметок к данным, чем предыдущий `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="e6e72-137">В предыдущем примере `Description` является необязательным, так как заметки к данным отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="e6e72-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e6e72-138">Следующая форма проверяет введенные пользователем данные, используя проверку, определенную в модели `Starship`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="e6e72-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> создает <xref:Microsoft.AspNetCore.Components.Forms.EditContext> в виде [каскадного значения](xref:blazor/components/cascading-values-and-parameters), которое отслеживает метаданные процесса редактирования, включая измененные поля и текущие сообщения проверки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="e6e72-140">Назначайте <xref:Microsoft.AspNetCore.Components.Forms.EditForm> **либо** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, **либо** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="e6e72-141">Назначение обоих значений не поддерживается, и возникает **ошибка времени выполнения**.</span><span class="sxs-lookup"><span data-stu-id="e6e72-141">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="e6e72-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> предоставляет удобные события для допустимой и недопустимой отправки формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="e6e72-143">Используйте <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>, чтобы применять пользовательский код для активации проверки и выполнения проверки значений полей.</span><span class="sxs-lookup"><span data-stu-id="e6e72-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="e6e72-144">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e6e72-144">In the following example:</span></span>

* <span data-ttu-id="e6e72-145">Метод `HandleSubmit` выполняется при нажатии кнопки **`Submit`** .</span><span class="sxs-lookup"><span data-stu-id="e6e72-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="e6e72-146">Форма проверяется путем вызова <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="e6e72-147">В зависимости от результата проверки выполняется дополнительный код.</span><span class="sxs-lookup"><span data-stu-id="e6e72-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="e6e72-148">Поместите бизнес-логику в метод, назначенный <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="e6e72-149">API платформы для очистки сообщений проверки непосредственно от <xref:Microsoft.AspNetCore.Components.Forms.EditContext> не существует.</span><span class="sxs-lookup"><span data-stu-id="e6e72-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="e6e72-150">Поэтому в целом не рекомендуется добавлять сообщения проверки в новый класс <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> в форме.</span><span class="sxs-lookup"><span data-stu-id="e6e72-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="e6e72-151">Для управления сообщениями проверки используйте [компонент проверяющего элемента управления](#validator-components) с [кодом проверки бизнес-логики](#business-logic-validation), как описано в этой статье.</span><span class="sxs-lookup"><span data-stu-id="e6e72-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="e6e72-152">Поддержка отображаемого имени</span><span class="sxs-lookup"><span data-stu-id="e6e72-152">Display name support</span></span>

<span data-ttu-id="e6e72-153">*Этот раздел относится к ASP.NET Core в релизе-кандидате 1 (RC1) платформы .NET 5 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="e6e72-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="e6e72-154">Следующие встроенные компоненты поддерживают отображаемые имена с помощью параметра `DisplayName`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="e6e72-155">В следующем примере компонента `InputDate`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="e6e72-156">Отображаемое имя (`DisplayName`) задается как `birthday`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="e6e72-157">Компонент привязан к свойству `BirthDate` как тип `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="e6e72-158">Если пользователь предоставляет не дату, возникает следующая ошибка проверки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="e6e72-159">Компоненты проверяющего элемента управления</span><span class="sxs-lookup"><span data-stu-id="e6e72-159">Validator components</span></span>

<span data-ttu-id="e6e72-160">Компоненты проверяющего элемента управления поддерживают проверку форм путем управления <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> для класса <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="e6e72-161">Платформа Blazor предоставляет компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> для прикрепления к формам поддержки проверки на основе [атрибутов проверки (заметок к данным)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="e6e72-161">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="e6e72-162">Создайте настраиваемые компоненты проверяющего элемента управления, чтобы обрабатывать сообщения проверки для различных форм на одной странице или одной и той же формы на разных этапах обработки, например проверку на стороне клиента с последующей проверкой на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="e6e72-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="e6e72-163">Показанный в этом разделе пример компонента проверяющего элемента управления (`CustomValidator`) используется в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="e6e72-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="e6e72-164">Проверка бизнес-логики</span><span class="sxs-lookup"><span data-stu-id="e6e72-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="e6e72-165">Серверная проверка</span><span class="sxs-lookup"><span data-stu-id="e6e72-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="e6e72-166">Во многих случаях вместо настраиваемых компонентов проверяющего элемента управления можно использовать настраиваемые атрибуты проверки заметок к данным.</span><span class="sxs-lookup"><span data-stu-id="e6e72-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="e6e72-167">Настраиваемые атрибуты, применяемые к модели формы, активируются с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="e6e72-168">При использовании вместе с проверкой на стороне сервера любые настраиваемые атрибуты, применяемые к модели, должны быть исполняемыми на сервере.</span><span class="sxs-lookup"><span data-stu-id="e6e72-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="e6e72-169">Дополнительные сведения см. в разделе <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="e6e72-170">Создайте компонент проверяющего элемента управления из класса <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="e6e72-171">Класс <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы является [каскадным параметром](xref:blazor/components/cascading-values-and-parameters) компонента.</span><span class="sxs-lookup"><span data-stu-id="e6e72-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="e6e72-172">При инициализации компонента проверяющего элемента управления создается новый класс <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> для поддержки текущего списка ошибок формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="e6e72-173">Хранилище сообщений получает ошибки, когда код разработчика в компоненте формы вызывает метод `DisplayErrors`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="e6e72-174">Ошибки передаются в метод `DisplayErrors` в классе [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span><span class="sxs-lookup"><span data-stu-id="e6e72-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="e6e72-175">В словаре ключом является имя поля формы, в котором есть одна ошибка или несколько.</span><span class="sxs-lookup"><span data-stu-id="e6e72-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="e6e72-176">Значением является список ошибок.</span><span class="sxs-lookup"><span data-stu-id="e6e72-176">The value is the error list.</span></span>
* <span data-ttu-id="e6e72-177">Сообщения очищаются, когда происходит любое из следующих событий.</span><span class="sxs-lookup"><span data-stu-id="e6e72-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="e6e72-178">В <xref:Microsoft.AspNetCore.Components.Forms.EditContext> запрашивается проверка при возникновении события <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="e6e72-179">Все ошибки очищаются.</span><span class="sxs-lookup"><span data-stu-id="e6e72-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="e6e72-180">В форме изменяется поле при возникновении события <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="e6e72-181">Очищаются только ошибки для этого поля.</span><span class="sxs-lookup"><span data-stu-id="e6e72-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="e6e72-182">В коде разработчика вызывается метод `ClearErrors`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="e6e72-183">Все ошибки очищаются.</span><span class="sxs-lookup"><span data-stu-id="e6e72-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="e6e72-184">Проверка бизнес-логики</span><span class="sxs-lookup"><span data-stu-id="e6e72-184">Business logic validation</span></span>

<span data-ttu-id="e6e72-185">Проверку бизнес-логики можно выполнить с помощью [компонента проверяющего элемента управления](#validator-components), который получает ошибки формы в словаре.</span><span class="sxs-lookup"><span data-stu-id="e6e72-185">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="e6e72-186">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e6e72-186">In the following example:</span></span>

* <span data-ttu-id="e6e72-187">Используется компонент `CustomValidator` из раздела [Компоненты проверяющего элемента управления](#validator-components) этой статьи.</span><span class="sxs-lookup"><span data-stu-id="e6e72-187">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="e6e72-188">Проверка требует значение для описания доставки (`Description`), если пользователь выбирает классификацию доставки `Defense` (`Classification`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-188">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="e6e72-189">Когда сообщения проверки задаются в компоненте, они добавляются в класс <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> проверяющего элемента управления и отображаются в классе <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-189">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="e6e72-190">Вместо [компонентов проверки](#validator-components) можно использовать атрибуты проверки заметок к данным.</span><span class="sxs-lookup"><span data-stu-id="e6e72-190">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="e6e72-191">Настраиваемые атрибуты, применяемые к модели формы, активируются с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-191">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="e6e72-192">При использовании вместе с проверкой на стороне сервера атрибуты должны быть исполняемыми на сервере.</span><span class="sxs-lookup"><span data-stu-id="e6e72-192">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="e6e72-193">Дополнительные сведения см. в разделе <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-193">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="e6e72-194">Серверная проверка</span><span class="sxs-lookup"><span data-stu-id="e6e72-194">Server validation</span></span>

<span data-ttu-id="e6e72-195">Серверную проверку можно выполнить с помощью [компонента проверяющего элемента управления](#validator-components) сервера.</span><span class="sxs-lookup"><span data-stu-id="e6e72-195">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="e6e72-196">Обрабатывайте проверку на стороне клиента в форме с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-196">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="e6e72-197">Когда форма проходит проверку на стороне клиента (вызывается <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>), отправляйте <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> в API внутреннего сервера для обработки формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-197">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="e6e72-198">Обрабатывайте проверку модели на сервере.</span><span class="sxs-lookup"><span data-stu-id="e6e72-198">Process model validation on the server.</span></span>
* <span data-ttu-id="e6e72-199">Серверный API включает как встроенную платформенную проверку заметок к данным, так и настраиваемую логику проверки, предоставленную разработчиком.</span><span class="sxs-lookup"><span data-stu-id="e6e72-199">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="e6e72-200">Если проверка на сервере пройдена, выполните обработку формы и отправьте обратно код состояния успеха (*200 — OK*).</span><span class="sxs-lookup"><span data-stu-id="e6e72-200">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="e6e72-201">Если проверка завершается неудачно, возвращайте код состояния сбоя (*400 — неверный запрос*) и ошибки проверки полей.</span><span class="sxs-lookup"><span data-stu-id="e6e72-201">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="e6e72-202">Отключите форму в случае успешного выполнения или отобразите ошибки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-202">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="e6e72-203">Основу приведенного ниже примера составляют следующие компоненты.</span><span class="sxs-lookup"><span data-stu-id="e6e72-203">The following example is based on:</span></span>

* <span data-ttu-id="e6e72-204">Размещенное решение Blazor, созданное [шаблоном проекта Blazor Hosted](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="e6e72-204">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="e6e72-205">Этот пример можно использовать с любыми защищенными размещенными решениями Blazor, описанными в [документации по безопасности и Identity](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="e6e72-205">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="e6e72-206">Пример формы *Starfleet Starship Database* из предыдущего раздела [Встроенные компоненты форм](#built-in-forms-components).</span><span class="sxs-lookup"><span data-stu-id="e6e72-206">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="e6e72-207">Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="e6e72-207">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="e6e72-208">Компонент `CustomValidator`, показанный в разделе [Компоненты проверяющего элемента управления](#validator-components).</span><span class="sxs-lookup"><span data-stu-id="e6e72-208">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="e6e72-209">В следующем примере серверный API проверяет, предоставлено ли значение для описания доставки (`Description`), если пользователь выбирает классификацию (`Classification`) доставки `Defense`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-209">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="e6e72-210">Поместите модель `Starship` в проект `Shared` решения, чтобы эту модель могли использовать клиентские и серверные приложения.</span><span class="sxs-lookup"><span data-stu-id="e6e72-210">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="e6e72-211">Так как для модели требуются заметки к данным, добавьте ссылку на пакет для [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) в файл проекта `Shared`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-211">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="e6e72-212">Чтобы определить последнюю версию пакета, не являющуюся предварительной, просмотрите **журнал версий** пакета по адресу [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="e6e72-212">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="e6e72-213">В проекте серверного API добавьте контроллер для обработки запросов проверки Starship (`Controllers/StarshipValidation.cs`) и возвращайте сообщения о неудачной проверке.</span><span class="sxs-lookup"><span data-stu-id="e6e72-213">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="e6e72-214">Заполнитель `{ASSEMBLY NAME}` в предыдущем примере — это имя сборки приложения (например, `BlazorSample.Server`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-214">In the preceding example, the placeholder `{ASSEMBLY NAME}` is the app's assembly name (for example, `BlazorSample.Server`).</span></span>

<span data-ttu-id="e6e72-215">Если на сервере возникает ошибка проверки привязки модели, то [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) обычно возвращает [ответ о неверном запросе по умолчанию](xref:web-api/index#default-badrequest-response) с <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-215">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="e6e72-216">Этот ответ, помимо ошибок проверки, содержит дополнительные данные, как показано в следующем примере, когда все поля формы *Starfleet Starship Database* не отправляются и форма не проходит проверку.</span><span class="sxs-lookup"><span data-stu-id="e6e72-216">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="e6e72-217">Если серверный API возвращает предыдущий ответ JSON по умолчанию, клиент может выполнить анализ ответа, чтобы получить дочерние элементы узла `errors`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-217">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="e6e72-218">Однако анализировать этот файл неудобно.</span><span class="sxs-lookup"><span data-stu-id="e6e72-218">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="e6e72-219">Для выполнения анализа JSON необходимо добавить дополнительный код после вызова <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>, чтобы создавать [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) ошибок при обработке ошибок проверки форм.</span><span class="sxs-lookup"><span data-stu-id="e6e72-219">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="e6e72-220">В идеале серверный API должен возвращать только ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-220">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="e6e72-221">Чтобы изменить ответ серверного API так, чтобы он возвращал только ошибки проверки, измените делегат, который вызывается для действий, помеченных <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-221">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e6e72-222">Для конечной точки API (`/StarshipValidation`) возвращайте <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> с помощью <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-222">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="e6e72-223">Для других конечных точек API сохраните поведение по умолчанию, возвращая результат объекта с помощью нового <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-223">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="e6e72-224">Дополнительные сведения см. в разделе <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-224">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="e6e72-225">В клиентском проекте добавьте компонент проверяющего элемента управления, показанный в разделе [Компоненты проверяющего элемента управления](#validator-components).</span><span class="sxs-lookup"><span data-stu-id="e6e72-225">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="e6e72-226">В клиентском проекте форма *Starfleet Starship Database* обновляется для отображения ошибок серверной проверки с помощью компонента `CustomValidator`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-226">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="e6e72-227">Когда серверный API возвращает сообщения проверки, они добавляются в <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> компонента `CustomValidator`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-227">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="e6e72-228">Эти ошибки можно вывести из <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы с помощью <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-228">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="e6e72-229">Вместо [компонентов проверки](#validator-components) можно использовать атрибуты проверки заметок к данным.</span><span class="sxs-lookup"><span data-stu-id="e6e72-229">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="e6e72-230">Настраиваемые атрибуты, применяемые к модели формы, активируются с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-230">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="e6e72-231">При использовании вместе с проверкой на стороне сервера атрибуты должны быть исполняемыми на сервере.</span><span class="sxs-lookup"><span data-stu-id="e6e72-231">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="e6e72-232">Дополнительные сведения см. в разделе <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-232">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="e6e72-233">Метод проверки на стороне сервера в этом разделе подходит для любого из примеров размещенного решения Blazor WebAssembly в этом наборе документации.</span><span class="sxs-lookup"><span data-stu-id="e6e72-233">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="e6e72-234">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="e6e72-234">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="e6e72-235">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="e6e72-235">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="e6e72-236">Сервер Identity</span><span class="sxs-lookup"><span data-stu-id="e6e72-236">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e6e72-237">InputText на основе события ввода</span><span class="sxs-lookup"><span data-stu-id="e6e72-237">InputText based on the input event</span></span>

<span data-ttu-id="e6e72-238">Используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.InputText>, чтобы создать пользовательский компонент, использующий событие `input`, а не событие `change`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-238">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e6e72-239">В следующем примере компонент `CustomInputText` наследует компонент `InputText` платформы задает привязку события (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) к событию `oninput`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-239">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="e6e72-240">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-240">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="e6e72-241">Компонент `CustomInputText` можно использовать везде, где используется <xref:Microsoft.AspNetCore.Components.Forms.InputText>:</span><span class="sxs-lookup"><span data-stu-id="e6e72-241">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="e6e72-242">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-242">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="e6e72-243">Переключатели</span><span class="sxs-lookup"><span data-stu-id="e6e72-243">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e6e72-244">Используйте компоненты `InputRadio` с компонентом `InputRadioGroup`, чтобы создать группу переключателей.</span><span class="sxs-lookup"><span data-stu-id="e6e72-244">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="e6e72-245">В следующем примере свойства добавляются в модель `Starship`, описанную в разделе [Встроенные компоненты форм](#built-in-forms-components).</span><span class="sxs-lookup"><span data-stu-id="e6e72-245">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="e6e72-246">Добавьте в приложение указанные ниже перечисления (`enums`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-246">Add the following `enums` to the app.</span></span> <span data-ttu-id="e6e72-247">Создайте файл для хранения `enums` или добавьте `enums` в файл `Starship.cs`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-247">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="e6e72-248">Сделайте `enums` доступным для модели `Starship` и формы *Starfleet Starship Database*.</span><span class="sxs-lookup"><span data-stu-id="e6e72-248">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="e6e72-249">Обновите форму *Starfleet Starship Database*, описанную в разделе [Встроенные компоненты форм](#built-in-forms-components).</span><span class="sxs-lookup"><span data-stu-id="e6e72-249">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="e6e72-250">Добавьте компоненты для создания следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="e6e72-250">Add the components to produce:</span></span>

* <span data-ttu-id="e6e72-251">группы переключателей для выбора производителя корабля;</span><span class="sxs-lookup"><span data-stu-id="e6e72-251">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="e6e72-252">группы переключателей для выбора цвета и типа двигателя корабля.</span><span class="sxs-lookup"><span data-stu-id="e6e72-252">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="e6e72-253">Если опустить `Name`, компоненты `InputRadio` будут группироваться по последнему предку.</span><span class="sxs-lookup"><span data-stu-id="e6e72-253">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e6e72-254">При работе с переключателями в форме привязка данных обрабатывается иначе, чем другие элементы, так как переключатели оцениваются как группа.</span><span class="sxs-lookup"><span data-stu-id="e6e72-254">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="e6e72-255">Значение каждого переключателя является фиксированным, но значение группы переключателей является значением выбранного переключателя.</span><span class="sxs-lookup"><span data-stu-id="e6e72-255">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="e6e72-256">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="e6e72-256">The following example shows how to:</span></span>

* <span data-ttu-id="e6e72-257">Обработка привязки данных для группы переключателей.</span><span class="sxs-lookup"><span data-stu-id="e6e72-257">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="e6e72-258">Поддержка проверки с помощью пользовательского компонента `InputRadio`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-258">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="e6e72-259">Следующий <xref:Microsoft.AspNetCore.Components.Forms.EditForm> использует предыдущий компонент `InputRadio` для получения и проверки оценки пользователя:</span><span class="sxs-lookup"><span data-stu-id="e6e72-259">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="e6e72-260">Привязка параметров элемента `<select>` к значениям `null` объекта C#</span><span class="sxs-lookup"><span data-stu-id="e6e72-260">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="e6e72-261">Не существует целесообразного способа представить значение параметра элемента `<select>` в виде значения `null` объекта C# по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="e6e72-261">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="e6e72-262">Атрибуты HTML не могут иметь значение `null`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-262">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="e6e72-263">Ближайшим эквивалентом `null` в HTML является отсутствие атрибута HTML `value` в элементе `<option>`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-263">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="e6e72-264">При выборе `<option>` без атрибута `value` браузер обрабатывает такое значение как *текстовое содержимое* элемента `<option>`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-264">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="e6e72-265">Платформа Blazor не пытается блокировать поведение по умолчанию, так как для этого потребуется:</span><span class="sxs-lookup"><span data-stu-id="e6e72-265">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="e6e72-266">создать цепочку специальных решений в платформе;</span><span class="sxs-lookup"><span data-stu-id="e6e72-266">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="e6e72-267">кардинально изменить текущее поведение платформы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-267">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e6e72-268">Приемлемым эквивалентом `null` в HTML является *пустая строка* (`value`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-268">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="e6e72-269">Платформа Blazor преобразует `null` в пустую строку для двусторонней привязки к значению `<select>`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-269">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e6e72-270">Платформа Blazor не выполняет автоматическое преобразование `null` в пустую строку при попытке двусторонней привязки к значению элемента `<select>`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-270">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="e6e72-271">Дополнительные сведения см. в разделе [Исправление привязки `<select>` к значению NULL (dotnet/aspnetcore 23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="e6e72-271">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="e6e72-272">Поддержка проверки</span><span class="sxs-lookup"><span data-stu-id="e6e72-272">Validation support</span></span>

<span data-ttu-id="e6e72-273">Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным к каскадному <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-273">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="e6e72-274">Чтобы включить поддержку проверки с помощью заметок к данным, требуется это явное действие.</span><span class="sxs-lookup"><span data-stu-id="e6e72-274">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e6e72-275">Чтобы использовать другую систему проверки, а не заметки к данным, замените <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пользовательской реализацией.</span><span class="sxs-lookup"><span data-stu-id="e6e72-275">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="e6e72-276">Реализация ASP.NET Core доступна для проверки в эталонном источнике: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="e6e72-276">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="e6e72-277">Предыдущие ссылки на источник ссылки предоставляют код из ветви `master` репозитория, которая представляет текущую разработку единицы продукта для следующего выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6e72-277">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="e6e72-278">Чтобы выбрать ветвь для другого выпуска, используйте селектор ветвей GitHub (например `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-278">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="e6e72-279">Blazor выполняет два типа проверки данных:</span><span class="sxs-lookup"><span data-stu-id="e6e72-279">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="e6e72-280">*Проверка поля* выполняется, когда пользователь выходит за пределы поля.</span><span class="sxs-lookup"><span data-stu-id="e6e72-280">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="e6e72-281">Во время проверки поля компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> связывает все результаты проверки с полем.</span><span class="sxs-lookup"><span data-stu-id="e6e72-281">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="e6e72-282">*Проверка модели* выполняется, когда пользователь отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="e6e72-282">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="e6e72-283">Во время проверки модели компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пытается определить поле на основе имени члена из результатов проверки.</span><span class="sxs-lookup"><span data-stu-id="e6e72-283">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="e6e72-284">Результаты проверки, не связанные с отдельным элементом, связаны с моделью, а не с полем.</span><span class="sxs-lookup"><span data-stu-id="e6e72-284">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="e6e72-285">Сводка проверки и компоненты сообщений о проверке</span><span class="sxs-lookup"><span data-stu-id="e6e72-285">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="e6e72-286">Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> содержит обзор всех сообщений проверки аналогично [вспомогательному методу тега сводки проверки](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="e6e72-286">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="e6e72-287">Выходные сообщения проверки для конкретной модели с параметром `Model`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-287">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="e6e72-288">Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> отображает сообщения проверки для определенного поля, которые похожи на [вспомогательный метод тега сообщения проверки](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e6e72-288">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e6e72-289">Укажите поле для проверки с помощью атрибута `For` и лямбда-выражения с именем свойства модели:</span><span class="sxs-lookup"><span data-stu-id="e6e72-289">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e6e72-290">Компоненты <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> и <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> поддерживают произвольные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="e6e72-290">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="e6e72-291">В созданный элемент `<div>` или `<ul>` добавляется любой атрибут, который не соответствует параметру компонента.</span><span class="sxs-lookup"><span data-stu-id="e6e72-291">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="e6e72-292">Вы можете управлять стилем сообщений проверки с помощью таблицы стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-292">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="e6e72-293">Класс `validation-message` по умолчанию задает для сообщений проверки красный цвет текста:</span><span class="sxs-lookup"><span data-stu-id="e6e72-293">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="e6e72-294">Пользовательские атрибуты проверки</span><span class="sxs-lookup"><span data-stu-id="e6e72-294">Custom validation attributes</span></span>

<span data-ttu-id="e6e72-295">Чтобы убедиться, что результат проверки правильно связан с полем при использовании [настраиваемого атрибута проверки](xref:mvc/models/validation#custom-attributes), передайте <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> контекста проверки при создании <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="e6e72-295">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="e6e72-296"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-296"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="e6e72-297">Внедрение служб для проверки в методе `IsValid` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="e6e72-297">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="e6e72-298">Настраиваемые атрибуты класса проверки</span><span class="sxs-lookup"><span data-stu-id="e6e72-298">Custom validation class attributes</span></span>

<span data-ttu-id="e6e72-299">Имена настраиваемых классов проверки полезны при интеграции с платформами CSS, такими как [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e6e72-299">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="e6e72-300">Чтобы указать имена настраиваемых классов проверки, создайте класс, производный от `FieldCssClassProvider`, и задайте класс в экземпляре <xref:Microsoft.AspNetCore.Components.Forms.EditContext>:</span><span class="sxs-lookup"><span data-stu-id="e6e72-300">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="e6e72-301">Пакет проверки заметок к данным в Blazor</span><span class="sxs-lookup"><span data-stu-id="e6e72-301">Blazor data annotations validation package</span></span>

<span data-ttu-id="e6e72-302">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) — это пакет, который выполняет проверку пропусков заполнения с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-302">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="e6e72-303">В настоящее время пакет является *экспериментальным*.</span><span class="sxs-lookup"><span data-stu-id="e6e72-303">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="e6e72-304">Пакет [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) имеет последнюю версию *RC* на [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). В настоящее время продолжайте использовать *экспериментальный* пакет RC.</span><span class="sxs-lookup"><span data-stu-id="e6e72-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="e6e72-305">В будущем выпуске сборка пакета может быть перемещена в платформу или в среду выполнения.</span><span class="sxs-lookup"><span data-stu-id="e6e72-305">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="e6e72-306">Дополнительные обновления смотрите в [репозитории объявлений GitHub](https://github.com/aspnet/Announcements), [репозитории GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore) или в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="e6e72-306">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a><span data-ttu-id="e6e72-307">Атрибут `[CompareProperty]`</span><span class="sxs-lookup"><span data-stu-id="e6e72-307">`[CompareProperty]` attribute</span></span>

<span data-ttu-id="e6e72-308"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> плохо работает с компонентом <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>, так как он не связывает результат проверки с конкретным элементом.</span><span class="sxs-lookup"><span data-stu-id="e6e72-308">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="e6e72-309">Это может привести к несогласованному поведению при проверке на уровне полей и при проверке всей модели при отправке.</span><span class="sxs-lookup"><span data-stu-id="e6e72-309">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="e6e72-310">*Экспериментальный* пакет [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) содержит дополнительный атрибут проверки `ComparePropertyAttribute`, который обходит эти ограничения.</span><span class="sxs-lookup"><span data-stu-id="e6e72-310">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e6e72-311">В приложении Blazor объект `[CompareProperty]` является непосредственной заменой атрибута [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="e6e72-311">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="e6e72-312">Вложенные модели, типы коллекций и сложные типы</span><span class="sxs-lookup"><span data-stu-id="e6e72-312">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="e6e72-313">Blazor обеспечивает поддержку проверки входных данных формы с помощью заметок к данным со встроенным <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-313">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="e6e72-314">Однако <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> проверяет только свойства верхнего уровня модели, привязанной к форме, которые не являются свойствами типа коллекции или сложного типа.</span><span class="sxs-lookup"><span data-stu-id="e6e72-314">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="e6e72-315">Чтобы проверить весь граф объектов привязанной модели, включая свойства типа коллекции и сложного типа, используйте `ObjectGraphDataAnnotationsValidator`, предоставляемый *экспериментальным* пакетом [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):</span><span class="sxs-lookup"><span data-stu-id="e6e72-315">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="e6e72-316">Делайте заметки для свойств модели с помощью `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-316">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="e6e72-317">В следующих классах модели класс `ShipDescription` содержит дополнительные заметки к данным для проверки, когда модель привязана к форме:</span><span class="sxs-lookup"><span data-stu-id="e6e72-317">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="e6e72-318">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-318">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="e6e72-319">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="e6e72-319">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="e6e72-320">Предоставление кнопки "Отправить" на основе проверки формы</span><span class="sxs-lookup"><span data-stu-id="e6e72-320">Enable the submit button based on form validation</span></span>

<span data-ttu-id="e6e72-321">Чтобы включить или отключить кнопку "Отправить" на основе проверки формы, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e6e72-321">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="e6e72-322">Используйте <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы, чтобы назначить модель при инициализации компонента.</span><span class="sxs-lookup"><span data-stu-id="e6e72-322">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="e6e72-323">Проверьте форму в обратном вызове <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> контекста, чтобы включить и отключить кнопку "Отправить".</span><span class="sxs-lookup"><span data-stu-id="e6e72-323">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="e6e72-324">Отсоедините обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="e6e72-324">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="e6e72-325">Дополнительные сведения см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-325">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="e6e72-326">При использовании <xref:Microsoft.AspNetCore.Components.Forms.EditContext> не следует также назначать <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> классу <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-326">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="e6e72-327">В примерах выше установите для параметра `formInvalid` значение `false`, если:</span><span class="sxs-lookup"><span data-stu-id="e6e72-327">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="e6e72-328">Форма предварительно загружена с допустимыми значениями по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e6e72-328">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="e6e72-329">Вы хотите, чтобы кнопка отправки была включена при загрузке формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-329">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="e6e72-330">Побочным эффектом предыдущего подхода является то, что компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> заполняется недопустимыми полями после того, как пользователь взаимодействует с одним полем.</span><span class="sxs-lookup"><span data-stu-id="e6e72-330">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="e6e72-331">Этот сценарий можно решить одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="e6e72-331">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="e6e72-332">Не используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> в форме.</span><span class="sxs-lookup"><span data-stu-id="e6e72-332">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="e6e72-333">Сделайте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> видимым при нажатии кнопки "Отправить" (например, в методе `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="e6e72-333">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="e6e72-334">Диагностика</span><span class="sxs-lookup"><span data-stu-id="e6e72-334">Troubleshoot</span></span>

> <span data-ttu-id="e6e72-335">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both (Для EditForm требуется указать параметр Model или EditContext, но не оба).</span><span class="sxs-lookup"><span data-stu-id="e6e72-335">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="e6e72-336">Убедитесь, что <xref:Microsoft.AspNetCore.Components.Forms.EditForm> имеет <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **или** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="e6e72-336">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="e6e72-337">Не используйте оба варианта для одной и той же формы.</span><span class="sxs-lookup"><span data-stu-id="e6e72-337">Don't use both for the same form.</span></span>

<span data-ttu-id="e6e72-338">При назначении <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> форме убедитесь, что создается экземпляр типа модели, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e6e72-338">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="e6e72-339">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e6e72-339">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
