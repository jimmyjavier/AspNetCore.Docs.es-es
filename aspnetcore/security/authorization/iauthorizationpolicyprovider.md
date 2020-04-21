---
title: Proveedores de directivas de autorización personalizada en ASP.NET Core
author: mjrousos
description: Aprenda a usar un IAuthorizationPolicyProvider personalizado en una aplicación ASP.NET Core para generar dinámicamente directivas de autorización.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661769"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="b7230-103">Proveedores de directivas de autorización personalizada que usan IAuthorizationPolicyProvider en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7230-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="b7230-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="b7230-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="b7230-105">Normalmente, cuando se usa la [autorización basada en directivas](xref:security/authorization/policies), las directivas se registran llamando como `AuthorizationOptions.AddPolicy` parte de la configuración del servicio de autorización.</span><span class="sxs-lookup"><span data-stu-id="b7230-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="b7230-106">En algunos escenarios, puede que no sea posible (o deseable) registrar todas las directivas de autorización de esta manera.</span><span class="sxs-lookup"><span data-stu-id="b7230-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="b7230-107">En esos casos, puede `IAuthorizationPolicyProvider` usar una personalizada para controlar cómo se proporcionan las directivas de autorización.</span><span class="sxs-lookup"><span data-stu-id="b7230-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="b7230-108">Entre los ejemplos de escenarios en los que un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado puede ser útil se incluyen:</span><span class="sxs-lookup"><span data-stu-id="b7230-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="b7230-109">Uso de un servicio externo para proporcionar evaluación de directivas.</span><span class="sxs-lookup"><span data-stu-id="b7230-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="b7230-110">Usar una amplia gama de directivas (para diferentes números de habitación o edades, por ejemplo), por lo que no tiene sentido agregar cada directiva de autorización individual con una `AuthorizationOptions.AddPolicy` llamada.</span><span class="sxs-lookup"><span data-stu-id="b7230-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="b7230-111">Crear directivas en tiempo de ejecución basadas en información de un origen de datos externo (como una base de datos) o determinar los requisitos de autorización dinámicamente a través de otro mecanismo.</span><span class="sxs-lookup"><span data-stu-id="b7230-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="b7230-112">[Vea o descargue código](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) de ejemplo desde el [repositorio De AspNetCore GitHub.](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="b7230-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="b7230-113">Descargue el archivo ZIP del repositorio dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="b7230-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="b7230-114">Descomprima el archivo.</span><span class="sxs-lookup"><span data-stu-id="b7230-114">Unzip the file.</span></span> <span data-ttu-id="b7230-115">Vaya a la carpeta del proyecto *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="b7230-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="b7230-116">Personalizar la recuperación de políticas</span><span class="sxs-lookup"><span data-stu-id="b7230-116">Customize policy retrieval</span></span>

<span data-ttu-id="b7230-117">ASP.NET aplicaciones principales usan `IAuthorizationPolicyProvider` una implementación de la interfaz para recuperar directivas de autorización.</span><span class="sxs-lookup"><span data-stu-id="b7230-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="b7230-118">De forma predeterminada, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) está registrado y se utiliza.</span><span class="sxs-lookup"><span data-stu-id="b7230-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="b7230-119">`DefaultAuthorizationPolicyProvider`políticas de `AuthorizationOptions` devolución de `IServiceCollection.AddAuthorization` lo proporcionado en una llamada.</span><span class="sxs-lookup"><span data-stu-id="b7230-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="b7230-120">Personalice este comportamiento registrando una implementación diferente `IAuthorizationPolicyProvider` en el contenedor de [inserción](xref:fundamentals/dependency-injection) de dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7230-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="b7230-121">La `IAuthorizationPolicyProvider` interfaz contiene tres API:</span><span class="sxs-lookup"><span data-stu-id="b7230-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="b7230-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) devuelve una directiva de autorización para un nombre determinado.</span><span class="sxs-lookup"><span data-stu-id="b7230-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="b7230-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) devuelve la directiva de autorización predeterminada (la directiva utilizada para `[Authorize]` los atributos sin una directiva especificada).</span><span class="sxs-lookup"><span data-stu-id="b7230-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="b7230-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) devuelve la directiva de autorización de reserva (la directiva utilizada por el Middleware de autorización cuando no se especifica ninguna directiva).</span><span class="sxs-lookup"><span data-stu-id="b7230-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="b7230-125">Mediante la implementación de estas API, puede personalizar cómo se proporcionan las directivas de autorización.</span><span class="sxs-lookup"><span data-stu-id="b7230-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="b7230-126">Ejemplo de atributo de autorización parametrizado</span><span class="sxs-lookup"><span data-stu-id="b7230-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="b7230-127">Un escenario `IAuthorizationPolicyProvider` en el `[Authorize]` que es útil es habilitar atributos personalizados cuyos requisitos dependen de un parámetro.</span><span class="sxs-lookup"><span data-stu-id="b7230-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="b7230-128">Por ejemplo, en la documentación de [autorización basada en directivas,](xref:security/authorization/policies) se usó una directiva basada en la edad ("AtLeast21") como ejemplo.</span><span class="sxs-lookup"><span data-stu-id="b7230-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="b7230-129">Si se deben poner a disposición de usuarios de *diferentes* edades acciones de controlador diferentes, podría ser útil tener muchas directivas diferentes basadas en la edad.</span><span class="sxs-lookup"><span data-stu-id="b7230-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="b7230-130">En lugar de registrar todas las diferentes directivas basadas en la edad que necesitará la aplicación, `AuthorizationOptions`puede generar las directivas dinámicamente con un archivo . `IAuthorizationPolicyProvider`</span><span class="sxs-lookup"><span data-stu-id="b7230-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="b7230-131">Para facilitar el uso de las directivas, puede `[MinimumAgeAuthorize(20)]`anotar acciones con un atributo de autorización personalizado como .</span><span class="sxs-lookup"><span data-stu-id="b7230-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="b7230-132">Atributos de autorización personalizada</span><span class="sxs-lookup"><span data-stu-id="b7230-132">Custom Authorization attributes</span></span>

<span data-ttu-id="b7230-133">Las directivas de autorización se identifican por sus nombres.</span><span class="sxs-lookup"><span data-stu-id="b7230-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="b7230-134">La `MinimumAgeAuthorizeAttribute` costumbre descrita anteriormente necesita asignar argumentos en una cadena que se puede usar para recuperar la directiva de autorización correspondiente.</span><span class="sxs-lookup"><span data-stu-id="b7230-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="b7230-135">Puede hacerlo derivando y `AuthorizeAttribute` haciendo `Age` que la `AuthorizeAttribute.Policy` propiedad envuelva la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b7230-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="b7230-136">Este tipo de `Policy` atributo tiene una cadena basada`"MinimumAge"`en el prefijo codificado de forma rígida ( ) y un entero pasado a través del constructor.</span><span class="sxs-lookup"><span data-stu-id="b7230-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="b7230-137">Puede aplicarlo a las acciones de `Authorize` la misma manera que a otros atributos, excepto que toma un entero como parámetro.</span><span class="sxs-lookup"><span data-stu-id="b7230-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="b7230-138">Custom IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="b7230-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="b7230-139">La `MinimumAgeAuthorizeAttribute` costumbre facilita la solicitud de directivas de autorización para cualquier edad mínima deseada.</span><span class="sxs-lookup"><span data-stu-id="b7230-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="b7230-140">El siguiente problema a resolver es asegurarse de que las directivas de autorización están disponibles para todas esas diferentes edades.</span><span class="sxs-lookup"><span data-stu-id="b7230-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="b7230-141">Aquí es `IAuthorizationPolicyProvider` donde es útil.</span><span class="sxs-lookup"><span data-stu-id="b7230-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="b7230-142">Al `MinimumAgeAuthorizationAttribute`utilizar , los nombres de `"MinimumAge" + Age`directiva de `IAuthorizationPolicyProvider` autorización seguirán el patrón, por lo que el personalizado debe generar directivas de autorización por:</span><span class="sxs-lookup"><span data-stu-id="b7230-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="b7230-143">Analizar la edad del nombre de la política.</span><span class="sxs-lookup"><span data-stu-id="b7230-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="b7230-144">Uso `AuthorizationPolicyBuilder` para crear un nuevo`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="b7230-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="b7230-145">En este y siguientes ejemplos se asumirá que el usuario se autentica a través de una cookie.</span><span class="sxs-lookup"><span data-stu-id="b7230-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="b7230-146">Debe `AuthorizationPolicyBuilder` construirse con al menos un nombre de esquema de autorización o siempre tener éxito.</span><span class="sxs-lookup"><span data-stu-id="b7230-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="b7230-147">De lo contrario, no hay información sobre cómo proporcionar un desafío al usuario y se producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="b7230-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="b7230-148">Adición de requisitos a `AuthorizationPolicyBuilder.AddRequirements`la política en función de la edad con .</span><span class="sxs-lookup"><span data-stu-id="b7230-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="b7230-149">En otros escenarios, `RequireClaim`puede `RequireRole`usar `RequireUserName` , , o en su lugar.</span><span class="sxs-lookup"><span data-stu-id="b7230-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="b7230-150">Múltiples proveedores de directivas de autorización</span><span class="sxs-lookup"><span data-stu-id="b7230-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="b7230-151">Al usar `IAuthorizationPolicyProvider` implementaciones personalizadas, tenga en cuenta `IAuthorizationPolicyProvider`que ASP.NET Core solo usa una instancia de .</span><span class="sxs-lookup"><span data-stu-id="b7230-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="b7230-152">Si un proveedor personalizado no puede proporcionar directivas de autorización para todos los nombres de directiva que se usarán, debe aplazar a un proveedor de copia de seguridad.</span><span class="sxs-lookup"><span data-stu-id="b7230-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="b7230-153">Por ejemplo, considere una aplicación que necesita directivas de edad personalizadas y una recuperación de directivas basada en roles más tradicional.</span><span class="sxs-lookup"><span data-stu-id="b7230-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="b7230-154">Dicha aplicación podría usar un proveedor de directivas de autorización personalizado que:</span><span class="sxs-lookup"><span data-stu-id="b7230-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="b7230-155">Intenta analizar los nombres de las directivas.</span><span class="sxs-lookup"><span data-stu-id="b7230-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="b7230-156">Llamadas a un proveedor `DefaultAuthorizationPolicyProvider`de directivas diferente (como ) si el nombre de la directiva no contiene una edad.</span><span class="sxs-lookup"><span data-stu-id="b7230-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="b7230-157">La `IAuthorizationPolicyProvider` implementación de ejemplo mostrada `DefaultAuthorizationPolicyProvider` anteriormente se puede actualizar para usar mediante la creación de un proveedor de directivas de copia de seguridad en su constructor (que se usará en caso de que el nombre de la directiva no coincida con su patrón esperado de 'MinimumAge' + edad).</span><span class="sxs-lookup"><span data-stu-id="b7230-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="b7230-158">A continuación, el `GetPolicyAsync` método se `BackupPolicyProvider` puede actualizar para usar el valor en lugar de devolver null:</span><span class="sxs-lookup"><span data-stu-id="b7230-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="b7230-159">Directiva predeterminada</span><span class="sxs-lookup"><span data-stu-id="b7230-159">Default policy</span></span>

<span data-ttu-id="b7230-160">Además de proporcionar directivas `IAuthorizationPolicyProvider` de autorización con nombre, una personalizada debe implementarse `GetDefaultPolicyAsync` para proporcionar una directiva de autorización para `[Authorize]` atributos sin un nombre de directiva especificado.</span><span class="sxs-lookup"><span data-stu-id="b7230-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="b7230-161">En muchos casos, este atributo de autorización solo requiere un usuario autenticado, `RequireAuthenticatedUser`por lo que puede realizar la directiva necesaria con una llamada a:</span><span class="sxs-lookup"><span data-stu-id="b7230-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="b7230-162">Al igual que con `IAuthorizationPolicyProvider`todos los aspectos de una costumbre, puede personalizar esto, según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b7230-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="b7230-163">En algunos casos, puede ser deseable recuperar la `IAuthorizationPolicyProvider`directiva predeterminada de una reserva.</span><span class="sxs-lookup"><span data-stu-id="b7230-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="b7230-164">Política de reserva</span><span class="sxs-lookup"><span data-stu-id="b7230-164">Fallback policy</span></span>

<span data-ttu-id="b7230-165">Opcionalmente, una función personalizada `IAuthorizationPolicyProvider` puede implementar `GetFallbackPolicyAsync` para proporcionar una directiva que se usa al combinar [directivas](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) y cuando no se especifica ninguna directiva.</span><span class="sxs-lookup"><span data-stu-id="b7230-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="b7230-166">Si `GetFallbackPolicyAsync` devuelve una directiva que no es null, el Middleware de autorización utiliza la directiva devuelta cuando no se especifica ninguna directiva para la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b7230-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="b7230-167">Si no se requiere ninguna directiva `null` de reserva, el proveedor puede devolver o diferir al proveedor de reserva:</span><span class="sxs-lookup"><span data-stu-id="b7230-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="b7230-168">Usar un IAuthorizationPolicyProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="b7230-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="b7230-169">Para utilizar directivas `IAuthorizationPolicyProvider`personalizadas desde un , debe:</span><span class="sxs-lookup"><span data-stu-id="b7230-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="b7230-170">Registre los `AuthorizationHandler` tipos adecuados con la inserción de dependencias (descritos en la [autorización basada en directivas),](xref:security/authorization/policies#authorization-handlers)como con todos los escenarios de autorización basados en directivas.</span><span class="sxs-lookup"><span data-stu-id="b7230-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="b7230-171">Registre el `IAuthorizationPolicyProvider` tipo personalizado en la colección `Startup.ConfigureServices`de servicios de inserción de dependencias de la aplicación (en ) para reemplazar el proveedor de directivas predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b7230-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="b7230-172">Un ejemplo `IAuthorizationPolicyProvider` personalizado completo está disponible en el [repositorio GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="b7230-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
