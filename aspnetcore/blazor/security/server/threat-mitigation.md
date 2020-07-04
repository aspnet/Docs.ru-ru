---
title: Руководство по предотвращению угроз для ASP.NET Core Blazor Server
author: guardrex
description: Узнайте, как устранить угрозы безопасности для приложений Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/05/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: a94dcd818c3f4e19ace57fad6390a84e704192bd
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242970"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>Руководство по предотвращению угроз для ASP.NET Core Blazor Server

Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)

Приложения Blazor Server принимают модель обработки данных *с отслеживанием состояния*, где сервер и клиент поддерживают долгосрочную связь. Устойчивое состояние обеспечивается [каналом](xref:blazor/state-management), который может охватывать соединения, которые также потенциально существуют длительное время.

Когда пользователь посещает сайт Blazor Server, сервер создает канал в памяти сервера. Канал указывает браузеру, какое содержимое подготавливается к просмотру, и реагирует на события, например, когда пользователь нажимает кнопку в пользовательском интерфейсе. Для выполнения этих действий канал вызывает функции JavaScript в браузере пользователя и методах .NET на сервере. Это двустороннее взаимодействие на основе JavaScript называется [взаимодействием с JavaScript (взаимодействием JS)](xref:blazor/call-javascript-from-dotnet).

Поскольку взаимодействие JS происходит через Интернет, а клиент использует удаленный браузер, приложения Blazor Server подвержены большинству проблем безопасности веб-приложений. В этом разделе описываются распространенные угрозы для приложений Blazor Server и предоставляются рекомендации по предотвращению угроз, направленные на приложения, подключенные к Интернету.

В ограниченных средах, например в корпоративных сетях или интрасетях, некоторые рекомендации по устранению рисков либо:

* не применяются в ограниченной среде;
* либо не стоят своих затрат на реализацию, поскольку в ограниченной среде угроза безопасности минимальна.

## <a name="blazor-and-shared-state"></a>Blazor и общее состояние

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>Нехватка ресурсов

Нехватка ресурсов может произойти, когда клиент взаимодействует с сервером и вынуждает сервер чрезмерно потреблять ресурсы. Чрезмерное потребление ресурсов в основном влияет на следующее:

* [ЦП](#cpu)
* [Память](#memory)
* [Клиентские подключения](#client-connections)

Атаки типа "отказ в обслуживании" (DoS) обычно направлены на истощение ресурсов приложения или сервера. Однако нехватка ресурсов не обязательно является результатом атаки на систему. Например, ограниченные ресурсы могут быть исчерпаны из-за высокой потребности пользователей. DoS рассматриваются в разделе [Атаки типа "отказ в обслуживании"](#denial-of-service-dos-attacks).

Ресурсы, внешние по отношению к платформе Blazor, такие как базы данных и дескрипторы файлов (используемые для чтения и записи файлов), могут также испытывать нехватку ресурсов. Для получения дополнительной информации см. <xref:performance/performance-best-practices>.

### <a name="cpu"></a>ЦП

Нехватка ресурсов ЦП может возникать, когда один или несколько клиентов вынуждают сервер интенсивно использовать ЦП.

Например, рассмотрим приложение Blazor Server, которое вычисляет *число Фибоначчи*. Число Фибоначчи формируется из последовательности Фибоначчи, где каждое число в последовательности является суммой двух предыдущих чисел. Объем работы, необходимый для получения результата, зависит от длины последовательности и размера начального значения. Если приложение не применяет ограничения для клиентских запросов, вычисления с интенсивным использованием ресурсов ЦП могут повлиять на время загрузки ЦП и уменьшить производительность выполнения других задач. Чрезмерное потребление ресурсов является проблемой безопасности, влияющей на доступность.

Нехватка ресурсов ЦП очень важна для всех общедоступных приложений. В обычных веб-приложениях время ожидания запросов и соединений является мерой защиты, однако приложения Blazor Server не предоставляют такие же меры безопасности. Приложения Blazor Server должны включать соответствующие проверки и ограничения перед выполнением операций, потенциально требовательных к ресурсам ЦП.

### <a name="memory"></a>Память

Нехватка памяти может возникать, когда один или несколько клиентов вынуждены принудительно использовать большой объем памяти.

Например, рассмотрим приложение на стороне сервера Blazor с компонентом, который принимает и отображает список элементов. Если приложение Blazor не устанавливает ограничения на количество разрешенных элементов или число элементов, преобразуемых для просмотра обратно клиенту, обработка и отрисовка с интенсивным использованием памяти может стать такой, что производительность сервера снизится. Сервер может аварийно завершить работу или замедлиться до сбоя.

Рассмотрим следующий сценарий хранения и отображения списка элементов, который приводит к потенциальной нехватке памяти на сервере:

* Элементы в свойстве или поле `List<MyItem>` используют память сервера. Если приложение разрешает неограниченный рост списка элементов, существует риск нехватки памяти на сервере. При нехватке памяти текущий сеанс завершается (со сбоем), и все параллельные сеансы в этом экземпляре сервера получают исключение нехватки памяти. Чтобы предотвратить возникновение этого сценария, приложение должно использовать структуру данных, которая накладывает ограничение на число элементов для одновременно работающих пользователей.
* Если для отрисовки не применяется схема подкачки, сервер использует дополнительную память для объектов, которые не отображаются в пользовательском интерфейсе. При отсутствии ограничения на количество элементов запросы к памяти могут привести к исчерпанию доступной памяти сервера. Чтобы предотвратить такой сценарий, используйте один из следующих подходов.
  * Используйте списки с разбивкой на страницы при преобразовании для просмотра.
  * Отображайте только первые 100–1000 элементов и требуйте от пользователя ввести условия поиска для поиска элементов за пределами списка отображаемых элементов.
  * Для более расширенного сценария преобразования для просмотра реализуйте списки или сетки, поддерживающие *виртуализацию*. С помощью виртуализации в списках отображается только подмножество элементов, видимых пользователю. Когда пользователь взаимодействует с полосой прокрутки в пользовательском интерфейсе, компонент отображает только те элементы, которые требуются для отображения. Элементы, которые в данный момент не требуются для вывода, могут храниться во вторичном хранилище, что является идеальным подходом. Неотображаемые элементы также могут храниться в памяти, что менее эффективно.

Приложения Blazor Server предлагают аналогичную модель программирования для других платформ пользовательского интерфейса для приложений с отслеживанием состояния, таких как WPF, Windows Forms или Blazor WebAssembly. Основное отличие состоит в том, что в некоторых платформах пользовательского интерфейса объем памяти, потребляемой приложением, принадлежит клиенту и влияет только на этот клиент. Например, приложение Blazor WebAssembly выполняется полностью на клиенте и использует только ресурсы памяти клиента. В сценарии Blazor Server память, потребляемая приложением, принадлежит серверу и является общей для клиентов на экземпляре сервера.

Требования к памяти на стороне сервера учитываются для всех приложений Blazor Server. Однако большинство веб-приложений не отслеживают состояние, и память, используемая при обработке запроса, освобождается при возврате ответа. В качестве общей рекомендации запретите клиентам выделять неограниченный объем памяти, как в любом другом серверном приложении, сохраняющем клиентские подключения. Объем памяти, потребляемой приложением Blazor Server, сохраняется дольше одного запроса.

> [!NOTE]
> Во время разработки можно использовать профилировщик или трассировку, захватываемую для оценки требований к памяти клиентов. Профилировщик или трассировка не захватывают память, выделенную для конкретного клиента. Чтобы захватить использование памяти конкретным клиентом во время разработки, захватите дамп и изучите потребность в памяти для всех объектов, имеющих корень в канале пользователя.

### <a name="client-connections"></a>Клиентские подключения

Нехватка подключений может возникать, когда один или несколько клиентов открывают слишком много одновременных подключений к серверу, что блокирует установку новых подключений другими клиентами.

Клиенты Blazor устанавливают одно подключение к сеансу и сохраняют его в открытом окне, пока открыто окно браузера. Требования к серверу для хранения всех подключений не относятся к приложениям Blazor. Учитывая постоянную природу подключений и возможности по отслеживанию состояния приложений Blazor Server, нехватка подключений повышает риск доступности приложения.

По умолчанию количество подключений на одного пользователя для приложения Blazor Server не ограничено. Если для приложения требуется ограничить количество подключений, воспользуйтесь одним или несколькими из следующих подходов.

* Требуйте проверку подлинности, которая естественным образом ограничивает возможность неавторизованных пользователей подключаться к приложению. Чтобы этот сценарий работал, для пользователей следует ограничить возможность подготовки новых пользователей.
* Ограничьте количество подключений для каждого пользователя. Ограничить количество подключений можно с помощью следующих подходов. Доступ к приложению следует предоставить только законным пользователям (например, если установлено ограничение на количество подключений на основе IP-адреса клиента).
  * На уровне приложения:
    * Расширяемость маршрутизации конечных точек.
    * Требуйте проверку подлинности для подключения к приложению и отслеживайте активные сеансы на пользователя.
    * Отклоняйте новые сеансы при достижении ограничения.
    * Используйте прокси-сервер WebSocket для подключения к приложению, например [службу Azure SignalR](/azure/azure-signalr/signalr-overview), которая мультиплексирует подключения от клиентов к приложению. Это обеспечивает приложению более высокую производительность подключений, чем может обеспечить один клиент, что предотвращает исчерпание подключений клиента к серверу.
  * На уровне сервера: Используйте прокси-сервер или шлюз для подключения к приложению. Например, служба [Azure Front Door](/azure/frontdoor/front-door-overview) позволяет определять, администрировать и отслеживать глобальную маршрутизацию веб-трафика к приложению.

## <a name="denial-of-service-dos-attacks"></a>Атаки типа "отказ в обслуживании" (DoS)

Атаки типа "отказ в обслуживании" (DoS) подразумевают, что клиент исчерпывает один или несколько ресурсов сервера, в результате чего приложение становится недоступным. Приложения Blazor Server включают некоторые ограничения по умолчанию и используют другие ограничения ASP.NET Core и SignalR для защиты от атак DoS, установленных в <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>.

| Ограничение приложения Blazor Server | Описание | Значение по умолчанию |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | Максимальное число отключенных каналов, которые заданный сервер удерживает в памяти за один раз. | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | Максимальное время, в течение которого отключенный канал удерживается в памяти, прежде чем будет сброшен. | 3 минуты |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | Максимальное время ожидания сервера до истечения времени ожидания асинхронного вызова функции JavaScript. | 1 минута |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | Максимальное число неподтвержденных пакетов преобразования для просмотра, которые сервер хранит в памяти на канал в указанное время для поддержки надежного повторного подключения. После достижения предела сервер прекращает создавать новые пакеты преобразования для просмотра, пока один или несколько пакетов не будут подтверждены клиентом. | 10 |

Задайте максимальный размер сообщения для одного входящего сообщения концентратора с <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions>.

| Предел SignalR и ASP.NET Core | Описание | Значение по умолчанию |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | Размер сообщения для отдельного сообщения. | 32 КБ |

## <a name="interactions-with-the-browser-client"></a>Взаимодействие с браузером (клиент)

Клиент взаимодействует с сервером через событие взаимодействия JS и завершение преобразования для просмотра. Взаимодействие между JavaScript и .NET осуществляется в обоих направлениях:

* События браузера отправляются с клиента на сервер асинхронным образом.
* Сервер отвечает асинхронно, при необходимости перерисовывает пользовательский интерфейс.

### <a name="javascript-functions-invoked-from-net"></a>Функции JavaScript, вызываемые из .NET

Для вызовов из методов .NET к JavaScript:

* Все вызовы имеют настраиваемое время ожидания, по окончании которого они завершаются сбоем, возвращая <xref:System.OperationCanceledException> вызывающей стороне.
  * Время ожидания вызовов (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) по умолчанию — одна минута. Сведения о том, как настроить это ограничение, см. в разделе <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.
  * Для управления отменой на основе каждого вызова можно предоставить токен отмены. Ориентируйтесь на время ожидания вызова по умолчанию, когда это возможно, и ограничивайте по времени любой вызов клиента, если предоставлен токен отмены.
* Результату вызова JavaScript нельзя доверять. Клиент приложения Blazor, запущенный в браузере, выполняет поиск вызываемой функции JavaScript. Вызывается функция и создается либо результат, либо ошибка. Вредоносный клиент может попытаться:
  * Вызывать проблемы в работе приложения, возвращая ошибку из функции JavaScript.
  * Вызывать непреднамеренное поведение сервера, возвращая непредвиденный результат из функции JavaScript.

Примите следующие меры предосторожности, чтобы защититься от возникновения указанных выше сценариев.

* Заключите вызовы взаимодействия JS в операторы [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch), чтобы учитывать ошибки, которые могут возникнуть во время вызовов. Для получения дополнительной информации см. <xref:blazor/fundamentals/handle-errors#javascript-interop>.
* Перед выполнением каких-либо действий проверьте данные, возвращаемые при вызовах взаимодействия JS, включая сообщения об ошибках.

### <a name="net-methods-invoked-from-the-browser"></a>Методы .NET, вызываемые из браузера

Не следует доверять вызовам из JavaScript к методам .NET. Когда метод .NET предоставляется JavaScript, рассмотрите способ вызова метода .NET:

* Рассматривайте любой метод .NET, предоставляемый JavaScript, так же, как общедоступную конечную точку приложения.
  * Проверьте входные данные.
    * Убедитесь, что значения находятся в пределах ожидаемых диапазонов.
    * Убедитесь, что у пользователя есть разрешение на выполнение запрошенного действия.
  * Не выделяйте чрезмерное количество ресурсов в рамках вызова метода .NET. Например, выполните проверки и задайте ограничения на использование ресурсов ЦП и памяти.
  * Примите во внимание, что статические методы и методы экземпляра могут предоставляться клиентам JavaScript. Избегайте совместного использования состояний в сеансах, если только не вызывается конструктор для предоставления общего состояния с соответствующими ограничениями.
    * Для методов экземпляров, предоставляемых через объекты `DotNetReference`, которые изначально были созданы с помощью внедрения зависимостей (DI), объекты должны быть зарегистрированы как объекты с областью действия. Это относится к любой службе DI, используемой приложением Blazor Server.
    * Для статических методов избегайте установки состояния, которое не может быть ограничено клиентом, за исключением случаев, когда приложение по умолчанию предоставляет состояние для всех пользователей в экземпляре сервера.
  * Старайтесь не передавать пользовательские данные в параметрах вызовов JavaScript. Если передача данных в параметрах является обязательной, убедитесь, что код JavaScript обрабатывает передачу данных без уязвимостей, которые делают возможными [межсайтовые сценарии (XSS)](#cross-site-scripting-xss). Например, не записывайте данные, предоставленные пользователем, в модель DOM, установив свойство `innerHTML` элемента. Для отключения `eval` и других ненадежных примитивов JavaScript рекомендуется использовать [политики безопасности содержимого (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).
* Избегайте реализации настраиваемой диспетчеризации вызовов .NET поверх реализации диспетчеризации платформы. Предоставление доступа к методам .NET для браузера является расширенным сценарием, не рекомендуемым для общих сценариев разработки Blazor.

### <a name="events"></a>События

События предоставляют точку входа для приложения Blazor Server. Те же правила защиты конечных точек в веб-приложениях применяются к обработке событий в приложениях Blazor Server. Вредоносный клиент может отправить в качестве полезных данных для события любые данные, которые он хочет отправить.

Пример:

* Событие изменения для `<select>` может отправить значение, которое не находится в параметрах, представленных приложением для клиента.
* `<input>` может отправить на сервер текстовые данные, минуя проверку на стороне клиента.

Приложение должно проверить данные для любого события, обрабатываемого приложением. [Компоненты форм](xref:blazor/forms-validation) платформы Blazor выполняют основные проверки. Если приложение использует настраиваемые компоненты форм, для проверки правильности данных событий необходимо написать пользовательский код.

События Blazor Server являются асинхронными, поэтому на сервер можно отправить несколько событий, прежде чем приложение сможет реагировать на них, создавая новое преобразование для просмотра. Это позволяет учитывать некоторые аспекты безопасности. Ограничение клиентских действий в приложении должно выполняться внутри обработчиков событий и не зависеть от текущего состояния представления, готового к просмотру.

Рассмотрим компонент счетчика, который должен позволить пользователю увеличить значение счетчика не более трех раз. Кнопка для увеличения счетчика зависит от значения `count`:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Клиент может отправить одно или несколько событий приращения, прежде чем платформа создаст новое преобразование для просмотра этого компонента. В результате пользователь может увеличить `count` *более трех раз*, поскольку кнопка не удаляется с помощью пользовательского интерфейса достаточно быстро. Правильный способ достижения предельного значения в три приращения `count` показан в следующем примере:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

При добавлении проверки `if (count < 3) { ... }` внутри обработчика решение об увеличении `count` зависит от текущего состояния приложения. Решение не зависит от состояния пользовательского интерфейса, которое было в предыдущем примере, что может быть временно устаревшим.

### <a name="guard-against-multiple-dispatches"></a>Защита от нескольких отправок

Если обратный вызов события асинхронно вызывает длительную операцию, например получение данных из внешней службы или базы данных, рассмотрите возможность использования защиты. Это условие может препятствовать постановке пользователем в очередь нескольких операций во время выполнения операции с визуальным отзывом. Следующий код компонента задает `isLoading` для `true`, пока `GetForecastAsync` получает данные с сервера. До тех пор, пока `isLoading` имеет значение `true`, кнопка отключена в пользовательском интерфейсе:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Защита, показанная в предыдущем примере, работает, если фоновая операция выполняется асинхронно с шаблоном `async``await`-.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Отмена в начале и избежание use-after-dispose

Помимо использования защиты, как описано в разделе [Защита от нескольких отправок](#guard-against-multiple-dispatches), рассмотрите возможность использования <xref:System.Threading.CancellationToken> для отмены длительных операций при удалении компонента. Этот подход предоставляет дополнительные преимущества предотвращения *use-after-dispose* в компонентах:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Избегайте событий, создающих большие объемы данных

Некоторые события модели DOM, такие как `oninput` или `onscroll`, могут создавать большой объем данных. Избегайте использования этих событий в приложениях Blazor Server.

## <a name="additional-security-guidance"></a>Дополнительные рекомендации по безопасности

Рекомендации по защите приложений ASP.NET Core применяются к приложениям Blazor Server и рассматриваются в следующих разделах:

* [Ведение журнала и конфиденциальные данные](#logging-and-sensitive-data)
* [Защита информации при передаче с помощью HTTPS](#protect-information-in-transit-with-https)
* [Межсайтовые сценарии (XSS)](#cross-site-scripting-xss)
* [Защита от атак из разных источников](#cross-origin-protection)
* [Защита от взлома действия щелчка мышью](#click-jacking)
* [Открытые перенаправления](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Ведение журнала и конфиденциальные данные

Взаимодействия JS между клиентом и сервером протоколируются в журналы сервера с помощью экземпляров <xref:Microsoft.Extensions.Logging.ILogger>. Blazor предотвращает регистрацию конфиденциальной информации, например фактических событий или входных и выходных данных взаимодействия JS.

При возникновении ошибки на сервере платформа уведомляет клиента и разрывает сеанс. По умолчанию клиент получает общее сообщение об ошибке, которое можно увидеть в средствах разработчика браузера.

Ошибка на стороне клиента не включает стек вызовов и не предоставляет сведения о причине ошибки, однако журналы сервера содержат такие сведения. В целях разработки конфиденциальные сведения об ошибках можно сделать доступными для клиента, включив подробные ошибки.

Включите подробные сведения об ошибках в JavaScript с помощью следующего:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* Задайте для ключа конфигурации `DetailedErrors` значение `true` в файле настроек приложения (`appsettings.json`). Ключ также можно задать с помощью переменной среды `ASPNETCORE_DETAILEDERRORS` со значением `true`.

> [!WARNING]
> Предоставление сведений об ошибках клиентам в Интернете является угрозой безопасности, которую всегда следует избегать.

### <a name="protect-information-in-transit-with-https"></a>Защита информации при передаче с помощью HTTPS

Blazor Server использует SignalR для обмена данными между клиентом и сервером. Обычно Blazor Server использует транспорт, который согласовывает SignalR (обычно это WebSocket).

Blazor Server не гарантирует целостность и конфиденциальность данных, передаваемых между сервером и клиентом. Всегда используйте HTTPS.

### <a name="cross-site-scripting-xss"></a>Межсайтовые сценарии (XSS)

Межсайтовые сценарии (XSS) позволяют неавторизованной стороне выполнять произвольную логику в контексте браузера. Скомпрометированное приложение потенциально может выполнять произвольный код в клиенте. Уязвимость может быть использована для выполнения ряда вредоносных действий на сервере.

* Отправка фиктивных или недопустимых событий на сервер.
* Отработка отказа/недопустимые завершения операций преобразования для просмотра.
* Избегайте диспетчеризации завершений операций преобразования для просмотра.
* Отправка вызовов взаимодействия из JavaScript в .NET.
* Изменение ответа вызовов взаимодействия с .NET на JavaScript.
* Избегайте отправки результатов взаимодействия .NET с JS.

Платформа Blazor Server предпринимает меры для защиты от некоторых из указанных ранее угроз.

* Прекращает создание новых обновлений пользовательского интерфейса, если клиент не будет подтверждать пакеты преобразования для просмотра. Настраивается с помощью <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>.
* Завершает время ожидания вызова .NET к JavaScript через одну минуту без получения ответа от клиента. Настраивается с помощью <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>.
* Выполняет базовую проверку всех входных данных, поступающих из браузера во время взаимодействия с JS:
  * Ссылки на .NET являются допустимыми и того типа, который ожидается методом .NET.
  * Данные имеют правильный формат.
  * В полезных данных содержится правильное число аргументов для метода.
  * Аргументы или результат можно правильно десериализовать перед вызовом метода.
* Выполняет базовую проверку всех входных данных, поступающих из браузера от отправленных событий:
  * Событие имеет допустимый тип.
  * Данные для события могут быть десериализованы.
  * С событием связан обработчик событий.

В дополнение к средствам защиты, реализуемым платформой, приложение должно быть закодировано разработчиком для защиты от угроз и выполнения соответствующих действий:

* Всегда проверяйте данные при обработке событий.
* Предпринимайте необходимые действия при получении недопустимых данных:
  * Пропускайте данные и возврат. Это позволяет приложению продолжить обработку запросов.
  * Если приложение определяет, что входные данные незаконны и не могут быть получены законным клиентом, вызовите исключение. Создание исключения разрывает канал вниз и завершает сеанс.
* Не следует доверять сообщению об ошибке, предоставленному в журналах завершения обработки пакетов, включенных в журналы. Эта ошибка предоставляется клиентом и не может быть доверенной, так как клиент может быть скомпрометирован.
* Не следует доверять входным вызовам взаимодействия JS в любом направлении между методами JavaScript и .NET.
* Приложение отвечает за проверку допустимости содержимого аргументов и результатов, даже если аргументы или результаты десериализованы правильно.

Чтобы существовала уязвимость XSS, приложение должно включить пользовательский ввод на странице, преобразованной для просмотра. Компоненты Blazor Server выполняют шаг времени компиляции, при котором разметка в файле `.razor` преобразуется в процедурную логику C#. Во время выполнения C# логика создает *дерево преобразования для просмотра*, описывающее элементы, текст и дочерние компоненты. Это применяется к модели DOM браузера через последовательность инструкций JavaScript (или сериализуется в HTML в случае предварительного преобразования для просмотра):

* Пользовательский ввод, отображаемый с помощью обычного синтаксиса Razor (например, `@someStringValue`), не предоставляет уязвимость XSS, так как синтаксис Razor добавляется в модель DOM с помощью команд, которые могут записывать только текст. Даже если значение включает HTML-разметку, значение отображается как статический текст. При предварительной отрисовке выходные данные кодируются в формате HTML, в результате чего содержимое отображается в виде статического текста.
* Теги сценариев не допускаются и не должны включаться в дерево преобразования для просмотра компонента приложения. Если тег сценария включен в разметку компонента, создается ошибка времени компиляции.
* Авторы компонентов могут создавать компоненты C# без использования Razor. Автор компонента несет ответственность за использование правильных API-интерфейсов при выдаче выходных данных. Например, используйте `builder.AddContent(0, someUserSuppliedString)` и *не* используйте `builder.AddMarkupContent(0, someUserSuppliedString)`, так как в последнем случае может быть создана уязвимость XSS.

В рамках защиты от атак XSS рассмотрите возможность реализации предотвращения взлома XSS, такие как [политики безопасности содержимого (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Для получения дополнительной информации см. <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Защита от атак из разных источников

Атаки с использованием разных источников предполагают, что клиент из другого источника выполняет действие с сервером. Вредоносным действием обычно является запрос GET или форма POST (подделка межсайтовых запросов, CSRF), но также возможно открытие вредоносного WebSocket. Приложения Blazor Server предлагают [те же гарантии, что и любое другое приложение SignalR, использующее протокол концентратора](xref:signalr/security):

* Доступ к приложениям Blazor Server можно получить из разных источников, если не будут предприняты дополнительные меры для их предотвращения. Чтобы отключить доступ из разных источников, отключите CORS в конечной точке, добавив ПО промежуточного слоя CORS в конвейер и добавив <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> в метаданные конечной точки Blazor или ограничив набор разрешенных источников, [настроив SignalR для общего доступа к ресурсам из разных источников](xref:signalr/security#cross-origin-resource-sharing).
* Если CORS включен, для защиты приложения могут потребоваться дополнительные действия в зависимости от конфигурации CORS. Если CORS глобально включена, CORS можно отключить для концентратора Blazor Server, добавив метаданные <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> в метаданные конечной точки после вызова <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в построителе маршрутов конечной точки.

Для получения дополнительной информации см. <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Защита от взлома действия щелчка мышью

Такие атаки подразумевают визуализацию сайта в качестве `<iframe>` в пределах сайта из другого источника, чтобы заставить пользователя выполнить действия на веб-узле при атаке.

Чтобы защитить приложение от преобразования для просмотра в `<iframe>`, используйте [политики безопасности содержимого (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) и заголовок `X-Frame-Options`. Дополнительные сведения см. в [веб-документации MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Открытые перенаправления

При запуске сеанса приложения Blazor Server сервер выполняет базовую проверку URL-адресов, отправляемых в процессе запуска сеанса. Платформа проверяет, является ли базовый URL-адрес родительским по отношению к текущему URL-адресу, прежде чем устанавливать канал. Платформа не выполняет никаких дополнительных проверок.

Когда пользователь выбирает ссылку в клиенте, URL-адрес ссылки отправляется на сервер, который определяет, какое действие следует предпринять. Например, приложение может выполнить навигацию на стороне клиента или указать браузеру, что необходимо перейти к новому расположению.

Компоненты также могут активировать запросы навигации программно с помощью <xref:Microsoft.AspNetCore.Components.NavigationManager>. В таких случаях приложение может выполнить навигацию на стороне клиента или указать браузеру, что необходимо перейти к новому расположению.

Компоненты должны:

* Избегать использования входных данных пользователя в аргументах вызова навигации.
* Проверять аргументы, чтобы убедиться, что целевой объект разрешен приложением.

В противном случае злонамеренный пользователь может заставить браузер перейти на управляемый злоумышленником сайт. В этом случае злоумышленник обманом заставляет приложение использовать некоторые вводимые пользователем данные в ходе вызова метода <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.

Этот совет также применим при преобразовании для просмотра ссылок в составе приложения:

* По возможности используйте относительные ссылки.
* Убедитесь, что ссылки на абсолютные адреса являются допустимыми, прежде чем включать их в страницу.

Для получения дополнительной информации см. <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Контрольный список по безопасности

Следующий список вопросов безопасности не является исчерпывающим:

* Проверяйте аргументы из событий.
* Проверяйте входные данные и результаты вызовов взаимодействия JS.
* Старайтесь не использовать (или проверять заранее) пользовательский ввод для вызовов .NET к взаимодействию JS.
* Запретите клиенту выделять неограниченный объем памяти.
  * Данные в компоненте.
  * Ссылки `DotNetObject`, возвращаемые клиенту.
* Обеспечьте защиту от нескольких отправок.
* Отменяйте длительные операции при удалении компонента.
* Избегайте событий, создающих большие объемы данных.
* Избегайте использования вводимых пользователем данных в рамках вызовов <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> и сначала проверяйте вводимые пользователем данные для URL-адресов с набором разрешенных источников.
* Не следует принимать решения об авторизации на основе состояния пользовательского интерфейса только лишь из состояния компонента.
* Для защиты от атак XSS рекомендуется использовать [политики безопасности содержимого (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).
* Рассмотрите возможность использования CSP и [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) для защиты от взлома действия щелчка мышью.
* Убедитесь, что параметры CORS подходят при включении CORS, или явно отключите CORS для приложений Blazor.
* Убедитесь, что ограничения на стороне сервера для приложения Blazor обеспечивают приемлемое взаимодействие с пользователем без неприемлемых уровней риска.