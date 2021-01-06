Компонент `FetchData` показывает, как:

* подготовить маркер доступа;
* использовать маркер доступа для вызова API защищенных ресурсов в приложении *Server*.

Директива [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) указывает на систему авторизации Blazor WebAssembly, в которой пользователь должен авторизовать для перехода в этот компонент. Наличие атрибута в приложении `Client` не мешает вызову API на сервере без соответствующих учетных данных. Приложение `Server` также должно использовать `[Authorize]` на соответствующих конечных точках, чтобы правильно защитить их.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> запрашивает маркера доступа, который можно добавить в запрос для вызова API. Если маркер кэшируется или служба может подготовить новый маркер доступа без вмешательства пользователя, запрос маркера будет выполнен. В противном случае запрос маркера завершается ошибкой <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, которая перехватывается инструкцией [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).

Чтобы получить фактический токен для включения в запрос, приложение должно проверить, что запрос был обработан, вызвав [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).

Если запрос был успешным, переменная маркера заполняется маркером доступа. Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> маркера предоставляет строку литерала для включения в заголовок запроса `Authorization`.

Если запрос завершился ошибкой, поскольку не удалось подготовить маркер без взаимодействия с пользователем, результирующий маркер содержит URL-адрес перенаправления. При переходе по этому URL-адресу пользователь переходит на страницу входа и возвращается к текущей странице после успешной проверки подлинности.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
