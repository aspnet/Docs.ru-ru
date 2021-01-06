<span data-ttu-id="75979-101">Компонент `RedirectToLogin` (`Shared/RedirectToLogin.razor`):</span><span class="sxs-lookup"><span data-stu-id="75979-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="75979-102">управляет перенаправлением неавторизованных пользователей на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="75979-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="75979-103">Сохраняет текущий URL-адрес, к которому пользователь пытается получить доступ, чтобы его можно было вернуть на эту страницу, если проверка подлинности прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="75979-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
