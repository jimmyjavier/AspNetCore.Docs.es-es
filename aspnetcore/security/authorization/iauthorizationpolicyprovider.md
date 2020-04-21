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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Proveedores de directivas de autorización personalizada que usan IAuthorizationPolicyProvider en ASP.NET Core 

Por [Mike Rousos](https://github.com/mjrousos)

Normalmente, cuando se usa la [autorización basada en directivas](xref:security/authorization/policies), las directivas se registran llamando como `AuthorizationOptions.AddPolicy` parte de la configuración del servicio de autorización. En algunos escenarios, puede que no sea posible (o deseable) registrar todas las directivas de autorización de esta manera. En esos casos, puede `IAuthorizationPolicyProvider` usar una personalizada para controlar cómo se proporcionan las directivas de autorización.

Entre los ejemplos de escenarios en los que un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado puede ser útil se incluyen:

* Uso de un servicio externo para proporcionar evaluación de directivas.
* Usar una amplia gama de directivas (para diferentes números de habitación o edades, por ejemplo), por lo que no tiene sentido agregar cada directiva de autorización individual con una `AuthorizationOptions.AddPolicy` llamada.
* Crear directivas en tiempo de ejecución basadas en información de un origen de datos externo (como una base de datos) o determinar los requisitos de autorización dinámicamente a través de otro mecanismo.

[Vea o descargue código](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) de ejemplo desde el [repositorio De AspNetCore GitHub.](https://github.com/dotnet/AspNetCore) Descargue el archivo ZIP del repositorio dotnet/AspNetCore. Descomprima el archivo. Vaya a la carpeta del proyecto *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Personalizar la recuperación de políticas

ASP.NET aplicaciones principales usan `IAuthorizationPolicyProvider` una implementación de la interfaz para recuperar directivas de autorización. De forma predeterminada, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) está registrado y se utiliza. `DefaultAuthorizationPolicyProvider`políticas de `AuthorizationOptions` devolución de `IServiceCollection.AddAuthorization` lo proporcionado en una llamada.

Personalice este comportamiento registrando una implementación diferente `IAuthorizationPolicyProvider` en el contenedor de [inserción](xref:fundamentals/dependency-injection) de dependencias de la aplicación. 

La `IAuthorizationPolicyProvider` interfaz contiene tres API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) devuelve una directiva de autorización para un nombre determinado.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) devuelve la directiva de autorización predeterminada (la directiva utilizada para `[Authorize]` los atributos sin una directiva especificada). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) devuelve la directiva de autorización de reserva (la directiva utilizada por el Middleware de autorización cuando no se especifica ninguna directiva). 

Mediante la implementación de estas API, puede personalizar cómo se proporcionan las directivas de autorización.

## <a name="parameterized-authorize-attribute-example"></a>Ejemplo de atributo de autorización parametrizado

Un escenario `IAuthorizationPolicyProvider` en el `[Authorize]` que es útil es habilitar atributos personalizados cuyos requisitos dependen de un parámetro. Por ejemplo, en la documentación de [autorización basada en directivas,](xref:security/authorization/policies) se usó una directiva basada en la edad ("AtLeast21") como ejemplo. Si se deben poner a disposición de usuarios de *diferentes* edades acciones de controlador diferentes, podría ser útil tener muchas directivas diferentes basadas en la edad. En lugar de registrar todas las diferentes directivas basadas en la edad que necesitará la aplicación, `AuthorizationOptions`puede generar las directivas dinámicamente con un archivo . `IAuthorizationPolicyProvider` Para facilitar el uso de las directivas, puede `[MinimumAgeAuthorize(20)]`anotar acciones con un atributo de autorización personalizado como .

## <a name="custom-authorization-attributes"></a>Atributos de autorización personalizada

Las directivas de autorización se identifican por sus nombres. La `MinimumAgeAuthorizeAttribute` costumbre descrita anteriormente necesita asignar argumentos en una cadena que se puede usar para recuperar la directiva de autorización correspondiente. Puede hacerlo derivando y `AuthorizeAttribute` haciendo `Age` que la `AuthorizeAttribute.Policy` propiedad envuelva la propiedad.

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

Este tipo de `Policy` atributo tiene una cadena basada`"MinimumAge"`en el prefijo codificado de forma rígida ( ) y un entero pasado a través del constructor.

Puede aplicarlo a las acciones de `Authorize` la misma manera que a otros atributos, excepto que toma un entero como parámetro.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Custom IAuthorizationPolicyProvider

La `MinimumAgeAuthorizeAttribute` costumbre facilita la solicitud de directivas de autorización para cualquier edad mínima deseada. El siguiente problema a resolver es asegurarse de que las directivas de autorización están disponibles para todas esas diferentes edades. Aquí es `IAuthorizationPolicyProvider` donde es útil.

Al `MinimumAgeAuthorizationAttribute`utilizar , los nombres de `"MinimumAge" + Age`directiva de `IAuthorizationPolicyProvider` autorización seguirán el patrón, por lo que el personalizado debe generar directivas de autorización por:

* Analizar la edad del nombre de la política.
* Uso `AuthorizationPolicyBuilder` para crear un nuevo`AuthorizationPolicy`
* En este y siguientes ejemplos se asumirá que el usuario se autentica a través de una cookie. Debe `AuthorizationPolicyBuilder` construirse con al menos un nombre de esquema de autorización o siempre tener éxito. De lo contrario, no hay información sobre cómo proporcionar un desafío al usuario y se producirá una excepción.
* Adición de requisitos a `AuthorizationPolicyBuilder.AddRequirements`la política en función de la edad con . En otros escenarios, `RequireClaim`puede `RequireRole`usar `RequireUserName` , , o en su lugar.

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

## <a name="multiple-authorization-policy-providers"></a>Múltiples proveedores de directivas de autorización

Al usar `IAuthorizationPolicyProvider` implementaciones personalizadas, tenga en cuenta `IAuthorizationPolicyProvider`que ASP.NET Core solo usa una instancia de . Si un proveedor personalizado no puede proporcionar directivas de autorización para todos los nombres de directiva que se usarán, debe aplazar a un proveedor de copia de seguridad. 

Por ejemplo, considere una aplicación que necesita directivas de edad personalizadas y una recuperación de directivas basada en roles más tradicional. Dicha aplicación podría usar un proveedor de directivas de autorización personalizado que:

* Intenta analizar los nombres de las directivas. 
* Llamadas a un proveedor `DefaultAuthorizationPolicyProvider`de directivas diferente (como ) si el nombre de la directiva no contiene una edad.

La `IAuthorizationPolicyProvider` implementación de ejemplo mostrada `DefaultAuthorizationPolicyProvider` anteriormente se puede actualizar para usar mediante la creación de un proveedor de directivas de copia de seguridad en su constructor (que se usará en caso de que el nombre de la directiva no coincida con su patrón esperado de 'MinimumAge' + edad).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

A continuación, el `GetPolicyAsync` método se `BackupPolicyProvider` puede actualizar para usar el valor en lugar de devolver null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Directiva predeterminada

Además de proporcionar directivas `IAuthorizationPolicyProvider` de autorización con nombre, una personalizada debe implementarse `GetDefaultPolicyAsync` para proporcionar una directiva de autorización para `[Authorize]` atributos sin un nombre de directiva especificado.

En muchos casos, este atributo de autorización solo requiere un usuario autenticado, `RequireAuthenticatedUser`por lo que puede realizar la directiva necesaria con una llamada a:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Al igual que con `IAuthorizationPolicyProvider`todos los aspectos de una costumbre, puede personalizar esto, según sea necesario. En algunos casos, puede ser deseable recuperar la `IAuthorizationPolicyProvider`directiva predeterminada de una reserva.

## <a name="fallback-policy"></a>Política de reserva

Opcionalmente, una función personalizada `IAuthorizationPolicyProvider` puede implementar `GetFallbackPolicyAsync` para proporcionar una directiva que se usa al combinar [directivas](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) y cuando no se especifica ninguna directiva. Si `GetFallbackPolicyAsync` devuelve una directiva que no es null, el Middleware de autorización utiliza la directiva devuelta cuando no se especifica ninguna directiva para la solicitud.

Si no se requiere ninguna directiva `null` de reserva, el proveedor puede devolver o diferir al proveedor de reserva:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Usar un IAuthorizationPolicyProvider personalizado

Para utilizar directivas `IAuthorizationPolicyProvider`personalizadas desde un , debe:

* Registre los `AuthorizationHandler` tipos adecuados con la inserción de dependencias (descritos en la [autorización basada en directivas),](xref:security/authorization/policies#authorization-handlers)como con todos los escenarios de autorización basados en directivas.
* Registre el `IAuthorizationPolicyProvider` tipo personalizado en la colección `Startup.ConfigureServices`de servicios de inserción de dependencias de la aplicación (en ) para reemplazar el proveedor de directivas predeterminado.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Un ejemplo `IAuthorizationPolicyProvider` personalizado completo está disponible en el [repositorio GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).
