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
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorización basada en directivas en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Debajo de las cubiertas, la [autorización basada en roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva preconfigurada. Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código. El resultado es una estructura de autorización más rica, reutilizable y comprobable.

Una directiva de autorización consta de uno o varios requisitos. Se registra como parte de la configuración `Startup.ConfigureServices` del servicio de autorización, en el método:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

En el ejemplo anterior, se crea una directiva "AtLeast21". Tiene un único&mdash;requisito que la de una edad mínima, que se suministra como parámetro para el requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

El servicio primario que determina si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>la autorización es acertada es:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

El código anterior resalta los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcadores sin métodos y el mecanismo para realizar un seguimiento de si la autorización es correcta.

Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> uno es responsable de verificar si se cumplen los requisitos:
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

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador utiliza para marcar si se han cumplido los requisitos:

```csharp
 context.Succeed(requirement)
```

El código siguiente muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:

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

El código siguiente `ConfigureServices`muestra un típico:

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

Uso <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o para autorización.

## <a name="applying-policies-to-mvc-controllers"></a>Aplicación de directivas a controladores MVC

Si usa sesión de Razor Pages, consulte [Aplicación de directivas a páginas](#applying-policies-to-razor-pages) de Razor en este documento.

Las directivas se aplican `[Authorize]` a los controladores mediante el atributo con el nombre de directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Aplicación de políticas a Razor Pages

Las directivas se aplican a `[Authorize]` Razor Pages mediante el atributo con el nombre de la directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Las directivas también se pueden aplicar a Razor Pages mediante una convención de [autorización.](xref:security/authorization/razor-pages-authorization)

## <a name="requirements"></a>Requisitos

Un requisito de autorización es una colección de parámetros de datos que una directiva puede usar para evaluar la entidad de seguridad de usuario actual. En nuestra política "Al menos21", el&mdash;requisito es un único parámetro de la edad mínima. Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía. Un requisito de edad mínima parametrizado podría implementarse de la siguiente manera:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si una directiva de autorización contiene varios requisitos de autorización, todos los requisitos deben pasarse para que la evaluación de directivas se realice correctamente. En otras palabras, varios requisitos de autorización agregados a una sola directiva de autorización se tratan en una base **AND.**

> [!NOTE]
> Un requisito no necesita tener datos o propiedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Controladores de autorización

Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito. El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.

Un requisito puede tener [varios controladores.](#security-authorization-policies-based-multiple-handlers) Un controlador puede heredar [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` está el requisito que se debe controlar. Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para controlar más de un tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Use un controlador para un requisito

<a name="security-authorization-handler-example"></a>

A continuación se muestra un ejemplo de una relación uno a uno en la que un controlador de edad mínima utiliza un único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

El código anterior determina si la entidad de seguridad de usuario actual tiene una fecha de notificación de nacimiento que ha sido emitida por un emisor conocido y de confianza. La autorización no puede producirse cuando falta la notificación, en cuyo caso se devuelve una tarea completada. Cuando hay una notificación, se calcula la edad del usuario. Si el usuario cumple con la edad mínima definida por el requisito, la autorización se considera correcta. Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como único parámetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Use un controlador para varios requisitos

A continuación se muestra un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

El código anterior atraviesa [PendingRequirements,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una propiedad que contiene requisitos no marcados como correctos. Para `ReadPermission` un requisito, el usuario debe ser un propietario o un patrocinador para acceder al recurso solicitado. En el caso `EditPermission` `DeletePermission` de un requisito o requisito, debe ser propietario para acceder al recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro del controlador

Los controladores se registran en la colección de servicios durante la configuración. Por ejemplo:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

El código anterior `MinimumAgeHandler` se registra como singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Los controladores se pueden registrar mediante cualquiera de las vidas de [servicio integradas.](xref:fundamentals/dependency-injection#service-lifetimes)

## <a name="what-should-a-handler-return"></a>¿Qué debe devolver un controlador?

Tenga en `Handle` cuenta que el método en el ejemplo de [controlador](#security-authorization-handler-example) no devuelve ningún valor. ¿Cómo se indica un estado de éxito o error?

* Un controlador indica que `context.Succeed(IAuthorizationRequirement requirement)`se ha realizado correctamente, pasando el requisito que se ha validado correctamente.

* Un controlador no necesita controlar los errores en general, ya que otros controladores para el mismo requisito pueden tener éxito.

* Para garantizar un error, incluso si `context.Fail`otros controladores de requisitos se realizan correctamente, llame a .

Si un `context.Succeed` controlador `context.Fail`llama o , todos los demás controladores todavía se llaman. Esto permite que los requisitos produzcan efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado correctamente o ha fallado un requisito. Cuando se `false`establece en , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` y versiones posteriores) cortocircuita la ejecución de controladores cuando se llama. `InvokeHandlersAfterFailure`de forma `true`predeterminada , en cuyo caso se llama a todos los controladores.

> [!NOTE]
> Se llama a los controladores de autorización incluso si se produce un error en la autenticación.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>¿Por qué querría varios controladores para un requisito?

En los casos en los que desee que la evaluación sea sobre una base **OR,** implemente varios controladores para un único requisito. Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave. Si deja su tarjeta de acceso en casa, la recepcionista imprime una etiqueta adhesiva temporal y le abre la puerta. En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Asegúrese de que ambos controladores están [registrados.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) Si cualquiera de los controladores `BuildingEntryRequirement`se realiza correctamente cuando una directiva evalúa el valor , la evaluación de directivas se realiza correctamente.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usar un func para cumplir una política

Puede haber situaciones en las que el cumplimiento de una política sea fácil de expresar en código. Es posible proporcionar una `Func<AuthorizationHandlerContext, bool>` al configurar la `RequireAssertion` directiva con el generador de directivas.

Por ejemplo, `BadgeEntryHandler` el anterior podría reescribirse de la siguiente manera:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acceso al contexto de solicitud MVC en controladores

El `HandleRequirementAsync` método que implementa en un controlador `AuthorizationHandlerContext` de `TRequirement` autorización tiene dos parámetros: a y el que está controlando. Los marcos de trabajo como MVC o Jabbr `Resource` son `AuthorizationHandlerContext` libres de agregar cualquier objeto a la propiedad en el para pasar información adicional.

Por ejemplo, MVC pasa una instancia de `Resource` [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) en la propiedad. Esta propiedad proporciona `HttpContext` `RouteData`acceso a , , y todo lo demás proporcionado por MVC y Razor Pages.

El uso `Resource` de la propiedad es específico del marco de trabajo. El uso `Resource` de la información de la propiedad limita las directivas de autorización a marcos concretos. Debe convertir `Resource` la propiedad `is` mediante la palabra clave y, a continuación, confirmar que `InvalidCastException` la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con un cuando se ejecuta en otros marcos:

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

Debajo de las cubiertas, la [autorización basada en roles](xref:security/authorization/roles) y la [autorización basada en notificaciones](xref:security/authorization/claims) usan un requisito, un controlador de requisitos y una directiva preconfigurada. Estos bloques de creación admiten la expresión de evaluaciones de autorización en el código. El resultado es una estructura de autorización más rica, reutilizable y comprobable.

Una directiva de autorización consta de uno o varios requisitos. Se registra como parte de la configuración `Startup.ConfigureServices` del servicio de autorización, en el método:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

En el ejemplo anterior, se crea una directiva "AtLeast21". Tiene un único&mdash;requisito que la de una edad mínima, que se suministra como parámetro para el requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

El servicio primario que determina si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>la autorización es acertada es:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

El código anterior resalta los dos métodos de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>es un servicio de marcadores sin métodos y el mecanismo para realizar un seguimiento de si la autorización es correcta.

Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> uno es responsable de verificar si se cumplen los requisitos:
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

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> clase es lo que el controlador utiliza para marcar si se han cumplido los requisitos:

```csharp
 context.Succeed(requirement)
```

El código siguiente muestra la implementación predeterminada simplificada (y anotada con comentarios) del servicio de autorización:

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

El código siguiente `ConfigureServices`muestra un típico:

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

Uso <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o para autorización.

## <a name="applying-policies-to-mvc-controllers"></a>Aplicación de directivas a controladores MVC

Si usa sesión de Razor Pages, consulte [Aplicación de directivas a páginas](#applying-policies-to-razor-pages) de Razor en este documento.

Las directivas se aplican `[Authorize]` a los controladores mediante el atributo con el nombre de directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Aplicación de políticas a Razor Pages

Las directivas se aplican a `[Authorize]` Razor Pages mediante el atributo con el nombre de la directiva. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Las directivas también se pueden aplicar a Razor Pages mediante una convención de [autorización.](xref:security/authorization/razor-pages-authorization)

## <a name="requirements"></a>Requisitos

Un requisito de autorización es una colección de parámetros de datos que una directiva puede usar para evaluar la entidad de seguridad de usuario actual. En nuestra política "Al menos21", el&mdash;requisito es un único parámetro de la edad mínima. Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que es una interfaz de marcador vacía. Un requisito de edad mínima parametrizado podría implementarse de la siguiente manera:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si una directiva de autorización contiene varios requisitos de autorización, todos los requisitos deben pasarse para que la evaluación de directivas se realice correctamente. En otras palabras, varios requisitos de autorización agregados a una sola directiva de autorización se tratan en una base **AND.**

> [!NOTE]
> Un requisito no necesita tener datos o propiedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Controladores de autorización

Un controlador de autorización es responsable de la evaluación de las propiedades de un requisito. El controlador de autorización evalúa los requisitos con respecto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) proporcionado para determinar si se permite el acceso.

Un requisito puede tener [varios controladores.](#security-authorization-policies-based-multiple-handlers) Un controlador puede heredar [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), donde `TRequirement` está el requisito que se debe controlar. Como alternativa, un controlador puede implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para controlar más de un tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Use un controlador para un requisito

<a name="security-authorization-handler-example"></a>

A continuación se muestra un ejemplo de una relación uno a uno en la que un controlador de edad mínima utiliza un único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

El código anterior determina si la entidad de seguridad de usuario actual tiene una fecha de notificación de nacimiento que ha sido emitida por un emisor conocido y de confianza. La autorización no puede producirse cuando falta la notificación, en cuyo caso se devuelve una tarea completada. Cuando hay una notificación, se calcula la edad del usuario. Si el usuario cumple con la edad mínima definida por el requisito, la autorización se considera correcta. Cuando la autorización se realiza correctamente, `context.Succeed` se invoca con el requisito satisfecho como único parámetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Use un controlador para varios requisitos

A continuación se muestra un ejemplo de una relación de uno a varios en la que un controlador de permisos puede controlar tres tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

El código anterior atraviesa [PendingRequirements,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una propiedad que contiene requisitos no marcados como correctos. Para `ReadPermission` un requisito, el usuario debe ser un propietario o un patrocinador para acceder al recurso solicitado. En el caso `EditPermission` `DeletePermission` de un requisito o requisito, debe ser propietario para acceder al recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro del controlador

Los controladores se registran en la colección de servicios durante la configuración. Por ejemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

El código anterior `MinimumAgeHandler` se registra como singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Los controladores se pueden registrar mediante cualquiera de las vidas de [servicio integradas.](xref:fundamentals/dependency-injection#service-lifetimes)

## <a name="what-should-a-handler-return"></a>¿Qué debe devolver un controlador?

Tenga en `Handle` cuenta que el método en el ejemplo de [controlador](#security-authorization-handler-example) no devuelve ningún valor. ¿Cómo se indica un estado de éxito o error?

* Un controlador indica que `context.Succeed(IAuthorizationRequirement requirement)`se ha realizado correctamente, pasando el requisito que se ha validado correctamente.

* Un controlador no necesita controlar los errores en general, ya que otros controladores para el mismo requisito pueden tener éxito.

* Para garantizar un error, incluso si `context.Fail`otros controladores de requisitos se realizan correctamente, llame a .

Si un `context.Succeed` controlador `context.Fail`llama o , todos los demás controladores todavía se llaman. Esto permite que los requisitos produzcan efectos secundarios, como el registro, que tiene lugar incluso si otro controlador ha validado correctamente o ha fallado un requisito. Cuando se `false`establece en , la propiedad [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` y versiones posteriores) cortocircuita la ejecución de controladores cuando se llama. `InvokeHandlersAfterFailure`de forma `true`predeterminada , en cuyo caso se llama a todos los controladores.

> [!NOTE]
> Se llama a los controladores de autorización incluso si se produce un error en la autenticación.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>¿Por qué querría varios controladores para un requisito?

En los casos en los que desee que la evaluación sea sobre una base **OR,** implemente varios controladores para un único requisito. Por ejemplo, Microsoft tiene puertas que solo se abren con tarjetas clave. Si deja su tarjeta de acceso en casa, la recepcionista imprime una etiqueta adhesiva temporal y le abre la puerta. En este escenario, tendría un único requisito, *BuildingEntry*, pero varios controladores, cada uno examinando un único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Asegúrese de que ambos controladores están [registrados.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) Si cualquiera de los controladores `BuildingEntryRequirement`se realiza correctamente cuando una directiva evalúa el valor , la evaluación de directivas se realiza correctamente.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usar un func para cumplir una política

Puede haber situaciones en las que el cumplimiento de una política sea fácil de expresar en código. Es posible proporcionar una `Func<AuthorizationHandlerContext, bool>` al configurar la `RequireAssertion` directiva con el generador de directivas.

Por ejemplo, `BadgeEntryHandler` el anterior podría reescribirse de la siguiente manera:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acceso al contexto de solicitud MVC en controladores

El `HandleRequirementAsync` método que implementa en un controlador `AuthorizationHandlerContext` de `TRequirement` autorización tiene dos parámetros: a y el que está controlando. Los marcos de trabajo como MVC o SignalR `Resource` son `AuthorizationHandlerContext` libres de agregar cualquier objeto a la propiedad en el para pasar información adicional.

Cuando se utiliza el enrutamiento de punto final, el Middleware de autorización controla normalmente la autorización. En este caso, la `Resource` propiedad <xref:Microsoft.AspNetCore.Http.Endpoint>es una instancia de . El punto de conexión se puede usar para sondear el recurso subyacente al que está enrutando. Por ejemplo:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Con el enrutamiento tradicional, o cuando la autorización se `Resource` produce <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> como parte del filtro de autorización de MVC, el valor de es una instancia. Esta propiedad proporciona `HttpContext` `RouteData`acceso a , , y todo lo demás proporcionado por MVC y Razor Pages.

El uso `Resource` de la propiedad es específico del marco de trabajo. El uso `Resource` de la información de la propiedad limita las directivas de autorización a marcos concretos. Debe convertir `Resource` la propiedad `is` mediante la palabra clave y, a continuación, confirmar que `InvalidCastException` la conversión se ha realizado correctamente para asegurarse de que el código no se bloquea con un cuando se ejecuta en otros marcos:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
