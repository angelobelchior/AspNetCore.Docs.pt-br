---
title: O que há de novo no ASP.NET Core 5,0
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 5,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
- 'Kestrel'
uid: aspnetcore-5.0
ms.openlocfilehash: 1f377f3be54ed8837d2857aed64c2d055ed9f582
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422581"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="e2910-103">O que há de novo no ASP.NET Core 5,0</span><span class="sxs-lookup"><span data-stu-id="e2910-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="e2910-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 5,0 com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="e2910-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="e2910-105">ASP.NET Core MVC e Razor melhorias</span><span class="sxs-lookup"><span data-stu-id="e2910-105">ASP.NET Core MVC and Razor improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="e2910-106">Data de associação de modelo como UTC</span><span class="sxs-lookup"><span data-stu-id="e2910-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="e2910-107">A associação de modelo agora dá suporte à associação de cadeias de caracteres de tempo UTC para `DateTime`</span><span class="sxs-lookup"><span data-stu-id="e2910-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="e2910-108">Se a solicitação contiver uma cadeia de caracteres de hora UTC, a associação de modelo a associará a um UTC `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="e2910-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="e2910-109">Por exemplo, a seguinte cadeia de caracteres de tempo está associada ao UTC `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="e2910-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="e2910-110">Associação de modelo e validação com tipos de registro do C# 9</span><span class="sxs-lookup"><span data-stu-id="e2910-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="e2910-111">Os [tipos de registro do C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) podem ser usados com a associação de modelo em um controlador MVC ou em uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="e2910-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a Razor Page.</span></span> <span data-ttu-id="e2910-112">Os tipos de registro são uma boa maneira de modelar dados que estão sendo transmitidos pela rede.</span><span class="sxs-lookup"><span data-stu-id="e2910-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="e2910-113">Por exemplo, o seguinte `PersonController` usa o `Person` tipo de registro com associação de modelo e validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="e2910-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="e2910-114">O arquivo `Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="e2910-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="e2910-115">Melhorias no DynamicRouteValueTransformer</span><span class="sxs-lookup"><span data-stu-id="e2910-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="e2910-116">ASP.NET Core 3,1 foi introduzido <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> como uma maneira de usar o ponto de extremidade personalizado para selecionar dinamicamente uma ação do controlador MVC ou uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="e2910-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a Razor page.</span></span> <span data-ttu-id="e2910-117">ASP.NET Core aplicativos 5,0 podem passar o estado para um `DynamicRouteValueTransformer` e filtrar o conjunto de pontos de extremidade escolhido.</span><span class="sxs-lookup"><span data-stu-id="e2910-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="e2910-118">Diversos</span><span class="sxs-lookup"><span data-stu-id="e2910-118">Miscellaneous</span></span>

* <span data-ttu-id="e2910-119">O atributo [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) pode ser aplicado a propriedades em um Razor modelo de página.</span><span class="sxs-lookup"><span data-stu-id="e2910-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a Razor Page model.</span></span>
* <span data-ttu-id="e2910-120">Os parâmetros e as propriedades associados do corpo são considerados obrigatórios por padrão.</span><span class="sxs-lookup"><span data-stu-id="e2910-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="e2910-121">API Web</span><span class="sxs-lookup"><span data-stu-id="e2910-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="e2910-122">Especificação de OpenAPI ativada por padrão</span><span class="sxs-lookup"><span data-stu-id="e2910-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="e2910-123">A [especificação de openapi](http://spec.openapis.org/oas/v3.0.3) é um padrão do setor para descrever as APIs http e integrá-las a processos comerciais complexos ou a terceiros.</span><span class="sxs-lookup"><span data-stu-id="e2910-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="e2910-124">O OpenAPI é amplamente suportado por todos os provedores de nuvem e muitos registros de API.</span><span class="sxs-lookup"><span data-stu-id="e2910-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="e2910-125">Os aplicativos que emitem OpenAPI documentos de APIs da Web têm uma variedade de novas oportunidades nas quais essas APIs podem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="e2910-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="e2910-126">Em parceria com os mantenedores do projeto de código-fonte aberto [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), o modelo de API ASP.NET Core contém uma dependência do NuGet em [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e2910-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="e2910-127">Swashbuckle é um popular pacote NuGet de software livre que emite documentos OpenAPI dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="e2910-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="e2910-128">Swashbuckle faz isso introspecção sobre os controladores de API e gerando o documento OpenAPI em tempo de execução, ou no momento da compilação usando a CLI do swashbuckle.</span><span class="sxs-lookup"><span data-stu-id="e2910-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="e2910-129">No ASP.NET Core 5,0, os modelos de API Web habilitam o suporte a OpenAPI por padrão.</span><span class="sxs-lookup"><span data-stu-id="e2910-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="e2910-130">Para desabilitar o OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="e2910-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="e2910-131">Na linha de comando:</span><span class="sxs-lookup"><span data-stu-id="e2910-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="e2910-132">No Visual Studio: desmarque **habilitar suporte a openapi**.</span><span class="sxs-lookup"><span data-stu-id="e2910-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="e2910-133">Todos os arquivos *. csproj* criados para projetos de API Web contêm a referência de pacote NuGet [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) .</span><span class="sxs-lookup"><span data-stu-id="e2910-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="e2910-134">O código gerado pelo modelo contém código `Startup.ConfigureServices` que ativa a geração de documentos openapi:</span><span class="sxs-lookup"><span data-stu-id="e2910-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="e2910-135">O `Startup.Configure` método adiciona o middleware Swashbuckle, que habilita o:</span><span class="sxs-lookup"><span data-stu-id="e2910-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="e2910-136">Processo de geração de documentos.</span><span class="sxs-lookup"><span data-stu-id="e2910-136">Document generation process.</span></span>
* <span data-ttu-id="e2910-137">Página da interface do usuário do Swagger por padrão no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e2910-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="e2910-138">O código gerado pelo modelo não expõe acidentalmente a descrição da API ao publicar em produção.</span><span class="sxs-lookup"><span data-stu-id="e2910-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="e2910-139">Importação de gerenciamento de API do Azure</span><span class="sxs-lookup"><span data-stu-id="e2910-139">Azure API Management Import</span></span>

<span data-ttu-id="e2910-140">Quando ASP.NET Core projetos de API habilitam o OpenAPI, o Visual Studio 2019 versão 16,8 e a publicação posterior oferecem automaticamente uma etapa adicional no fluxo de publicação.</span><span class="sxs-lookup"><span data-stu-id="e2910-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="e2910-141">Os desenvolvedores que usam o [Gerenciamento de API do Azure](xref:tutorials/publish-to-azure-api-management-using-vs) têm a oportunidade de importar automaticamente as APIs para o gerenciamento de API do Azure durante o fluxo de publicação:</span><span class="sxs-lookup"><span data-stu-id="e2910-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Importação do gerenciamento de API do Azure/publicação do VS](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="e2910-143">Experiência de inicialização melhor para projetos de API Web</span><span class="sxs-lookup"><span data-stu-id="e2910-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="e2910-144">Com o OpenAPI habilitado por padrão, a experiência de inicialização do aplicativo (F5) para desenvolvedores de API Web melhora significativamente.</span><span class="sxs-lookup"><span data-stu-id="e2910-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="e2910-145">Com o ASP.NET Core 5,0, o modelo de API da Web vem pré-configurado para carregar a página da interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="e2910-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="e2910-146">A página da interface do usuário do Swagger fornece a documentação adicionada para a API publicada e permite testar as APIs com um único clique.</span><span class="sxs-lookup"><span data-stu-id="e2910-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![exibição do Swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a><span data-ttu-id="e2910-148">Aprimoramentos de desempenho</span><span class="sxs-lookup"><span data-stu-id="e2910-148">Performance improvements</span></span>

<span data-ttu-id="e2910-149">Para o .NET 5, fizemos melhorias significativas Blazor WebAssembly no desempenho do tempo de execução com um foco específico em processamento de interface do usuário e SERIALIZAÇÃO JSON complexos.</span><span class="sxs-lookup"><span data-stu-id="e2910-149">For .NET 5, we made significant improvements to Blazor WebAssembly runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="e2910-150">Em nossos testes de desempenho, Blazor WebAssembly no .NET 5 é de duas a três vezes mais rápida para a maioria dos cenários.</span><span class="sxs-lookup"><span data-stu-id="e2910-150">In our performance tests, Blazor WebAssembly in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="e2910-151">Para obter mais informações, consulte [blog do ASP.net: atualizações do ASP.NET Core no .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="e2910-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="e2910-152">Isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="e2910-152">CSS isolation</span></span>

<span data-ttu-id="e2910-153">Blazor Agora dá suporte à definição de estilos CSS que têm o escopo de um determinado componente.</span><span class="sxs-lookup"><span data-stu-id="e2910-153">Blazor now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="e2910-154">Estilos CSS específicos de componentes facilitam o raciocínio sobre os estilos em um aplicativo e evitam efeitos colaterais não intencionais de estilos globais.</span><span class="sxs-lookup"><span data-stu-id="e2910-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="e2910-155">Para obter mais informações, consulte <xref:blazor/components/css-isolation>.</span><span class="sxs-lookup"><span data-stu-id="e2910-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="e2910-156">Novo `InputFile` componente</span><span class="sxs-lookup"><span data-stu-id="e2910-156">New `InputFile` component</span></span>

<span data-ttu-id="e2910-157">O `InputFile` componente permite ler um ou mais arquivos selecionados por um usuário para upload.</span><span class="sxs-lookup"><span data-stu-id="e2910-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="e2910-158">Para obter mais informações, consulte <xref:blazor/file-uploads>.</span><span class="sxs-lookup"><span data-stu-id="e2910-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="e2910-159">Novos `InputRadio` `InputRadioGroup` componentes e</span><span class="sxs-lookup"><span data-stu-id="e2910-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="e2910-160">Blazor tem `InputRadio` componentes internos e `InputRadioGroup` que simplificam a vinculação de dados a grupos de botões de opção com validação integrada.</span><span class="sxs-lookup"><span data-stu-id="e2910-160">Blazor has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="e2910-161">Para obter mais informações, consulte <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="e2910-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="e2910-162">Virtualização de componente</span><span class="sxs-lookup"><span data-stu-id="e2910-162">Component virtualization</span></span>

<span data-ttu-id="e2910-163">Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura.</span><span class="sxs-lookup"><span data-stu-id="e2910-163">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="e2910-164">Para obter mais informações, consulte <xref:blazor/forms-validation#radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="e2910-164">For more information, see <xref:blazor/forms-validation#radio-buttons>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="e2910-165">`ontoggle` suporte a eventos</span><span class="sxs-lookup"><span data-stu-id="e2910-165">`ontoggle` event support</span></span>

<span data-ttu-id="e2910-166">Blazor os eventos agora dão suporte ao `ontoggle` evento dom.</span><span class="sxs-lookup"><span data-stu-id="e2910-166">Blazor events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="e2910-167">Para obter mais informações, consulte <xref:blazor/components/event-handling#event-argument-types>.</span><span class="sxs-lookup"><span data-stu-id="e2910-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="e2910-168">Definir foco da interface do usuário em Blazor aplicativos</span><span class="sxs-lookup"><span data-stu-id="e2910-168">Set UI focus in Blazor apps</span></span>

<span data-ttu-id="e2910-169">Use o `FocusAsync` método de conveniência em referências de elemento para definir o foco da interface do usuário para esse elemento.</span><span class="sxs-lookup"><span data-stu-id="e2910-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="e2910-170">Para obter mais informações, consulte <xref:blazor/components/event-handling#focus-an-element>.</span><span class="sxs-lookup"><span data-stu-id="e2910-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="e2910-171">Atributos da classe de validação personalizada</span><span class="sxs-lookup"><span data-stu-id="e2910-171">Custom validation class attributes</span></span>

<span data-ttu-id="e2910-172">Os nomes de classe de validação personalizada são úteis na integração com estruturas CSS, como bootstrap.</span><span class="sxs-lookup"><span data-stu-id="e2910-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="e2910-173">Para obter mais informações, consulte <xref:blazor/forms-validation#custom-validation-class-attributes>.</span><span class="sxs-lookup"><span data-stu-id="e2910-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="e2910-174">Suporte do IAsyncDisposable</span><span class="sxs-lookup"><span data-stu-id="e2910-174">IAsyncDisposable support</span></span>

<span data-ttu-id="e2910-175">Blazor Agora, os componentes oferecem suporte à <xref:System.IAsyncDisposable> interface para a versão assíncrona de recursos alocados.</span><span class="sxs-lookup"><span data-stu-id="e2910-175">Blazor components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="e2910-176">Isolamento de JavaScript e referências de objeto</span><span class="sxs-lookup"><span data-stu-id="e2910-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="e2910-177">Blazor habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão.</span><span class="sxs-lookup"><span data-stu-id="e2910-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="e2910-178">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="e2910-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="e2910-179">Os componentes do formulário dão suporte ao nome para exibição</span><span class="sxs-lookup"><span data-stu-id="e2910-179">Form components support display name</span></span>

<span data-ttu-id="e2910-180">Os seguintes componentes internos dão suporte à exibição de nomes com o `DisplayName` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="e2910-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="e2910-181">Para obter mais informações, consulte <xref:blazor/forms-validation#display-name-support>.</span><span class="sxs-lookup"><span data-stu-id="e2910-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="e2910-182">Capturar todos os parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="e2910-182">Catch-all route parameters</span></span>

<span data-ttu-id="e2910-183">Catch-todos os parâmetros de rota, que capturam caminhos entre vários limites de pasta, têm suporte em componentes.</span><span class="sxs-lookup"><span data-stu-id="e2910-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="e2910-184">Para obter mais informações, consulte <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="e2910-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="e2910-185">Melhorias na depuração</span><span class="sxs-lookup"><span data-stu-id="e2910-185">Debugging improvements</span></span>

<span data-ttu-id="e2910-186">Os Blazor WebAssembly aplicativos de depuração foram aprimorados no ASP.NET Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="e2910-186">Debugging Blazor WebAssembly apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="e2910-187">Além disso, agora há suporte para a depuração no Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="e2910-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="e2910-188">Para obter mais informações, consulte <xref:blazor/debug>.</span><span class="sxs-lookup"><span data-stu-id="e2910-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="e2910-189">Microsoft Identity v 2.0 e MSAL v 2.0</span><span class="sxs-lookup"><span data-stu-id="e2910-189">Microsoft Identity v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="e2910-190">Blazor a segurança agora usa o Microsoft Identity v 2.0 ( [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) e o [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) ) e o MSAL v 2.0.</span><span class="sxs-lookup"><span data-stu-id="e2910-190">Blazor security now uses Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) and [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="e2910-191">Para obter mais informações, consulte os tópicos na [ Blazor segurança e no Identity nó](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="e2910-191">For more information, see the topics in the [Blazor Security and Identity node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="e2910-192">Armazenamento de navegador protegido para Blazor Server</span><span class="sxs-lookup"><span data-stu-id="e2910-192">Protected Browser Storage for Blazor Server</span></span>

<span data-ttu-id="e2910-193">Blazor Server Agora, os aplicativos podem usar o suporte interno para armazenar o estado do aplicativo no navegador que foi protegido contra violação usando a proteção de dados do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2910-193">Blazor Server apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="e2910-194">Os dados podem ser armazenados em armazenamento de navegador local ou de sessão.</span><span class="sxs-lookup"><span data-stu-id="e2910-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="e2910-195">Para obter mais informações, consulte <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="e2910-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="e2910-196">Blazor WebAssembly prerenderizando</span><span class="sxs-lookup"><span data-stu-id="e2910-196">Blazor WebAssembly prerendering</span></span>

<span data-ttu-id="e2910-197">A integração de componentes é aprimorada em modelos de hospedagem, e os Blazor WebAssembly aplicativos agora podem sair da saída no servidor.</span><span class="sxs-lookup"><span data-stu-id="e2910-197">Component integration is improved across hosting models, and Blazor WebAssembly apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="e2910-198">Aprimoramentos de corte/vinculação</span><span class="sxs-lookup"><span data-stu-id="e2910-198">Trimming/linking improvements</span></span>

<span data-ttu-id="e2910-199">Blazor WebAssembly executa a filtragem/vinculação de IL (linguagem intermediária) durante uma compilação para cortar o IL desnecessário dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2910-199">Blazor WebAssembly performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="e2910-200">Com o lançamento do ASP.NET Core 5,0, o Blazor WebAssembly realiza uma remoção aprimorada com opções de configuração adicionais.</span><span class="sxs-lookup"><span data-stu-id="e2910-200">With the release of ASP.NET Core 5.0, Blazor WebAssembly performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="e2910-201">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-trimmer> e [Opções de corte](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="e2910-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="e2910-202">Analisador de compatibilidade do navegador</span><span class="sxs-lookup"><span data-stu-id="e2910-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="e2910-203">Blazor WebAssembly os aplicativos direcionam a área completa da superfície do .NET API, mas nem todas as APIs do .NET têm suporte no Webassembly devido a restrições de área restrita do navegador.</span><span class="sxs-lookup"><span data-stu-id="e2910-203">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="e2910-204">APIs sem suporte são lançadas <xref:System.PlatformNotSupportedException> quando em execução no Webassembly.</span><span class="sxs-lookup"><span data-stu-id="e2910-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="e2910-205">Um analisador de compatibilidade de plataforma avisa o desenvolvedor quando o aplicativo usa APIs que não são compatíveis com as plataformas de destino do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2910-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="e2910-206">Para obter mais informações, consulte <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span><span class="sxs-lookup"><span data-stu-id="e2910-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="e2910-207">Assemblies de carga lenta</span><span class="sxs-lookup"><span data-stu-id="e2910-207">Lazy load assemblies</span></span>

<span data-ttu-id="e2910-208">Blazor WebAssembly o desempenho de inicialização do aplicativo pode ser melhorado ao adiar o carregamento de alguns assemblies de aplicativo até que eles sejam necessários.</span><span class="sxs-lookup"><span data-stu-id="e2910-208">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="e2910-209">Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="e2910-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="e2910-210">Suporte à globalização atualizado</span><span class="sxs-lookup"><span data-stu-id="e2910-210">Updated globalization support</span></span>

<span data-ttu-id="e2910-211">O suporte à globalização está disponível para o Blazor WebAssembly com base em componentes internacionais para Unicode (ICU).</span><span class="sxs-lookup"><span data-stu-id="e2910-211">Globalization support is available for Blazor WebAssembly based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="e2910-212">Para obter mais informações, consulte <xref:blazor/globalization-localization>.</span><span class="sxs-lookup"><span data-stu-id="e2910-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="e2910-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="e2910-213">gRPC</span></span>

<span data-ttu-id="e2910-214">Muitos aprimoramentos de preformabilidade foram feitos no [gRPC](https://grpc.io/).</span><span class="sxs-lookup"><span data-stu-id="e2910-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="e2910-215">Para obter mais informações, consulte [melhorias de desempenho do gRPC no .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span><span class="sxs-lookup"><span data-stu-id="e2910-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="e2910-216">Para obter mais informações sobre o gRPC, consulte <xref:grpc/index> .</span><span class="sxs-lookup"><span data-stu-id="e2910-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## SignalR

### <a name="no-locsignalr-hub-filters"></a><span data-ttu-id="e2910-217">SignalR Filtros de Hub</span><span class="sxs-lookup"><span data-stu-id="e2910-217">SignalR Hub filters</span></span>

<span data-ttu-id="e2910-218">SignalR Os filtros de Hub, chamados de pipelines de Hub no ASP.NET SignalR , são um recurso que permite que o código seja executado antes e depois que os métodos de Hub são chamados.</span><span class="sxs-lookup"><span data-stu-id="e2910-218">SignalR Hub filters, called Hub pipelines in ASP.NET SignalR, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="e2910-219">A execução de código antes e depois dos métodos de Hub são chamados é semelhante a como o middleware tem a capacidade de executar código antes e depois de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="e2910-219">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="e2910-220">Usos comuns incluem log, tratamento de erros e validação de argumento.</span><span class="sxs-lookup"><span data-stu-id="e2910-220">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="e2910-221">Para obter mais informações, consulte [usar filtros de Hub SignalR no ASP.NET Core ](xref:signalr/hub-filters).</span><span class="sxs-lookup"><span data-stu-id="e2910-221">For more information, see [Use hub filters in ASP.NET Core SignalR](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="e2910-222">SignalR invocações de Hub paralelas</span><span class="sxs-lookup"><span data-stu-id="e2910-222">SignalR parallel hub invocations</span></span>

<span data-ttu-id="e2910-223">ASP.NET Core SignalR agora é capaz de lidar com invocações de Hub paralelas.</span><span class="sxs-lookup"><span data-stu-id="e2910-223">ASP.NET Core SignalR is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="e2910-224">O comportamento padrão pode ser alterado para permitir que os clientes invoquem mais de um método de Hub por vez:</span><span class="sxs-lookup"><span data-stu-id="e2910-224">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="e2910-225">Adicionado suporte a MessagePack no SignalR cliente Java</span><span class="sxs-lookup"><span data-stu-id="e2910-225">Added Messagepack support in SignalR Java client</span></span>

<span data-ttu-id="e2910-226">Um novo pacote, [com. Microsoft. signalr. MessagePack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adiciona suporte a MessagePack ao SignalR cliente Java.</span><span class="sxs-lookup"><span data-stu-id="e2910-226">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the SignalR Java client.</span></span> <span data-ttu-id="e2910-227">Para usar o protocolo Hub MessagePack, adicione `.withHubProtocol(new MessagePackHubProtocol())` ao construtor de conexões:</span><span class="sxs-lookup"><span data-stu-id="e2910-227">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* <span data-ttu-id="e2910-228">Pontos de extremidade recarregáveis por meio da configuração: Kestrel pode detectar alterações na configuração passada para [ KestrelServerOptions.Configlhar](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) e desassociar de pontos de extremidade existentes e associar a novos pontos de extremidade sem a necessidade de uma reinicialização do aplicativo quando o `reloadOnChange` parâmetro é `true` .</span><span class="sxs-lookup"><span data-stu-id="e2910-228">Reloadable endpoints via configuration: Kestrel can detect changes to configuration passed to [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="e2910-229">Por padrão, ao usar <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> ou <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , o é Kestrel associado à subseção de configuração [" Kestrel "](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) com `reloadOnChange` habilitado.</span><span class="sxs-lookup"><span data-stu-id="e2910-229">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel binds to the ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="e2910-230">Os aplicativos devem passar `reloadOnChange: true` ao chamar `KestrelServerOptions.Configure` manualmente para obter pontos de extremidade recarregáveis.</span><span class="sxs-lookup"><span data-stu-id="e2910-230">Apps must pass `reloadOnChange: true` when calling `KestrelServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="e2910-231">Aprimoramentos de cabeçalhos de resposta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e2910-231">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="e2910-232">Para obter mais informações, consulte [melhorias de desempenho](#performance-improvements) na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="e2910-232">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="e2910-233">Suporte para tipos de pontos de extremidade adicionais no transporte de soquetes: adicionar à nova API introduzida no <xref:System.Net.Sockets?displayProperty=nameWithType> , o transporte padrão de soquetes no [Kestrel](xref:fundamentals/servers/kestrel) permite a ligação com os identificadores de arquivo existentes e os soquetes de domínio do UNIX.</span><span class="sxs-lookup"><span data-stu-id="e2910-233">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [Kestrel](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="e2910-234">O suporte para associação a identificadores de arquivo existentes permite usar a `Systemd` integração existente sem exigir o `libuv` transporte.</span><span class="sxs-lookup"><span data-stu-id="e2910-234">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="e2910-235">Decodificação de cabeçalho personalizado em Kestrel : os aplicativos podem especificar qual <xref:System.Text.Encoding> usar para interpretar cabeçalhos de entrada com base no nome do cabeçalho, em vez de padronizar para UTF-8.</span><span class="sxs-lookup"><span data-stu-id="e2910-235">Custom header decoding in Kestrel: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="e2910-236">Defina o cabeçalho </span><span class="sxs-lookup"><span data-stu-id="e2910-236">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="e2910-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` Propriedade para especificar qual codificação usar:</span><span class="sxs-lookup"><span data-stu-id="e2910-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="e2910-238">Kestrel opções específicas de ponto de extremidade por meio da configuração</span><span class="sxs-lookup"><span data-stu-id="e2910-238">Kestrel endpoint-specific options via configuration</span></span>

<span data-ttu-id="e2910-239">Foi adicionado suporte para a configuração de Kestrel opções específicas do ponto de extremidade por meio da [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e2910-239">Support has been added for configuring Kestrel’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="e2910-240">As configurações específicas do ponto de extremidade incluem:</span><span class="sxs-lookup"><span data-stu-id="e2910-240">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="e2910-241">Protocolos HTTP usados</span><span class="sxs-lookup"><span data-stu-id="e2910-241">HTTP protocols used</span></span>
* <span data-ttu-id="e2910-242">Protocolos TLS usados</span><span class="sxs-lookup"><span data-stu-id="e2910-242">TLS protocols used</span></span>
* <span data-ttu-id="e2910-243">Certificado selecionado</span><span class="sxs-lookup"><span data-stu-id="e2910-243">Certificate selected</span></span>
* <span data-ttu-id="e2910-244">Modo de certificado cient</span><span class="sxs-lookup"><span data-stu-id="e2910-244">Cient certificate mode</span></span>

<span data-ttu-id="e2910-245">A configuração permite especificar qual certificado é selecionado com base no nome do servidor especificado.</span><span class="sxs-lookup"><span data-stu-id="e2910-245">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="e2910-246">O nome do servidor faz parte da extensão de Indicação de Nome de Servidor (SNI) para o protocolo TLS, conforme indicado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="e2910-246">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="e2910-247">Kestrela configuração do também dá suporte a um prefixo curinga no nome do host.</span><span class="sxs-lookup"><span data-stu-id="e2910-247">Kestrel's configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="e2910-248">O exemplo a seguir mostra como especificar o ponto de extremidade específico usando um arquivo de configuração:</span><span class="sxs-lookup"><span data-stu-id="e2910-248">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="e2910-249">Indicação de Nome de Servidor (SNI) é uma extensão de TLS para incluir um domínio virtual como parte da negociação SSL.</span><span class="sxs-lookup"><span data-stu-id="e2910-249">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="e2910-250">O que isso significa efetivamente é que o nome de domínio virtual ou um nome de host pode ser usado para identificar o ponto de extremidade de rede.</span><span class="sxs-lookup"><span data-stu-id="e2910-250">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="e2910-251">Aprimoramentos de desempenho</span><span class="sxs-lookup"><span data-stu-id="e2910-251">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="e2910-252">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e2910-252">HTTP/2</span></span>

* <span data-ttu-id="e2910-253">Reduções significativas em alocações no caminho de código HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e2910-253">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="e2910-254">Suporte para [compactação dinâmica HPack](https://tools.ietf.org/html/rfc7541) de cabeçalhos de resposta http/2 no [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="e2910-254">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="e2910-255">Para obter mais informações, consulte [tamanho da tabela de cabeçalho](xref:fundamentals/servers/kestrel#header-table-size) e [HPACK: a Killer (recurso) silenciosa do http/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span><span class="sxs-lookup"><span data-stu-id="e2910-255">For more information, see [Header table size](xref:fundamentals/servers/kestrel#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="e2910-256">Enviando quadros de PING HTTP/2: o HTTP/2 tem um mecanismo para enviar quadros de PING para garantir que uma conexão ociosa ainda esteja funcional.</span><span class="sxs-lookup"><span data-stu-id="e2910-256">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="e2910-257">Garantir uma conexão viável é especialmente útil ao trabalhar com fluxos de vida útil longa que geralmente estão ociosos, mas apenas verificar a atividade intermitentemente, por exemplo, fluxos de gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2910-257">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="e2910-258">Os aplicativos podem enviar quadros de PING periódicos no [Kestrel](xref:fundamentals/servers/kestrel) definindo limites em <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> :</span><span class="sxs-lookup"><span data-stu-id="e2910-258">Apps can send periodic PING frames in [Kestrel](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="e2910-259">Contêineres</span><span class="sxs-lookup"><span data-stu-id="e2910-259">Containers</span></span>

<span data-ttu-id="e2910-260">Antes do .NET 5,0, a criação e a publicação de um *Dockerfile* para um aplicativo ASP.NET Core precisou extrair todo o SDK do .NET Core e a imagem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2910-260">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="e2910-261">Com esta versão, a extração de bytes de imagens do SDK é reduzida e os bytes recebidos para a imagem de ASP.NET Core são amplamente eliminados.</span><span class="sxs-lookup"><span data-stu-id="e2910-261">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="e2910-262">Para obter mais informações, consulte [este comentário sobre o problema do GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span><span class="sxs-lookup"><span data-stu-id="e2910-262">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="e2910-263">Autenticação e autorização</span><span class="sxs-lookup"><span data-stu-id="e2910-263">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="e2910-264">Azure Active Directory autenticação com a Microsoft. Identity . Site</span><span class="sxs-lookup"><span data-stu-id="e2910-264">Azure Active Directory authentication with Microsoft.Identity.Web</span></span>

<span data-ttu-id="e2910-265">Os modelos de projeto ASP.NET Core agora se integram <xref:Microsoft.Identity.Web?displayProperty=fullName> ao para lidar com a autenticação com o Azure AD ( [diretório de atividades do Azure](/azure/active-directory/fundamentals/active-directory-whatis) ).</span><span class="sxs-lookup"><span data-stu-id="e2910-265">The ASP.NET Core project templates now integrate with <xref:Microsoft.Identity.Web?displayProperty=fullName> to handle authentication with [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="e2910-266">O [Microsoft. Identity . O pacote da Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) fornece:</span><span class="sxs-lookup"><span data-stu-id="e2910-266">The [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) provides:</span></span>

* <span data-ttu-id="e2910-267">Uma melhor experiência de autenticação por meio do Azure AD.</span><span class="sxs-lookup"><span data-stu-id="e2910-267">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="e2910-268">Uma maneira mais fácil de acessar os recursos do Azure em nome de seus usuários, incluindo [Microsoft Graph](/graph/overview).</span><span class="sxs-lookup"><span data-stu-id="e2910-268">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="e2910-269">Consulte o [Microsoft. Identity . Exemplo da Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), que começa com um logon básico e avança por meio de multilocação, usando as APIs do Azure, usando Microsoft Graph e protegendo suas próprias APIs.</span><span class="sxs-lookup"><span data-stu-id="e2910-269">See the [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="e2910-270">`Microsoft.Identity.Web` está disponível junto com o .NET 5.</span><span class="sxs-lookup"><span data-stu-id="e2910-270">`Microsoft.Identity.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="e2910-271">Permitir acesso anônimo a um ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="e2910-271">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="e2910-272">O `AllowAnonymous` método de extensão permite o acesso anônimo a um ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="e2910-272">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="e2910-273">Manipulação personalizada de falhas de autorização</span><span class="sxs-lookup"><span data-stu-id="e2910-273">Custom handling of authorization failures</span></span>

<span data-ttu-id="e2910-274">A manipulação personalizada de falhas de autorização agora é mais fácil com a nova interface [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) que é invocada pelo [middleware](xref:fundamentals/middleware/index)de [autorização](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) .</span><span class="sxs-lookup"><span data-stu-id="e2910-274">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e2910-275">A implementação padrão permanece a mesma, mas um manipulador personalizado pode ser registrado em [injeção de dependência, que permite respostas HTTP personalizadas com base na razão pela qual a autorização falhou.</span><span class="sxs-lookup"><span data-stu-id="e2910-275">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="e2910-276">Consulte [Este exemplo](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) que demonstra o uso do `IAuthorizationMiddlewareResultHandler` .</span><span class="sxs-lookup"><span data-stu-id="e2910-276">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="e2910-277">Autorização ao usar o roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="e2910-277">Authorization when using endpoint routing</span></span>

<span data-ttu-id="e2910-278">A autorização ao usar o roteamento de ponto de extremidade agora recebe o `HttpContext` em vez da instância de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="e2910-278">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="e2910-279">Isso permite que o middleware de autorização acesse o `RouteData` e outras propriedades do `HttpContext` que não estavam acessíveis por meio da <xref:Microsoft.AspNetCore.Http.Endpoint> classe.</span><span class="sxs-lookup"><span data-stu-id="e2910-279">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="e2910-280">O ponto de extremidade pode ser buscado no contexto usando o [contexto. GetEndPoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span><span class="sxs-lookup"><span data-stu-id="e2910-280">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="e2910-281">Controle de acesso baseado em função com autenticação Kerberos e LDAP no Linux</span><span class="sxs-lookup"><span data-stu-id="e2910-281">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="e2910-282">Consulte [autenticação Kerberos e RBAC (controle de acesso baseado em função)](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="e2910-282">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="e2910-283">Aprimoramentos da API</span><span class="sxs-lookup"><span data-stu-id="e2910-283">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="e2910-284">Métodos de extensão JSON para HttpRequest e HttpResponse</span><span class="sxs-lookup"><span data-stu-id="e2910-284">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="e2910-285">Os dados JSON podem ser lidos e gravados de um `HttpRequest` e `HttpResponse` usando os <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> métodos New e `WriteAsJsonAsync` extension.</span><span class="sxs-lookup"><span data-stu-id="e2910-285">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="e2910-286">Esses métodos de extensão usam o [System.Text.Jsno](xref:System.Text.Json) serializador para manipular os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="e2910-286">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="e2910-287">O novo `HasJsonContentType` método de extensão também pode verificar se uma solicitação tem um tipo de conteúdo JSON.</span><span class="sxs-lookup"><span data-stu-id="e2910-287">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="e2910-288">Os métodos de extensão JSON podem ser combinados com o [Roteamento de ponto de extremidade](xref:fundamentals/routing) para criar APIs JSON em um estilo de programação que chamamos \* **de rota para o código** _.</span><span class="sxs-lookup"><span data-stu-id="e2910-288">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \* **route to code** _.</span></span> <span data-ttu-id="e2910-289">É uma nova opção para os desenvolvedores que desejam criar APIs JSON básicas de forma leve.</span><span class="sxs-lookup"><span data-stu-id="e2910-289">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="e2910-290">Por exemplo, um aplicativo Web que tem apenas alguns pontos de extremidade pode optar por usar a rota para código em vez da funcionalidade completa do ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="e2910-290">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="e2910-291">Para obter mais informações sobre os novos métodos de extensão JSON e rotear para código, consulte [este .net show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span><span class="sxs-lookup"><span data-stu-id="e2910-291">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="e2910-292">System. Diagnostics. Activity</span><span class="sxs-lookup"><span data-stu-id="e2910-292">System.Diagnostics.Activity</span></span>

<span data-ttu-id="e2910-293">O formato padrão para <xref:System.Diagnostics.Activity?displayProperty=fullName> agora usa como padrão o formato W3C.</span><span class="sxs-lookup"><span data-stu-id="e2910-293">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="e2910-294">Isso torna o suporte de rastreamento distribuído em ASP.NET Core interoperável com mais estruturas por padrão.</span><span class="sxs-lookup"><span data-stu-id="e2910-294">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="e2910-295">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="e2910-295">FromBodyAttribute</span></span>

<span data-ttu-id="e2910-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> o tem suporte para configurar uma opção que permite que esses parâmetros ou propriedades sejam considerados opcionais:</span><span class="sxs-lookup"><span data-stu-id="e2910-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> ow supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="e2910-297">Aprimoramentos diversos</span><span class="sxs-lookup"><span data-stu-id="e2910-297">Miscellaneous improvements</span></span>

<span data-ttu-id="e2910-298">Começamos a aplicar [anotações anuláveis](/dotnet/csharp/nullable-references#attributes-describe-apis) a ASP.NET Core assemblies.</span><span class="sxs-lookup"><span data-stu-id="e2910-298">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="e2910-299">Planejamos anotar a maior parte da superfície da API pública comum da estrutura do .NET 5.</span><span class="sxs-lookup"><span data-stu-id="e2910-299">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="e2910-300">Controlar a ativação da classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="e2910-300">Control Startup class activation</span></span>

<span data-ttu-id="e2910-301">Uma sobrecarga adicional foi <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> adicionada para permitir que um aplicativo forneça um método de fábrica para controlar a `Startup` ativação de classe.</span><span class="sxs-lookup"><span data-stu-id="e2910-301">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="e2910-302">Controlar a `Startup` ativação de classe é útil para passar parâmetros adicionais para `Startup` o que são inicializados junto com o host:</span><span class="sxs-lookup"><span data-stu-id="e2910-302">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="e2910-303">Atualização automática com a inspeção dotnet</span><span class="sxs-lookup"><span data-stu-id="e2910-303">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="e2910-304">No .NET 5, a execução do [dotnet Watch](xref:tutorials/dotnet-watch) em um projeto ASP.NET Core inicia o navegador padrão e atualiza automaticamente o navegador conforme as alterações são feitas no código.</span><span class="sxs-lookup"><span data-stu-id="e2910-304">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="e2910-305">Isso significa que você pode:</span><span class="sxs-lookup"><span data-stu-id="e2910-305">This means you can:</span></span>

<span data-ttu-id="e2910-306">_ Abra um projeto de ASP.NET Core em um editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e2910-306">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="e2910-307">Execute `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="e2910-307">Run `dotnet watch`.</span></span>
* <span data-ttu-id="e2910-308">Concentre-se nas alterações de código enquanto as ferramentas lidam com a recriação, reinicialização e recarregamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2910-308">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

<span data-ttu-id="e2910-309">Esperamos trazer a funcionalidade de atualização automática para o Visual Studio no futuro.</span><span class="sxs-lookup"><span data-stu-id="e2910-309">We hope to bring the auto refresh functionality to Visual Studio in the future.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="e2910-310">Formatador de agente do console</span><span class="sxs-lookup"><span data-stu-id="e2910-310">Console Logger Formatter</span></span>

<span data-ttu-id="e2910-311">Foram feitas melhorias no provedor de log do console na `Microsoft.Extensions.Logging` biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e2910-311">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="e2910-312">Agora, os desenvolvedores podem implementar um personalizado `ConsoleFormatter` para exercer controle completo sobre a formatação e a colorização da saída do console.</span><span class="sxs-lookup"><span data-stu-id="e2910-312">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="e2910-313">As APIs do formatador permitem formatação avançada implementando um subconjunto das sequências de escape VT-100.</span><span class="sxs-lookup"><span data-stu-id="e2910-313">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="e2910-314">O VT-100 tem suporte da maioria dos terminais modernos.</span><span class="sxs-lookup"><span data-stu-id="e2910-314">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="e2910-315">O agente de log do console pode analisar as sequências de escape em terminais sem suporte, permitindo que os desenvolvedores criem um único formatador para todos os terminais.</span><span class="sxs-lookup"><span data-stu-id="e2910-315">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="e2910-316">Agente do console JSON</span><span class="sxs-lookup"><span data-stu-id="e2910-316">JSON Console Logger</span></span>

<span data-ttu-id="e2910-317">Além de dar suporte a formatadores personalizados, também adicionamos um formatador JSON interno que emite logs estruturados de JSON ao console.</span><span class="sxs-lookup"><span data-stu-id="e2910-317">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="e2910-318">O código a seguir mostra como alternar do agente de log padrão para JSON:</span><span class="sxs-lookup"><span data-stu-id="e2910-318">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="e2910-319">As mensagens de log emitidas para o console são formatadas como JSON:</span><span class="sxs-lookup"><span data-stu-id="e2910-319">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
