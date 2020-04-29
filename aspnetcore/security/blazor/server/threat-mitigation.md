---
title: Guía de mitigación de amenazas Blazor para ASP.net Core Server
author: guardrex
description: Obtenga información acerca de cómo mitigar las Blazor amenazas de seguridad para las aplicaciones de servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/threat-mitigation
ms.openlocfilehash: 9a5e313153e5c5c17fc723cc9768c49ffd828007
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206345"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>Guía de mitigación de amenazas para ASP.NET Core servidor increíblemente

Por [Javier Calvarro Nelson](https://github.com/javiercn)

Las aplicaciones de servidor increíbles adoptan un modelo de procesamiento de datos con *Estado* , donde el servidor y el cliente mantienen una relación de larga duración. Un [circuito](xref:blazor/state-management)mantiene el estado persistente, que puede abarcar conexiones que también son potencialmente de larga duración.

Cuando un usuario visita un sitio de servidor más rápido, el servidor crea un circuito en la memoria del servidor. El circuito indica al explorador qué contenido se va a representar y responde a los eventos, como cuando el usuario selecciona un botón en la interfaz de usuario. Para realizar estas acciones, un circuito invoca las funciones de JavaScript en el explorador del usuario y los métodos de .NET en el servidor. Esta interacción basada en JavaScript bidireccional se conoce como [interoperabilidad de JavaScript (interoperabilidad de JS)](xref:blazor/call-javascript-from-dotnet).

Dado que la interoperabilidad de JS se produce a través de Internet y el cliente usa un explorador remoto, las aplicaciones de servidor increíbles comparten la mayoría de los problemas de seguridad de las aplicaciones Web. En este tema se describen las amenazas comunes a las aplicaciones de servidor increíbles y se proporciona una guía de mitigación de amenazas centrada en las aplicaciones orientadas a Internet.

En entornos restringidos, como dentro de redes corporativas o intranets, algunas de las instrucciones de mitigación:

* No se aplica en el entorno restringido.
* No merece la pena implementar porque el riesgo de seguridad es bajo en un entorno restringido.

## <a name="resource-exhaustion"></a>Agotamiento de recursos

El agotamiento de recursos puede producirse cuando un cliente interactúa con el servidor y hace que el servidor consuma demasiados recursos. El consumo excesivo de Recursos afecta principalmente a:

* [CPU](#cpu)
* [Memoria](#memory)
* [Conexiones de cliente](#client-connections)

Los ataques por denegación de servicio (DoS) suelen intentar agotar los recursos de una aplicación o un servidor. Sin embargo, el agotamiento de recursos no es necesariamente el resultado de un ataque en el sistema. Por ejemplo, los recursos finitos se pueden agotar debido a una alta demanda del usuario. DoS se describe con más detalle en la sección [ataques por denegación de servicio (dos)](#denial-of-service-dos-attacks) .

Los recursos externos al marco increíble, como las bases de datos y los identificadores de archivo (que se usan para leer y escribir archivos), también pueden experimentar el agotamiento de los recursos. Para obtener más información, vea <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

El agotamiento de la CPU puede producirse cuando uno o varios clientes fuerzan al servidor a realizar un trabajo intensivo de la CPU.

Por ejemplo, considere una aplicación de servidor más brillante que calcula un *número de Fibonnacci*. Un número de Fibonnacci se genera a partir de una secuencia de Fibonnacci, donde cada número de la secuencia es la suma de los dos números anteriores. La cantidad de trabajo necesaria para llegar a la respuesta depende de la longitud de la secuencia y el tamaño del valor inicial. Si la aplicación no impone límites en la solicitud de un cliente, los cálculos de uso intensivo de la CPU pueden dominar el tiempo de la CPU y reducir el rendimiento de otras tareas. Un consumo excesivo de recursos es un problema de seguridad que afecta a la disponibilidad.

El agotamiento de la CPU es un problema para todas las aplicaciones orientadas al público. En las aplicaciones web normales, las solicitudes y conexiones agotan el tiempo de espera como medida de seguridad, pero las aplicaciones de servidor increíbles no proporcionan las mismas medidas de seguridad. Las aplicaciones de servidor increíbles deben incluir comprobaciones y límites adecuados antes de llevar a cabo un trabajo potencialmente intensivo de la CPU.

### <a name="memory"></a>Memoria

El agotamiento de la memoria puede producirse cuando uno o varios clientes fuerzan al servidor a consumir una gran cantidad de memoria.

Por ejemplo, supongamos que tiene una aplicación de servidor más increíblemente con un componente que acepta y muestra una lista de elementos. Si la aplicación increíblemente alta no impone límites en el número de elementos permitidos o el número de elementos representados en el cliente, el procesamiento y la representación con un uso intensivo de memoria pueden dominar la memoria del servidor hasta el punto en el que el rendimiento del servidor se vea afectado. El servidor puede bloquearse o ralentizarse hasta el punto en el que parece que se ha bloqueado.

Considere el siguiente escenario para mantener y mostrar una lista de los elementos que pertenecen a un posible escenario de agotamiento de memoria en el servidor:

* Los elementos de una `List<MyItem>` propiedad o un campo utilizan la memoria del servidor. Si la aplicación permite que la lista de elementos crezca sin límite, existe el riesgo de que el servidor se quede sin memoria. Quedarse sin memoria hace que la sesión actual finalice (bloqueo) y todas las sesiones simultáneas en esa instancia de servidor reciban una excepción de memoria insuficiente. Para evitar que se produzca este escenario, la aplicación debe usar una estructura de datos que impone un límite de elementos a los usuarios simultáneos.
* Si no se usa un esquema de paginación para la representación, el servidor utiliza memoria adicional para los objetos que no están visibles en la interfaz de usuario. Sin un límite en cuanto al número de elementos, las demandas de memoria pueden agotar la memoria del servidor disponible. Para evitar este escenario, use uno de los métodos siguientes:
  * Usar listas paginadas al representar.
  * Solo se muestran los primeros 100 a 1.000 elementos y se requiere que el usuario escriba criterios de búsqueda para buscar elementos más allá de los elementos mostrados.
  * Para un escenario de representación más avanzado, implemente listas o cuadrículas que admitan la *Virtualización*. Con la virtualización, las listas solo representan un subconjunto de elementos actualmente visibles para el usuario. Cuando el usuario interactúa con la barra de desplazamiento en la interfaz de usuario, el componente solo representa los elementos necesarios para la presentación. Los elementos que no son necesarios actualmente para la presentación se pueden almacenar en el almacenamiento secundario, que es el enfoque ideal. Los elementos no mostrados también se pueden almacenar en memoria, lo que es menos idóneo.

Las aplicaciones de servidor increíbles ofrecen un modelo de programación similar a otros marcos de interfaz de usuario para aplicaciones con estado, como WPF, Windows Forms o webassembly. La principal diferencia es que en varios marcos de interfaz de usuario la memoria consumida por la aplicación pertenece al cliente y solo afecta a ese cliente individual. Por ejemplo, una aplicación webassembly increíble se ejecuta completamente en el cliente y solo utiliza recursos de memoria del cliente. En el escenario de servidor increíble, la memoria consumida por la aplicación pertenece al servidor y se comparte entre los clientes en la instancia del servidor.

Las peticiones de memoria del lado servidor son una consideración para todas las aplicaciones de servidor increíbles. Sin embargo, la mayoría de las aplicaciones web no tienen estado y la memoria que se usa al procesar una solicitud se libera cuando se devuelve la respuesta. Como recomendación general, no permita que los clientes asignen una cantidad de memoria sin enlazar como en cualquier otra aplicación del lado servidor que conserve las conexiones de cliente. La memoria consumida por una aplicación de servidor increíblemente larga se conserva durante más tiempo que una única solicitud.

> [!NOTE]
> Durante el desarrollo, se puede usar un generador de perfiles o un seguimiento capturado para evaluar las demandas de memoria de los clientes. Un generador de perfiles o seguimiento no capturará la memoria asignada a un cliente específico. Para capturar el uso de memoria de un cliente específico durante el desarrollo, Capture un volcado y examine la demanda de memoria de todos los objetos cuya raíz se encuentra en el circuito de un usuario.

### <a name="client-connections"></a>Conexiones de cliente

El agotamiento de la conexión puede producirse cuando uno o varios clientes abren demasiadas conexiones simultáneas al servidor, lo que impide que otros clientes establezcan nuevas conexiones.

Los clientes más increíbles establecen una conexión única por sesión y mantienen la conexión abierta mientras la ventana del explorador esté abierta. La demanda en el servidor de mantenimiento de todas las conexiones no es específica de las aplicaciones increíbles. Dada la naturaleza persistente de las conexiones y la naturaleza con estado de las aplicaciones de servidor increíbles, el agotamiento de la conexión es un riesgo mayor para la disponibilidad de la aplicación.

De forma predeterminada, no hay ningún límite en cuanto al número de conexiones por usuario para una aplicación de servidor más brillante. Si la aplicación requiere un límite de conexión, realice uno o varios de los métodos siguientes:

* Requiere autenticación, que de forma natural limita la posibilidad de que usuarios no autorizados se conecten a la aplicación. Para que este escenario sea eficaz, los usuarios deben evitar el aprovisionamiento de nuevos usuarios en.
* Limite el número de conexiones por usuario. La limitación de las conexiones se puede realizar mediante los siguientes enfoques. Preste atención para permitir que los usuarios legítimos tengan acceso a la aplicación (por ejemplo, cuando se establece un límite de conexión en función de la dirección IP del cliente).
  * En el nivel de la aplicación:
    * Extensibilidad de enrutamiento de extremo.
    * Requerir autenticación para conectarse a la aplicación y realizar un seguimiento de las sesiones activas por usuario.
    * Rechazar nuevas sesiones al alcanzar un límite.
    * Conexiones de WebSocket de proxy a una aplicación mediante el uso de un proxy, como el [servicio Azure signalr](/azure/azure-signalr/signalr-overview) que multiplexa las conexiones de los clientes a una aplicación. Esto proporciona una aplicación con mayor capacidad de conexión de la que puede establecer un solo cliente, lo que impide que un cliente agote las conexiones al servidor.
  * En el nivel de servidor: Use un proxy o puerta de enlace delante de la aplicación. Por ejemplo, la [puerta frontal de Azure](/azure/frontdoor/front-door-overview) permite definir, administrar y supervisar el enrutamiento global del tráfico web a una aplicación.

## <a name="denial-of-service-dos-attacks"></a>Ataques por denegación de servicio (DoS)

Los ataques por denegación de servicio (DoS) implican a un cliente que hace que el servidor agote uno o más de sus recursos, lo que hace que la aplicación no esté disponible. Las aplicaciones de servidor increíbles incluyen algunos límites predeterminados y se basan en otros límites de ASP.NET Core y Signalr para protegerse frente a ataques de DoS:

| Límite de aplicación de servidor de extraordinarias                            | Descripción | Valor predeterminado |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Número máximo de circuitos desconectados que un servidor determinado contiene en la memoria a la vez. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Cantidad máxima de tiempo que un circuito desconectado se mantiene en la memoria antes de que se detenga. | 3 minutos |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Cantidad máxima de tiempo que el servidor espera antes de agotar el tiempo de espera de una invocación de función JavaScript asincrónica. | 1 minuto. |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Número máximo de lotes de representación no confirmados el servidor mantiene en memoria por circuito en un momento dado para admitir una reconexión sólida. Después de alcanzar el límite, el servidor deja de generar nuevos lotes de representación hasta que el cliente ha confirmado uno o varios lotes. | 10 |


| Límite de signalr y ASP.NET Core             | Descripción | Valor predeterminado |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Tamaño del mensaje para un mensaje individual. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interacciones con el explorador (cliente)

Un cliente interactúa con el servidor a través de la distribución de eventos de interoperabilidad de JS y la finalización de la representación. La comunicación de interoperabilidad de JS se dirige a ambos sentidos entre JavaScript y .NET:

* Los eventos del explorador se envían desde el cliente al servidor de manera asincrónica.
* El servidor responde de forma asincrónica representando la interfaz de usuario según sea necesario.

### <a name="javascript-functions-invoked-from-net"></a>Funciones de JavaScript invocadas desde .NET

Para las llamadas de métodos de .NET a JavaScript:

* Todas las invocaciones tienen un tiempo de espera configurable después del cual se produce <xref:System.OperationCanceledException> un error y devuelven un al llamador.
  * Hay un tiempo de espera predeterminado para las llamadas`CircuitOptions.JSInteropDefaultCallTimeout`() de un minuto. Para configurar este límite, vea <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.
  * Se puede proporcionar un token de cancelación para controlar la cancelación en cada llamada. Confíe en el tiempo de espera de la llamada predeterminado, siempre que sea posible, y en cualquier llamada al cliente si se proporciona un token de cancelación.
* No se puede confiar en el resultado de una llamada de JavaScript. El Blazor cliente de la aplicación que se ejecuta en el explorador busca la función de JavaScript que se va a invocar. Se invoca la función y se produce el resultado o un error. Un cliente malintencionado puede intentar:
  * Producir un problema en la aplicación devolviendo un error de la función de JavaScript.
  * Inducir un comportamiento no deseado en el servidor devolviendo un resultado inesperado de la función de JavaScript.

Tome las siguientes precauciones para protegerse frente a los escenarios anteriores:

* Ajuste las llamadas de interoperabilidad de JS dentro de las instrucciones [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para tener en cuenta los errores que pueden producirse durante las invocaciones. Para obtener más información, vea <xref:blazor/handle-errors#javascript-interop>.
* Valide los datos devueltos por las invocaciones de interoperabilidad de JS, incluidos los mensajes de error, antes de realizar cualquier acción.

### <a name="net-methods-invoked-from-the-browser"></a>Métodos de .NET que se invocan desde el explorador

No confíe en llamadas de JavaScript a métodos de .NET. Cuando un método .NET se exponga a JavaScript, tenga en cuenta cómo se invoca el método .NET:

* Trate cualquier método .NET expuesto a JavaScript como lo haría con un punto de conexión público a la aplicación.
  * Valide la entrada.
    * Asegúrese de que los valores están dentro de los intervalos esperados.
    * Asegúrese de que el usuario tiene permiso para realizar la acción solicitada.
  * No asigne una cantidad excesiva de recursos como parte de la invocación de método de .NET. Por ejemplo, realice comprobaciones y coloque límites en el uso de la CPU y la memoria.
  * Tenga en cuenta que los métodos estáticos y de instancia se pueden exponer a los clientes de JavaScript. Evite compartir el estado entre las sesiones a menos que el diseño llame al estado de uso compartido con las restricciones adecuadas.
    * Para los métodos de instancia `DotNetReference` expuestos a través de objetos que se crearon originalmente a través de la inserción de dependencias (di), los objetos se deben registrar como objetos con ámbito. Esto se aplica a cualquier servicio DI que Blazor use la aplicación de servidor.
    * En el caso de los métodos estáticos, evite establecer el estado que no se puede limitar al cliente a menos que la aplicación comparta explícitamente el estado por diseño entre todos los usuarios de una instancia de servidor.
  * Evite pasar datos proporcionados por el usuario en parámetros a llamadas de JavaScript. Si pasar datos en parámetros es absolutamente necesario, asegúrese de que el código de JavaScript controla el paso de los datos sin introducir vulnerabilidades de [scripting entre sitios (XSS)](#cross-site-scripting-xss) . Por ejemplo, no escriba los datos proporcionados por el usuario en el Document Object Model (DOM) `innerHTML` estableciendo la propiedad de un elemento. Considere la posibilidad de usar la Directiva de seguridad de `eval` [contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para deshabilitar y otras primitivas de JavaScript no seguras.
* Evite implementar la distribución personalizada de las invocaciones de .NET sobre la implementación de distribución del marco. Exponer métodos .NET en el explorador es un escenario avanzado, no recomendado para el desarrollo Blazor general.

### <a name="events"></a>Eventos

Los eventos proporcionan un punto de entrada Blazor a una aplicación de servidor. Las mismas reglas para proteger los puntos de conexión de las aplicaciones web se aplican Blazor al control de eventos en las aplicaciones de servidor. Un cliente malintencionado puede enviar cualquier dato que desee enviar como carga para un evento.

Por ejemplo:

* Un evento de cambio de `<select>` un puede enviar un valor que no está dentro de las opciones que la aplicación presentó al cliente.
* `<input>` Podría enviar cualquier dato de texto al servidor, omitiendo la validación del lado cliente.

La aplicación debe validar los datos de los eventos que controla la aplicación. Los Blazor [componentes de formularios](xref:blazor/forms-validation) de marco realizan validaciones básicas. Si la aplicación usa componentes de formularios personalizados, se debe escribir código personalizado para validar los datos de evento según corresponda.

BlazorLos eventos de servidor son asincrónicos, por lo que se pueden enviar varios eventos al servidor antes de que la aplicación tenga tiempo de reaccionar mediante la generación de una nueva representación. Esto tiene algunas implicaciones de seguridad que se deben tener en cuenta. La limitación de las acciones de cliente en la aplicación debe realizarse dentro de los controladores de eventos y no depender del estado de vista representado actual.

Considere un componente de contador que debe permitir que un usuario incremente un contador un máximo de tres veces. El botón para incrementar el contador se basa condicionalmente en el valor de `count`:

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

Un cliente puede enviar uno o varios eventos de incremento antes de que el marco de trabajo genere una nueva representación de este componente. El resultado es que el `count` usuario puede incrementar *tres veces el número de veces* que la interfaz de usuario no haya quitado el botón con la suficiente rapidez. En el ejemplo siguiente se muestra la manera correcta `count` de lograr el límite de tres incrementos:

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

Al agregar la `if (count < 3) { ... }` comprobación dentro del controlador, la decisión de incrementar `count` se basa en el estado actual de la aplicación. La decisión no se basa en el estado de la interfaz de usuario tal como se encontraba en el ejemplo anterior, lo que podría estar temporalmente obsoleto.

### <a name="guard-against-multiple-dispatches"></a>Protección contra varios envíos

Si una devolución de llamada de evento invoca de forma asincrónica una operación de ejecución prolongada, como la recuperación de datos de una base de datos o un servicio externo, considere la posibilidad de usar una protección. La protección puede impedir que el usuario pueda poner en cola varias operaciones mientras la operación está en curso con comentarios visuales. El código de componente siguiente `isLoading` establece `true` en `GetForecastAsync` mientras obtiene datos del servidor. Aunque `isLoading` es `true`, el botón está deshabilitado en la interfaz de usuario:

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

El patrón de protección que se muestra en el ejemplo anterior funciona si la operación en segundo plano `async` - `await` se ejecuta de forma asincrónica con el patrón.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>CANCELAR pronto y evitar usar-After-Dispose

Además de usar una protección como se describe en la sección [protección contra varias distribuciones](#guard-against-multiple-dispatches) , considere la posibilidad <xref:System.Threading.CancellationToken> de usar para cancelar operaciones de ejecución prolongada cuando se desecha el componente. Este enfoque tiene la ventaja adicional de evitar *usar-After-Dispose* en los componentes:

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

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Evitar eventos que generan grandes cantidades de datos

Algunos eventos DOM, como `oninput` o `onscroll`, pueden generar una gran cantidad de datos. Evite el uso de estos Blazor eventos en las aplicaciones de servidor.

## <a name="additional-security-guidance"></a>Instrucciones de seguridad adicionales

Las instrucciones para proteger ASP.NET Core aplicaciones se aplican Blazor a las aplicaciones de servidor y se describen en las siguientes secciones:

* [Registro y datos confidenciales](#logging-and-sensitive-data)
* [Protección de la información en tránsito con HTTPS](#protect-information-in-transit-with-https)
* [Scripting entre sitios (XSS)](#cross-site-scripting-xss))
* [Protección entre orígenes](#cross-origin-protection)
* [Hacer clic en el conector](#click-jacking)
* [Redirecciones abiertas](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Registro y datos confidenciales

Las interacciones de interoperabilidad de JS entre el cliente y el servidor se <xref:Microsoft.Extensions.Logging.ILogger> registran en los registros del servidor con instancias. Blazorevita el registro de información confidencial, como eventos reales o entradas y salidas de interoperabilidad de JS.

Cuando se produce un error en el servidor, el marco de trabajo notifica al cliente y anula la sesión. De forma predeterminada, el cliente recibe un mensaje de error genérico que puede verse en las herramientas de desarrollo del explorador.

El error del lado cliente no incluye la pila de llamadas y no proporciona detalles sobre la causa del error, pero los registros del servidor contienen esa información. Para fines de desarrollo, la información de errores confidenciales puede ponerse a disposición del cliente mediante la habilitación de errores detallados.

Habilitar errores detallados con:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`clave de configuración. Por ejemplo, establezca la `ASPNETCORE_DETAILEDERRORS` variable de entorno en un valor `true`de.

> [!WARNING]
> Exponer información de errores a los clientes de Internet es un riesgo de seguridad que siempre debe evitarse.

### <a name="protect-information-in-transit-with-https"></a>Protección de la información en tránsito con HTTPS

BlazorEl servidor SignalR usa para la comunicación entre el cliente y el servidor. BlazorNormalmente, el servidor usa el SignalR transporte que negocia, que suele ser WebSockets.

BlazorEl servidor no garantiza la integridad y confidencialidad de los datos enviados entre el servidor y el cliente. Use siempre HTTPS.

### <a name="cross-site-scripting-xss"></a>Scripting entre sitios (XSS)

El scripting entre sitios (XSS) permite que una entidad no autorizada ejecute una lógica arbitraria en el contexto del explorador. Una aplicación en peligro podría ejecutar código arbitrario en el cliente. La vulnerabilidad podría usarse para realizar una serie de acciones malintencionadas en el servidor:

* Envíe eventos falsos o no válidos al servidor.
* Errores de envío o finalizaciones de representación no válidas.
* Evite enviar las finalizaciones de representación.
* Envío de llamadas de interoperabilidad desde JavaScript a .NET.
* Modifique la respuesta de las llamadas de interoperabilidad de .NET a JavaScript.
* Evite el envío de resultados de interoperabilidad de .NET a JS.

El Blazor marco de trabajo de servidor toma medidas para protegerse frente a algunas de las amenazas anteriores:

* Detiene la generación de nuevas actualizaciones de la interfaz de usuario si el cliente no reconoce los lotes de representación. Configurado con `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.
* Agota el tiempo de espera cualquier llamada de .NET a JavaScript después de un minuto sin recibir una respuesta del cliente. Configurado con `CircuitOptions.JSInteropDefaultCallTimeout`.
* Realiza la validación básica de todas las entradas procedentes del explorador durante la interoperabilidad de JS:
  * Las referencias de .NET son válidas y del tipo esperado por el método de .NET.
  * Los datos no tienen un formato incorrecto.
  * El número correcto de argumentos para el método está presente en la carga.
  * Los argumentos o el resultado se pueden deserializar correctamente antes de invocar el método.
* Realiza la validación básica en todas las entradas procedentes del explorador de los eventos enviados:
  * El evento tiene un tipo válido.
  * Los datos para el evento se pueden deserializar.
  * Hay un controlador de eventos asociado al evento.

Además de las medidas de seguridad que implementa el marco, el desarrollador debe codificar la aplicación para protegerse frente a las amenazas y tomar las medidas adecuadas:

* Valide siempre los datos al controlar los eventos.
* Tomar las medidas adecuadas al recibir datos no válidos:
  * Omitir los datos y devolverlos. Esto permite que la aplicación siga procesando las solicitudes.
  * Si la aplicación determina que la entrada es ilegítimo y no se pudo producir por un cliente legítimo, inicie una excepción. Producir una excepción anula el circuito y finaliza la sesión.
* No confíe en el mensaje de error proporcionado por las finalizaciones de procesamiento por lotes incluidas en los registros. El cliente proporciona el error y, por lo general, no se puede confiar en él, ya que es posible que el cliente esté en peligro.
* No confíe en la entrada en las llamadas de interoperabilidad de JS en ambas direcciones entre los métodos de .NET y JavaScript.
* La aplicación es responsable de validar que el contenido de los argumentos y los resultados son válidos, incluso si los argumentos o resultados se deserializan correctamente.

Para que exista una vulnerabilidad de XSS, la aplicación debe incorporar los datos proporcionados por el usuario en la página representada. BlazorLos componentes de servidor ejecutan un paso en tiempo de compilación en el que el marcado de un archivo *. Razor* se transforma en una lógica de C# de procedimientos. En tiempo de ejecución, la lógica de C# crea un *árbol de representación* que describe los elementos, el texto y los componentes secundarios. Esto se aplica al DOM del explorador a través de una secuencia de instrucciones de JavaScript (o se serializa a HTML en el caso de la representación previa):

* Los datos proporcionados por el usuario a través de sintaxis Razor `@someStringValue`normal (por ejemplo,) no exponen una vulnerabilidad de XSS porque la sintaxis Razor se agrega al Dom a través de comandos que solo pueden escribir texto. Aunque el valor incluya el marcado HTML, el valor se muestra como texto estático. Al prerepresentar, la salida está codificada en HTML, que también muestra el contenido como texto estático.
* No se permiten etiquetas de script y no deben incluirse en el árbol de representación de componentes de la aplicación. Si se incluye una etiqueta de script en el marcado de un componente, se genera un error en tiempo de compilación.
* Los autores de componentes pueden crear componentes en C# sin usar Razor. El autor del componente es responsable de usar las API correctas al emitir la salida. Por ejemplo, use `builder.AddContent(0, someUserSuppliedString)` y *Not* `builder.AddMarkupContent(0, someUserSuppliedString)`, ya que el último podría crear una vulnerabilidad XSS.

Como parte de la protección contra ataques XSS, considere la posibilidad de implementar mitigaciones XSS, como la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Para obtener más información, vea <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Protección entre orígenes

Los ataques entre orígenes implican a un cliente de un origen diferente que realiza una acción en el servidor. La acción malintencionada es normalmente una solicitud GET o un POST de formulario (falsificación de solicitud entre sitios, CSRF), pero también es posible abrir un WebSocket malintencionado. BlazorLas aplicaciones de servidor ofrecen [las mismas garantías de SignalR que cualquier otra aplicación que use el protocolo de concentrador ofrece](xref:signalr/security):

* BlazorSe puede acceder a las aplicaciones de servidor entre orígenes, a menos que se tomen medidas adicionales para evitarlo. Para deshabilitar el acceso entre orígenes, deshabilite CORS en el punto de conexión agregando el middleware de CORS a la `DisableCorsAttribute` canalización Blazor y agregando a los metadatos del punto de conexión o limite el conjunto de orígenes permitidos mediante la [configuración SignalR para el uso compartido de recursos entre orígenes](xref:signalr/security#cross-origin-resource-sharing).
* Si CORS está habilitado, podrían ser necesarios pasos adicionales para proteger la aplicación en función de la configuración de CORS. Si CORS está habilitado globalmente, CORS se puede deshabilitar Blazor para el concentrador de `DisableCorsAttribute` servidor agregando los metadatos a `hub.MapBlazorHub()`los metadatos del punto de conexión después de llamar a.

Para obtener más información, vea <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Hacer clic en el conector

La toma de clics implica representar un sitio como `<iframe>` dentro de un sitio de un origen diferente para engañar al usuario en la realización de acciones en el sitio bajo ataque.

Para proteger una aplicación de la representación dentro de una `<iframe>`, usa la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) y el `X-Frame-Options` encabezado. Para obtener más información, vea [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Redirecciones abiertas

Cuando se Blazor inicia una sesión de aplicación de servidor, el servidor realiza la validación básica de las direcciones URL que se envían como parte del inicio de la sesión. El marco comprueba que la dirección URL base es un elemento primario de la dirección URL actual antes de establecer el circuito. El marco de trabajo no realiza ninguna comprobación adicional.

Cuando un usuario selecciona un vínculo en el cliente, la dirección URL del vínculo se envía al servidor, lo que determina la acción que se debe realizar. Por ejemplo, la aplicación puede realizar una navegación en el lado cliente o indicar al explorador que vaya a la nueva ubicación.

Los componentes también pueden desencadenar solicitudes de navegación mediante programación `NavigationManager`a través del uso de. En estos casos, la aplicación puede realizar una navegación del lado cliente o indicar al explorador que vaya a la nueva ubicación.

Los componentes deben:

* Evite el uso de datos proporcionados por el usuario como parte de los argumentos de la llamada de navegación.
* Valide los argumentos para asegurarse de que la aplicación permite el destino.

De lo contrario, un usuario malintencionado puede forzar al explorador para que vaya a un sitio controlado por un atacante. En este escenario, el atacante engaña a la aplicación para que use algunos datos proporcionados por el usuario como parte `NavigationManager.Navigate` de la invocación del método.

Este Consejo también se aplica cuando se representan vínculos como parte de la aplicación:

* Si es posible, use vínculos relativos.
* Compruebe que los destinos absolutos de los vínculos son válidos antes de incluirlos en una página.

Para obtener más información, vea <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Lista de comprobación de seguridad

La siguiente lista de consideraciones de seguridad no es completa:

* Validar argumentos de eventos.
* Valide las entradas y los resultados de las llamadas de interoperabilidad de JS.
* Evite usar (o validar previamente) la entrada del usuario para las llamadas de interoperabilidad de .NET a JS.
* Impedir que el cliente asigne una cantidad de memoria sin enlazar.
  * Datos dentro del componente.
  * `DotNetObject`referencias devueltas al cliente.
* Protéjase contra varios envíos.
* Cancela las operaciones de ejecución prolongada cuando se desecha el componente.
* Evite eventos que generen grandes cantidades de datos.
* Evite el uso de datos proporcionados por el `NavigationManager.Navigate` usuario como parte de las llamadas a y valide primero los datos proporcionados por el usuario para las direcciones URL en un conjunto de orígenes permitidos si no se puede evitar.
* No tome decisiones de autorización basadas en el estado de la interfaz de usuario, sino solo en el estado del componente.
* Considere la posibilidad de usar la [Directiva de seguridad de contenido (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para protegerse frente a ataques XSS.
* Considere la posibilidad de usar CSP y [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) para protegerse frente a clics.
* Asegúrese de que la configuración de CORS sea adecuada al habilitar CORS o Blazor deshabilitar de forma explícita CORS para las aplicaciones.
* Pruebe para asegurarse de que los límites del lado servidor de Blazor la aplicación proporcionan una experiencia de usuario aceptable sin niveles inaceptables de riesgo.
