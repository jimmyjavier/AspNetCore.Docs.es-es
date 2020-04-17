---
title: Autorización basada en directivas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo crear y usar controladores de directivas de autorización para aplicar los requisitos de autorización en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488766"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="24e9d-103">Autorización basada en directivas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24e9d-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24e9d-104">Debajo de las cubiertas, la [autorización basada en roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva preconfigurada.</span><span class="sxs-lookup"><span data-stu-id="24e9d-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="24e9d-105">Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código.</span><span class="sxs-lookup"><span data-stu-id="24e9d-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="24e9d-106">El resultado es una estructura de autorización más rica, reutilizable y comprobable.</span><span class="sxs-lookup"><span data-stu-id="24e9d-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="24e9d-107">Una directiva de autorización consta de uno o varios requisitos.</span><span class="sxs-lookup"><span data-stu-id="24e9d-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="24e9d-108">Se registra como parte de la configuración `Startup.ConfigureServices` del servicio de autorización, en el método:</span><span class="sxs-lookup"><span data-stu-id="24e9d-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="24e9d-109">En el ejemplo anterior, se crea una directiva "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="24e9d-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="24e9d-110">Tiene un único&mdash;requisito que la de una edad mínima, que se suministra como parámetro para el requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="24e9d-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="24e9d-111">IAuthorizationService</span></span> 

<span data-ttu-id="24e9d-112">El servicio primario que determina si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>la autorización es acertada es:</span><span class="sxs-lookup"><span data-stu-id="24e9d-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="24e9d-113">El código anterior resalta los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="24e9d-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="24e9d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcadores sin métodos y el mecanismo para realizar un seguimiento de si la autorización es correcta.</span><span class="sxs-lookup"><span data-stu-id="24e9d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="24e9d-115">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> uno es responsable de verificar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="24e9d-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador utiliza para marcar si se han cumplido los requisitos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="24e9d-117">El código siguiente muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:</span><span class="sxs-lookup"><span data-stu-id="24e9d-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="24e9d-118">El código siguiente `ConfigureServices`muestra un típico:</span><span class="sxs-lookup"><span data-stu-id="24e9d-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="24e9d-119">Uso <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o para autorización.</span><span class="sxs-lookup"><span data-stu-id="24e9d-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="24e9d-120">Aplicación de directivas a controladores MVC</span><span class="sxs-lookup"><span data-stu-id="24e9d-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="24e9d-121">Si usa sesión de Razor Pages, consulte [Aplicación de directivas a páginas](#applying-policies-to-razor-pages) de Razor en este documento.</span><span class="sxs-lookup"><span data-stu-id="24e9d-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="24e9d-122">Las directivas se aplican `[Authorize]` a los controladores mediante el atributo con el nombre de directiva.</span><span class="sxs-lookup"><span data-stu-id="24e9d-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="24e9d-123">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="24e9d-124">Aplicación de políticas a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="24e9d-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="24e9d-125">Las directivas se aplican a `[Authorize]` Razor Pages mediante el atributo con el nombre de la directiva.</span><span class="sxs-lookup"><span data-stu-id="24e9d-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="24e9d-126">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="24e9d-127">Las directivas también se pueden aplicar a Razor Pages mediante una convención de [autorización.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="24e9d-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="24e9d-128">Requisitos</span><span class="sxs-lookup"><span data-stu-id="24e9d-128">Requirements</span></span>

<span data-ttu-id="24e9d-129">Un requisito de autorización es una colección de parámetros de datos que una directiva puede usar para evaluar la entidad de seguridad de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="24e9d-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="24e9d-130">En nuestra política "Al menos21", el&mdash;requisito es un único parámetro de la edad mínima.</span><span class="sxs-lookup"><span data-stu-id="24e9d-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="24e9d-131">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía.</span><span class="sxs-lookup"><span data-stu-id="24e9d-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="24e9d-132">Un requisito de edad mínima parametrizado podría implementarse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="24e9d-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="24e9d-133">Si una directiva de autorización contiene varios requisitos de autorización, todos los requisitos deben pasarse para que la evaluación de directivas se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="24e9d-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="24e9d-134">En otras palabras, varios requisitos de autorización agregados a una sola directiva de autorización se tratan en una base **AND.**</span><span class="sxs-lookup"><span data-stu-id="24e9d-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="24e9d-135">Un requisito no necesita tener datos o propiedades.</span><span class="sxs-lookup"><span data-stu-id="24e9d-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="24e9d-136">Controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="24e9d-136">Authorization handlers</span></span>

<span data-ttu-id="24e9d-137">Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="24e9d-138">El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.</span><span class="sxs-lookup"><span data-stu-id="24e9d-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="24e9d-139">Un requisito puede tener [varios controladores.](#security-authorization-policies-based-multiple-handlers)</span><span class="sxs-lookup"><span data-stu-id="24e9d-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="24e9d-140">Un controlador puede heredar [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` está el requisito que se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="24e9d-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="24e9d-141">Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para controlar más de un tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="24e9d-142">Use un controlador para un requisito</span><span class="sxs-lookup"><span data-stu-id="24e9d-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="24e9d-143">A continuación se muestra un ejemplo de una relación uno a uno en la que un controlador de edad mínima utiliza un único requisito:</span><span class="sxs-lookup"><span data-stu-id="24e9d-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="24e9d-144">El código anterior determina si la entidad de seguridad de usuario actual tiene una fecha de notificación de nacimiento que ha sido emitida por un emisor conocido y de confianza.</span><span class="sxs-lookup"><span data-stu-id="24e9d-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="24e9d-145">La autorización no puede producirse cuando falta la notificación, en cuyo caso se devuelve una tarea completada.</span><span class="sxs-lookup"><span data-stu-id="24e9d-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="24e9d-146">Cuando hay una notificación, se calcula la edad del usuario.</span><span class="sxs-lookup"><span data-stu-id="24e9d-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="24e9d-147">Si el usuario cumple con la edad mínima definida por el requisito, la autorización se considera correcta.</span><span class="sxs-lookup"><span data-stu-id="24e9d-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="24e9d-148">Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como único parámetro.</span><span class="sxs-lookup"><span data-stu-id="24e9d-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="24e9d-149">Use un controlador para varios requisitos</span><span class="sxs-lookup"><span data-stu-id="24e9d-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="24e9d-150">A continuación se muestra un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="24e9d-151">El código anterior atraviesa [PendingRequirements,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una propiedad que contiene requisitos no marcados como correctos.</span><span class="sxs-lookup"><span data-stu-id="24e9d-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="24e9d-152">Para `ReadPermission` un requisito, el usuario debe ser un propietario o un patrocinador para acceder al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="24e9d-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="24e9d-153">En el caso `EditPermission` `DeletePermission` de un requisito o requisito, debe ser propietario para acceder al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="24e9d-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="24e9d-154">Registro del controlador</span><span class="sxs-lookup"><span data-stu-id="24e9d-154">Handler registration</span></span>

<span data-ttu-id="24e9d-155">Los controladores se registran en la colección de servicios durante la configuración.</span><span class="sxs-lookup"><span data-stu-id="24e9d-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="24e9d-156">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="24e9d-157">El código anterior `MinimumAgeHandler` se registra como singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="24e9d-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="24e9d-158">Los controladores se pueden registrar mediante cualquiera de las vidas de [servicio integradas.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="24e9d-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="24e9d-159">¿Qué debe devolver un controlador?</span><span class="sxs-lookup"><span data-stu-id="24e9d-159">What should a handler return?</span></span>

<span data-ttu-id="24e9d-160">Tenga en `Handle` cuenta que el método en el ejemplo de [controlador](#security-authorization-handler-example) no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="24e9d-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="24e9d-161">¿Cómo se indica un estado de éxito o error?</span><span class="sxs-lookup"><span data-stu-id="24e9d-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="24e9d-162">Un controlador indica que `context.Succeed(IAuthorizationRequirement requirement)`se ha realizado correctamente, pasando el requisito que se ha validado correctamente.</span><span class="sxs-lookup"><span data-stu-id="24e9d-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="24e9d-163">Un controlador no necesita controlar los errores en general, ya que otros controladores para el mismo requisito pueden tener éxito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="24e9d-164">Para garantizar un error, incluso si `context.Fail`otros controladores de requisitos se realizan correctamente, llame a .</span><span class="sxs-lookup"><span data-stu-id="24e9d-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="24e9d-165">Si un `context.Succeed` controlador `context.Fail`llama o , todos los demás controladores todavía se llaman.</span><span class="sxs-lookup"><span data-stu-id="24e9d-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="24e9d-166">Esto permite que los requisitos produzcan efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado correctamente o ha fallado un requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="24e9d-167">Cuando se `false`establece en , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` y versiones posteriores) cortocircuita la ejecución de controladores cuando se llama.</span><span class="sxs-lookup"><span data-stu-id="24e9d-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="24e9d-168">`InvokeHandlersAfterFailure`de forma `true`predeterminada , en cuyo caso se llama a todos los controladores.</span><span class="sxs-lookup"><span data-stu-id="24e9d-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="24e9d-169">Se llama a los controladores de autorización incluso si se produce un error en la autenticación.</span><span class="sxs-lookup"><span data-stu-id="24e9d-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="24e9d-170">¿Por qué querría varios controladores para un requisito?</span><span class="sxs-lookup"><span data-stu-id="24e9d-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="24e9d-171">En los casos en los que desee que la evaluación sea sobre una base **OR,** implemente varios controladores para un único requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="24e9d-172">Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave.</span><span class="sxs-lookup"><span data-stu-id="24e9d-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="24e9d-173">Si deja su tarjeta de acceso en casa, la recepcionista imprime una etiqueta adhesiva temporal y le abre la puerta.</span><span class="sxs-lookup"><span data-stu-id="24e9d-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="24e9d-174">En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="24e9d-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="24e9d-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="24e9d-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="24e9d-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="24e9d-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="24e9d-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="24e9d-178">Asegúrese de que ambos controladores están [registrados.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)</span><span class="sxs-lookup"><span data-stu-id="24e9d-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="24e9d-179">Si cualquiera de los controladores `BuildingEntryRequirement`se realiza correctamente cuando una directiva evalúa el valor , la evaluación de directivas se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="24e9d-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="24e9d-180">Usar un func para cumplir una política</span><span class="sxs-lookup"><span data-stu-id="24e9d-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="24e9d-181">Puede haber situaciones en las que el cumplimiento de una política sea fácil de expresar en código.</span><span class="sxs-lookup"><span data-stu-id="24e9d-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="24e9d-182">Es posible proporcionar una `Func<AuthorizationHandlerContext, bool>` al configurar la `RequireAssertion` directiva con el generador de directivas.</span><span class="sxs-lookup"><span data-stu-id="24e9d-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="24e9d-183">Por ejemplo, `BadgeEntryHandler` el anterior podría reescribirse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="24e9d-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="24e9d-184">Acceso al contexto de solicitud MVC en controladores</span><span class="sxs-lookup"><span data-stu-id="24e9d-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="24e9d-185">El `HandleRequirementAsync` método que implementa en un controlador `AuthorizationHandlerContext` de `TRequirement` autorización tiene dos parámetros: a y el que está controlando.</span><span class="sxs-lookup"><span data-stu-id="24e9d-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="24e9d-186">Los marcos de trabajo como MVC o Jabbr `Resource` son `AuthorizationHandlerContext` libres de agregar cualquier objeto a la propiedad en el para pasar información adicional.</span><span class="sxs-lookup"><span data-stu-id="24e9d-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="24e9d-187">Por ejemplo, MVC pasa una instancia de `Resource` [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) en la propiedad.</span><span class="sxs-lookup"><span data-stu-id="24e9d-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="24e9d-188">Esta propiedad proporciona `HttpContext` `RouteData`acceso a , , y todo lo demás proporcionado por MVC y Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="24e9d-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="24e9d-189">El uso `Resource` de la propiedad es específico del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="24e9d-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="24e9d-190">El uso `Resource` de la información de la propiedad limita las directivas de autorización a marcos concretos.</span><span class="sxs-lookup"><span data-stu-id="24e9d-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="24e9d-191">Debe convertir `Resource` la propiedad `is` mediante la palabra clave y, a continuación, confirmar que `InvalidCastException` la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con un cuando se ejecuta en otros marcos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="24e9d-192">Debajo de las cubiertas, la [autorización basada en roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva preconfigurada.</span><span class="sxs-lookup"><span data-stu-id="24e9d-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="24e9d-193">Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código.</span><span class="sxs-lookup"><span data-stu-id="24e9d-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="24e9d-194">El resultado es una estructura de autorización más rica, reutilizable y comprobable.</span><span class="sxs-lookup"><span data-stu-id="24e9d-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="24e9d-195">Una directiva de autorización consta de uno o varios requisitos.</span><span class="sxs-lookup"><span data-stu-id="24e9d-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="24e9d-196">Se registra como parte de la configuración `Startup.ConfigureServices` del servicio de autorización, en el método:</span><span class="sxs-lookup"><span data-stu-id="24e9d-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="24e9d-197">En el ejemplo anterior, se crea una directiva "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="24e9d-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="24e9d-198">Tiene un único&mdash;requisito que la de una edad mínima, que se suministra como parámetro para el requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="24e9d-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="24e9d-199">IAuthorizationService</span></span> 

<span data-ttu-id="24e9d-200">El servicio primario que determina si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>la autorización es acertada es:</span><span class="sxs-lookup"><span data-stu-id="24e9d-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="24e9d-201">El código anterior resalta los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="24e9d-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="24e9d-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcadores sin métodos y el mecanismo para realizar un seguimiento de si la autorización es correcta.</span><span class="sxs-lookup"><span data-stu-id="24e9d-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="24e9d-203">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> uno es responsable de verificar si se cumplen los requisitos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="24e9d-204">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador utiliza para marcar si se han cumplido los requisitos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="24e9d-205">El código siguiente muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:</span><span class="sxs-lookup"><span data-stu-id="24e9d-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="24e9d-206">El código siguiente `ConfigureServices`muestra un típico:</span><span class="sxs-lookup"><span data-stu-id="24e9d-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="24e9d-207">Uso <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o para autorización.</span><span class="sxs-lookup"><span data-stu-id="24e9d-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="24e9d-208">Aplicación de directivas a controladores MVC</span><span class="sxs-lookup"><span data-stu-id="24e9d-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="24e9d-209">Si usa sesión de Razor Pages, consulte [Aplicación de directivas a páginas](#applying-policies-to-razor-pages) de Razor en este documento.</span><span class="sxs-lookup"><span data-stu-id="24e9d-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="24e9d-210">Las directivas se aplican `[Authorize]` a los controladores mediante el atributo con el nombre de directiva.</span><span class="sxs-lookup"><span data-stu-id="24e9d-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="24e9d-211">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="24e9d-212">Aplicación de políticas a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="24e9d-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="24e9d-213">Las directivas se aplican a `[Authorize]` Razor Pages mediante el atributo con el nombre de la directiva.</span><span class="sxs-lookup"><span data-stu-id="24e9d-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="24e9d-214">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="24e9d-215">Las directivas también se pueden aplicar a Razor Pages mediante una convención de [autorización.](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="24e9d-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="24e9d-216">Requisitos</span><span class="sxs-lookup"><span data-stu-id="24e9d-216">Requirements</span></span>

<span data-ttu-id="24e9d-217">Un requisito de autorización es una colección de parámetros de datos que una directiva puede usar para evaluar la entidad de seguridad de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="24e9d-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="24e9d-218">En nuestra política "Al menos21", el&mdash;requisito es un único parámetro de la edad mínima.</span><span class="sxs-lookup"><span data-stu-id="24e9d-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="24e9d-219">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía.</span><span class="sxs-lookup"><span data-stu-id="24e9d-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="24e9d-220">Un requisito de edad mínima parametrizado podría implementarse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="24e9d-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="24e9d-221">Si una directiva de autorización contiene varios requisitos de autorización, todos los requisitos deben pasarse para que la evaluación de directivas se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="24e9d-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="24e9d-222">En otras palabras, varios requisitos de autorización agregados a una sola directiva de autorización se tratan en una base **AND.**</span><span class="sxs-lookup"><span data-stu-id="24e9d-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="24e9d-223">Un requisito no necesita tener datos o propiedades.</span><span class="sxs-lookup"><span data-stu-id="24e9d-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="24e9d-224">Controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="24e9d-224">Authorization handlers</span></span>

<span data-ttu-id="24e9d-225">Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="24e9d-226">El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.</span><span class="sxs-lookup"><span data-stu-id="24e9d-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="24e9d-227">Un requisito puede tener [varios controladores.](#security-authorization-policies-based-multiple-handlers)</span><span class="sxs-lookup"><span data-stu-id="24e9d-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="24e9d-228">Un controlador puede heredar [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` está el requisito que se debe controlar.</span><span class="sxs-lookup"><span data-stu-id="24e9d-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="24e9d-229">Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para controlar más de un tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="24e9d-230">Use un controlador para un requisito</span><span class="sxs-lookup"><span data-stu-id="24e9d-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="24e9d-231">A continuación se muestra un ejemplo de una relación uno a uno en la que un controlador de edad mínima utiliza un único requisito:</span><span class="sxs-lookup"><span data-stu-id="24e9d-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="24e9d-232">El código anterior determina si la entidad de seguridad de usuario actual tiene una fecha de notificación de nacimiento que ha sido emitida por un emisor conocido y de confianza.</span><span class="sxs-lookup"><span data-stu-id="24e9d-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="24e9d-233">La autorización no puede producirse cuando falta la notificación, en cuyo caso se devuelve una tarea completada.</span><span class="sxs-lookup"><span data-stu-id="24e9d-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="24e9d-234">Cuando hay una notificación, se calcula la edad del usuario.</span><span class="sxs-lookup"><span data-stu-id="24e9d-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="24e9d-235">Si el usuario cumple con la edad mínima definida por el requisito, la autorización se considera correcta.</span><span class="sxs-lookup"><span data-stu-id="24e9d-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="24e9d-236">Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como único parámetro.</span><span class="sxs-lookup"><span data-stu-id="24e9d-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="24e9d-237">Use un controlador para varios requisitos</span><span class="sxs-lookup"><span data-stu-id="24e9d-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="24e9d-238">A continuación se muestra un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="24e9d-239">El código anterior atraviesa [PendingRequirements,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una propiedad que contiene requisitos no marcados como correctos.</span><span class="sxs-lookup"><span data-stu-id="24e9d-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="24e9d-240">Para `ReadPermission` un requisito, el usuario debe ser un propietario o un patrocinador para acceder al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="24e9d-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="24e9d-241">En el caso `EditPermission` `DeletePermission` de un requisito o requisito, debe ser propietario para acceder al recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="24e9d-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="24e9d-242">Registro del controlador</span><span class="sxs-lookup"><span data-stu-id="24e9d-242">Handler registration</span></span>

<span data-ttu-id="24e9d-243">Los controladores se registran en la colección de servicios durante la configuración.</span><span class="sxs-lookup"><span data-stu-id="24e9d-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="24e9d-244">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="24e9d-245">El código anterior `MinimumAgeHandler` se registra como singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="24e9d-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="24e9d-246">Los controladores se pueden registrar mediante cualquiera de las vidas de [servicio integradas.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="24e9d-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="24e9d-247">¿Qué debe devolver un controlador?</span><span class="sxs-lookup"><span data-stu-id="24e9d-247">What should a handler return?</span></span>

<span data-ttu-id="24e9d-248">Tenga en `Handle` cuenta que el método en el ejemplo de [controlador](#security-authorization-handler-example) no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="24e9d-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="24e9d-249">¿Cómo se indica un estado de éxito o error?</span><span class="sxs-lookup"><span data-stu-id="24e9d-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="24e9d-250">Un controlador indica que `context.Succeed(IAuthorizationRequirement requirement)`se ha realizado correctamente, pasando el requisito que se ha validado correctamente.</span><span class="sxs-lookup"><span data-stu-id="24e9d-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="24e9d-251">Un controlador no necesita controlar los errores en general, ya que otros controladores para el mismo requisito pueden tener éxito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="24e9d-252">Para garantizar un error, incluso si `context.Fail`otros controladores de requisitos se realizan correctamente, llame a .</span><span class="sxs-lookup"><span data-stu-id="24e9d-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="24e9d-253">Si un `context.Succeed` controlador `context.Fail`llama o , todos los demás controladores todavía se llaman.</span><span class="sxs-lookup"><span data-stu-id="24e9d-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="24e9d-254">Esto permite que los requisitos produzcan efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado correctamente o ha fallado un requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="24e9d-255">Cuando se `false`establece en , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` y versiones posteriores) cortocircuita la ejecución de controladores cuando se llama.</span><span class="sxs-lookup"><span data-stu-id="24e9d-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="24e9d-256">`InvokeHandlersAfterFailure`de forma `true`predeterminada , en cuyo caso se llama a todos los controladores.</span><span class="sxs-lookup"><span data-stu-id="24e9d-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="24e9d-257">Se llama a los controladores de autorización incluso si se produce un error en la autenticación.</span><span class="sxs-lookup"><span data-stu-id="24e9d-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="24e9d-258">¿Por qué querría varios controladores para un requisito?</span><span class="sxs-lookup"><span data-stu-id="24e9d-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="24e9d-259">En los casos en los que desee que la evaluación sea sobre una base **OR,** implemente varios controladores para un único requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="24e9d-260">Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave.</span><span class="sxs-lookup"><span data-stu-id="24e9d-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="24e9d-261">Si deja su tarjeta de acceso en casa, la recepcionista imprime una etiqueta adhesiva temporal y le abre la puerta.</span><span class="sxs-lookup"><span data-stu-id="24e9d-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="24e9d-262">En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.</span><span class="sxs-lookup"><span data-stu-id="24e9d-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="24e9d-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="24e9d-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="24e9d-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="24e9d-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="24e9d-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="24e9d-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="24e9d-266">Asegúrese de que ambos controladores están [registrados.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)</span><span class="sxs-lookup"><span data-stu-id="24e9d-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="24e9d-267">Si cualquiera de los controladores `BuildingEntryRequirement`se realiza correctamente cuando una directiva evalúa el valor , la evaluación de directivas se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="24e9d-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="24e9d-268">Usar un func para cumplir una política</span><span class="sxs-lookup"><span data-stu-id="24e9d-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="24e9d-269">Puede haber situaciones en las que el cumplimiento de una política sea fácil de expresar en código.</span><span class="sxs-lookup"><span data-stu-id="24e9d-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="24e9d-270">Es posible proporcionar una `Func<AuthorizationHandlerContext, bool>` al configurar la `RequireAssertion` directiva con el generador de directivas.</span><span class="sxs-lookup"><span data-stu-id="24e9d-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="24e9d-271">Por ejemplo, `BadgeEntryHandler` el anterior podría reescribirse de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="24e9d-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="24e9d-272">Acceso al contexto de solicitud MVC en controladores</span><span class="sxs-lookup"><span data-stu-id="24e9d-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="24e9d-273">El `HandleRequirementAsync` método que implementa en un controlador `AuthorizationHandlerContext` de `TRequirement` autorización tiene dos parámetros: a y el que está controlando.</span><span class="sxs-lookup"><span data-stu-id="24e9d-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="24e9d-274">Los marcos de trabajo como MVC o SignalR `Resource` son `AuthorizationHandlerContext` libres de agregar cualquier objeto a la propiedad en el para pasar información adicional.</span><span class="sxs-lookup"><span data-stu-id="24e9d-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="24e9d-275">Cuando se utiliza el enrutamiento de punto final, el Middleware de autorización controla normalmente la autorización.</span><span class="sxs-lookup"><span data-stu-id="24e9d-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="24e9d-276">En este caso, la `Resource` propiedad <xref:Microsoft.AspNetCore.Http.Endpoint>es una instancia de .</span><span class="sxs-lookup"><span data-stu-id="24e9d-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="24e9d-277">El punto de conexión se puede usar para sondear el recurso subyacente al que está enrutando.</span><span class="sxs-lookup"><span data-stu-id="24e9d-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="24e9d-278">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="24e9d-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="24e9d-279">Con el enrutamiento tradicional, o cuando la autorización se `Resource` produce <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> como parte del filtro de autorización de MVC, el valor de es una instancia.</span><span class="sxs-lookup"><span data-stu-id="24e9d-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="24e9d-280">Esta propiedad proporciona `HttpContext` `RouteData`acceso a , , y todo lo demás proporcionado por MVC y Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="24e9d-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="24e9d-281">El uso `Resource` de la propiedad es específico del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="24e9d-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="24e9d-282">El uso `Resource` de la información de la propiedad limita las directivas de autorización a marcos concretos.</span><span class="sxs-lookup"><span data-stu-id="24e9d-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="24e9d-283">Debe convertir `Resource` la propiedad `is` mediante la palabra clave y, a continuación, confirmar que `InvalidCastException` la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con un cuando se ejecuta en otros marcos:</span><span class="sxs-lookup"><span data-stu-id="24e9d-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
