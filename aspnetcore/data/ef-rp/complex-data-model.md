---
title: Часть 5. Razor Pages с EF Core в ASP.NET Core — модель данных
author: rick-anderson
description: Часть 5 серии руководств по Razor Pages и Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 55269c6985534b49cc2567b2d197e46d9b7b1fd7
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722531"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="861b6-103">Часть 5. Razor Pages с EF Core в ASP.NET Core — модель данных</span><span class="sxs-lookup"><span data-stu-id="861b6-103">Part 5, Razor Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="861b6-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="861b6-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="861b6-105">Предыдущие руководства работали с базовой моделью данных, состоящей из трех сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="861b6-106">В этом руководстве:</span><span class="sxs-lookup"><span data-stu-id="861b6-106">In this tutorial:</span></span>

* <span data-ttu-id="861b6-107">Добавляются дополнительные сущности и связи.</span><span class="sxs-lookup"><span data-stu-id="861b6-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="861b6-108">Настраивается модель данных с помощью указания правил для форматирования, проверки и сопоставления базы данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="861b6-109">Готовая модель данных показана на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="861b6-109">The completed data model is shown in the following illustration:</span></span>

![Схема сущностей](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="861b6-111">Сущность Student</span><span class="sxs-lookup"><span data-stu-id="861b6-111">The Student entity</span></span>

![Сущность Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="861b6-113">Замените код в файле *Models/Student.cs* на следующий:</span><span class="sxs-lookup"><span data-stu-id="861b6-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="861b6-114">В приведенном выше коде добавляется свойство `FullName` и добавляются следующие атрибуты к существующим свойствам:</span><span class="sxs-lookup"><span data-stu-id="861b6-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="861b6-115">Вычисляемое свойство FullName</span><span class="sxs-lookup"><span data-stu-id="861b6-115">The FullName calculated property</span></span>

<span data-ttu-id="861b6-116">`FullName` — это вычисляемое свойство, которое возвращает значение, созданное путем объединения двух других свойств.</span><span class="sxs-lookup"><span data-stu-id="861b6-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="861b6-117">`FullName` не может быть задано, поэтому имеет только метод доступа get.</span><span class="sxs-lookup"><span data-stu-id="861b6-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="861b6-118">В базе данных не создается столбец `FullName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="861b6-119">Атрибут DataType</span><span class="sxs-lookup"><span data-stu-id="861b6-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="861b6-120">Сейчас для дат зачисления учащихся на всех страницах отображаются время и дата, хотя важна только дата.</span><span class="sxs-lookup"><span data-stu-id="861b6-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="861b6-121">Используя атрибуты заметок к данным, вы можете внести в код одно изменение, позволяющее исправить формат отображения на каждой странице, где отображаются эти данные.</span><span class="sxs-lookup"><span data-stu-id="861b6-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="861b6-122">Атрибут [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) указывает тип данных более точно по сравнению со встроенным типом базы данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="861b6-123">В этом случае следует отображать отобразить только дату, а не дату и время.</span><span class="sxs-lookup"><span data-stu-id="861b6-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="861b6-124">В [перечислении DataType](/dotnet/api/system.componentmodel.dataannotations.datatype) представлено множество типов данных, таких как Date, Time, PhoneNumber, Currency, EmailAddress и других. Атрибут `DataType` также обеспечивает автоматическое предоставление функций для определенных типов в приложении.</span><span class="sxs-lookup"><span data-stu-id="861b6-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="861b6-125">Пример:</span><span class="sxs-lookup"><span data-stu-id="861b6-125">For example:</span></span>

* <span data-ttu-id="861b6-126">Ссылка `mailto:` для `DataType.EmailAddress` создается автоматически.</span><span class="sxs-lookup"><span data-stu-id="861b6-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="861b6-127">Средство выбора даты предоставляется для `DataType.Date` в большинстве браузеров.</span><span class="sxs-lookup"><span data-stu-id="861b6-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="861b6-128">Атрибут `DataType` создает атрибуты HTML 5 `data-`.</span><span class="sxs-lookup"><span data-stu-id="861b6-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="861b6-129">Атрибуты `DataType` не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="861b6-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="861b6-130">Атрибут DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="861b6-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="861b6-131">`DataType.Date` не задает формат отображаемой даты.</span><span class="sxs-lookup"><span data-stu-id="861b6-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="861b6-132">По умолчанию поле даты отображается с использованием форматов, установленных в [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) сервера.</span><span class="sxs-lookup"><span data-stu-id="861b6-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="861b6-133">С помощью атрибута `DisplayFormat` можно явно указать формат даты:</span><span class="sxs-lookup"><span data-stu-id="861b6-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="861b6-134">Параметр `ApplyFormatInEditMode` указывает, что формат должен применяться к пользовательскому интерфейсу редактирования.</span><span class="sxs-lookup"><span data-stu-id="861b6-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="861b6-135">Некоторым полям не следует использовать `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="861b6-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="861b6-136">Например, обозначение денежной единицы в общем случае не должно отображаться в поле редактирования текста.</span><span class="sxs-lookup"><span data-stu-id="861b6-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="861b6-137">Атрибут `DisplayFormat` можно использовать отдельно.</span><span class="sxs-lookup"><span data-stu-id="861b6-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="861b6-138">Однако чаще всего `DataType` рекомендуется применять вместе с атрибутом `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="861b6-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="861b6-139">Атрибут `DataType` передает семантику данных (в отличие от способа их вывода на экран).</span><span class="sxs-lookup"><span data-stu-id="861b6-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="861b6-140">Атрибут `DataType` дает следующие преимущества, недоступные в `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="861b6-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="861b6-141">Поддержка функций HTML5 в браузере.</span><span class="sxs-lookup"><span data-stu-id="861b6-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="861b6-142">Например, отображение элемента управления календарем, соответствующего языковому стандарту обозначения денежной единицы, ссылок электронной почты, проверки входных данных на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="861b6-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="861b6-143">По умолчанию формат отображения данных в браузере определяется в соответствии с установленным языковым стандартом.</span><span class="sxs-lookup"><span data-stu-id="861b6-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="861b6-144">Дополнительные сведения см. в документации по [вспомогательной функции тегов \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="861b6-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="861b6-145">Атрибут StringLength</span><span class="sxs-lookup"><span data-stu-id="861b6-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="861b6-146">С помощью атрибутов можно указать правила проверки данных и сообщения об ошибках проверки.</span><span class="sxs-lookup"><span data-stu-id="861b6-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="861b6-147">Атрибут [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) указывает минимальную и максимальную длину символов, разрешенных в поле данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="861b6-148">Представленный код задает ограничение длины имен в 50 символов.</span><span class="sxs-lookup"><span data-stu-id="861b6-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="861b6-149">Пример, в котором задается минимальная длина строки, приводится [далее](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="861b6-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="861b6-150">Атрибут `StringLength` также обеспечивает проверку на стороне клиента и на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="861b6-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="861b6-151">Минимальное значение не оказывает влияния на схему базы данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="861b6-152">Атрибут `StringLength` не запрещает пользователю ввести пробел в качестве имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="861b6-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="861b6-153">Атрибут [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) можно использовать для применения ограничений к входным данным.</span><span class="sxs-lookup"><span data-stu-id="861b6-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="861b6-154">Например, следующий код требует, чтобы первый символ был прописной буквой, а остальные символы были буквенными:</span><span class="sxs-lookup"><span data-stu-id="861b6-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="861b6-156">В **обозревателе объектов SQL Server** (SSOX) откройте конструктор таблиц учащихся, дважды щелкнув таблицу **Student** (Учащийся).</span><span class="sxs-lookup"><span data-stu-id="861b6-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Таблица учащихся в SSOX до миграций](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="861b6-158">На предыдущем изображении показана схемы для таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="861b6-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="861b6-159">Поля имен имеют тип `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="861b6-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="861b6-160">Когда далее в этом учебнике будет создана и применена миграция, поля имен станут `nvarchar(50)` из-за атрибутов длины строки.</span><span class="sxs-lookup"><span data-stu-id="861b6-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="861b6-162">В средстве SQLite изучите определения столбцов для таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="861b6-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="861b6-163">Поля имен имеют тип `Text`.</span><span class="sxs-lookup"><span data-stu-id="861b6-163">The name fields have type `Text`.</span></span> <span data-ttu-id="861b6-164">Обратите внимание, что первое поле имени называется `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="861b6-165">В следующем разделе вы измените имя этого столбца на `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="861b6-166">Атрибут Column</span><span class="sxs-lookup"><span data-stu-id="861b6-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="861b6-167">Атрибуты могут управлять, как именно классы и свойства сопоставляются с базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="861b6-168">В модели `Student` атрибут `Column` используется для сопоставления имени свойства `FirstMidName` с "FirstName" в базе данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="861b6-169">При создании базы данных имена свойств в модели используются для имен столбцов (кроме случая, когда используется атрибут `Column`).</span><span class="sxs-lookup"><span data-stu-id="861b6-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="861b6-170">Модель `Student` использует `FirstMidName` для поля имени, так как это поле также может содержать отчество.</span><span class="sxs-lookup"><span data-stu-id="861b6-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="861b6-171">С атрибутом `[Column]` поле `Student.FirstMidName` в модели данных сопоставляется со столбцом `FirstName` таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="861b6-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="861b6-172">Добавление атрибута `Column` изменяет модель для поддержки `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="861b6-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="861b6-173">Модель, поддерживающая `SchoolContext`, больше не соответствует базе данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="861b6-174">Это несоответствие будет устранено путем добавления миграции далее в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="861b6-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="861b6-175">Атрибут Required</span><span class="sxs-lookup"><span data-stu-id="861b6-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="861b6-176">Атрибут `Required` делает свойства имен обязательными полями.</span><span class="sxs-lookup"><span data-stu-id="861b6-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="861b6-177">Атрибут `Required` не нужен для типов, не допускающих значения NULL, например для типов значений (таких как `DateTime`, `int` и `double`).</span><span class="sxs-lookup"><span data-stu-id="861b6-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="861b6-178">Типы, которые не могут принимать значение null, автоматически обрабатываются как обязательные поля.</span><span class="sxs-lookup"><span data-stu-id="861b6-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="861b6-179">Для применения `MinimumLength` нужно использовать атрибут `Required` с `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="861b6-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="861b6-180">`MinimumLength` и `Required` разрешают использовать пробелы при проверке.</span><span class="sxs-lookup"><span data-stu-id="861b6-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="861b6-181">Используйте атрибут `RegularExpression` для полного контроля над строкой.</span><span class="sxs-lookup"><span data-stu-id="861b6-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="861b6-182">Атрибут Display</span><span class="sxs-lookup"><span data-stu-id="861b6-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="861b6-183">Атрибут `Display` указывает, что заголовки для текстовых полей должны иметь вид "First Name" (Имя), "Last Name" (Фамилия), "Full Name" (Полное имя) и "Enrollment Date" (Дата зачисления).</span><span class="sxs-lookup"><span data-stu-id="861b6-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="861b6-184">По умолчанию в заголовках не использовался пробел для разделения слов, например "Lastname".</span><span class="sxs-lookup"><span data-stu-id="861b6-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="861b6-185">Создание миграции</span><span class="sxs-lookup"><span data-stu-id="861b6-185">Create a migration</span></span>

<span data-ttu-id="861b6-186">Запустите приложение и перейдите на страницу Students.</span><span class="sxs-lookup"><span data-stu-id="861b6-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="861b6-187">Возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="861b6-187">An exception is thrown.</span></span> <span data-ttu-id="861b6-188">Атрибут `[Column]` приводит к тому, что EF ожидает столбец с именем `FirstName`, но имя столбца в базе данных по-прежнему `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="861b6-190">Сообщение об ошибке подобно приведенному ниже.</span><span class="sxs-lookup"><span data-stu-id="861b6-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="861b6-191">В PMC введите следующие команды для создания миграции и обновления базы данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="861b6-192">Первая из этих команд выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="861b6-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="861b6-193">Это предупреждение вызвано тем, что поля имен теперь ограничены 50 символами.</span><span class="sxs-lookup"><span data-stu-id="861b6-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="861b6-194">Если имя в базе данных имеет больше 50 символов, символы с 51-го до последнего будут потеряны.</span><span class="sxs-lookup"><span data-stu-id="861b6-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="861b6-195">Откройте таблицу "Student" (Учащийся) в окне SSOX:</span><span class="sxs-lookup"><span data-stu-id="861b6-195">Open the Student table in SSOX:</span></span>

  ![Таблица учащихся в SSOX после миграций](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="861b6-197">До применения миграции столбцы имен имели тип [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="861b6-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="861b6-198">Теперь столбцы имен имеют тип `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="861b6-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="861b6-199">Имя столбца изменилось с `FirstMidName` на `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="861b6-201">Сообщение об ошибке подобно приведенному ниже.</span><span class="sxs-lookup"><span data-stu-id="861b6-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="861b6-202">Откройте командное окно в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-202">Open a command window in the project folder.</span></span> <span data-ttu-id="861b6-203">Введите следующие команды для создания миграции и обновления базы данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="861b6-204">Команда обновления базы данных выводит ошибку наподобие приведенной ниже.</span><span class="sxs-lookup"><span data-stu-id="861b6-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="861b6-205">В этом учебнике устранить ошибку можно, удалив и заново создав исходную миграцию.</span><span class="sxs-lookup"><span data-stu-id="861b6-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="861b6-206">Дополнительные сведения см. в предупреждении, касающемся SQLite, в начале [учебника по миграции](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="861b6-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="861b6-207">Удалите папку *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="861b6-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="861b6-208">Выполните следующие команды, чтобы удалить базу данных, создать новую исходную миграцию и применить ее:</span><span class="sxs-lookup"><span data-stu-id="861b6-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="861b6-209">Изучите таблицу Student в средстве SQLite.</span><span class="sxs-lookup"><span data-stu-id="861b6-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="861b6-210">Столбец, ранее называвшийся FirstMidName, теперь имеет имя FirstName.</span><span class="sxs-lookup"><span data-stu-id="861b6-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="861b6-211">Запустите приложение и перейдите на страницу Students.</span><span class="sxs-lookup"><span data-stu-id="861b6-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="861b6-212">Обратите внимание, что значения времени не вводятся и не отображаются вместе с датами.</span><span class="sxs-lookup"><span data-stu-id="861b6-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="861b6-213">Выберите **Create New** (Создать) и попробуйте ввести имя длиной более 50 символов.</span><span class="sxs-lookup"><span data-stu-id="861b6-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="861b6-214">В следующих разделах сборка приложения на некоторых этапах приводит к возникновению ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="861b6-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="861b6-215">В инструкциях указано, когда производить сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="861b6-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="861b6-216">Сущность Instructor</span><span class="sxs-lookup"><span data-stu-id="861b6-216">The Instructor Entity</span></span>

![Сущность Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="861b6-218">Создайте файл *Models/Instructor.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="861b6-219">На одной строке могут находиться несколько атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="861b6-220">Атрибуты `HireDate` можно записать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="861b6-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="861b6-221">Свойства навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-221">Navigation properties</span></span>

<span data-ttu-id="861b6-222">`CourseAssignments` и `OfficeAssignment` — это свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="861b6-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="861b6-223">Преподаватель может проводить любое количество курсов, поэтому `CourseAssignments` определен как коллекция.</span><span class="sxs-lookup"><span data-stu-id="861b6-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="861b6-224">Преподаватель может иметь не более одного кабинета, поэтому свойство `OfficeAssignment` содержит одну сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="861b6-225">`OfficeAssignment` имеет значение null, если кабинет не назначен.</span><span class="sxs-lookup"><span data-stu-id="861b6-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="861b6-226">Сущность OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="861b6-226">The OfficeAssignment entity</span></span>

![Сущность OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="861b6-228">Создайте файл *Models/OfficeAssignment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="861b6-229">Атрибут Key</span><span class="sxs-lookup"><span data-stu-id="861b6-229">The Key attribute</span></span>

<span data-ttu-id="861b6-230">Атрибут `[Key]` используется для идентификации свойства в качестве первичного ключа (PK), когда имя свойства отличается от classnameID или ID.</span><span class="sxs-lookup"><span data-stu-id="861b6-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="861b6-231">Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="861b6-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="861b6-232">Назначение кабинета существует только в связи с преподавателем, которому оно назначено.</span><span class="sxs-lookup"><span data-stu-id="861b6-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="861b6-233">Первичный ключ `OfficeAssignment` также является внешним ключом (FK) для сущности `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="861b6-234">EF Core не распознает `InstructorID` в качестве первичного ключа `OfficeAssignment` автоматически, так как `InstructorID` не соответствует соглашению об именовании ID или classnameID.</span><span class="sxs-lookup"><span data-stu-id="861b6-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="861b6-235">Таким образом, атрибут `Key` используется для определения `InstructorID` в качестве первичного ключа:</span><span class="sxs-lookup"><span data-stu-id="861b6-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="861b6-236">По умолчанию EF Core считает ключ созданным не базой данных, так как столбец предназначен для идентифицирующего отношения.</span><span class="sxs-lookup"><span data-stu-id="861b6-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="861b6-237">Свойство навигации Instructor</span><span class="sxs-lookup"><span data-stu-id="861b6-237">The Instructor navigation property</span></span>

<span data-ttu-id="861b6-238">Свойство навигации `Instructor.OfficeAssignment` может иметь значение NULL, так как строка `OfficeAssignment` для преподавателя может отсутствовать.</span><span class="sxs-lookup"><span data-stu-id="861b6-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="861b6-239">Преподавателю может быть не назначен кабинет.</span><span class="sxs-lookup"><span data-stu-id="861b6-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="861b6-240">Свойство навигации `OfficeAssignment.Instructor` всегда будет иметь сущность Instructor, так как тип внешнего ключа `InstructorID` — это тип значения `int`, не допускающий значения NULL.</span><span class="sxs-lookup"><span data-stu-id="861b6-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="861b6-241">Назначение кабинета не может существовать без преподавателя.</span><span class="sxs-lookup"><span data-stu-id="861b6-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="861b6-242">Когда сущность `Instructor` имеет связанную сущность `OfficeAssignment`, каждая из них имеет ссылку на другую в своем свойстве навигации.</span><span class="sxs-lookup"><span data-stu-id="861b6-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="861b6-243">Сущность Course</span><span class="sxs-lookup"><span data-stu-id="861b6-243">The Course Entity</span></span>

![Сущность Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="861b6-245">Обновите *Models/Course.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="861b6-246">Сущность `Course` имеет свойство внешнего ключа (FK) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="861b6-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="861b6-247">`DepartmentID` указывает на связанную сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="861b6-248">Сущность `Course` имеет свойство навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="861b6-249">EF Core не требует свойства внешнего ключа для модели данных, если она имеет свойство навигации для связанной сущности.</span><span class="sxs-lookup"><span data-stu-id="861b6-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="861b6-250">EF Core автоматически создает внешние ключи в базе данных по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="861b6-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="861b6-251">EF Core создает [теневые свойства](/ef/core/modeling/shadow-properties) для автоматически созданных внешних ключей.</span><span class="sxs-lookup"><span data-stu-id="861b6-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="861b6-252">Однако явное включение внешнего ключа в модель данных позволяет сделать обновления проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="861b6-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="861b6-253">Например, рассмотрим модель, где свойство внешнего ключа `DepartmentID`*не* включено.</span><span class="sxs-lookup"><span data-stu-id="861b6-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="861b6-254">При получении сущности курса для редактирования:</span><span class="sxs-lookup"><span data-stu-id="861b6-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="861b6-255">свойство `Department` имеет значение NULL, если оно не загружено явно;</span><span class="sxs-lookup"><span data-stu-id="861b6-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="861b6-256">для обновления сущности курса нужно сначала получить сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="861b6-257">Если свойство внешнего ключа `DepartmentID` включено в модель данных, получать сущность `Department` перед обновлением не нужно.</span><span class="sxs-lookup"><span data-stu-id="861b6-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="861b6-258">Атрибут DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="861b6-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="861b6-259">Атрибут `[DatabaseGenerated(DatabaseGeneratedOption.None)]` указывает, что первичный ключ предоставляется приложением, а не создается базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="861b6-260">По умолчанию EF Core предполагает, что значения первичного ключа создаются базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="861b6-261">Обычно лучше всего использовать значения, созданные базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="861b6-262">Для сущностей `Course` пользователь указывает первичный ключ.</span><span class="sxs-lookup"><span data-stu-id="861b6-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="861b6-263">Например, номер курса, такой как серия 1000 для кафедры математики и серия 2000 для кафедры английского языка.</span><span class="sxs-lookup"><span data-stu-id="861b6-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="861b6-264">Атрибут `DatabaseGenerated` также может использоваться для создания значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="861b6-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="861b6-265">Например, база данных может автоматически создать поле даты для записи даты, когда строка была создана или изменена.</span><span class="sxs-lookup"><span data-stu-id="861b6-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="861b6-266">Дополнительные сведения см. в разделе [Созданные свойства](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="861b6-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="861b6-267">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="861b6-268">Свойства внешнего ключа (FK) и свойства навигации в сущности `Course` отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="861b6-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="861b6-269">Курс назначается одной кафедре, поэтому имеется внешний ключ `DepartmentID` и свойство навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="861b6-270">На курс может быть зачислено любое количество учащихся, поэтому свойство навигации `Enrollments` является коллекцией:</span><span class="sxs-lookup"><span data-stu-id="861b6-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="861b6-271">Курс могут вести несколько преподавателей, поэтому свойство навигации `CourseAssignments` является коллекцией:</span><span class="sxs-lookup"><span data-stu-id="861b6-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="861b6-272">`CourseAssignment` описано [далее](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="861b6-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="861b6-273">Сущность Department</span><span class="sxs-lookup"><span data-stu-id="861b6-273">The Department entity</span></span>

![Сущность Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="861b6-275">Создайте файл *Models/Department.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="861b6-276">Атрибут Column</span><span class="sxs-lookup"><span data-stu-id="861b6-276">The Column attribute</span></span>

<span data-ttu-id="861b6-277">Ранее атрибут `Column` использовался, чтобы изменить сопоставление имени столбца.</span><span class="sxs-lookup"><span data-stu-id="861b6-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="861b6-278">В коде для сущности `Department` атрибут `Column` можно использовать, чтобы изменить сопоставление типов данных SQL.</span><span class="sxs-lookup"><span data-stu-id="861b6-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="861b6-279">Столбец `Budget` определяется с помощью типа money SQL Server в базе данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="861b6-280">Сопоставление столбцов обычно не требуется.</span><span class="sxs-lookup"><span data-stu-id="861b6-280">Column mapping is generally not required.</span></span> <span data-ttu-id="861b6-281">EF Core выбирает соответствующий тип данных SQL Server на основе типа среды CLR для свойства.</span><span class="sxs-lookup"><span data-stu-id="861b6-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="861b6-282">Тип `decimal` среды CLR сопоставляется с типом `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="861b6-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="861b6-283">`Budget` используется для денежных единиц, хотя для этого лучше подходит тип данных money.</span><span class="sxs-lookup"><span data-stu-id="861b6-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="861b6-284">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="861b6-285">Свойства внешнего ключа и навигации отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="861b6-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="861b6-286">Кафедра может иметь или не иметь администратора.</span><span class="sxs-lookup"><span data-stu-id="861b6-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="861b6-287">Администратор всегда является преподавателем.</span><span class="sxs-lookup"><span data-stu-id="861b6-287">An administrator is always an instructor.</span></span> <span data-ttu-id="861b6-288">Поэтому свойство `InstructorID` включается в качестве внешнего ключа в сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="861b6-289">Свойство навигации называется `Administrator`, но содержит сущность `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="861b6-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="861b6-290">Вопросительный знак (?) в приведенном выше коде указывает, что свойство допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="861b6-291">Кафедра может иметь несколько курсов, поэтому доступно свойство навигации Courses:</span><span class="sxs-lookup"><span data-stu-id="861b6-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="861b6-292">По соглашению EF Core разрешает каскадное удаление для внешних ключей, не допускающих значение null, и связей "многие ко многим".</span><span class="sxs-lookup"><span data-stu-id="861b6-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="861b6-293">Это поведение по умолчанию может привести к циклическим правилам каскадного удаления.</span><span class="sxs-lookup"><span data-stu-id="861b6-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="861b6-294">Такие правила вызывают исключение при добавлении миграции.</span><span class="sxs-lookup"><span data-stu-id="861b6-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="861b6-295">Например, если свойство `Department.InstructorID` было определено как не допускающее значения NULL, EF Core настроит правило каскадного удаления.</span><span class="sxs-lookup"><span data-stu-id="861b6-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="861b6-296">В этом случае кафедра будет удалена, если будет удален преподаватель, назначенный ее администратором.</span><span class="sxs-lookup"><span data-stu-id="861b6-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="861b6-297">В такой ситуации правило ограничения будет более целесообразным.</span><span class="sxs-lookup"><span data-stu-id="861b6-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="861b6-298">Приведенный ниже [текучий API](#fluent-api-alternative-to-attributes) задает правило ограничения и отключает правило каскадного удаления.</span><span class="sxs-lookup"><span data-stu-id="861b6-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="861b6-299">Сущность Enrollment</span><span class="sxs-lookup"><span data-stu-id="861b6-299">The Enrollment entity</span></span>

<span data-ttu-id="861b6-300">Запись зачисления обозначает один курс, который проходит один учащийся.</span><span class="sxs-lookup"><span data-stu-id="861b6-300">An enrollment record is for one course taken by one student.</span></span>

![Сущность Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="861b6-302">Обновите *Models/Enrollment.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="861b6-303">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="861b6-304">Свойства внешнего ключа и навигации отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="861b6-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="861b6-305">Запись зачисления предназначена для одного курса, поэтому доступно свойство первичного ключа `CourseID` и свойство навигации `Course`:</span><span class="sxs-lookup"><span data-stu-id="861b6-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="861b6-306">Запись зачисления предназначена для одного учащегося, поэтому доступно свойство первичного ключа `StudentID` и свойство навигации `Student`:</span><span class="sxs-lookup"><span data-stu-id="861b6-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="861b6-307">Связи многие ко многим</span><span class="sxs-lookup"><span data-stu-id="861b6-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="861b6-308">Между сущностями `Student` и `Course` действует связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="861b6-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="861b6-309">Сущности `Enrollment` выступают в качестве таблицы соединения многие ко многим *с полезными данными* в базе данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="861b6-310">Фраза "с полезными данными" означает, что таблица `Enrollment` содержит дополнительные данные, кроме внешних ключей для присоединяемых таблиц (в данном случае — первичный ключ и `Grade`).</span><span class="sxs-lookup"><span data-stu-id="861b6-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="861b6-311">На следующем рисунке показано, как выглядят эти связи на схеме сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="861b6-312">(Эта схема была создана с помощью [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) для EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="861b6-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="861b6-313">Создание схемы не является частью руководства.)</span><span class="sxs-lookup"><span data-stu-id="861b6-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Связь многие ко многим между Student и Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="861b6-315">Каждая линия связи имеет 1 на одном конце и звездочку (\*) на другом, указывая характер один ко многим.</span><span class="sxs-lookup"><span data-stu-id="861b6-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="861b6-316">Если таблица `Enrollment` не включала в себя сведения об оценках, ей потребуется содержать всего два внешних ключа (`CourseID` и `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="861b6-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="861b6-317">Таблицу соединения многие ко многим без полезных данных иногда называют чистой таблицей соединения (PJT).</span><span class="sxs-lookup"><span data-stu-id="861b6-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="861b6-318">Сущности `Instructor` и `Course` имеют связь многие ко многим с использованием чистой таблицы соединения.</span><span class="sxs-lookup"><span data-stu-id="861b6-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="861b6-319">Примечание. EF 6.x поддерживает неявные таблицы соединения для связей многие ко многим, но EF Core — нет.</span><span class="sxs-lookup"><span data-stu-id="861b6-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="861b6-320">Дополнительные сведения см. в статье [Связи многие ко многим в EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="861b6-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="861b6-321">Сущность CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="861b6-321">The CourseAssignment entity</span></span>

![Сущность CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="861b6-323">Создайте файл *Models/CourseAssignment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="861b6-324">Для связи "многие ко многим" между Instructor и Courses нужна таблица соединения, сущностью для которой является CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="861b6-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="861b6-326">Обычно для сущности соединения используется имя `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="861b6-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="861b6-327">Например, таблицей соединения Instructor и Courses, использующей этот шаблон, будет `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="861b6-328">Однако рекомендуется использовать имя, которое описывает эту связь.</span><span class="sxs-lookup"><span data-stu-id="861b6-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="861b6-329">Модели данных создаются простыми и разрастаются.</span><span class="sxs-lookup"><span data-stu-id="861b6-329">Data models start out simple and grow.</span></span> <span data-ttu-id="861b6-330">Таблицы соединения без полезных данных (PJT) часто начинают включать их.</span><span class="sxs-lookup"><span data-stu-id="861b6-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="861b6-331">Если в начале задать описательное имя сущности, его не нужно менять при изменениях таблицы соединения.</span><span class="sxs-lookup"><span data-stu-id="861b6-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="861b6-332">Оптимально, если сущность соединения имеет собственное естественное имя (возможно, из одного слова) в бизнес-среде.</span><span class="sxs-lookup"><span data-stu-id="861b6-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="861b6-333">Например, Books и Customers можно связать с сущностью соединения Ratings.</span><span class="sxs-lookup"><span data-stu-id="861b6-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="861b6-334">Связь многие ко многим между Instructor и Courses `CourseAssignment` является более предпочтительным вариантом, чем `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="861b6-335">Составной ключ</span><span class="sxs-lookup"><span data-stu-id="861b6-335">Composite key</span></span>

<span data-ttu-id="861b6-336">Два внешних ключа в `CourseAssignment` (`InstructorID` и `CourseID`) совместно однозначно определяют каждую строку таблицы `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="861b6-337">`CourseAssignment` не требуется выделенный первичный ключ.</span><span class="sxs-lookup"><span data-stu-id="861b6-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="861b6-338">Свойства `InstructorID` и `CourseID` выступают в качестве составного первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="861b6-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="861b6-339">Единственным способом указать составные первичные ключи для EF Core является *текучий API*.</span><span class="sxs-lookup"><span data-stu-id="861b6-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="861b6-340">Следующий раздел описывает, как настроить составной первичный ключ.</span><span class="sxs-lookup"><span data-stu-id="861b6-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="861b6-341">Составной ключ обеспечивает следующее:</span><span class="sxs-lookup"><span data-stu-id="861b6-341">The composite key ensures that:</span></span>

* <span data-ttu-id="861b6-342">Для одного курса допускается несколько строк.</span><span class="sxs-lookup"><span data-stu-id="861b6-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="861b6-343">Для одного преподавателя допускается несколько строк.</span><span class="sxs-lookup"><span data-stu-id="861b6-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="861b6-344">Несколько строк для одного преподавателя и курса недопустимы.</span><span class="sxs-lookup"><span data-stu-id="861b6-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="861b6-345">Сущность соединения `Enrollment` определяет собственный первичный ключ, поэтому подобные дубликаты возможны.</span><span class="sxs-lookup"><span data-stu-id="861b6-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="861b6-346">Для предотвращения подобных дубликатов:</span><span class="sxs-lookup"><span data-stu-id="861b6-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="861b6-347">добавьте уникальный индекс для полей внешнего ключа или</span><span class="sxs-lookup"><span data-stu-id="861b6-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="861b6-348">настройте `Enrollment` с первичным составным ключом аналогично `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="861b6-349">Дополнительные сведения см. в разделе [Индексы](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="861b6-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="861b6-350">Обновление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="861b6-350">Update the database context</span></span>

<span data-ttu-id="861b6-351">Обновите файл *Data/SchoolContext.cs*, добавив в него следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="861b6-352">Предыдущий код добавляет новые сущности и настраивает составной первичный ключ сущности `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="861b6-353">Текучий API вместо атрибутов</span><span class="sxs-lookup"><span data-stu-id="861b6-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="861b6-354">Метод `OnModelCreating` в предыдущем коде использует для настройки поведения EF Core *текучий API*.</span><span class="sxs-lookup"><span data-stu-id="861b6-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="861b6-355">Этот API называется "текучим", так как часто используется для объединения серии вызовов методов в один оператор.</span><span class="sxs-lookup"><span data-stu-id="861b6-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="861b6-356">В [следующем коде](/ef/core/modeling/#use-fluent-api-to-configure-a-model) показан пример текучего API:</span><span class="sxs-lookup"><span data-stu-id="861b6-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="861b6-357">В этом учебнике текучий API используется только для сопоставления базы данных, которое невозможно выполнить с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="861b6-358">Однако текучий API позволяет задать большинство правил форматирования, проверки и сопоставления, которые можно указать с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="861b6-359">Некоторые атрибуты, такие как `MinimumLength`, невозможно применить с текучим API.</span><span class="sxs-lookup"><span data-stu-id="861b6-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="861b6-360">`MinimumLength` не изменяет схему, он лишь применяет правило проверки минимальной длины.</span><span class="sxs-lookup"><span data-stu-id="861b6-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="861b6-361">Некоторые разработчики предпочитают использовать текучий API монопольно, чтобы оставить свои классы сущностей "чистыми".</span><span class="sxs-lookup"><span data-stu-id="861b6-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="861b6-362">Атрибуты и текучий API можно смешивать.</span><span class="sxs-lookup"><span data-stu-id="861b6-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="861b6-363">Существует несколько конфигураций, которые можно реализовать только с помощью текучего API (с указанием составного первичного ключа).</span><span class="sxs-lookup"><span data-stu-id="861b6-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="861b6-364">Существует несколько конфигураций, которые можно реализовать только с помощью атрибутов (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="861b6-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="861b6-365">Рекомендации по использованию текучего API и атрибутов:</span><span class="sxs-lookup"><span data-stu-id="861b6-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="861b6-366">Выберите один из двух этих подходов.</span><span class="sxs-lookup"><span data-stu-id="861b6-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="861b6-367">Используйте выбранный подход максимально согласованно.</span><span class="sxs-lookup"><span data-stu-id="861b6-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="861b6-368">Некоторые атрибуты из этого руководства используются:</span><span class="sxs-lookup"><span data-stu-id="861b6-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="861b6-369">только для проверки (например, `MinimumLength`);</span><span class="sxs-lookup"><span data-stu-id="861b6-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="861b6-370">только для конфигурации EF Core (например, `HasKey`);</span><span class="sxs-lookup"><span data-stu-id="861b6-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="861b6-371">для проверки и конфигурации EF Core (например, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="861b6-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="861b6-372">Дополнительные сведения о сравнении атрибутов и текучего API см. в разделе [Методы конфигурации](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="861b6-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="861b6-373">Схема сущностей</span><span class="sxs-lookup"><span data-stu-id="861b6-373">Entity diagram</span></span>

<span data-ttu-id="861b6-374">Ниже показана схема, создаваемая средствами EF Power Tools для завершенной модели School.</span><span class="sxs-lookup"><span data-stu-id="861b6-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Схема сущностей](complex-data-model/_static/diagram.png)

<span data-ttu-id="861b6-376">На предыдущей схеме показано следующее:</span><span class="sxs-lookup"><span data-stu-id="861b6-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="861b6-377">Несколько линий связи один ко многим (1 к \*).</span><span class="sxs-lookup"><span data-stu-id="861b6-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="861b6-378">Линия связи один к нулю или одному (1 к 0..1) между сущностями `Instructor` и `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="861b6-379">Линия связи нуль или один ко многим (0..1 к \*) между сущностями `Instructor` и `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="861b6-380">Заполнение базы данных</span><span class="sxs-lookup"><span data-stu-id="861b6-380">Seed the database</span></span>

<span data-ttu-id="861b6-381">Обновите код в файле *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="861b6-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="861b6-382">Предыдущий код предоставляет начальные данные для новых сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="861b6-383">Основная часть кода создает объекты сущностей и загружает демонстрационные данные.</span><span class="sxs-lookup"><span data-stu-id="861b6-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="861b6-384">Демонстрационные данные используются для проверки.</span><span class="sxs-lookup"><span data-stu-id="861b6-384">The sample data is used for testing.</span></span> <span data-ttu-id="861b6-385">См. `Enrollments` и `CourseAssignments`, чтобы ознакомиться с примерами заполнения данными таблиц соединения "многие ко многим".</span><span class="sxs-lookup"><span data-stu-id="861b6-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="861b6-386">Добавление миграции</span><span class="sxs-lookup"><span data-stu-id="861b6-386">Add a migration</span></span>

<span data-ttu-id="861b6-387">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="861b6-389">В PMC выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="861b6-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="861b6-390">Предыдущая команда отображает предупреждение о возможной потере данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="861b6-391">При выполнении команды `database update` возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="861b6-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="861b6-392">В следующем разделе вы узнаете, что делать с этой ошибкой.</span><span class="sxs-lookup"><span data-stu-id="861b6-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="861b6-394">Если добавить миграцию и выполнить команду `database update`, произойдет следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="861b6-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="861b6-395">В следующем разделе вы узнаете, как ее избежать.</span><span class="sxs-lookup"><span data-stu-id="861b6-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="861b6-396">Применение миграции либо удаление и повторное создание</span><span class="sxs-lookup"><span data-stu-id="861b6-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="861b6-397">Теперь у вас есть база данных, и пора подумать о том, как применять к ней изменения.</span><span class="sxs-lookup"><span data-stu-id="861b6-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="861b6-398">В этом руководстве показано два подхода:</span><span class="sxs-lookup"><span data-stu-id="861b6-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="861b6-399">[Удаление и повторное создание базы данных](#drop).</span><span class="sxs-lookup"><span data-stu-id="861b6-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="861b6-400">Выберите этот раздел, если вы используете SQLite.</span><span class="sxs-lookup"><span data-stu-id="861b6-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="861b6-401">[Применение миграции к существующей базе данных](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="861b6-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="861b6-402">Инструкции в этом разделе подходят только для SQL Server, но **не для SQLite**.</span><span class="sxs-lookup"><span data-stu-id="861b6-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="861b6-403">Для SQL Server применимы оба подхода.</span><span class="sxs-lookup"><span data-stu-id="861b6-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="861b6-404">Хотя метод с применением миграции является более сложным и трудоемким, в реальной рабочей среде лучше использовать именно его.</span><span class="sxs-lookup"><span data-stu-id="861b6-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="861b6-405">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="861b6-405">Drop and re-create the database</span></span>

<span data-ttu-id="861b6-406">[Пропустите этот раздел](#apply-the-migration), если вы используете SQL Server и хотите применить миграцию в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="861b6-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="861b6-407">Чтобы в EF Core принудительно создать базу данных, удалить, а затем обновить ее, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="861b6-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="861b6-409">Выполните следующие команды в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="861b6-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="861b6-410">Удалите папку *Migrations*, а затем выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="861b6-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="861b6-412">Откройте командное окно и перейдите в папку проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="861b6-413">Папка проекта содержит файл *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="861b6-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="861b6-414">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="861b6-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="861b6-415">Удалите папку *Migrations*, а затем выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="861b6-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="861b6-416">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="861b6-416">Run the app.</span></span> <span data-ttu-id="861b6-417">При запуске приложения выполняется метод `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="861b6-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="861b6-418">`DbInitializer.Initialize` заполняет новую базу данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="861b6-420">Откройте базу данных в SSOX.</span><span class="sxs-lookup"><span data-stu-id="861b6-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="861b6-421">Если SSOX был открыт ранее, нажмите кнопку **Обновить**.</span><span class="sxs-lookup"><span data-stu-id="861b6-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="861b6-422">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="861b6-422">Expand the **Tables** node.</span></span> <span data-ttu-id="861b6-423">Отображаются созданные таблицы.</span><span class="sxs-lookup"><span data-stu-id="861b6-423">The created tables are displayed.</span></span>

  ![Таблицы в SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="861b6-425">Изучите таблицу **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="861b6-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="861b6-426">Щелкните правой кнопкой мыши таблицу **CourseAssignment** и выберите пункт **Просмотреть данные**.</span><span class="sxs-lookup"><span data-stu-id="861b6-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="861b6-427">Убедитесь, что таблица **CourseAssignment** содержит данные.</span><span class="sxs-lookup"><span data-stu-id="861b6-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Данные CourseAssignment в SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="861b6-430">Используйте средство SQLite для просмотра базы данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="861b6-431">новые таблицы и столбцы;</span><span class="sxs-lookup"><span data-stu-id="861b6-431">New tables and columns.</span></span>
* <span data-ttu-id="861b6-432">заполненные данные в таблицах, например в таблице **CourseAssignment**.</span><span class="sxs-lookup"><span data-stu-id="861b6-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="861b6-433">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="861b6-433">Apply the migration</span></span>

<span data-ttu-id="861b6-434">Этот раздел является необязательным.</span><span class="sxs-lookup"><span data-stu-id="861b6-434">This section is optional.</span></span> <span data-ttu-id="861b6-435">Эти действия подходят только для SQL Server LocalDB и только в том случае, если вы пропустили предыдущий раздел [Удаление и повторное создание базы данных](#drop).</span><span class="sxs-lookup"><span data-stu-id="861b6-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="861b6-436">При выполнении миграций с существующими данными могут действовать ограничения внешнего ключа, которым существующие данные не соответствуют.</span><span class="sxs-lookup"><span data-stu-id="861b6-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="861b6-437">Для рабочих данных нужно предпринять меры по переносу существующих данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="861b6-438">Этот раздел содержит пример того, как устранить нарушения ограничений внешнего ключа.</span><span class="sxs-lookup"><span data-stu-id="861b6-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="861b6-439">Вносите эти изменения кода только после создания резервной копии.</span><span class="sxs-lookup"><span data-stu-id="861b6-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="861b6-440">Не вносите эти изменения в код, если вы выполнили инструкции из предыдущего раздела [Удаление и повторное создание базы данных](#drop).</span><span class="sxs-lookup"><span data-stu-id="861b6-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="861b6-441">Файл *{метка_времени}_ComplexDataModel.cs* содержит следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="861b6-442">Предыдущий код добавляет в таблицу `Course` внешний ключ `DepartmentID`, не допускающий значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="861b6-443">База данных из предыдущего учебника содержит строки в `Course`, поэтому эту таблицу невозможно обновить с помощью миграций.</span><span class="sxs-lookup"><span data-stu-id="861b6-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="861b6-444">Чтобы заставить миграцию `ComplexDataModel` работать с существующими данными:</span><span class="sxs-lookup"><span data-stu-id="861b6-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="861b6-445">Изменить код, чтобы присвоить новому столбцу (`DepartmentID`) значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="861b6-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="861b6-446">Создайте фиктивную кафедру с именем "Temp" для использования по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="861b6-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="861b6-447">Устранение ограничений внешнего ключа</span><span class="sxs-lookup"><span data-stu-id="861b6-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="861b6-448">В классе миграции `Up` обновите метод `ComplexDataModel`:</span><span class="sxs-lookup"><span data-stu-id="861b6-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="861b6-449">Откройте файл *{метка_времени}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="861b6-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="861b6-450">Закомментируйте строку кода, которая добавляет столбец `DepartmentID` в таблицу `Course`.</span><span class="sxs-lookup"><span data-stu-id="861b6-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="861b6-451">Добавьте выделенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="861b6-451">Add the following highlighted code.</span></span> <span data-ttu-id="861b6-452">Новый код идет после блока `.CreateTable( name: "Department"`.</span><span class="sxs-lookup"><span data-stu-id="861b6-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="861b6-453">После внесения описанных выше изменений существующие строки `Course` будут связаны с кафедрой "Temp" после выполнения метода `ComplexDataModel.Up`.</span><span class="sxs-lookup"><span data-stu-id="861b6-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="861b6-454">Инструкции на случай описанной здесь ситуации упрощены в этом учебнике.</span><span class="sxs-lookup"><span data-stu-id="861b6-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="861b6-455">Рабочее приложение:</span><span class="sxs-lookup"><span data-stu-id="861b6-455">A production app would:</span></span>

* <span data-ttu-id="861b6-456">включает код или сценарии, чтобы добавить строки `Department` и связанные строки `Course` к новым строкам `Department`;</span><span class="sxs-lookup"><span data-stu-id="861b6-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="861b6-457">не использует кафедру "Temp" или значение по умолчанию для `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="861b6-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="861b6-459">Выполните следующие команды в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="861b6-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="861b6-460">Так как метод `DbInitializer.Initialize` предназначен для работы только с пустой базой данных, используйте SSOX для удаления всех строк в таблицах Student и Course.</span><span class="sxs-lookup"><span data-stu-id="861b6-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="861b6-461">(К таблице Enrollment будет применено каскадное удаление.)</span><span class="sxs-lookup"><span data-stu-id="861b6-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="861b6-463">Если вы используете SQL Server LocalDB с Visual Studio Code, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="861b6-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="861b6-464">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="861b6-464">Run the app.</span></span> <span data-ttu-id="861b6-465">При запуске приложения выполняется метод `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="861b6-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="861b6-466">`DbInitializer.Initialize` заполняет новую базу данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="861b6-467">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="861b6-467">Next steps</span></span>

<span data-ttu-id="861b6-468">В следующих двух учебниках рассказывается о том, как считывать и обновлять связанные данные.</span><span class="sxs-lookup"><span data-stu-id="861b6-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="861b6-469">[Предыдущий учебник](xref:data/ef-rp/migrations)
> [Следующий учебник](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="861b6-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="861b6-470">Предыдущие руководства работали с базовой моделью данных, состоящей из трех сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="861b6-471">В этом руководстве:</span><span class="sxs-lookup"><span data-stu-id="861b6-471">In this tutorial:</span></span>

* <span data-ttu-id="861b6-472">Добавляются дополнительные сущности и связи.</span><span class="sxs-lookup"><span data-stu-id="861b6-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="861b6-473">Настраивается модель данных с помощью указания правил для форматирования, проверки и сопоставления базы данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="861b6-474">Классы сущностей для готовой модели данных показаны на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="861b6-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Схема сущностей](complex-data-model/_static/diagram.png)

<span data-ttu-id="861b6-476">При возникновении проблем, которые вам не удается устранить, скачайте [готовое приложение](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="861b6-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="861b6-477">Настройка модели данных с использованием атрибутов</span><span class="sxs-lookup"><span data-stu-id="861b6-477">Customize the data model with attributes</span></span>

<span data-ttu-id="861b6-478">В этом разделе модель данных настраивается с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="861b6-479">Атрибут DataType</span><span class="sxs-lookup"><span data-stu-id="861b6-479">The DataType attribute</span></span>

<span data-ttu-id="861b6-480">Страницы учащихся сейчас отображают время и дату зачисления.</span><span class="sxs-lookup"><span data-stu-id="861b6-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="861b6-481">Как правило, поля даты отображают только дату без времени.</span><span class="sxs-lookup"><span data-stu-id="861b6-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="861b6-482">Обновите *Models/Student.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="861b6-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="861b6-483">Атрибут [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) указывает тип данных более точно по сравнению со встроенным типом базы данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="861b6-484">В этом случае следует отображать отобразить только дату, а не дату и время.</span><span class="sxs-lookup"><span data-stu-id="861b6-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="861b6-485">В [перечислении DataType](/dotnet/api/system.componentmodel.dataannotations.datatype) представлено множество типов данных, таких как Date, Time, PhoneNumber, Currency, EmailAddress и других. Атрибут `DataType` также обеспечивает автоматическое предоставление функций для определенных типов в приложении.</span><span class="sxs-lookup"><span data-stu-id="861b6-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="861b6-486">Пример:</span><span class="sxs-lookup"><span data-stu-id="861b6-486">For example:</span></span>

* <span data-ttu-id="861b6-487">Ссылка `mailto:` для `DataType.EmailAddress` создается автоматически.</span><span class="sxs-lookup"><span data-stu-id="861b6-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="861b6-488">Средство выбора даты предоставляется для `DataType.Date` в большинстве браузеров.</span><span class="sxs-lookup"><span data-stu-id="861b6-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="861b6-489">Атрибут `DataType` создает атрибуты HTML 5 `data-`, которые используются браузерами с поддержкой HTML 5.</span><span class="sxs-lookup"><span data-stu-id="861b6-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="861b6-490">Атрибуты `DataType` не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="861b6-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="861b6-491">`DataType.Date` не задает формат отображаемой даты.</span><span class="sxs-lookup"><span data-stu-id="861b6-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="861b6-492">По умолчанию поле даты отображается с использованием форматов, установленных в [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) сервера.</span><span class="sxs-lookup"><span data-stu-id="861b6-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="861b6-493">С помощью атрибута `DisplayFormat` можно явно указать формат даты:</span><span class="sxs-lookup"><span data-stu-id="861b6-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="861b6-494">Параметр `ApplyFormatInEditMode` указывает, что формат должен применяться к пользовательскому интерфейсу редактирования.</span><span class="sxs-lookup"><span data-stu-id="861b6-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="861b6-495">Некоторым полям не следует использовать `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="861b6-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="861b6-496">Например, обозначение денежной единицы в общем случае не должно отображаться в поле редактирования текста.</span><span class="sxs-lookup"><span data-stu-id="861b6-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="861b6-497">Атрибут `DisplayFormat` можно использовать отдельно.</span><span class="sxs-lookup"><span data-stu-id="861b6-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="861b6-498">Однако чаще всего `DataType` рекомендуется применять вместе с атрибутом `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="861b6-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="861b6-499">Атрибут `DataType` передает семантику данных (в отличие от способа их вывода на экран).</span><span class="sxs-lookup"><span data-stu-id="861b6-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="861b6-500">Атрибут `DataType` дает следующие преимущества, недоступные в `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="861b6-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="861b6-501">Поддержка функций HTML5 в браузере.</span><span class="sxs-lookup"><span data-stu-id="861b6-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="861b6-502">Например, отображение элемента управления календарем, соответствующего языковому стандарту обозначения денежной единицы, ссылок электронной почты, проверки входных данных на стороне клиента и т. д.</span><span class="sxs-lookup"><span data-stu-id="861b6-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="861b6-503">По умолчанию формат отображения данных в браузере определяется в соответствии с установленным языковым стандартом.</span><span class="sxs-lookup"><span data-stu-id="861b6-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="861b6-504">Дополнительные сведения см. в документации по [вспомогательной функции тегов \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="861b6-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="861b6-505">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="861b6-505">Run the app.</span></span> <span data-ttu-id="861b6-506">Перейдите на страницу индекса учащихся.</span><span class="sxs-lookup"><span data-stu-id="861b6-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="861b6-507">Время больше не отображается.</span><span class="sxs-lookup"><span data-stu-id="861b6-507">Times are no longer displayed.</span></span> <span data-ttu-id="861b6-508">Каждое представление, использующее модель `Student`, отображает дату без времени.</span><span class="sxs-lookup"><span data-stu-id="861b6-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Страница указателя учащихся с датами без времени](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="861b6-510">Атрибут StringLength</span><span class="sxs-lookup"><span data-stu-id="861b6-510">The StringLength attribute</span></span>

<span data-ttu-id="861b6-511">С помощью атрибутов можно указать правила проверки данных и сообщения об ошибках проверки.</span><span class="sxs-lookup"><span data-stu-id="861b6-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="861b6-512">Атрибут [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) указывает минимальную и максимальную длину символов, разрешенных в поле данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="861b6-513">Атрибут `StringLength` также обеспечивает проверку на стороне клиента и на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="861b6-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="861b6-514">Минимальное значение не оказывает влияния на схему базы данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="861b6-515">Обновите модель`Student`, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="861b6-516">Предыдущий код задает ограничение длины имен в 50 символов.</span><span class="sxs-lookup"><span data-stu-id="861b6-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="861b6-517">Атрибут `StringLength` не запрещает пользователю ввести пробел в качестве имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="861b6-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="861b6-518">Атрибут [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) используется для применения ограничений к входным данным.</span><span class="sxs-lookup"><span data-stu-id="861b6-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="861b6-519">Например, следующий код требует, чтобы первый символ был прописной буквой, а остальные символы были буквенными:</span><span class="sxs-lookup"><span data-stu-id="861b6-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="861b6-520">Запустите приложение:</span><span class="sxs-lookup"><span data-stu-id="861b6-520">Run the app:</span></span>

* <span data-ttu-id="861b6-521">Перейдите на страницу учащихся.</span><span class="sxs-lookup"><span data-stu-id="861b6-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="861b6-522">Выберите **Create New** (Создать) и введите имя длиной более 50 символов.</span><span class="sxs-lookup"><span data-stu-id="861b6-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="861b6-523">Выберите **Create** (Создать), проверка на стороне клиента отображает сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="861b6-523">Select **Create**, client-side validation shows an error message.</span></span>

![Страница указателя учащихся с ошибками длины строки](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="861b6-525">В **обозревателе объектов SQL Server** (SSOX) откройте конструктор таблиц учащихся, дважды щелкнув таблицу **Student** (Учащийся).</span><span class="sxs-lookup"><span data-stu-id="861b6-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Таблица учащихся в SSOX до миграций](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="861b6-527">На предыдущем изображении показана схемы для таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="861b6-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="861b6-528">Поля имен имеют тип `nvarchar(MAX)`, так как миграции для базы данных не выполнялись.</span><span class="sxs-lookup"><span data-stu-id="861b6-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="861b6-529">Когда в дальнейшем миграции будут выполнены, поля имен станут `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="861b6-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="861b6-530">Атрибут Column</span><span class="sxs-lookup"><span data-stu-id="861b6-530">The Column attribute</span></span>

<span data-ttu-id="861b6-531">Атрибуты могут управлять, как именно классы и свойства сопоставляются с базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="861b6-532">В этом разделе атрибут `Column` используется для сопоставления имени свойства `FirstMidName` с "FirstName" в базе данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="861b6-533">При создании базы данных имена свойств в модели используются для имен столбцов (кроме случая, когда используется атрибут `Column`).</span><span class="sxs-lookup"><span data-stu-id="861b6-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="861b6-534">Модель `Student` использует `FirstMidName` для поля имени, так как это поле также может содержать отчество.</span><span class="sxs-lookup"><span data-stu-id="861b6-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="861b6-535">Обновите файл *Student.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="861b6-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="861b6-536">С учетом предыдущего изменения `Student.FirstMidName` в приложении сопоставляется со столбцом `FirstName` таблицы `Student`.</span><span class="sxs-lookup"><span data-stu-id="861b6-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="861b6-537">Добавление атрибута `Column` изменяет модель для поддержки `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="861b6-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="861b6-538">Модель, поддерживающая `SchoolContext`, больше не соответствует базе данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="861b6-539">Если приложение запускается перед применением миграций, возникает следующее исключение:</span><span class="sxs-lookup"><span data-stu-id="861b6-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="861b6-540">Для обновления базы данных сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="861b6-540">To update the DB:</span></span>

* <span data-ttu-id="861b6-541">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-541">Build the project.</span></span>
* <span data-ttu-id="861b6-542">Откройте командное окно в папке проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-542">Open a command window in the project folder.</span></span> <span data-ttu-id="861b6-543">Введите следующие команды для создания миграции и обновления базы данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="861b6-546">Команда `migrations add ColumnFirstName` выдает следующее предупреждающее сообщение:</span><span class="sxs-lookup"><span data-stu-id="861b6-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="861b6-547">Это предупреждение вызвано тем, что поля имен теперь ограничены 50 символами.</span><span class="sxs-lookup"><span data-stu-id="861b6-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="861b6-548">Если имя в базе данных имеет больше 50 символов, символы с 51 до последнего будут потеряны.</span><span class="sxs-lookup"><span data-stu-id="861b6-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="861b6-549">Проверьте работу приложения.</span><span class="sxs-lookup"><span data-stu-id="861b6-549">Test the app.</span></span>

<span data-ttu-id="861b6-550">Откройте таблицу "Student" (Учащийся) в окне SSOX:</span><span class="sxs-lookup"><span data-stu-id="861b6-550">Open the Student table in SSOX:</span></span>

![Таблица учащихся в SSOX после миграций](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="861b6-552">До применения миграции столбцы имен имели тип [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="861b6-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="861b6-553">Теперь столбцы имен имеют тип `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="861b6-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="861b6-554">Имя столбца изменилось с `FirstMidName` на `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="861b6-555">В следующем разделе сборка приложения на некоторых этапах приводит к возникновению ошибок компилятора.</span><span class="sxs-lookup"><span data-stu-id="861b6-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="861b6-556">В инструкциях указано, когда производить сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="861b6-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="861b6-557">Обновление сущности Student</span><span class="sxs-lookup"><span data-stu-id="861b6-557">Student entity update</span></span>

![Сущность Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="861b6-559">Обновите *Models/Student.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="861b6-560">Атрибут Required</span><span class="sxs-lookup"><span data-stu-id="861b6-560">The Required attribute</span></span>

<span data-ttu-id="861b6-561">Атрибут `Required` делает свойства имен обязательными полями.</span><span class="sxs-lookup"><span data-stu-id="861b6-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="861b6-562">Атрибут `Required` не нужен для типов, не допускающих значения null, например для типов значений (`DateTime`, `int`, `double` и т. д.).</span><span class="sxs-lookup"><span data-stu-id="861b6-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="861b6-563">Типы, которые не могут принимать значение null, автоматически обрабатываются как обязательные поля.</span><span class="sxs-lookup"><span data-stu-id="861b6-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="861b6-564">Атрибут `Required` можно заменить параметром минимальной длины в атрибуте `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="861b6-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="861b6-565">Атрибут Display</span><span class="sxs-lookup"><span data-stu-id="861b6-565">The Display attribute</span></span>

<span data-ttu-id="861b6-566">Атрибут `Display` указывает, что заголовки для текстовых полей должны иметь вид "First Name" (Имя), "Last Name" (Фамилия), "Full Name" (Полное имя) и "Enrollment Date" (Дата зачисления).</span><span class="sxs-lookup"><span data-stu-id="861b6-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="861b6-567">По умолчанию в заголовках не использовался пробел для разделения слов, например "Lastname".</span><span class="sxs-lookup"><span data-stu-id="861b6-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="861b6-568">Вычисляемое свойство FullName</span><span class="sxs-lookup"><span data-stu-id="861b6-568">The FullName calculated property</span></span>

<span data-ttu-id="861b6-569">`FullName` — это вычисляемое свойство, которое возвращает значение, созданное путем объединения двух других свойств.</span><span class="sxs-lookup"><span data-stu-id="861b6-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="861b6-570">`FullName` не может быть задано, оно имеет только метод доступа get.</span><span class="sxs-lookup"><span data-stu-id="861b6-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="861b6-571">В базе данных не создается столбец `FullName`.</span><span class="sxs-lookup"><span data-stu-id="861b6-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="861b6-572">Создание сущности Instructor</span><span class="sxs-lookup"><span data-stu-id="861b6-572">Create the Instructor Entity</span></span>

![Сущность Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="861b6-574">Создайте файл *Models/Instructor.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="861b6-575">На одной строке могут находиться несколько атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="861b6-576">Атрибуты `HireDate` можно записать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="861b6-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="861b6-577">Свойства навигации CourseAssignments и OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="861b6-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="861b6-578">`CourseAssignments` и `OfficeAssignment` — это свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="861b6-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="861b6-579">Преподаватель может проводить любое количество курсов, поэтому `CourseAssignments` определен как коллекция.</span><span class="sxs-lookup"><span data-stu-id="861b6-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="861b6-580">Если свойство навигации содержит несколько сущностей:</span><span class="sxs-lookup"><span data-stu-id="861b6-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="861b6-581">Оно должно быть типом списка, где можно добавлять, удалять и обновлять записи.</span><span class="sxs-lookup"><span data-stu-id="861b6-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="861b6-582">К типам свойств навигации относятся следующие:</span><span class="sxs-lookup"><span data-stu-id="861b6-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="861b6-583">Если указан тип `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="861b6-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="861b6-584">Сущность `CourseAssignment` описана в разделе по связям многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="861b6-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="861b6-585">Бизнес-правила университета Contoso указывают, что преподаватель может иметь не более одного кабинета.</span><span class="sxs-lookup"><span data-stu-id="861b6-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="861b6-586">Свойство `OfficeAssignment` содержит отдельную сущность `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="861b6-587">`OfficeAssignment` имеет значение null, если кабинет не назначен.</span><span class="sxs-lookup"><span data-stu-id="861b6-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="861b6-588">Создание сущности OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="861b6-588">Create the OfficeAssignment entity</span></span>

![Сущность OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="861b6-590">Создайте файл *Models/OfficeAssignment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="861b6-591">Атрибут Key</span><span class="sxs-lookup"><span data-stu-id="861b6-591">The Key attribute</span></span>

<span data-ttu-id="861b6-592">Атрибут `[Key]` используется для идентификации свойства в качестве первичного ключа (PK), когда имя свойства отличается от classnameID или ID.</span><span class="sxs-lookup"><span data-stu-id="861b6-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="861b6-593">Между сущностями `Instructor` и `OfficeAssignment` действует связь один к нулю или к одному.</span><span class="sxs-lookup"><span data-stu-id="861b6-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="861b6-594">Назначение кабинета существует только в связи с преподавателем, которому оно назначено.</span><span class="sxs-lookup"><span data-stu-id="861b6-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="861b6-595">Первичный ключ `OfficeAssignment` также является внешним ключом (FK) для сущности `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="861b6-596">EF Core не распознает автоматически `InstructorID` как первичный ключ объекта `OfficeAssignment` по следующей причине:</span><span class="sxs-lookup"><span data-stu-id="861b6-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="861b6-597">`InstructorID` не соблюдает соглашение об именовании ID или classnameID.</span><span class="sxs-lookup"><span data-stu-id="861b6-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="861b6-598">Таким образом, атрибут `Key` используется для определения `InstructorID` в качестве первичного ключа:</span><span class="sxs-lookup"><span data-stu-id="861b6-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="861b6-599">По умолчанию EF Core считает ключ созданным не базой данных, так как столбец предназначен для идентифицирующего отношения.</span><span class="sxs-lookup"><span data-stu-id="861b6-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="861b6-600">Свойство навигации Instructor</span><span class="sxs-lookup"><span data-stu-id="861b6-600">The Instructor navigation property</span></span>

<span data-ttu-id="861b6-601">Свойство навигации `OfficeAssignment` для сущности `Instructor` допускает значение null по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="861b6-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="861b6-602">Ссылочные типы (например, классы) допускают значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="861b6-603">Преподавателю может быть не назначен кабинет.</span><span class="sxs-lookup"><span data-stu-id="861b6-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="861b6-604">Сущность `OfficeAssignment` имеет свойство навигации `Instructor`, не допускающее значение null, по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="861b6-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="861b6-605">`InstructorID` не допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="861b6-606">Назначение кабинета не может существовать без преподавателя.</span><span class="sxs-lookup"><span data-stu-id="861b6-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="861b6-607">Когда сущность `Instructor` имеет связанную сущность `OfficeAssignment`, каждая из них имеет ссылку на другую в своем свойстве навигации.</span><span class="sxs-lookup"><span data-stu-id="861b6-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="861b6-608">Атрибут `[Required]` можно применить к свойству навигации `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="861b6-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="861b6-609">Предыдущий код указывает, что должен существовать связанный преподаватель.</span><span class="sxs-lookup"><span data-stu-id="861b6-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="861b6-610">Этот код не нужен, так как внешний ключ `InstructorID` (который также является первичным) не допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="861b6-611">Изменение сущности Course</span><span class="sxs-lookup"><span data-stu-id="861b6-611">Modify the Course Entity</span></span>

![Сущность Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="861b6-613">Обновите *Models/Course.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="861b6-614">Сущность `Course` имеет свойство внешнего ключа (FK) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="861b6-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="861b6-615">`DepartmentID` указывает на связанную сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="861b6-616">Сущность `Course` имеет свойство навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="861b6-617">EF Core не требует свойство внешнего ключа для модели данных, если она имеет свойство навигации для связанной сущности.</span><span class="sxs-lookup"><span data-stu-id="861b6-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="861b6-618">EF Core автоматически создает внешние ключи в базе данных по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="861b6-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="861b6-619">EF Core создает [теневые свойства](/ef/core/modeling/shadow-properties) для автоматически созданных внешних ключей.</span><span class="sxs-lookup"><span data-stu-id="861b6-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="861b6-620">Наличие внешнего ключа в модели данных позволяет сделать обновления проще и эффективнее.</span><span class="sxs-lookup"><span data-stu-id="861b6-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="861b6-621">Например, рассмотрим модель, где свойство внешнего ключа `DepartmentID`*не* включено.</span><span class="sxs-lookup"><span data-stu-id="861b6-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="861b6-622">При получении сущности курса для редактирования:</span><span class="sxs-lookup"><span data-stu-id="861b6-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="861b6-623">сущность `Department` имеет значение null, если она не загружена явно;</span><span class="sxs-lookup"><span data-stu-id="861b6-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="861b6-624">для обновления сущности курса нужно сначала получить сущность `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="861b6-625">Если свойство внешнего ключа `DepartmentID` включено в модель данных, получать сущность `Department` перед обновлением не нужно.</span><span class="sxs-lookup"><span data-stu-id="861b6-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="861b6-626">Атрибут DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="861b6-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="861b6-627">Атрибут `[DatabaseGenerated(DatabaseGeneratedOption.None)]` указывает, что первичный ключ предоставляется приложением, а не создается базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="861b6-628">По умолчанию EF Core предполагает, что значения первичного ключа создаются базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="861b6-629">Обычно лучше всего использовать значения первичного ключа, созданные базой данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="861b6-630">Для сущностей `Course` пользователь указывает первичный ключ.</span><span class="sxs-lookup"><span data-stu-id="861b6-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="861b6-631">Например, номер курса, такой как серия 1000 для кафедры математики и серия 2000 для кафедры английского языка.</span><span class="sxs-lookup"><span data-stu-id="861b6-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="861b6-632">Атрибут `DatabaseGenerated` также может использоваться для создания значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="861b6-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="861b6-633">Например, база данных может автоматически создать поле даты для записи даты, когда строка была создана или изменена.</span><span class="sxs-lookup"><span data-stu-id="861b6-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="861b6-634">Дополнительные сведения см. в разделе [Созданные свойства](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="861b6-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="861b6-635">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="861b6-636">Свойства внешнего ключа (FK) и свойства навигации в сущности `Course` отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="861b6-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="861b6-637">Курс назначается одной кафедре, поэтому имеется внешний ключ `DepartmentID` и свойство навигации `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="861b6-638">На курс может быть зачислено любое количество учащихся, поэтому свойство навигации `Enrollments` является коллекцией:</span><span class="sxs-lookup"><span data-stu-id="861b6-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="861b6-639">Курс могут вести несколько преподавателей, поэтому свойство навигации `CourseAssignments` является коллекцией:</span><span class="sxs-lookup"><span data-stu-id="861b6-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="861b6-640">`CourseAssignment` описано [далее](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="861b6-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="861b6-641">Создание сущности Department</span><span class="sxs-lookup"><span data-stu-id="861b6-641">Create the Department entity</span></span>

![Сущность Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="861b6-643">Создайте файл *Models/Department.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="861b6-644">Атрибут Column</span><span class="sxs-lookup"><span data-stu-id="861b6-644">The Column attribute</span></span>

<span data-ttu-id="861b6-645">Ранее атрибут `Column` использовался, чтобы изменить сопоставление имени столбца.</span><span class="sxs-lookup"><span data-stu-id="861b6-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="861b6-646">В коде для сущности `Department` атрибут `Column` можно использовать, чтобы изменить сопоставление типов данных SQL.</span><span class="sxs-lookup"><span data-stu-id="861b6-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="861b6-647">Столбец `Budget` определяется с помощью типа money SQL Server в базе данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="861b6-648">Сопоставление столбцов обычно не требуется.</span><span class="sxs-lookup"><span data-stu-id="861b6-648">Column mapping is generally not required.</span></span> <span data-ttu-id="861b6-649">В общем случае EF Core выбирает соответствующий тип данных SQL Server на основе типа среды CLR для свойства.</span><span class="sxs-lookup"><span data-stu-id="861b6-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="861b6-650">Тип `decimal` среды CLR сопоставляется с типом `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="861b6-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="861b6-651">`Budget` используется для денежных единиц, хотя для этого лучше подходит тип данных money.</span><span class="sxs-lookup"><span data-stu-id="861b6-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="861b6-652">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="861b6-653">Свойства внешнего ключа и навигации отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="861b6-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="861b6-654">Кафедра может иметь или не иметь администратора.</span><span class="sxs-lookup"><span data-stu-id="861b6-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="861b6-655">Администратор всегда является преподавателем.</span><span class="sxs-lookup"><span data-stu-id="861b6-655">An administrator is always an instructor.</span></span> <span data-ttu-id="861b6-656">Поэтому свойство `InstructorID` включается в качестве внешнего ключа в сущность `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="861b6-657">Свойство навигации называется `Administrator`, но содержит сущность `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="861b6-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="861b6-658">Вопросительный знак (?) в приведенном выше коде указывает, что свойство допускает значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="861b6-659">Кафедра может иметь несколько курсов, поэтому доступно свойство навигации Courses:</span><span class="sxs-lookup"><span data-stu-id="861b6-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="861b6-660">Примечание. По соглашению EF Core разрешает каскадное удаление для внешних ключей, не допускающих значение null, и связей "многие ко многим".</span><span class="sxs-lookup"><span data-stu-id="861b6-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="861b6-661">Каскадное удаление может привести к циклическим правилам каскадного удаления.</span><span class="sxs-lookup"><span data-stu-id="861b6-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="861b6-662">Такие правила вызывают исключение при добавлении миграции.</span><span class="sxs-lookup"><span data-stu-id="861b6-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="861b6-663">Например, если свойство `Department.InstructorID` было определено как не допускающее значения NULL:</span><span class="sxs-lookup"><span data-stu-id="861b6-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="861b6-664">EF Core настраивает правило каскадного удаления для удаления кафедры при удалении преподавателя.</span><span class="sxs-lookup"><span data-stu-id="861b6-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="861b6-665">Удаление кафедры при удалении преподавателя не является запланированным поведением.</span><span class="sxs-lookup"><span data-stu-id="861b6-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="861b6-666">Следующий [текучий API](#fluent-api-alternative-to-attributes) будет задавать правило ограничения вместо каскада.</span><span class="sxs-lookup"><span data-stu-id="861b6-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="861b6-667">Предыдущий код отключает каскадное удаление для связи кафедры и преподавателя.</span><span class="sxs-lookup"><span data-stu-id="861b6-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="861b6-668">Обновление сущности Enrollment</span><span class="sxs-lookup"><span data-stu-id="861b6-668">Update the Enrollment entity</span></span>

<span data-ttu-id="861b6-669">Запись зачисления обозначает один курс, который проходит один учащийся.</span><span class="sxs-lookup"><span data-stu-id="861b6-669">An enrollment record is for one course taken by one student.</span></span>

![Сущность Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="861b6-671">Обновите *Models/Enrollment.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="861b6-672">Свойства внешнего ключа и навигации</span><span class="sxs-lookup"><span data-stu-id="861b6-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="861b6-673">Свойства внешнего ключа и навигации отражают следующие связи:</span><span class="sxs-lookup"><span data-stu-id="861b6-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="861b6-674">Запись зачисления предназначена для одного курса, поэтому доступно свойство первичного ключа `CourseID` и свойство навигации `Course`:</span><span class="sxs-lookup"><span data-stu-id="861b6-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="861b6-675">Запись зачисления предназначена для одного учащегося, поэтому доступно свойство первичного ключа `StudentID` и свойство навигации `Student`:</span><span class="sxs-lookup"><span data-stu-id="861b6-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="861b6-676">Связи многие ко многим</span><span class="sxs-lookup"><span data-stu-id="861b6-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="861b6-677">Между сущностями `Student` и `Course` действует связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="861b6-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="861b6-678">Сущности `Enrollment` выступают в качестве таблицы соединения многие ко многим *с полезными данными* в базе данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="861b6-679">Фраза "с полезными данными" означает, что таблица `Enrollment` содержит дополнительные данные, кроме внешних ключей для присоединяемых таблиц (в данном случае — первичный ключ и `Grade`).</span><span class="sxs-lookup"><span data-stu-id="861b6-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="861b6-680">На следующем рисунке показано, как выглядят эти связи на схеме сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="861b6-681">(Эта схема была создана с помощью [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) для EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="861b6-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="861b6-682">Создание схемы не является частью руководства.)</span><span class="sxs-lookup"><span data-stu-id="861b6-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Связь многие ко многим между Student и Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="861b6-684">Каждая линия связи имеет 1 на одном конце и звездочку (\*) на другом, указывая характер один ко многим.</span><span class="sxs-lookup"><span data-stu-id="861b6-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="861b6-685">Если таблица `Enrollment` не включала в себя сведения об оценках, ей потребуется содержать всего два внешних ключа (`CourseID` и `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="861b6-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="861b6-686">Таблицу соединения многие ко многим без полезных данных иногда называют чистой таблицей соединения (PJT).</span><span class="sxs-lookup"><span data-stu-id="861b6-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="861b6-687">Сущности `Instructor` и `Course` имеют связь многие ко многим с использованием чистой таблицы соединения.</span><span class="sxs-lookup"><span data-stu-id="861b6-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="861b6-688">Примечание. EF 6.x поддерживает неявные таблицы соединения для связей многие ко многим, но EF Core — нет.</span><span class="sxs-lookup"><span data-stu-id="861b6-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="861b6-689">Дополнительные сведения см. в статье [Связи многие ко многим в EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="861b6-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="861b6-690">Сущность CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="861b6-690">The CourseAssignment entity</span></span>

![Сущность CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="861b6-692">Создайте файл *Models/CourseAssignment.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="861b6-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="861b6-693">Связь между Instructor и Courses</span><span class="sxs-lookup"><span data-stu-id="861b6-693">Instructor-to-Courses</span></span>

![Связь многие ко многим между Instructor и Courses](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="861b6-695">Связь многие ко многим между Instructor и Courses:</span><span class="sxs-lookup"><span data-stu-id="861b6-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="861b6-696">Нуждается в таблице соединения, которая должна быть представлена набором сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="861b6-697">Является чистой таблицей соединения (таблицей без полезных данных).</span><span class="sxs-lookup"><span data-stu-id="861b6-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="861b6-698">Обычно для сущности соединения используется имя `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="861b6-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="861b6-699">Например, таблицей соединения Instructor и Courses, использующей этот шаблон, является `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="861b6-700">Однако рекомендуется использовать имя, которое описывает эту связь.</span><span class="sxs-lookup"><span data-stu-id="861b6-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="861b6-701">Модели данных создаются простыми и разрастаются.</span><span class="sxs-lookup"><span data-stu-id="861b6-701">Data models start out simple and grow.</span></span> <span data-ttu-id="861b6-702">Соединения без полезных данных (PJT) часто начинают включать их.</span><span class="sxs-lookup"><span data-stu-id="861b6-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="861b6-703">Если в начале задать описательное имя сущности, его не нужно менять при изменениях таблицы соединения.</span><span class="sxs-lookup"><span data-stu-id="861b6-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="861b6-704">Оптимально, если сущность соединения имеет собственное естественное имя (возможно, из одного слова) в бизнес-среде.</span><span class="sxs-lookup"><span data-stu-id="861b6-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="861b6-705">Например, Books и Customers можно связать с сущностью соединения Ratings.</span><span class="sxs-lookup"><span data-stu-id="861b6-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="861b6-706">Связь многие ко многим между Instructor и Courses `CourseAssignment` является более предпочтительным вариантом, чем `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="861b6-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="861b6-707">Составной ключ</span><span class="sxs-lookup"><span data-stu-id="861b6-707">Composite key</span></span>

<span data-ttu-id="861b6-708">Внешние ключи не допускают значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-708">FKs are not nullable.</span></span> <span data-ttu-id="861b6-709">Два внешних ключа в `CourseAssignment` (`InstructorID` и `CourseID`) совместно однозначно определяют каждую строку таблицы `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="861b6-710">`CourseAssignment` не требуется выделенный первичный ключ.</span><span class="sxs-lookup"><span data-stu-id="861b6-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="861b6-711">Свойства `InstructorID` и `CourseID` выступают в качестве составного первичного ключа.</span><span class="sxs-lookup"><span data-stu-id="861b6-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="861b6-712">Единственным способом указать составные первичные ключи для EF Core является *текучий API*.</span><span class="sxs-lookup"><span data-stu-id="861b6-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="861b6-713">Следующий раздел описывает, как настроить составной первичный ключ.</span><span class="sxs-lookup"><span data-stu-id="861b6-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="861b6-714">Составной ключ обеспечивает следующее:</span><span class="sxs-lookup"><span data-stu-id="861b6-714">The composite key ensures:</span></span>

* <span data-ttu-id="861b6-715">Для одного курса допускается несколько строк.</span><span class="sxs-lookup"><span data-stu-id="861b6-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="861b6-716">Для одного преподавателя допускается несколько строк.</span><span class="sxs-lookup"><span data-stu-id="861b6-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="861b6-717">Несколько строк для одного преподавателя и курса недопустимы.</span><span class="sxs-lookup"><span data-stu-id="861b6-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="861b6-718">Сущность соединения `Enrollment` определяет собственный первичный ключ, поэтому подобные дубликаты возможны.</span><span class="sxs-lookup"><span data-stu-id="861b6-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="861b6-719">Для предотвращения подобных дубликатов:</span><span class="sxs-lookup"><span data-stu-id="861b6-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="861b6-720">добавьте уникальный индекс для полей внешнего ключа или</span><span class="sxs-lookup"><span data-stu-id="861b6-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="861b6-721">настройте `Enrollment` с первичным составным ключом аналогично `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="861b6-722">Дополнительные сведения см. в разделе [Индексы](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="861b6-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="861b6-723">Изменение контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="861b6-723">Update the DB context</span></span>

<span data-ttu-id="861b6-724">Добавьте выделенный ниже код в файл *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="861b6-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="861b6-725">Предыдущий код добавляет новые сущности и настраивает составной первичный ключ сущности `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="861b6-726">Текучий API вместо атрибутов</span><span class="sxs-lookup"><span data-stu-id="861b6-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="861b6-727">Метод `OnModelCreating` в предыдущем коде использует для настройки поведения EF Core *текучий API*.</span><span class="sxs-lookup"><span data-stu-id="861b6-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="861b6-728">Этот API называется "текучим", так как часто используется для объединения серии вызовов методов в один оператор.</span><span class="sxs-lookup"><span data-stu-id="861b6-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="861b6-729">В [следующем коде](/ef/core/modeling/#use-fluent-api-to-configure-a-model) показан пример текучего API:</span><span class="sxs-lookup"><span data-stu-id="861b6-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="861b6-730">В этом руководстве текучий API используется только для сопоставления базы данных, которое невозможно выполнить с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="861b6-731">Однако текучий API позволяет задать большинство правил форматирования, проверки и сопоставления, которые можно указать с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="861b6-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="861b6-732">Некоторые атрибуты, такие как `MinimumLength`, невозможно применить с текучим API.</span><span class="sxs-lookup"><span data-stu-id="861b6-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="861b6-733">`MinimumLength` не изменяет схему, он лишь применяет правило проверки минимальной длины.</span><span class="sxs-lookup"><span data-stu-id="861b6-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="861b6-734">Некоторые разработчики предпочитают использовать текучий API монопольно, чтобы оставить свои классы сущностей "чистыми".</span><span class="sxs-lookup"><span data-stu-id="861b6-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="861b6-735">Атрибуты и текучий API можно смешивать.</span><span class="sxs-lookup"><span data-stu-id="861b6-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="861b6-736">Существует несколько конфигураций, которые можно реализовать только с помощью текучего API (с указанием составного первичного ключа).</span><span class="sxs-lookup"><span data-stu-id="861b6-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="861b6-737">Существует несколько конфигураций, которые можно реализовать только с помощью атрибутов (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="861b6-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="861b6-738">Рекомендации по использованию текучего API и атрибутов:</span><span class="sxs-lookup"><span data-stu-id="861b6-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="861b6-739">Выберите один из двух этих подходов.</span><span class="sxs-lookup"><span data-stu-id="861b6-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="861b6-740">Используйте выбранный подход максимально согласованно.</span><span class="sxs-lookup"><span data-stu-id="861b6-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="861b6-741">Некоторые атрибуты из этого руководства используются:</span><span class="sxs-lookup"><span data-stu-id="861b6-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="861b6-742">только для проверки (например, `MinimumLength`);</span><span class="sxs-lookup"><span data-stu-id="861b6-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="861b6-743">только для конфигурации EF Core (например, `HasKey`);</span><span class="sxs-lookup"><span data-stu-id="861b6-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="861b6-744">для проверки и конфигурации EF Core (например, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="861b6-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="861b6-745">Дополнительные сведения о сравнении атрибутов и текучего API см. в разделе [Методы конфигурации](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="861b6-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="861b6-746">Схема сущностей, показывающая связи</span><span class="sxs-lookup"><span data-stu-id="861b6-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="861b6-747">Ниже показана схема, создаваемая средствами EF Power Tools для завершенной модели School.</span><span class="sxs-lookup"><span data-stu-id="861b6-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Схема сущностей](complex-data-model/_static/diagram.png)

<span data-ttu-id="861b6-749">На предыдущей схеме показано следующее:</span><span class="sxs-lookup"><span data-stu-id="861b6-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="861b6-750">Несколько линий связи один ко многим (1 к \*).</span><span class="sxs-lookup"><span data-stu-id="861b6-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="861b6-751">Линия связи один к нулю или одному (1 к 0..1) между сущностями `Instructor` и `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="861b6-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="861b6-752">Линия связи нуль или один ко многим (0..1 к \*) между сущностями `Instructor` и `Department`.</span><span class="sxs-lookup"><span data-stu-id="861b6-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="861b6-753">Заполнение базы данных тестовыми данными</span><span class="sxs-lookup"><span data-stu-id="861b6-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="861b6-754">Обновите код в файле *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="861b6-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="861b6-755">Предыдущий код предоставляет начальные данные для новых сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="861b6-756">Основная часть кода создает объекты сущностей и загружает демонстрационные данные.</span><span class="sxs-lookup"><span data-stu-id="861b6-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="861b6-757">Демонстрационные данные используются для проверки.</span><span class="sxs-lookup"><span data-stu-id="861b6-757">The sample data is used for testing.</span></span> <span data-ttu-id="861b6-758">См. `Enrollments` и `CourseAssignments`, чтобы ознакомиться с примерами заполнения данными таблиц соединения "многие ко многим".</span><span class="sxs-lookup"><span data-stu-id="861b6-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="861b6-759">Добавление миграции</span><span class="sxs-lookup"><span data-stu-id="861b6-759">Add a migration</span></span>

<span data-ttu-id="861b6-760">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="861b6-763">Предыдущая команда отображает предупреждение о возможной потере данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="861b6-764">При выполнении команды `database update` возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="861b6-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="861b6-765">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="861b6-765">Apply the migration</span></span>

<span data-ttu-id="861b6-766">Теперь у вас есть база данных, и пора подумать о том, как применять к ней будущие изменения.</span><span class="sxs-lookup"><span data-stu-id="861b6-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="861b6-767">В этом руководстве показано два подхода:</span><span class="sxs-lookup"><span data-stu-id="861b6-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="861b6-768">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="861b6-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="861b6-769">[Применение миграции к существующей базе данных](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="861b6-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="861b6-770">Хотя этот метод является более сложным и трудоемким, в реальной рабочей среде лучше использовать именно его.</span><span class="sxs-lookup"><span data-stu-id="861b6-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="861b6-771">**Примечание**. Это необязательный раздел руководства.</span><span class="sxs-lookup"><span data-stu-id="861b6-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="861b6-772">Вы можете выполнить удаление и повторное создание и пропустить этот раздел.</span><span class="sxs-lookup"><span data-stu-id="861b6-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="861b6-773">Если вы хотите выполнить инструкции в этом разделе, не выполняйте удаление и повторное создание.</span><span class="sxs-lookup"><span data-stu-id="861b6-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="861b6-774">Удаление и повторное создание базы данных</span><span class="sxs-lookup"><span data-stu-id="861b6-774">Drop and re-create the database</span></span>

<span data-ttu-id="861b6-775">Код в обновленном `DbInitializer` предоставляет начальные данные для новых сущностей.</span><span class="sxs-lookup"><span data-stu-id="861b6-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="861b6-776">Чтобы выполнить в EF Core принудительное создание новой базы данных, удаление и обновление базы данных:</span><span class="sxs-lookup"><span data-stu-id="861b6-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="861b6-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="861b6-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="861b6-778">Выполните следующие команды в **консоли диспетчера пакетов** (PMC):</span><span class="sxs-lookup"><span data-stu-id="861b6-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="861b6-779">Чтобы просмотреть справочную информацию, выполните команду `Get-Help about_EntityFrameworkCore` в PMC.</span><span class="sxs-lookup"><span data-stu-id="861b6-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="861b6-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="861b6-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="861b6-781">Откройте командное окно и перейдите в папку проекта.</span><span class="sxs-lookup"><span data-stu-id="861b6-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="861b6-782">Папка проекта содержит файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="861b6-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="861b6-783">Введите в командном окне следующее:</span><span class="sxs-lookup"><span data-stu-id="861b6-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="861b6-784">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="861b6-784">Run the app.</span></span> <span data-ttu-id="861b6-785">При запуске приложения выполняется метод `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="861b6-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="861b6-786">`DbInitializer.Initialize` заполняет новую базу данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="861b6-787">Откройте базу данных в SSOX:</span><span class="sxs-lookup"><span data-stu-id="861b6-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="861b6-788">Если SSOX был открыт ранее, нажмите кнопку **Обновить**.</span><span class="sxs-lookup"><span data-stu-id="861b6-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="861b6-789">Разверните узел **Таблицы**.</span><span class="sxs-lookup"><span data-stu-id="861b6-789">Expand the **Tables** node.</span></span> <span data-ttu-id="861b6-790">Отображаются созданные таблицы.</span><span class="sxs-lookup"><span data-stu-id="861b6-790">The created tables are displayed.</span></span>

![Таблицы в SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="861b6-792">Изучите таблицу **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="861b6-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="861b6-793">Щелкните правой кнопкой мыши таблицу **CourseAssignment** и выберите пункт **Просмотреть данные**.</span><span class="sxs-lookup"><span data-stu-id="861b6-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="861b6-794">Убедитесь, что таблица **CourseAssignment** содержит данные.</span><span class="sxs-lookup"><span data-stu-id="861b6-794">Verify the **CourseAssignment** table contains data.</span></span>

![Данные CourseAssignment в SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="861b6-796">Применение миграции к существующей базе данных</span><span class="sxs-lookup"><span data-stu-id="861b6-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="861b6-797">Этот раздел является необязательным.</span><span class="sxs-lookup"><span data-stu-id="861b6-797">This section is optional.</span></span> <span data-ttu-id="861b6-798">Эти действия подходят только в том случае, если вы пропустили предыдущий раздел [Удаление и повторное создание базы данных](#drop).</span><span class="sxs-lookup"><span data-stu-id="861b6-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="861b6-799">При выполнении миграций с существующими данными могут действовать ограничения внешнего ключа, которым существующие данные не соответствуют.</span><span class="sxs-lookup"><span data-stu-id="861b6-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="861b6-800">Для рабочих данных нужно предпринять меры по переносу существующих данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="861b6-801">Этот раздел содержит пример того, как устранить нарушения ограничений внешнего ключа.</span><span class="sxs-lookup"><span data-stu-id="861b6-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="861b6-802">Вносите эти изменения кода только после создания резервной копии.</span><span class="sxs-lookup"><span data-stu-id="861b6-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="861b6-803">Не вносите эти изменения кода, если вы выполнили инструкции из предыдущего раздела и обновили базу данных.</span><span class="sxs-lookup"><span data-stu-id="861b6-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="861b6-804">Файл *{метка_времени}_ComplexDataModel.cs* содержит следующий код:</span><span class="sxs-lookup"><span data-stu-id="861b6-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="861b6-805">Предыдущий код добавляет в таблицу `Course` внешний ключ `DepartmentID`, не допускающий значение null.</span><span class="sxs-lookup"><span data-stu-id="861b6-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="861b6-806">База данных из предыдущего руководства содержит строки в `Course`, поэтому эту таблицу невозможно обновить с помощью миграций.</span><span class="sxs-lookup"><span data-stu-id="861b6-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="861b6-807">Чтобы заставить миграцию `ComplexDataModel` работать с существующими данными:</span><span class="sxs-lookup"><span data-stu-id="861b6-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="861b6-808">Изменить код, чтобы присвоить новому столбцу (`DepartmentID`) значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="861b6-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="861b6-809">Создайте фиктивную кафедру с именем "Temp" для использования по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="861b6-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="861b6-810">Устранение ограничений внешнего ключа</span><span class="sxs-lookup"><span data-stu-id="861b6-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="861b6-811">Обновите метод `Up` в классах `ComplexDataModel`.</span><span class="sxs-lookup"><span data-stu-id="861b6-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="861b6-812">Откройте файл *{метка_времени}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="861b6-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="861b6-813">Закомментируйте строку кода, которая добавляет столбец `DepartmentID` в таблицу `Course`.</span><span class="sxs-lookup"><span data-stu-id="861b6-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="861b6-814">Добавьте выделенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="861b6-814">Add the following highlighted code.</span></span> <span data-ttu-id="861b6-815">Новый код идет после блока `.CreateTable( name: "Department"`.</span><span class="sxs-lookup"><span data-stu-id="861b6-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="861b6-816">После внесения описанных выше изменений существующие строки `Course` будут связаны с кафедрой "Temp" после выполнения метода `ComplexDataModel` `Up`.</span><span class="sxs-lookup"><span data-stu-id="861b6-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="861b6-817">Рабочее приложение:</span><span class="sxs-lookup"><span data-stu-id="861b6-817">A production app would:</span></span>

* <span data-ttu-id="861b6-818">включает код или сценарии, чтобы добавить строки `Department` и связанные строки `Course` к новым строкам `Department`;</span><span class="sxs-lookup"><span data-stu-id="861b6-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="861b6-819">не использует кафедру "Temp" или значение по умолчанию для `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="861b6-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="861b6-820">Следующее руководство посвящено связанным данным.</span><span class="sxs-lookup"><span data-stu-id="861b6-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="861b6-821">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="861b6-821">Additional resources</span></span>

* [<span data-ttu-id="861b6-822">Версия руководства на YouTube (часть 1)</span><span class="sxs-lookup"><span data-stu-id="861b6-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="861b6-823">Версия руководства на YouTube (часть 2)</span><span class="sxs-lookup"><span data-stu-id="861b6-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="861b6-824">[Назад](xref:data/ef-rp/migrations)
> [Вперед](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="861b6-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
