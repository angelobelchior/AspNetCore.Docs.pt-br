---
title: Autorização baseada em políticas no ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar manipuladores de política de autorização para impor requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 82ed4cc2ce47d3bd85ca9c2ba2bbeb075eaefcef
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635327"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="3c0aa-103">Autorização baseada em políticas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c0aa-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3c0aa-104">Sob os bastidores, a autorização [baseada em função](xref:security/authorization/roles) e a [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="3c0aa-105">Esses blocos de construção dão suporte à expressão de avaliações de autorização no código.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="3c0aa-106">O resultado é uma estrutura de autorização mais rica, reutilizável e que pode ser testada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="3c0aa-107">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="3c0aa-108">Ele é registrado como parte da configuração do serviço de autorização, no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="3c0aa-109">No exemplo anterior, uma política "AtLeast21" é criada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="3c0aa-110">Ele tem um requisito único &mdash; de uma idade mínima, que é fornecida como um parâmetro para o requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="3c0aa-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="3c0aa-111">IAuthorizationService</span></span> 

<span data-ttu-id="3c0aa-112">O serviço primário que determina se a autorização é bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="3c0aa-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="3c0aa-113">O código anterior realça os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="3c0aa-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> é um serviço de marcador sem métodos e o mecanismo para controlar se a autorização é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="3c0aa-115">Cada uma <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos são atendidos:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="3c0aa-116">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="3c0aa-117">O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="3c0aa-118">O código a seguir mostra um típico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3c0aa-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="3c0aa-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` para autorização.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="3c0aa-120">Aplicar políticas a controladores MVC</span><span class="sxs-lookup"><span data-stu-id="3c0aa-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="3c0aa-121">Se você estiver usando Razor páginas, consulte [aplicar políticas a Razor páginas](#apply-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="3c0aa-122">As políticas são aplicadas aos controladores usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3c0aa-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="3c0aa-124">Aplicar políticas a Razor páginas</span><span class="sxs-lookup"><span data-stu-id="3c0aa-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="3c0aa-125">As políticas são aplicadas às Razor páginas usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3c0aa-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="3c0aa-127">As políticas ***não*** podem ser aplicadas no Razor nível do manipulador de páginas, elas devem ser aplicadas à página.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="3c0aa-128">As políticas podem ser aplicadas a Razor páginas usando uma [Convenção de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="3c0aa-129">Requisitos</span><span class="sxs-lookup"><span data-stu-id="3c0aa-129">Requirements</span></span>

<span data-ttu-id="3c0aa-130">Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="3c0aa-131">Em nossa política de "AtLeast21", o requisito é um único parâmetro &mdash; de idade mínima.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="3c0aa-132">Um requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="3c0aa-133">Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="3c0aa-134">Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos deverão ser aprovados para que a avaliação da política seja realizada com êxito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="3c0aa-135">Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados em uma base **e** .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="3c0aa-136">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="3c0aa-137">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="3c0aa-137">Authorization handlers</span></span>

<span data-ttu-id="3c0aa-138">Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="3c0aa-139">O manipulador de autorização avalia os requisitos em relação a um [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornecido para determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="3c0aa-140">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="3c0aa-141">Um manipulador pode herdar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), em que `TRequirement` é o requisito a ser manipulado.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="3c0aa-142">Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="3c0aa-143">Usar um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="3c0aa-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="3c0aa-144">Veja a seguir um exemplo de uma relação um-para-um na qual um manipulador de idade mínima utiliza um único requisito:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="3c0aa-145">O código anterior determina se a entidade de usuário atual tem uma declaração de data de nascimento que foi emitida por um emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="3c0aa-146">A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída é retornada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="3c0aa-147">Quando uma declaração está presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="3c0aa-148">Se o usuário atender à idade mínima definida pelo requisito, a autorização será considerada bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="3c0aa-149">Quando a autorização é bem-sucedida, `context.Succeed` é invocada com o requisito atendido como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="3c0aa-150">Usar um manipulador para vários requisitos</span><span class="sxs-lookup"><span data-stu-id="3c0aa-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="3c0aa-151">Veja a seguir um exemplo de uma relação um-para-muitos na qual um manipulador de permissão pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="3c0aa-152">O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; uma propriedade que contém os requisitos não marcados como bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="3c0aa-153">Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="3c0aa-154">No caso de um `EditPermission` requisito ou `DeletePermission` , ele deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="3c0aa-155">Registro de manipulador</span><span class="sxs-lookup"><span data-stu-id="3c0aa-155">Handler registration</span></span>

<span data-ttu-id="3c0aa-156">Os manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="3c0aa-157">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="3c0aa-158">O código anterior é registrado `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="3c0aa-159">Os manipuladores podem ser registrados usando qualquer tempo de vida de [serviço](xref:fundamentals/dependency-injection#service-lifetimes)interno.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="3c0aa-160">O que um manipulador deve retornar?</span><span class="sxs-lookup"><span data-stu-id="3c0aa-160">What should a handler return?</span></span>

<span data-ttu-id="3c0aa-161">Observe que o `Handle` método no [exemplo do manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="3c0aa-162">Como é indicado um status de êxito ou falha?</span><span class="sxs-lookup"><span data-stu-id="3c0aa-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="3c0aa-163">Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)` , passando o requisito que foi validado com êxito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="3c0aa-164">Um manipulador não precisa lidar com falhas geralmente, já que outros manipuladores para o mesmo requisito podem ser bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="3c0aa-165">Para garantir a falha, mesmo que outros manipuladores de requisitos tenham êxito, chame `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="3c0aa-166">Se um manipulador chamar `context.Succeed` ou `context.Fail` , todos os outros manipuladores ainda serão chamados.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="3c0aa-167">Isso permite que os requisitos produzam efeitos colaterais, como registro em log, que ocorre mesmo se outro manipulador tiver validado ou reprovado com êxito um requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="3c0aa-168">Quando definido como `false` , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Core 1,1 e posterior) circuitos curtos a execução de manipuladores quando `context.Fail` é chamada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="3c0aa-169">`InvokeHandlersAfterFailure``true`o padrão é, nesse caso, todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="3c0aa-170">Os manipuladores de autorização são chamados mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="3c0aa-171">Por que desejo vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="3c0aa-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="3c0aa-172">Nos casos em que você deseja que a avaliação seja em um **ou** base, implemente vários manipuladores para um único requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="3c0aa-173">Por exemplo, a Microsoft tem portas que só são abertas com cartões-chave.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="3c0aa-174">Se você deixar o cartão-chave em casa, o recepcionista imprime um adesivo temporário e abre a porta para você.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="3c0aa-175">Nesse cenário, você teria um único requisito, *BuildingEntry*, mas vários manipuladores, cada um examinando um único requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="3c0aa-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="3c0aa-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="3c0aa-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3c0aa-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="3c0aa-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3c0aa-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="3c0aa-179">Verifique se ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="3c0aa-180">Se um dos manipuladores for executado com sucesso quando uma política avaliar o `BuildingEntryRequirement` , a avaliação da política será realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="3c0aa-181">Usar um Func para atender a uma política</span><span class="sxs-lookup"><span data-stu-id="3c0aa-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="3c0aa-182">Pode haver situações em que o cumprimento de uma política é simples de expressar no código.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="3c0aa-183">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua política com o `RequireAssertion` Policy Builder.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="3c0aa-184">Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="3c0aa-185">Acessar o contexto de solicitação MVC nos manipuladores</span><span class="sxs-lookup"><span data-stu-id="3c0aa-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="3c0aa-186">O `HandleRequirementAsync` método que você implementa em um manipulador de autorização tem dois parâmetros: um `AuthorizationHandlerContext` e o `TRequirement` que você está lidando.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="3c0aa-187">Estruturas como o MVC ou SignalR são livres para adicionar qualquer objeto à `Resource` propriedade no `AuthorizationHandlerContext` para passar informações extras.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="3c0aa-188">Ao usar o roteamento de ponto de extremidade, a autorização normalmente é manipulada pelo middleware de autorização.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="3c0aa-189">Nesse caso, a `Resource` propriedade é uma instância do <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="3c0aa-190">O ponto de extremidade pode ser usado para investigar o recurso subjacente ao qual você está encaminhando.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="3c0aa-191">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="3c0aa-192">O ponto de extremidade não fornece acesso ao atual `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="3c0aa-193">Ao usar o roteamento de ponto de extremidade, use `IHttpContextAcessor` para acessar `HttpContext` dentro de um manipulador de autorização.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="3c0aa-194">Para obter mais informações, consulte [usar HttpContext de componentes personalizados](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="3c0aa-195">Com o roteamento tradicional, ou quando a autorização acontece como parte do filtro de autorização do MVC, o valor de `Resource` é uma <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instância.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="3c0aa-196">Essa propriedade fornece acesso a `HttpContext` , `RouteData` e a todos os outros fornecidos pelo MVC e por Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="3c0aa-197">O uso da `Resource` propriedade é específico da estrutura.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="3c0aa-198">O uso de informações na `Resource` Propriedade limita suas políticas de autorização a estruturas específicas.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="3c0aa-199">Você deve converter a `Resource` propriedade usando a `is` palavra-chave e, em seguida, confirmar se a conversão foi bem-sucedida para garantir que seu código não falhe com um `InvalidCastException` quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="3c0aa-200">Exigir globalmente que todos os usuários sejam autenticados</span><span class="sxs-lookup"><span data-stu-id="3c0aa-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3c0aa-201">Sob os bastidores, a autorização [baseada em função](xref:security/authorization/roles) e a [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="3c0aa-202">Esses blocos de construção dão suporte à expressão de avaliações de autorização no código.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="3c0aa-203">O resultado é uma estrutura de autorização mais rica, reutilizável e que pode ser testada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="3c0aa-204">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="3c0aa-205">Ele é registrado como parte da configuração do serviço de autorização, no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="3c0aa-206">No exemplo anterior, uma política "AtLeast21" é criada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="3c0aa-207">Ele tem um requisito único &mdash; de uma idade mínima, que é fornecida como um parâmetro para o requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="3c0aa-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="3c0aa-208">IAuthorizationService</span></span> 

<span data-ttu-id="3c0aa-209">O serviço primário que determina se a autorização é bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="3c0aa-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="3c0aa-210">O código anterior realça os dois métodos do [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="3c0aa-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> é um serviço de marcador sem métodos e o mecanismo para controlar se a autorização é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="3c0aa-212">Cada uma <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos são atendidos:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="3c0aa-213">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="3c0aa-214">O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="3c0aa-215">O código a seguir mostra um típico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3c0aa-215">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="3c0aa-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` para autorização.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="3c0aa-217">Aplicar políticas a controladores MVC</span><span class="sxs-lookup"><span data-stu-id="3c0aa-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="3c0aa-218">Se você estiver usando Razor páginas, consulte [aplicar políticas a Razor páginas](#apply-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="3c0aa-219">As políticas são aplicadas aos controladores usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3c0aa-220">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="3c0aa-221">Aplicar políticas a Razor páginas</span><span class="sxs-lookup"><span data-stu-id="3c0aa-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="3c0aa-222">As políticas são aplicadas às Razor páginas usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3c0aa-223">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="3c0aa-224">As políticas também podem ser aplicadas a Razor páginas usando uma [Convenção de autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="3c0aa-225">Requisitos</span><span class="sxs-lookup"><span data-stu-id="3c0aa-225">Requirements</span></span>

<span data-ttu-id="3c0aa-226">Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="3c0aa-227">Em nossa política de "AtLeast21", o requisito é um único parâmetro &mdash; de idade mínima.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="3c0aa-228">Um requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="3c0aa-229">Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="3c0aa-230">Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos deverão ser aprovados para que a avaliação da política seja realizada com êxito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="3c0aa-231">Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados em uma base **e** .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="3c0aa-232">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="3c0aa-233">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="3c0aa-233">Authorization handlers</span></span>

<span data-ttu-id="3c0aa-234">Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="3c0aa-235">O manipulador de autorização avalia os requisitos em relação a um [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornecido para determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="3c0aa-236">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="3c0aa-237">Um manipulador pode herdar [ \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), em que `TRequirement` é o requisito a ser manipulado.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="3c0aa-238">Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="3c0aa-239">Usar um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="3c0aa-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="3c0aa-240">Veja a seguir um exemplo de uma relação um-para-um na qual um manipulador de idade mínima utiliza um único requisito:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="3c0aa-241">O código anterior determina se a entidade de usuário atual tem uma declaração de data de nascimento que foi emitida por um emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="3c0aa-242">A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída é retornada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="3c0aa-243">Quando uma declaração está presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="3c0aa-244">Se o usuário atender à idade mínima definida pelo requisito, a autorização será considerada bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="3c0aa-245">Quando a autorização é bem-sucedida, `context.Succeed` é invocada com o requisito atendido como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="3c0aa-246">Usar um manipulador para vários requisitos</span><span class="sxs-lookup"><span data-stu-id="3c0aa-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="3c0aa-247">Veja a seguir um exemplo de uma relação um-para-muitos na qual um manipulador de permissão pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="3c0aa-248">O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; uma propriedade que contém os requisitos não marcados como bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="3c0aa-249">Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="3c0aa-250">No caso de um `EditPermission` requisito ou `DeletePermission` , ele deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="3c0aa-251">Registro de manipulador</span><span class="sxs-lookup"><span data-stu-id="3c0aa-251">Handler registration</span></span>

<span data-ttu-id="3c0aa-252">Os manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="3c0aa-253">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="3c0aa-254">O código anterior é registrado `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="3c0aa-255">Os manipuladores podem ser registrados usando qualquer tempo de vida de [serviço](xref:fundamentals/dependency-injection#service-lifetimes)interno.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="3c0aa-256">O que um manipulador deve retornar?</span><span class="sxs-lookup"><span data-stu-id="3c0aa-256">What should a handler return?</span></span>

<span data-ttu-id="3c0aa-257">Observe que o `Handle` método no [exemplo do manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="3c0aa-258">Como é indicado um status de êxito ou falha?</span><span class="sxs-lookup"><span data-stu-id="3c0aa-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="3c0aa-259">Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)` , passando o requisito que foi validado com êxito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="3c0aa-260">Um manipulador não precisa lidar com falhas geralmente, já que outros manipuladores para o mesmo requisito podem ser bem-sucedidos.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="3c0aa-261">Para garantir a falha, mesmo que outros manipuladores de requisitos tenham êxito, chame `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="3c0aa-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="3c0aa-262">Se um manipulador chamar `context.Succeed` ou `context.Fail` , todos os outros manipuladores ainda serão chamados.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="3c0aa-263">Isso permite que os requisitos produzam efeitos colaterais, como registro em log, que ocorre mesmo se outro manipulador tiver validado ou reprovado com êxito um requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="3c0aa-264">Quando definido como `false` , a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Core 1,1 e posterior) circuitos curtos a execução de manipuladores quando `context.Fail` é chamada.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="3c0aa-265">`InvokeHandlersAfterFailure``true`o padrão é, nesse caso, todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="3c0aa-266">Os manipuladores de autorização são chamados mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="3c0aa-267">Por que desejo vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="3c0aa-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="3c0aa-268">Nos casos em que você deseja que a avaliação seja em um **ou** base, implemente vários manipuladores para um único requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="3c0aa-269">Por exemplo, a Microsoft tem portas que só são abertas com cartões-chave.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="3c0aa-270">Se você deixar o cartão-chave em casa, o recepcionista imprime um adesivo temporário e abre a porta para você.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="3c0aa-271">Nesse cenário, você teria um único requisito, *BuildingEntry*, mas vários manipuladores, cada um examinando um único requisito.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="3c0aa-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="3c0aa-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="3c0aa-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3c0aa-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="3c0aa-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3c0aa-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="3c0aa-275">Verifique se ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="3c0aa-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="3c0aa-276">Se um dos manipuladores for executado com sucesso quando uma política avaliar o `BuildingEntryRequirement` , a avaliação da política será realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="3c0aa-277">Usar um Func para atender a uma política</span><span class="sxs-lookup"><span data-stu-id="3c0aa-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="3c0aa-278">Pode haver situações em que o cumprimento de uma política é simples de expressar no código.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="3c0aa-279">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua política com o `RequireAssertion` Policy Builder.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="3c0aa-280">Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="3c0aa-281">Acessar o contexto de solicitação MVC nos manipuladores</span><span class="sxs-lookup"><span data-stu-id="3c0aa-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="3c0aa-282">O `HandleRequirementAsync` método que você implementa em um manipulador de autorização tem dois parâmetros: um `AuthorizationHandlerContext` e o `TRequirement` que você está lidando.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="3c0aa-283">Estruturas como o MVC ou SignalR são livres para adicionar qualquer objeto à `Resource` propriedade no `AuthorizationHandlerContext` para passar informações extras.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="3c0aa-284">Por exemplo, o MVC passa uma instância de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) na `Resource` propriedade.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="3c0aa-285">Essa propriedade fornece acesso a `HttpContext` , `RouteData` e a todos os outros fornecidos pelo MVC e por Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="3c0aa-286">O uso da `Resource` propriedade é específico da estrutura.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="3c0aa-287">O uso de informações na `Resource` Propriedade limita suas políticas de autorização a estruturas específicas.</span><span class="sxs-lookup"><span data-stu-id="3c0aa-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="3c0aa-288">Você deve converter a `Resource` propriedade usando a `is` palavra-chave e, em seguida, confirmar se a conversão foi bem-sucedida para garantir que seu código não falhe com um `InvalidCastException` quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="3c0aa-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
