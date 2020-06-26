---
title: Introducción a la autorización en ASP.NET Core
author: rick-anderson
description: Conozca los aspectos básicos de la autorización y cómo funciona la autorización en ASP.NET Core aplicaciones.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: b0d6c91adfb5dc273aeb662cb2c249626ef1d3c6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405437"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="c0d77-103">Introducción a la autorización en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0d77-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="c0d77-104">La autorización se refiere al proceso que determina lo que un usuario puede hacer.</span><span class="sxs-lookup"><span data-stu-id="c0d77-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="c0d77-105">Por ejemplo, un usuario administrativo puede crear una biblioteca de documentos, agregar documentos, editar documentos y eliminarlos.</span><span class="sxs-lookup"><span data-stu-id="c0d77-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="c0d77-106">Un usuario no administrativo que trabaja con la biblioteca solo está autorizado para leer los documentos.</span><span class="sxs-lookup"><span data-stu-id="c0d77-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="c0d77-107">La autorización es ortogonal e independiente de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="c0d77-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="c0d77-108">Sin embargo, la autorización requiere un mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="c0d77-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="c0d77-109">La autenticación es el proceso de determinar quién es un usuario.</span><span class="sxs-lookup"><span data-stu-id="c0d77-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="c0d77-110">La autenticación puede crear una o varias identidades para el usuario actual.</span><span class="sxs-lookup"><span data-stu-id="c0d77-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="c0d77-111">Para obtener más información acerca de la autenticación en ASP.NET Core, vea <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="c0d77-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="c0d77-112">Tipos de autorización</span><span class="sxs-lookup"><span data-stu-id="c0d77-112">Authorization types</span></span>

<span data-ttu-id="c0d77-113">La autorización de ASP.NET Core proporciona un [rol](xref:security/authorization/roles) sencillo y declarativo y un modelo avanzado [basado en directivas](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="c0d77-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="c0d77-114">La autorización se expresa en requisitos y los controladores evalúan las notificaciones de un usuario en relación con los requisitos.</span><span class="sxs-lookup"><span data-stu-id="c0d77-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="c0d77-115">Las comprobaciones imperativas pueden basarse en directivas o directivas simples que evalúan la identidad del usuario y las propiedades del recurso al que el usuario está intentando obtener acceso.</span><span class="sxs-lookup"><span data-stu-id="c0d77-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="c0d77-116">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="c0d77-116">Namespaces</span></span>

<span data-ttu-id="c0d77-117">Los componentes de autorización, incluidos los `AuthorizeAttribute` `AllowAnonymousAttribute` atributos y, se encuentran en el `Microsoft.AspNetCore.Authorization` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="c0d77-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="c0d77-118">Consulte la documentación sobre la [autorización simple](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="c0d77-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
