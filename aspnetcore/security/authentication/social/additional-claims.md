---
<span data-ttu-id="d303f-101">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-102">'Blazor'</span></span>
- <span data-ttu-id="d303f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-103">'Identity'</span></span>
- <span data-ttu-id="d303f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-105">'Razor'</span></span>
- <span data-ttu-id="d303f-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="d303f-107">Conservar notificaciones y tokens adicionales de proveedores externos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d303f-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d303f-108">Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter.</span><span class="sxs-lookup"><span data-stu-id="d303f-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="d303f-109">Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.</span><span class="sxs-lookup"><span data-stu-id="d303f-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="d303f-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d303f-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d303f-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d303f-111">Prerequisites</span></span>

<span data-ttu-id="d303f-112">Decida qué proveedores de autenticación externos admitir en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d303f-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="d303f-113">Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="d303f-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="d303f-114">Para obtener más información, vea <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="d303f-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="d303f-115">La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="d303f-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="d303f-116">Establecimiento del identificador de cliente y el secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="d303f-116">Set the client ID and client secret</span></span>

<span data-ttu-id="d303f-117">El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="d303f-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="d303f-118">El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor.</span><span class="sxs-lookup"><span data-stu-id="d303f-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="d303f-119">Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor.</span><span class="sxs-lookup"><span data-stu-id="d303f-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="d303f-120">Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:</span><span class="sxs-lookup"><span data-stu-id="d303f-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="d303f-121">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="d303f-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="d303f-122">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="d303f-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="d303f-123">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="d303f-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="d303f-124">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="d303f-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="d303f-125">Otros proveedores de autenticación</span><span class="sxs-lookup"><span data-stu-id="d303f-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="d303f-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="d303f-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="d303f-127">La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:</span><span class="sxs-lookup"><span data-stu-id="d303f-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="d303f-128">Establecer el ámbito de autenticación</span><span class="sxs-lookup"><span data-stu-id="d303f-128">Establish the authentication scope</span></span>

<span data-ttu-id="d303f-129">Especifique la lista de permisos que se van a recuperar del proveedor mediante la especificación de <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="d303f-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="d303f-130">Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="d303f-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="d303f-131">Proveedor</span><span class="sxs-lookup"><span data-stu-id="d303f-131">Provider</span></span>  | <span data-ttu-id="d303f-132">Ámbito</span><span class="sxs-lookup"><span data-stu-id="d303f-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="d303f-133">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-134">'Blazor'</span></span>
- <span data-ttu-id="d303f-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-135">'Identity'</span></span>
- <span data-ttu-id="d303f-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-137">'Razor'</span></span>
- <span data-ttu-id="d303f-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-139">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-140">'Blazor'</span></span>
- <span data-ttu-id="d303f-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-141">'Identity'</span></span>
- <span data-ttu-id="d303f-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-143">'Razor'</span></span>
- <span data-ttu-id="d303f-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-144">'SignalR' uid:</span></span> 

<span data-ttu-id="d303f-145">----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-146">'Blazor'</span></span>
- <span data-ttu-id="d303f-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-147">'Identity'</span></span>
- <span data-ttu-id="d303f-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-149">'Razor'</span></span>
- <span data-ttu-id="d303f-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-151">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-152">'Blazor'</span></span>
- <span data-ttu-id="d303f-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-153">'Identity'</span></span>
- <span data-ttu-id="d303f-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-155">'Razor'</span></span>
- <span data-ttu-id="d303f-156">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-157">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-158">'Blazor'</span></span>
- <span data-ttu-id="d303f-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-159">'Identity'</span></span>
- <span data-ttu-id="d303f-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-161">'Razor'</span></span>
- <span data-ttu-id="d303f-162">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-163">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-164">'Blazor'</span></span>
- <span data-ttu-id="d303f-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-165">'Identity'</span></span>
- <span data-ttu-id="d303f-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-167">'Razor'</span></span>
- <span data-ttu-id="d303f-168">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-169">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-170">'Blazor'</span></span>
- <span data-ttu-id="d303f-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-171">'Identity'</span></span>
- <span data-ttu-id="d303f-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-173">'Razor'</span></span>
- <span data-ttu-id="d303f-174">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-175">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-176">'Blazor'</span></span>
- <span data-ttu-id="d303f-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-177">'Identity'</span></span>
- <span data-ttu-id="d303f-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-179">'Razor'</span></span>
- <span data-ttu-id="d303f-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-181">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-182">'Blazor'</span></span>
- <span data-ttu-id="d303f-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-183">'Identity'</span></span>
- <span data-ttu-id="d303f-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-185">'Razor'</span></span>
- <span data-ttu-id="d303f-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-187">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-188">'Blazor'</span></span>
- <span data-ttu-id="d303f-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-189">'Identity'</span></span>
- <span data-ttu-id="d303f-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-191">'Razor'</span></span>
- <span data-ttu-id="d303f-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-193">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-194">'Blazor'</span></span>
- <span data-ttu-id="d303f-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-195">'Identity'</span></span>
- <span data-ttu-id="d303f-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-197">'Razor'</span></span>
- <span data-ttu-id="d303f-198">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-199">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-200">'Blazor'</span></span>
- <span data-ttu-id="d303f-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-201">'Identity'</span></span>
- <span data-ttu-id="d303f-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-203">'Razor'</span></span>
- <span data-ttu-id="d303f-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-205">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-206">'Blazor'</span></span>
- <span data-ttu-id="d303f-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-207">'Identity'</span></span>
- <span data-ttu-id="d303f-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-209">'Razor'</span></span>
- <span data-ttu-id="d303f-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-211">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-212">'Blazor'</span></span>
- <span data-ttu-id="d303f-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-213">'Identity'</span></span>
- <span data-ttu-id="d303f-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-215">'Razor'</span></span>
- <span data-ttu-id="d303f-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-217">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-218">'Blazor'</span></span>
- <span data-ttu-id="d303f-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-219">'Identity'</span></span>
- <span data-ttu-id="d303f-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-221">'Razor'</span></span>
- <span data-ttu-id="d303f-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-223">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-224">'Blazor'</span></span>
- <span data-ttu-id="d303f-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-225">'Identity'</span></span>
- <span data-ttu-id="d303f-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-227">'Razor'</span></span>
- <span data-ttu-id="d303f-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-229">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-230">'Blazor'</span></span>
- <span data-ttu-id="d303f-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-231">'Identity'</span></span>
- <span data-ttu-id="d303f-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-233">'Razor'</span></span>
- <span data-ttu-id="d303f-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-235">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-236">'Blazor'</span></span>
- <span data-ttu-id="d303f-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-237">'Identity'</span></span>
- <span data-ttu-id="d303f-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-239">'Razor'</span></span>
- <span data-ttu-id="d303f-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-241">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-242">'Blazor'</span></span>
- <span data-ttu-id="d303f-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-243">'Identity'</span></span>
- <span data-ttu-id="d303f-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-245">'Razor'</span></span>
- <span data-ttu-id="d303f-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-247">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-248">'Blazor'</span></span>
- <span data-ttu-id="d303f-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-249">'Identity'</span></span>
- <span data-ttu-id="d303f-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-251">'Razor'</span></span>
- <span data-ttu-id="d303f-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-253">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-254">'Blazor'</span></span>
- <span data-ttu-id="d303f-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-255">'Identity'</span></span>
- <span data-ttu-id="d303f-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-257">'Razor'</span></span>
- <span data-ttu-id="d303f-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-259">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-260">'Blazor'</span></span>
- <span data-ttu-id="d303f-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-261">'Identity'</span></span>
- <span data-ttu-id="d303f-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-263">'Razor'</span></span>
- <span data-ttu-id="d303f-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-265">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-266">'Blazor'</span></span>
- <span data-ttu-id="d303f-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-267">'Identity'</span></span>
- <span data-ttu-id="d303f-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-269">'Razor'</span></span>
- <span data-ttu-id="d303f-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-271">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-272">'Blazor'</span></span>
- <span data-ttu-id="d303f-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-273">'Identity'</span></span>
- <span data-ttu-id="d303f-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-275">'Razor'</span></span>
- <span data-ttu-id="d303f-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-277">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-278">'Blazor'</span></span>
- <span data-ttu-id="d303f-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-279">'Identity'</span></span>
- <span data-ttu-id="d303f-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-281">'Razor'</span></span>
- <span data-ttu-id="d303f-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-283">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-284">'Blazor'</span></span>
- <span data-ttu-id="d303f-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-285">'Identity'</span></span>
- <span data-ttu-id="d303f-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-287">'Razor'</span></span>
- <span data-ttu-id="d303f-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-289">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-290">'Blazor'</span></span>
- <span data-ttu-id="d303f-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-291">'Identity'</span></span>
- <span data-ttu-id="d303f-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-293">'Razor'</span></span>
- <span data-ttu-id="d303f-294">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-295">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-296">'Blazor'</span></span>
- <span data-ttu-id="d303f-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-297">'Identity'</span></span>
- <span data-ttu-id="d303f-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-299">'Razor'</span></span>
- <span data-ttu-id="d303f-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-301">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-302">'Blazor'</span></span>
- <span data-ttu-id="d303f-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-303">'Identity'</span></span>
- <span data-ttu-id="d303f-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-305">'Razor'</span></span>
- <span data-ttu-id="d303f-306">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-307">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-308">'Blazor'</span></span>
- <span data-ttu-id="d303f-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-309">'Identity'</span></span>
- <span data-ttu-id="d303f-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-311">'Razor'</span></span>
- <span data-ttu-id="d303f-312">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-313">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-314">'Blazor'</span></span>
- <span data-ttu-id="d303f-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-315">'Identity'</span></span>
- <span data-ttu-id="d303f-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-317">'Razor'</span></span>
- <span data-ttu-id="d303f-318">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-319">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-320">'Blazor'</span></span>
- <span data-ttu-id="d303f-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-321">'Identity'</span></span>
- <span data-ttu-id="d303f-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-323">'Razor'</span></span>
- <span data-ttu-id="d303f-324">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-324">'SignalR' uid:</span></span> 

<span data-ttu-id="d303f-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="d303f-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="d303f-326">En la aplicación de ejemplo, `userinfo.profile` el ámbito de Google lo agrega automáticamente el marco de trabajo cuando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> se llama a en <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d303f-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="d303f-327">Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones.</span><span class="sxs-lookup"><span data-stu-id="d303f-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="d303f-328">En el ejemplo siguiente, `https://www.googleapis.com/auth/user.birthday.read` se agrega el ámbito de Google para recuperar el cumpleaños de un usuario:</span><span class="sxs-lookup"><span data-stu-id="d303f-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="d303f-329">Asignar claves de datos de usuario y crear notificaciones</span><span class="sxs-lookup"><span data-stu-id="d303f-329">Map user data keys and create claims</span></span>

<span data-ttu-id="d303f-330">En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="d303f-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="d303f-331">Para obtener más información sobre los tipos de notificaciones, vea <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="d303f-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="d303f-332">La aplicación de ejemplo crea `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) a partir de las `locale` `picture` claves y en los datos de usuario de Google:</span><span class="sxs-lookup"><span data-stu-id="d303f-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="d303f-333">En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , <xref:Microsoft.AspNetCore.Identity.IdentityUser> `ApplicationUser` se inicia una sesión de () en la aplicación con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="d303f-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="d303f-334">Durante el proceso de inicio de sesión, el <xref:Microsoft.AspNetCore.Identity.UserManager%601> puede almacenar `ApplicationUser` notificaciones para los datos de usuario disponibles en <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="d303f-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="d303f-335">En la aplicación de ejemplo, `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. CS*) establece las `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) para la sesión iniciada `ApplicationUser` , incluida una notificación para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="d303f-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="d303f-336">De forma predeterminada, las notificaciones de un usuario se almacenan en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="d303f-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="d303f-337">Si la cookie de autenticación es demasiado grande, puede provocar un error en la aplicación porque:</span><span class="sxs-lookup"><span data-stu-id="d303f-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="d303f-338">El explorador detecta que el encabezado de la cookie es demasiado largo.</span><span class="sxs-lookup"><span data-stu-id="d303f-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="d303f-339">El tamaño total de la solicitud es demasiado grande.</span><span class="sxs-lookup"><span data-stu-id="d303f-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="d303f-340">Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:</span><span class="sxs-lookup"><span data-stu-id="d303f-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="d303f-341">Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d303f-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="d303f-342">Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el middleware de autenticación de cookies <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para almacenar la identidad entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d303f-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="d303f-343">Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.</span><span class="sxs-lookup"><span data-stu-id="d303f-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="d303f-344">Guardar el token de acceso</span><span class="sxs-lookup"><span data-stu-id="d303f-344">Save the access token</span></span>

<span data-ttu-id="d303f-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define si los tokens de acceso y de actualización deben almacenarse en <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> después de una autorización correcta.</span><span class="sxs-lookup"><span data-stu-id="d303f-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="d303f-346">`SaveTokens`se establece en de `false` forma predeterminada para reducir el tamaño de la cookie de autenticación final.</span><span class="sxs-lookup"><span data-stu-id="d303f-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="d303f-347">La aplicación de ejemplo establece el valor `SaveTokens` de `true` en en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="d303f-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="d303f-348">Cuando se `OnPostConfirmationAsync` ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en el `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="d303f-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="d303f-349">La aplicación de ejemplo guarda el token de acceso en `OnPostConfirmationAsync` (nuevo registro de usuario) y `OnGetCallbackAsync` (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="d303f-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="d303f-350">Cómo agregar tokens personalizados adicionales</span><span class="sxs-lookup"><span data-stu-id="d303f-350">How to add additional custom tokens</span></span>

<span data-ttu-id="d303f-351">Para mostrar cómo agregar un token personalizado, que se almacena como parte de `SaveTokens` , la aplicación de ejemplo agrega un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con el actual <xref:System.DateTime> para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="d303f-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="d303f-352">Creación y adición de notificaciones</span><span class="sxs-lookup"><span data-stu-id="d303f-352">Creating and adding claims</span></span>

<span data-ttu-id="d303f-353">El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección.</span><span class="sxs-lookup"><span data-stu-id="d303f-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="d303f-354">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="d303f-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="d303f-355">Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.</span><span class="sxs-lookup"><span data-stu-id="d303f-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="d303f-356">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="d303f-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="d303f-357">Eliminación de las notificaciones y las acciones de notificación</span><span class="sxs-lookup"><span data-stu-id="d303f-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="d303f-358">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones del especificado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la colección.</span><span class="sxs-lookup"><span data-stu-id="d303f-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="d303f-359">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad.</span><span class="sxs-lookup"><span data-stu-id="d303f-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="d303f-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.</span><span class="sxs-lookup"><span data-stu-id="d303f-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="d303f-361">Salida de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="d303f-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d303f-362">Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter.</span><span class="sxs-lookup"><span data-stu-id="d303f-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="d303f-363">Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.</span><span class="sxs-lookup"><span data-stu-id="d303f-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="d303f-364">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d303f-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d303f-365">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d303f-365">Prerequisites</span></span>

<span data-ttu-id="d303f-366">Decida qué proveedores de autenticación externos admitir en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d303f-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="d303f-367">Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="d303f-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="d303f-368">Para obtener más información, vea <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="d303f-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="d303f-369">La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="d303f-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="d303f-370">Establecimiento del identificador de cliente y el secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="d303f-370">Set the client ID and client secret</span></span>

<span data-ttu-id="d303f-371">El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="d303f-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="d303f-372">El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor.</span><span class="sxs-lookup"><span data-stu-id="d303f-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="d303f-373">Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor.</span><span class="sxs-lookup"><span data-stu-id="d303f-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="d303f-374">Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:</span><span class="sxs-lookup"><span data-stu-id="d303f-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="d303f-375">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="d303f-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="d303f-376">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="d303f-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="d303f-377">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="d303f-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="d303f-378">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="d303f-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="d303f-379">Otros proveedores de autenticación</span><span class="sxs-lookup"><span data-stu-id="d303f-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="d303f-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="d303f-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="d303f-381">La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:</span><span class="sxs-lookup"><span data-stu-id="d303f-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="d303f-382">Establecer el ámbito de autenticación</span><span class="sxs-lookup"><span data-stu-id="d303f-382">Establish the authentication scope</span></span>

<span data-ttu-id="d303f-383">Especifique la lista de permisos que se van a recuperar del proveedor mediante la especificación de <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="d303f-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="d303f-384">Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="d303f-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="d303f-385">Proveedor</span><span class="sxs-lookup"><span data-stu-id="d303f-385">Provider</span></span>  | <span data-ttu-id="d303f-386">Ámbito</span><span class="sxs-lookup"><span data-stu-id="d303f-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="d303f-387">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-388">'Blazor'</span></span>
- <span data-ttu-id="d303f-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-389">'Identity'</span></span>
- <span data-ttu-id="d303f-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-391">'Razor'</span></span>
- <span data-ttu-id="d303f-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-393">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-394">'Blazor'</span></span>
- <span data-ttu-id="d303f-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-395">'Identity'</span></span>
- <span data-ttu-id="d303f-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-397">'Razor'</span></span>
- <span data-ttu-id="d303f-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-398">'SignalR' uid:</span></span> 

<span data-ttu-id="d303f-399">----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-400">'Blazor'</span></span>
- <span data-ttu-id="d303f-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-401">'Identity'</span></span>
- <span data-ttu-id="d303f-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-403">'Razor'</span></span>
- <span data-ttu-id="d303f-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-405">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-406">'Blazor'</span></span>
- <span data-ttu-id="d303f-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-407">'Identity'</span></span>
- <span data-ttu-id="d303f-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-409">'Razor'</span></span>
- <span data-ttu-id="d303f-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-411">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-412">'Blazor'</span></span>
- <span data-ttu-id="d303f-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-413">'Identity'</span></span>
- <span data-ttu-id="d303f-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-415">'Razor'</span></span>
- <span data-ttu-id="d303f-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-417">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-418">'Blazor'</span></span>
- <span data-ttu-id="d303f-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-419">'Identity'</span></span>
- <span data-ttu-id="d303f-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-421">'Razor'</span></span>
- <span data-ttu-id="d303f-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-423">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-424">'Blazor'</span></span>
- <span data-ttu-id="d303f-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-425">'Identity'</span></span>
- <span data-ttu-id="d303f-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-427">'Razor'</span></span>
- <span data-ttu-id="d303f-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-429">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-430">'Blazor'</span></span>
- <span data-ttu-id="d303f-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-431">'Identity'</span></span>
- <span data-ttu-id="d303f-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-433">'Razor'</span></span>
- <span data-ttu-id="d303f-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-435">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-436">'Blazor'</span></span>
- <span data-ttu-id="d303f-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-437">'Identity'</span></span>
- <span data-ttu-id="d303f-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-439">'Razor'</span></span>
- <span data-ttu-id="d303f-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-441">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-442">'Blazor'</span></span>
- <span data-ttu-id="d303f-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-443">'Identity'</span></span>
- <span data-ttu-id="d303f-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-445">'Razor'</span></span>
- <span data-ttu-id="d303f-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-447">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-448">'Blazor'</span></span>
- <span data-ttu-id="d303f-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-449">'Identity'</span></span>
- <span data-ttu-id="d303f-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-451">'Razor'</span></span>
- <span data-ttu-id="d303f-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-453">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-454">'Blazor'</span></span>
- <span data-ttu-id="d303f-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-455">'Identity'</span></span>
- <span data-ttu-id="d303f-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-457">'Razor'</span></span>
- <span data-ttu-id="d303f-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-459">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-460">'Blazor'</span></span>
- <span data-ttu-id="d303f-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-461">'Identity'</span></span>
- <span data-ttu-id="d303f-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-463">'Razor'</span></span>
- <span data-ttu-id="d303f-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-465">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-466">'Blazor'</span></span>
- <span data-ttu-id="d303f-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-467">'Identity'</span></span>
- <span data-ttu-id="d303f-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-469">'Razor'</span></span>
- <span data-ttu-id="d303f-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-471">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-472">'Blazor'</span></span>
- <span data-ttu-id="d303f-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-473">'Identity'</span></span>
- <span data-ttu-id="d303f-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-475">'Razor'</span></span>
- <span data-ttu-id="d303f-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-477">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-478">'Blazor'</span></span>
- <span data-ttu-id="d303f-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-479">'Identity'</span></span>
- <span data-ttu-id="d303f-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-481">'Razor'</span></span>
- <span data-ttu-id="d303f-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-483">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-484">'Blazor'</span></span>
- <span data-ttu-id="d303f-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-485">'Identity'</span></span>
- <span data-ttu-id="d303f-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-487">'Razor'</span></span>
- <span data-ttu-id="d303f-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-489">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-490">'Blazor'</span></span>
- <span data-ttu-id="d303f-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-491">'Identity'</span></span>
- <span data-ttu-id="d303f-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-493">'Razor'</span></span>
- <span data-ttu-id="d303f-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-495">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-496">'Blazor'</span></span>
- <span data-ttu-id="d303f-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-497">'Identity'</span></span>
- <span data-ttu-id="d303f-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-499">'Razor'</span></span>
- <span data-ttu-id="d303f-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-501">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-502">'Blazor'</span></span>
- <span data-ttu-id="d303f-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-503">'Identity'</span></span>
- <span data-ttu-id="d303f-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-505">'Razor'</span></span>
- <span data-ttu-id="d303f-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-507">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-508">'Blazor'</span></span>
- <span data-ttu-id="d303f-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-509">'Identity'</span></span>
- <span data-ttu-id="d303f-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-511">'Razor'</span></span>
- <span data-ttu-id="d303f-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-513">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-514">'Blazor'</span></span>
- <span data-ttu-id="d303f-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-515">'Identity'</span></span>
- <span data-ttu-id="d303f-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-517">'Razor'</span></span>
- <span data-ttu-id="d303f-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-519">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-520">'Blazor'</span></span>
- <span data-ttu-id="d303f-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-521">'Identity'</span></span>
- <span data-ttu-id="d303f-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-523">'Razor'</span></span>
- <span data-ttu-id="d303f-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-525">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-526">'Blazor'</span></span>
- <span data-ttu-id="d303f-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-527">'Identity'</span></span>
- <span data-ttu-id="d303f-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-529">'Razor'</span></span>
- <span data-ttu-id="d303f-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-531">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-532">'Blazor'</span></span>
- <span data-ttu-id="d303f-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-533">'Identity'</span></span>
- <span data-ttu-id="d303f-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-535">'Razor'</span></span>
- <span data-ttu-id="d303f-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-537">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-538">'Blazor'</span></span>
- <span data-ttu-id="d303f-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-539">'Identity'</span></span>
- <span data-ttu-id="d303f-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-541">'Razor'</span></span>
- <span data-ttu-id="d303f-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-543">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-544">'Blazor'</span></span>
- <span data-ttu-id="d303f-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-545">'Identity'</span></span>
- <span data-ttu-id="d303f-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-547">'Razor'</span></span>
- <span data-ttu-id="d303f-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-549">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-550">'Blazor'</span></span>
- <span data-ttu-id="d303f-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-551">'Identity'</span></span>
- <span data-ttu-id="d303f-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-553">'Razor'</span></span>
- <span data-ttu-id="d303f-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-555">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-556">'Blazor'</span></span>
- <span data-ttu-id="d303f-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-557">'Identity'</span></span>
- <span data-ttu-id="d303f-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-559">'Razor'</span></span>
- <span data-ttu-id="d303f-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-561">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-562">'Blazor'</span></span>
- <span data-ttu-id="d303f-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-563">'Identity'</span></span>
- <span data-ttu-id="d303f-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-565">'Razor'</span></span>
- <span data-ttu-id="d303f-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-567">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-568">'Blazor'</span></span>
- <span data-ttu-id="d303f-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-569">'Identity'</span></span>
- <span data-ttu-id="d303f-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-571">'Razor'</span></span>
- <span data-ttu-id="d303f-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d303f-573">Título: autor: Descripción: monikerRange: MS. Author: MS. Custom: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="d303f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d303f-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d303f-574">'Blazor'</span></span>
- <span data-ttu-id="d303f-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d303f-575">'Identity'</span></span>
- <span data-ttu-id="d303f-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d303f-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="d303f-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d303f-577">'Razor'</span></span>
- <span data-ttu-id="d303f-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d303f-578">'SignalR' uid:</span></span> 

<span data-ttu-id="d303f-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="d303f-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="d303f-580">En la aplicación de ejemplo, `userinfo.profile` el ámbito de Google lo agrega automáticamente el marco de trabajo cuando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> se llama a en <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d303f-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="d303f-581">Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones.</span><span class="sxs-lookup"><span data-stu-id="d303f-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="d303f-582">En el ejemplo siguiente, `https://www.googleapis.com/auth/user.birthday.read` se agrega el ámbito de Google para recuperar el cumpleaños de un usuario:</span><span class="sxs-lookup"><span data-stu-id="d303f-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="d303f-583">Asignar claves de datos de usuario y crear notificaciones</span><span class="sxs-lookup"><span data-stu-id="d303f-583">Map user data keys and create claims</span></span>

<span data-ttu-id="d303f-584">En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="d303f-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="d303f-585">Para obtener más información sobre los tipos de notificaciones, vea <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="d303f-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="d303f-586">La aplicación de ejemplo crea `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) a partir de las `locale` `picture` claves y en los datos de usuario de Google:</span><span class="sxs-lookup"><span data-stu-id="d303f-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="d303f-587">En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , <xref:Microsoft.AspNetCore.Identity.IdentityUser> `ApplicationUser` se inicia una sesión de () en la aplicación con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="d303f-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="d303f-588">Durante el proceso de inicio de sesión, el <xref:Microsoft.AspNetCore.Identity.UserManager%601> puede almacenar `ApplicationUser` notificaciones para los datos de usuario disponibles en <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="d303f-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="d303f-589">En la aplicación de ejemplo, `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. CS*) establece las `urn:google:locale` notificaciones de configuración regional () e imagen ( `urn:google:picture` ) para la sesión iniciada `ApplicationUser` , incluida una notificación para <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="d303f-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="d303f-590">De forma predeterminada, las notificaciones de un usuario se almacenan en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="d303f-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="d303f-591">Si la cookie de autenticación es demasiado grande, puede provocar un error en la aplicación porque:</span><span class="sxs-lookup"><span data-stu-id="d303f-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="d303f-592">El explorador detecta que el encabezado de la cookie es demasiado largo.</span><span class="sxs-lookup"><span data-stu-id="d303f-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="d303f-593">El tamaño total de la solicitud es demasiado grande.</span><span class="sxs-lookup"><span data-stu-id="d303f-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="d303f-594">Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:</span><span class="sxs-lookup"><span data-stu-id="d303f-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="d303f-595">Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d303f-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="d303f-596">Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el middleware de autenticación de cookies <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para almacenar la identidad entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d303f-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="d303f-597">Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.</span><span class="sxs-lookup"><span data-stu-id="d303f-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="d303f-598">Guardar el token de acceso</span><span class="sxs-lookup"><span data-stu-id="d303f-598">Save the access token</span></span>

<span data-ttu-id="d303f-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define si los tokens de acceso y de actualización deben almacenarse en <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> después de una autorización correcta.</span><span class="sxs-lookup"><span data-stu-id="d303f-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="d303f-600">`SaveTokens`se establece en de `false` forma predeterminada para reducir el tamaño de la cookie de autenticación final.</span><span class="sxs-lookup"><span data-stu-id="d303f-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="d303f-601">La aplicación de ejemplo establece el valor `SaveTokens` de `true` en en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="d303f-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="d303f-602">Cuando se `OnPostConfirmationAsync` ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en el `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="d303f-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="d303f-603">La aplicación de ejemplo guarda el token de acceso en `OnPostConfirmationAsync` (nuevo registro de usuario) y `OnGetCallbackAsync` (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="d303f-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="d303f-604">Cómo agregar tokens personalizados adicionales</span><span class="sxs-lookup"><span data-stu-id="d303f-604">How to add additional custom tokens</span></span>

<span data-ttu-id="d303f-605">Para mostrar cómo agregar un token personalizado, que se almacena como parte de `SaveTokens` , la aplicación de ejemplo agrega un elemento <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con el actual <xref:System.DateTime> para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="d303f-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="d303f-606">Creación y adición de notificaciones</span><span class="sxs-lookup"><span data-stu-id="d303f-606">Creating and adding claims</span></span>

<span data-ttu-id="d303f-607">El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección.</span><span class="sxs-lookup"><span data-stu-id="d303f-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="d303f-608">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="d303f-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="d303f-609">Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.</span><span class="sxs-lookup"><span data-stu-id="d303f-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="d303f-610">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="d303f-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="d303f-611">Eliminación de las notificaciones y las acciones de notificación</span><span class="sxs-lookup"><span data-stu-id="d303f-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="d303f-612">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones del especificado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la colección.</span><span class="sxs-lookup"><span data-stu-id="d303f-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="d303f-613">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad.</span><span class="sxs-lookup"><span data-stu-id="d303f-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="d303f-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.</span><span class="sxs-lookup"><span data-stu-id="d303f-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="d303f-615">Salida de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="d303f-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d303f-616">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d303f-616">Additional resources</span></span>

* <span data-ttu-id="d303f-617">[aplicación dotnet/AspNetCore Engineering SocialSample](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): la aplicación de ejemplo vinculada se encuentra en la rama [de ingeniería del repositorio de github dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="d303f-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="d303f-618">La `master` rama contiene código bajo desarrollo activo para la próxima versión de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="d303f-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="d303f-619">Para ver una versión de la aplicación de ejemplo para una versión de lanzamiento de ASP.NET Core, use la lista desplegable **bifurcar** para seleccionar una rama de versión (por ejemplo, `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="d303f-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
