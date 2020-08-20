---
title: Autorização baseada em recursos no ASP.NET Core
author: scottaddie
description: Saiba como implementar a autorização baseada em recursos em um aplicativo ASP.NET Core quando um atributo de autorização não for suficiente.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/resourcebased
ms.openlocfilehash: bb47f3452d29dfeea0e4d3c4a9c22a06869a3fe7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631349"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="9cae4-103">Autorização baseada em recursos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cae4-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="9cae4-104">A estratégia de autorização depende do recurso que está sendo acessado.</span><span class="sxs-lookup"><span data-stu-id="9cae4-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="9cae4-105">Considere um documento que tenha uma propriedade de autor.</span><span class="sxs-lookup"><span data-stu-id="9cae4-105">Consider a document that has an author property.</span></span> <span data-ttu-id="9cae4-106">Somente o autor tem permissão para atualizar o documento.</span><span class="sxs-lookup"><span data-stu-id="9cae4-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="9cae4-107">Consequentemente, o documento deve ser recuperado do armazenamento de dados antes que a avaliação de autorização possa ocorrer.</span><span class="sxs-lookup"><span data-stu-id="9cae4-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="9cae4-108">A avaliação de atributo ocorre antes da vinculação de dados e antes da execução do manipulador de página ou da ação que carrega o documento.</span><span class="sxs-lookup"><span data-stu-id="9cae4-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="9cae4-109">Por esses motivos, a autorização declarativa com um `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="9cae4-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="9cae4-110">Em vez disso, você pode invocar um método de autorização personalizado &mdash; um estilo conhecido como *autorização imperativa*.</span><span class="sxs-lookup"><span data-stu-id="9cae4-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="9cae4-111">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9cae4-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="9cae4-112">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9cae4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="9cae4-113">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9cae4-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="9cae4-114">[Criar um aplicativo ASP.NET Core com os dados do usuário protegidos por autorização](xref:security/authorization/secure-data) contém um aplicativo de exemplo que usa a autorização baseada em recursos.</span><span class="sxs-lookup"><span data-stu-id="9cae4-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="9cae4-115">Usar autorização imperativa</span><span class="sxs-lookup"><span data-stu-id="9cae4-115">Use imperative authorization</span></span>

<span data-ttu-id="9cae4-116">A autorização é implementada como um serviço [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) e é registrada na coleção de serviços dentro da `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="9cae4-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="9cae4-117">O serviço é disponibilizado por meio de [injeção de dependência](xref:fundamentals/dependency-injection) para manipuladores de página ou ações.</span><span class="sxs-lookup"><span data-stu-id="9cae4-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="9cae4-118">`IAuthorizationService` tem duas `AuthorizeAsync` sobrecargas de método: uma aceita o recurso e o nome da política e a outra aceitando o recurso e uma lista de requisitos a serem avaliados.</span><span class="sxs-lookup"><span data-stu-id="9cae4-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="9cae4-119">No exemplo a seguir, o recurso a ser protegido é carregado em um `Document` objeto personalizado.</span><span class="sxs-lookup"><span data-stu-id="9cae4-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="9cae4-120">Uma `AuthorizeAsync` sobrecarga é invocada para determinar se o usuário atual tem permissão para editar o documento fornecido.</span><span class="sxs-lookup"><span data-stu-id="9cae4-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="9cae4-121">Uma política de autorização personalizada "EditPolicy" é fatorada na decisão.</span><span class="sxs-lookup"><span data-stu-id="9cae4-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="9cae4-122">Consulte [autorização personalizada baseada em políticas](xref:security/authorization/policies) para obter mais informações sobre a criação de políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="9cae4-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="9cae4-123">Os exemplos de código a seguir pressupõem que a autenticação foi executada e definiu a `User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="9cae4-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="9cae4-124">Gravar um manipulador baseado em recursos</span><span class="sxs-lookup"><span data-stu-id="9cae4-124">Write a resource-based handler</span></span>

<span data-ttu-id="9cae4-125">Escrever um manipulador para autorização baseada em recursos não é muito diferente de [escrever um manipulador de requisitos simples](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="9cae4-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="9cae4-126">Crie uma classe de requisito personalizada e implemente uma classe de manipulador de requisitos.</span><span class="sxs-lookup"><span data-stu-id="9cae4-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="9cae4-127">Para obter mais informações sobre como criar uma classe de requisito, consulte [requirements](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="9cae4-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="9cae4-128">A classe Handler especifica o requisito e o tipo de recurso.</span><span class="sxs-lookup"><span data-stu-id="9cae4-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="9cae4-129">Por exemplo, um manipulador que utiliza um `SameAuthorRequirement` e um `Document` recurso segue:</span><span class="sxs-lookup"><span data-stu-id="9cae4-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="9cae4-130">No exemplo anterior, imagine que `SameAuthorRequirement` seja um caso especial de uma classe mais genérica `SpecificAuthorRequirement` .</span><span class="sxs-lookup"><span data-stu-id="9cae4-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="9cae4-131">A `SpecificAuthorRequirement` classe (não mostrada) contém uma `Name` propriedade que representa o nome do autor.</span><span class="sxs-lookup"><span data-stu-id="9cae4-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="9cae4-132">A `Name` propriedade pode ser definida para o usuário atual.</span><span class="sxs-lookup"><span data-stu-id="9cae4-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="9cae4-133">Registre o requisito e o manipulador em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9cae4-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="9cae4-134">Requisitos operacionais</span><span class="sxs-lookup"><span data-stu-id="9cae4-134">Operational requirements</span></span>

<span data-ttu-id="9cae4-135">Se você estiver tomando decisões com base nos resultados de operações CRUD (criar, ler, atualizar, excluir), use a classe auxiliar [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) .</span><span class="sxs-lookup"><span data-stu-id="9cae4-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="9cae4-136">Essa classe permite que você grave um único manipulador em vez de uma classe individual para cada tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="9cae4-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="9cae4-137">Para usá-lo, forneça alguns nomes de operação:</span><span class="sxs-lookup"><span data-stu-id="9cae4-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="9cae4-138">O manipulador é implementado da seguinte maneira, usando um `OperationAuthorizationRequirement` requisito e um `Document` recurso:</span><span class="sxs-lookup"><span data-stu-id="9cae4-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="9cae4-139">O manipulador anterior valida a operação usando o recurso, a identidade do usuário e a propriedade do requisito `Name` .</span><span class="sxs-lookup"><span data-stu-id="9cae4-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="9cae4-140">Desafio e proíba com um manipulador de recursos operacionais</span><span class="sxs-lookup"><span data-stu-id="9cae4-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="9cae4-141">Esta seção mostra como os resultados de desafio e proíba são processados e como o desafio e proíba são diferentes.</span><span class="sxs-lookup"><span data-stu-id="9cae4-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="9cae4-142">Para chamar um manipulador de recursos operacionais, especifique a operação ao invocar `AuthorizeAsync` em seu manipulador de página ou ação.</span><span class="sxs-lookup"><span data-stu-id="9cae4-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="9cae4-143">O exemplo a seguir determina se o usuário autenticado tem permissão para exibir o documento fornecido.</span><span class="sxs-lookup"><span data-stu-id="9cae4-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="9cae4-144">Os exemplos de código a seguir pressupõem que a autenticação foi executada e definiu a `User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="9cae4-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="9cae4-145">Se a autorização for realizada com sucesso, a página para exibir o documento será retornada.</span><span class="sxs-lookup"><span data-stu-id="9cae4-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="9cae4-146">Se a autorização falhar, mas o usuário for autenticado, o retorno `ForbidResult` informará a qualquer middleware de autenticação que a autorização falhou.</span><span class="sxs-lookup"><span data-stu-id="9cae4-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="9cae4-147">Um `ChallengeResult` é retornado quando A autenticação deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="9cae4-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="9cae4-148">Para clientes de navegador interativos, pode ser apropriado redirecionar o usuário para uma página de logon.</span><span class="sxs-lookup"><span data-stu-id="9cae4-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="9cae4-149">Se a autorização for realizada com sucesso, a exibição do documento será retornada.</span><span class="sxs-lookup"><span data-stu-id="9cae4-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="9cae4-150">Se a autorização falhar, `ChallengeResult` o retorno informará qualquer middleware de autenticação que a autorização falhou e o middleware poderá tomar a resposta apropriada.</span><span class="sxs-lookup"><span data-stu-id="9cae4-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="9cae4-151">Uma resposta apropriada pode retornar um código de status 401 ou 403.</span><span class="sxs-lookup"><span data-stu-id="9cae4-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="9cae4-152">Para clientes de navegador interativos, isso pode significar o redirecionamento do usuário para uma página de logon.</span><span class="sxs-lookup"><span data-stu-id="9cae4-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
