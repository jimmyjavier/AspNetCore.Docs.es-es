> [!NOTE]
> <span data-ttu-id="b6223-101">Si el Azure Portal proporciona el URI de ámbito de la aplicación y la aplicación produce una excepción no controlada cuando recibe una respuesta *401 no autorizada* de la API, intente usar un URI de ámbito que no incluya el esquema y el host.</span><span class="sxs-lookup"><span data-stu-id="b6223-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="b6223-102">Por ejemplo, el Azure Portal puede proporcionar uno de los siguientes formatos de URI de ámbito:</span><span class="sxs-lookup"><span data-stu-id="b6223-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="b6223-103">Intente proporcionar el URI del ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="b6223-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
