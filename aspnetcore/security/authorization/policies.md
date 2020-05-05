---
title: Autorización basada en directivas en ASP.NET Core
author: rick-anderson
description: Aprenda a crear y usar controladores de directivas de autorización para aplicar los requisitos de autorización en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 3b6fcef91355bf22e5aa185652d9489a44998db0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777506"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="32218-103">Autorización basada en directivas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32218-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="32218-104">En segundo plano, la autorización basada en [roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva configurada previamente.</span><span class="sxs-lookup"><span data-stu-id="32218-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="32218-105">Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código.</span><span class="sxs-lookup"><span data-stu-id="32218-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="32218-106">El resultado es una estructura de autorización más enriquecida, reutilizable y comprobable.</span><span class="sxs-lookup"><span data-stu-id="32218-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="32218-107">Una directiva de autorización se compone de uno o varios requisitos.</span><span class="sxs-lookup"><span data-stu-id="32218-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="32218-108">Se registra como parte de la configuración del servicio de autorización, en `Startup.ConfigureServices` el método:</span><span class="sxs-lookup"><span data-stu-id="32218-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="32218-109">En el ejemplo anterior, se crea una directiva "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="32218-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="32218-110">Tiene un único requisito&mdash;de una edad mínima, que se proporciona como un parámetro al requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="32218-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="32218-111">IAuthorizationService</span></span> 

<span data-ttu-id="32218-112">El servicio principal que determina si la autorización se realiza <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>correctamente es:</span><span class="sxs-lookup"><span data-stu-id="32218-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="32218-113">En el código anterior se resaltan los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="32218-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="32218-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcador sin métodos y el mecanismo para hacer un seguimiento de si la autorización se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="32218-115">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> uno de ellos es responsable de comprobar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="32218-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="32218-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador usa para marcar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="32218-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="32218-117">En el código siguiente se muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:</span><span class="sxs-lookup"><span data-stu-id="32218-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="32218-118">En el código siguiente se muestra `ConfigureServices`un típico:</span><span class="sxs-lookup"><span data-stu-id="32218-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="32218-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` para la autorización.</span><span class="sxs-lookup"><span data-stu-id="32218-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="32218-120">Aplicar directivas a los controladores de MVC</span><span class="sxs-lookup"><span data-stu-id="32218-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="32218-121">Si utiliza Razor páginas, consulte [aplicar directivas Razor a las páginas](#applying-policies-to-razor-pages) de este documento.</span><span class="sxs-lookup"><span data-stu-id="32218-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="32218-122">Las directivas se aplican a los controladores `[Authorize]` mediante el uso del atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="32218-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="32218-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="32218-124">Aplicar directivas a Razor páginas</span><span class="sxs-lookup"><span data-stu-id="32218-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="32218-125">Las directivas se aplican a Razor las páginas `[Authorize]` mediante el atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="32218-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="32218-126">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="32218-127">Las directivas también se pueden aplicar Razor a las páginas mediante una [Convención de autorización](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="32218-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="32218-128">Requisitos</span><span class="sxs-lookup"><span data-stu-id="32218-128">Requirements</span></span>

<span data-ttu-id="32218-129">Un requisito de autorización es una colección de parámetros de datos que una Directiva puede usar para evaluar la entidad de seguridad de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="32218-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="32218-130">En la Directiva "AtLeast21", el requisito es un parámetro&mdash;único que es la edad mínima.</span><span class="sxs-lookup"><span data-stu-id="32218-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="32218-131">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía.</span><span class="sxs-lookup"><span data-stu-id="32218-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="32218-132">Un requisito de edad mínima parametrizada podría implementarse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="32218-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="32218-133">Si una directiva de autorización contiene varios requisitos de autorización, deben cumplirse todos los requisitos para que la evaluación de la Directiva se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="32218-134">En otras palabras, los distintos requisitos de autorización que se agregan a una sola directiva de autorización se tratan de manera **y** .</span><span class="sxs-lookup"><span data-stu-id="32218-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="32218-135">No es necesario que un requisito tenga datos ni propiedades.</span><span class="sxs-lookup"><span data-stu-id="32218-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="32218-136">Controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="32218-136">Authorization handlers</span></span>

<span data-ttu-id="32218-137">Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="32218-138">El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.</span><span class="sxs-lookup"><span data-stu-id="32218-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="32218-139">Un requisito puede tener [varios controladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="32218-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="32218-140">Un controlador puede heredar [\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` donde es el requisito que se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="32218-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="32218-141">Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para administrar más de un tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="32218-142">Usar un controlador para un requisito</span><span class="sxs-lookup"><span data-stu-id="32218-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="32218-143">El siguiente es un ejemplo de una relación uno a uno en la que un controlador de edad mínima emplea un único requisito:</span><span class="sxs-lookup"><span data-stu-id="32218-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="32218-144">El código anterior determina si la entidad de seguridad de usuario actual tiene una declaración de nacimiento de fecha que ha sido emitida por un emisor conocido y de confianza.</span><span class="sxs-lookup"><span data-stu-id="32218-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="32218-145">No se puede realizar la autorización cuando falta la demanda, en cuyo caso se devuelve una tarea completada.</span><span class="sxs-lookup"><span data-stu-id="32218-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="32218-146">Cuando existe una demanda, se calcula la edad del usuario.</span><span class="sxs-lookup"><span data-stu-id="32218-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="32218-147">Si el usuario cumple la edad mínima definida por el requisito, la autorización se considera correcta.</span><span class="sxs-lookup"><span data-stu-id="32218-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="32218-148">Cuando la autorización se realiza `context.Succeed` correctamente, se invoca con el requisito satisfecho como su único parámetro.</span><span class="sxs-lookup"><span data-stu-id="32218-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="32218-149">Usar un controlador para varios requisitos</span><span class="sxs-lookup"><span data-stu-id="32218-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="32218-150">El siguiente es un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="32218-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="32218-151">El código anterior atraviesa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una propiedad que contiene requisitos no marcados como correctos.</span><span class="sxs-lookup"><span data-stu-id="32218-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="32218-152">Para un `ReadPermission` requisito, el usuario debe ser un propietario o un patrocinador para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="32218-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="32218-153">En el caso de un `EditPermission` requisito `DeletePermission` de o, debe ser un propietario para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="32218-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="32218-154">Registro del controlador</span><span class="sxs-lookup"><span data-stu-id="32218-154">Handler registration</span></span>

<span data-ttu-id="32218-155">Los controladores se registran en la colección de servicios durante la configuración.</span><span class="sxs-lookup"><span data-stu-id="32218-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="32218-156">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="32218-157">El código anterior se registra `MinimumAgeHandler` como singleton mediante la invocación `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`de.</span><span class="sxs-lookup"><span data-stu-id="32218-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="32218-158">Los controladores se pueden registrar con cualquiera de las [duraciones de servicio](xref:fundamentals/dependency-injection#service-lifetimes)integradas.</span><span class="sxs-lookup"><span data-stu-id="32218-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="32218-159">¿Qué debe devolver un controlador?</span><span class="sxs-lookup"><span data-stu-id="32218-159">What should a handler return?</span></span>

<span data-ttu-id="32218-160">Tenga en cuenta `Handle` que el método del [ejemplo de controlador](#security-authorization-handler-example) no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="32218-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="32218-161">¿Cómo se indica el estado correcto o incorrecto?</span><span class="sxs-lookup"><span data-stu-id="32218-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="32218-162">Un controlador indica que se ha `context.Succeed(IAuthorizationRequirement requirement)`realizado correctamente llamando a, pasando el requisito que se ha validado correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="32218-163">Un controlador no necesita controlar los errores por lo general, ya que otros controladores para el mismo requisito pueden realizarse correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="32218-164">Para garantizar un error, incluso si se realizan correctamente otros controladores de `context.Fail`requisitos, llame a.</span><span class="sxs-lookup"><span data-stu-id="32218-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="32218-165">Si un controlador llama `context.Succeed` a `context.Fail`o, todavía se llama a todos los controladores restantes.</span><span class="sxs-lookup"><span data-stu-id="32218-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="32218-166">Esto permite a los requisitos producir efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado o no un requisito correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="32218-167">Cuando se establece `false`en, la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.net Core 1,1 y versiones posteriores) cortocircuita la ejecución de los controladores `context.Fail` cuando se llama a.</span><span class="sxs-lookup"><span data-stu-id="32218-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="32218-168">`InvokeHandlersAfterFailure`tiene como valor `true`predeterminado, en cuyo caso se llama a todos los controladores.</span><span class="sxs-lookup"><span data-stu-id="32218-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="32218-169">Se llama a los controladores de autorización incluso si se produce un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="32218-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="32218-170">¿Por qué sería conveniente tener varios controladores para un requisito?</span><span class="sxs-lookup"><span data-stu-id="32218-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="32218-171">En los casos en los que desea que la evaluación esté en una base de **o** , implemente varios controladores para un único requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="32218-172">Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave.</span><span class="sxs-lookup"><span data-stu-id="32218-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="32218-173">Si deja la tarjeta de la llave en casa, el recepcionista imprime una etiqueta temporal y abre la puerta.</span><span class="sxs-lookup"><span data-stu-id="32218-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="32218-174">En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="32218-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="32218-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="32218-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="32218-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="32218-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="32218-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="32218-178">Asegúrese de que ambos controladores están [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="32218-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="32218-179">Si alguno de los controladores se realiza correctamente cuando una directiva `BuildingEntryRequirement`evalúa el, la evaluación de la Directiva se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="32218-180">Usar un FUNC para cumplir una directiva</span><span class="sxs-lookup"><span data-stu-id="32218-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="32218-181">Puede haber situaciones en las que el cumplimiento de una directiva sea fácil de expresar en el código.</span><span class="sxs-lookup"><span data-stu-id="32218-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="32218-182">Es posible proporcionar un `Func<AuthorizationHandlerContext, bool>` al configurar la Directiva con el `RequireAssertion` generador de directivas.</span><span class="sxs-lookup"><span data-stu-id="32218-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="32218-183">Por ejemplo, el anterior `BadgeEntryHandler` podría volver a escribirse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="32218-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="32218-184">Acceso al contexto de solicitud MVC en los controladores</span><span class="sxs-lookup"><span data-stu-id="32218-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="32218-185">El `HandleRequirementAsync` método que se implementa en un controlador de autorización tiene dos parámetros `AuthorizationHandlerContext` : un `TRequirement` y el que se está controlando.</span><span class="sxs-lookup"><span data-stu-id="32218-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="32218-186">Los marcos como MVC o Jabbr pueden agregar cualquier objeto a la `Resource` propiedad en `AuthorizationHandlerContext` para pasar información adicional.</span><span class="sxs-lookup"><span data-stu-id="32218-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="32218-187">Por ejemplo, MVC pasa una instancia de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) en la `Resource` propiedad.</span><span class="sxs-lookup"><span data-stu-id="32218-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="32218-188">Esta propiedad proporciona acceso a `HttpContext`, `RouteData`y todo lo demás proporcionado por MVC y Razor pages.</span><span class="sxs-lookup"><span data-stu-id="32218-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="32218-189">El uso de la `Resource` propiedad es específico de Framework.</span><span class="sxs-lookup"><span data-stu-id="32218-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="32218-190">El uso de la `Resource` información en la propiedad limita las directivas de autorización a marcos concretos.</span><span class="sxs-lookup"><span data-stu-id="32218-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="32218-191">Debe convertir la `Resource` propiedad mediante la `is` palabra clave y, a continuación, confirmar que la conversión se ha realizado correctamente para asegurarse de `InvalidCastException` que el código no se bloquea con cuando se ejecuta en otros marcos:</span><span class="sxs-lookup"><span data-stu-id="32218-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="32218-192">En segundo plano, la autorización basada en [roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva configurada previamente.</span><span class="sxs-lookup"><span data-stu-id="32218-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="32218-193">Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código.</span><span class="sxs-lookup"><span data-stu-id="32218-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="32218-194">El resultado es una estructura de autorización más enriquecida, reutilizable y comprobable.</span><span class="sxs-lookup"><span data-stu-id="32218-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="32218-195">Una directiva de autorización se compone de uno o varios requisitos.</span><span class="sxs-lookup"><span data-stu-id="32218-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="32218-196">Se registra como parte de la configuración del servicio de autorización, en `Startup.ConfigureServices` el método:</span><span class="sxs-lookup"><span data-stu-id="32218-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="32218-197">En el ejemplo anterior, se crea una directiva "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="32218-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="32218-198">Tiene un único requisito&mdash;de una edad mínima, que se proporciona como un parámetro al requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="32218-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="32218-199">IAuthorizationService</span></span> 

<span data-ttu-id="32218-200">El servicio principal que determina si la autorización se realiza <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>correctamente es:</span><span class="sxs-lookup"><span data-stu-id="32218-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="32218-201">En el código anterior se resaltan los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="32218-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="32218-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcador sin métodos y el mecanismo para hacer un seguimiento de si la autorización se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="32218-203">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> uno de ellos es responsable de comprobar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="32218-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="32218-204">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador usa para marcar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="32218-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="32218-205">En el código siguiente se muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:</span><span class="sxs-lookup"><span data-stu-id="32218-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="32218-206">En el código siguiente se muestra `ConfigureServices`un típico:</span><span class="sxs-lookup"><span data-stu-id="32218-206">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="32218-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` para la autorización.</span><span class="sxs-lookup"><span data-stu-id="32218-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="32218-208">Aplicar directivas a los controladores de MVC</span><span class="sxs-lookup"><span data-stu-id="32218-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="32218-209">Si utiliza Razor páginas, consulte [aplicar directivas Razor a las páginas](#applying-policies-to-razor-pages) de este documento.</span><span class="sxs-lookup"><span data-stu-id="32218-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="32218-210">Las directivas se aplican a los controladores `[Authorize]` mediante el uso del atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="32218-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="32218-211">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="32218-212">Aplicar directivas a Razor páginas</span><span class="sxs-lookup"><span data-stu-id="32218-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="32218-213">Las directivas se aplican a Razor las páginas `[Authorize]` mediante el atributo con el nombre de la Directiva.</span><span class="sxs-lookup"><span data-stu-id="32218-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="32218-214">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="32218-215">Las directivas también se pueden aplicar Razor a las páginas mediante una [Convención de autorización](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="32218-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="32218-216">Requisitos</span><span class="sxs-lookup"><span data-stu-id="32218-216">Requirements</span></span>

<span data-ttu-id="32218-217">Un requisito de autorización es una colección de parámetros de datos que una Directiva puede usar para evaluar la entidad de seguridad de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="32218-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="32218-218">En la Directiva "AtLeast21", el requisito es un parámetro&mdash;único que es la edad mínima.</span><span class="sxs-lookup"><span data-stu-id="32218-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="32218-219">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía.</span><span class="sxs-lookup"><span data-stu-id="32218-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="32218-220">Un requisito de edad mínima parametrizada podría implementarse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="32218-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="32218-221">Si una directiva de autorización contiene varios requisitos de autorización, deben cumplirse todos los requisitos para que la evaluación de la Directiva se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="32218-222">En otras palabras, los distintos requisitos de autorización que se agregan a una sola directiva de autorización se tratan de manera **y** .</span><span class="sxs-lookup"><span data-stu-id="32218-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="32218-223">No es necesario que un requisito tenga datos ni propiedades.</span><span class="sxs-lookup"><span data-stu-id="32218-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="32218-224">Controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="32218-224">Authorization handlers</span></span>

<span data-ttu-id="32218-225">Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="32218-226">El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.</span><span class="sxs-lookup"><span data-stu-id="32218-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="32218-227">Un requisito puede tener [varios controladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="32218-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="32218-228">Un controlador puede heredar [\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` donde es el requisito que se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="32218-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="32218-229">Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para administrar más de un tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="32218-230">Usar un controlador para un requisito</span><span class="sxs-lookup"><span data-stu-id="32218-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="32218-231">El siguiente es un ejemplo de una relación uno a uno en la que un controlador de edad mínima emplea un único requisito:</span><span class="sxs-lookup"><span data-stu-id="32218-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="32218-232">El código anterior determina si la entidad de seguridad de usuario actual tiene una declaración de nacimiento de fecha que ha sido emitida por un emisor conocido y de confianza.</span><span class="sxs-lookup"><span data-stu-id="32218-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="32218-233">No se puede realizar la autorización cuando falta la demanda, en cuyo caso se devuelve una tarea completada.</span><span class="sxs-lookup"><span data-stu-id="32218-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="32218-234">Cuando existe una demanda, se calcula la edad del usuario.</span><span class="sxs-lookup"><span data-stu-id="32218-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="32218-235">Si el usuario cumple la edad mínima definida por el requisito, la autorización se considera correcta.</span><span class="sxs-lookup"><span data-stu-id="32218-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="32218-236">Cuando la autorización se realiza `context.Succeed` correctamente, se invoca con el requisito satisfecho como su único parámetro.</span><span class="sxs-lookup"><span data-stu-id="32218-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="32218-237">Usar un controlador para varios requisitos</span><span class="sxs-lookup"><span data-stu-id="32218-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="32218-238">El siguiente es un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="32218-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="32218-239">El código anterior atraviesa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una propiedad que contiene requisitos no marcados como correctos.</span><span class="sxs-lookup"><span data-stu-id="32218-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="32218-240">Para un `ReadPermission` requisito, el usuario debe ser un propietario o un patrocinador para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="32218-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="32218-241">En el caso de un `EditPermission` requisito `DeletePermission` de o, debe ser un propietario para tener acceso al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="32218-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="32218-242">Registro del controlador</span><span class="sxs-lookup"><span data-stu-id="32218-242">Handler registration</span></span>

<span data-ttu-id="32218-243">Los controladores se registran en la colección de servicios durante la configuración.</span><span class="sxs-lookup"><span data-stu-id="32218-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="32218-244">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="32218-245">El código anterior se registra `MinimumAgeHandler` como singleton mediante la invocación `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`de.</span><span class="sxs-lookup"><span data-stu-id="32218-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="32218-246">Los controladores se pueden registrar con cualquiera de las [duraciones de servicio](xref:fundamentals/dependency-injection#service-lifetimes)integradas.</span><span class="sxs-lookup"><span data-stu-id="32218-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="32218-247">¿Qué debe devolver un controlador?</span><span class="sxs-lookup"><span data-stu-id="32218-247">What should a handler return?</span></span>

<span data-ttu-id="32218-248">Tenga en cuenta `Handle` que el método del [ejemplo de controlador](#security-authorization-handler-example) no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="32218-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="32218-249">¿Cómo se indica el estado correcto o incorrecto?</span><span class="sxs-lookup"><span data-stu-id="32218-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="32218-250">Un controlador indica que se ha `context.Succeed(IAuthorizationRequirement requirement)`realizado correctamente llamando a, pasando el requisito que se ha validado correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="32218-251">Un controlador no necesita controlar los errores por lo general, ya que otros controladores para el mismo requisito pueden realizarse correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="32218-252">Para garantizar un error, incluso si se realizan correctamente otros controladores de `context.Fail`requisitos, llame a.</span><span class="sxs-lookup"><span data-stu-id="32218-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="32218-253">Si un controlador llama `context.Succeed` a `context.Fail`o, todavía se llama a todos los controladores restantes.</span><span class="sxs-lookup"><span data-stu-id="32218-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="32218-254">Esto permite a los requisitos producir efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado o no un requisito correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="32218-255">Cuando se establece `false`en, la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.net Core 1,1 y versiones posteriores) cortocircuita la ejecución de los controladores `context.Fail` cuando se llama a.</span><span class="sxs-lookup"><span data-stu-id="32218-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="32218-256">`InvokeHandlersAfterFailure`tiene como valor `true`predeterminado, en cuyo caso se llama a todos los controladores.</span><span class="sxs-lookup"><span data-stu-id="32218-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="32218-257">Se llama a los controladores de autorización incluso si se produce un error de autenticación.</span><span class="sxs-lookup"><span data-stu-id="32218-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="32218-258">¿Por qué sería conveniente tener varios controladores para un requisito?</span><span class="sxs-lookup"><span data-stu-id="32218-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="32218-259">En los casos en los que desea que la evaluación esté en una base de **o** , implemente varios controladores para un único requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="32218-260">Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave.</span><span class="sxs-lookup"><span data-stu-id="32218-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="32218-261">Si deja la tarjeta de la llave en casa, el recepcionista imprime una etiqueta temporal y abre la puerta.</span><span class="sxs-lookup"><span data-stu-id="32218-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="32218-262">En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.</span><span class="sxs-lookup"><span data-stu-id="32218-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="32218-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="32218-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="32218-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="32218-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="32218-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="32218-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="32218-266">Asegúrese de que ambos controladores están [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="32218-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="32218-267">Si alguno de los controladores se realiza correctamente cuando una directiva `BuildingEntryRequirement`evalúa el, la evaluación de la Directiva se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="32218-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="32218-268">Usar un FUNC para cumplir una directiva</span><span class="sxs-lookup"><span data-stu-id="32218-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="32218-269">Puede haber situaciones en las que el cumplimiento de una directiva sea fácil de expresar en el código.</span><span class="sxs-lookup"><span data-stu-id="32218-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="32218-270">Es posible proporcionar un `Func<AuthorizationHandlerContext, bool>` al configurar la Directiva con el `RequireAssertion` generador de directivas.</span><span class="sxs-lookup"><span data-stu-id="32218-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="32218-271">Por ejemplo, el anterior `BadgeEntryHandler` podría volver a escribirse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="32218-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="32218-272">Acceso al contexto de solicitud MVC en los controladores</span><span class="sxs-lookup"><span data-stu-id="32218-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="32218-273">El `HandleRequirementAsync` método que se implementa en un controlador de autorización tiene dos parámetros `AuthorizationHandlerContext` : un `TRequirement` y el que se está controlando.</span><span class="sxs-lookup"><span data-stu-id="32218-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="32218-274">Los marcos de trabajo como MVC SignalR o pueden agregar cualquier objeto a la `Resource` propiedad en `AuthorizationHandlerContext` para pasar información adicional.</span><span class="sxs-lookup"><span data-stu-id="32218-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="32218-275">Al usar el enrutamiento de punto de conexión, la autorización se controla normalmente mediante el middleware de autorización.</span><span class="sxs-lookup"><span data-stu-id="32218-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="32218-276">En este caso, la `Resource` propiedad es una instancia de <xref:Microsoft.AspNetCore.Http.Endpoint>.</span><span class="sxs-lookup"><span data-stu-id="32218-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="32218-277">El punto de conexión se puede usar para sondear el recurso subyacente al que se va a enrutar.</span><span class="sxs-lookup"><span data-stu-id="32218-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="32218-278">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="32218-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="32218-279">Con el enrutamiento tradicional, o cuando la autorización se produce como parte del filtro de autorización de MVC `Resource` , el <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> valor de es una instancia de.</span><span class="sxs-lookup"><span data-stu-id="32218-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="32218-280">Esta propiedad proporciona acceso a `HttpContext`, `RouteData`y todo lo demás proporcionado por MVC y Razor pages.</span><span class="sxs-lookup"><span data-stu-id="32218-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="32218-281">El uso de la `Resource` propiedad es específico de Framework.</span><span class="sxs-lookup"><span data-stu-id="32218-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="32218-282">El uso de la `Resource` información en la propiedad limita las directivas de autorización a marcos concretos.</span><span class="sxs-lookup"><span data-stu-id="32218-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="32218-283">Debe convertir la `Resource` propiedad mediante la `is` palabra clave y, a continuación, confirmar que la conversión se ha realizado correctamente para asegurarse de `InvalidCastException` que el código no se bloquea con cuando se ejecuta en otros marcos:</span><span class="sxs-lookup"><span data-stu-id="32218-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
