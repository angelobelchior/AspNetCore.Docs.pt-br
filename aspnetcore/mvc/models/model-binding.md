---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ca2f071ccb84fdb2eb06f533fc4d088ad1b1c785
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393880"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="9e20f-103">Model binding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e20f-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e20f-104">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="9e20f-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="9e20f-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9e20f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="9e20f-106">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="9e20f-106">What is Model binding</span></span>

<span data-ttu-id="9e20f-107">Os controladores e Razor as páginas funcionam com dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="9e20f-108">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="9e20f-109">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="9e20f-110">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-110">Model binding automates this process.</span></span> <span data-ttu-id="9e20f-111">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="9e20f-111">The model binding system:</span></span>

* <span data-ttu-id="9e20f-112">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="9e20f-113">Fornece os dados para controladores e Razor páginas em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="9e20f-114">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="9e20f-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="9e20f-115">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="9e20f-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="9e20f-116">Example</span></span>

<span data-ttu-id="9e20f-117">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e20f-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="9e20f-118">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="9e20f-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="9e20f-119">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e20f-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="9e20f-120">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="9e20f-121">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9e20f-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="9e20f-122">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="9e20f-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="9e20f-123">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="9e20f-124">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="9e20f-125">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="9e20f-126">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="9e20f-127">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="9e20f-128">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="9e20f-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="9e20f-129">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="9e20f-130">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="9e20f-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="9e20f-131">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="9e20f-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="9e20f-132">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="9e20f-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="9e20f-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="9e20f-133">Targets</span></span>

<span data-ttu-id="9e20f-134">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="9e20f-135">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="9e20f-136">Parâmetros do Razor método de manipulador Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="9e20f-137">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="9e20f-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="9e20f-138">[BindProperty] attribute</span></span>

<span data-ttu-id="9e20f-139">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="9e20f-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="9e20f-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="9e20f-140">[BindProperties] attribute</span></span>

<span data-ttu-id="9e20f-141">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="9e20f-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="9e20f-142">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="9e20f-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="9e20f-143">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="9e20f-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="9e20f-144">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9e20f-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="9e20f-145">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="9e20f-146">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="9e20f-147">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="9e20f-148">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="9e20f-149">Origens</span><span class="sxs-lookup"><span data-stu-id="9e20f-149">Sources</span></span>

<span data-ttu-id="9e20f-150">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="9e20f-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="9e20f-151">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="9e20f-151">Form fields</span></span>
1. <span data-ttu-id="9e20f-152">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="9e20f-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="9e20f-153">Rotear dados</span><span class="sxs-lookup"><span data-stu-id="9e20f-153">Route data</span></span>
1. <span data-ttu-id="9e20f-154">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="9e20f-154">Query string parameters</span></span>
1. <span data-ttu-id="9e20f-155">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="9e20f-155">Uploaded files</span></span>

<span data-ttu-id="9e20f-156">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="9e20f-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="9e20f-157">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="9e20f-157">There are a few exceptions:</span></span>

* <span data-ttu-id="9e20f-158">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="9e20f-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="9e20f-159">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="9e20f-160">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="9e20f-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="9e20f-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="9e20f-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9e20f-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="9e20f-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="9e20f-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="9e20f-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="9e20f-166">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-166">These attributes:</span></span>

* <span data-ttu-id="9e20f-167">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e20f-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="9e20f-168">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="9e20f-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="9e20f-169">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="9e20f-170">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e20f-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="9e20f-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="9e20f-171">[FromBody] attribute</span></span>

<span data-ttu-id="9e20f-172">Aplique o `[FromBody]` atributo a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="9e20f-173">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="9e20f-174">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="9e20f-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="9e20f-175">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="9e20f-176">Por exemplo, a ação a seguir `Create` especifica que seu `pet` parâmetro é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="9e20f-177">A `Pet` classe especifica que sua `Breed` propriedade é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="9e20f-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="9e20f-178">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9e20f-178">In the preceding example:</span></span>

* <span data-ttu-id="9e20f-179">O `[FromQuery]` atributo é ignorado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="9e20f-180">A `Breed` propriedade não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="9e20f-181">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="9e20f-182">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a `Breed` propriedade.</span><span class="sxs-lookup"><span data-stu-id="9e20f-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="9e20f-183">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="9e20f-184">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para ligar outros `[FromBody]` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="9e20f-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="9e20f-185">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="9e20f-185">Additional sources</span></span>

<span data-ttu-id="9e20f-186">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="9e20f-187">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="9e20f-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="9e20f-188">Por exemplo, talvez você queira dados do cookie estado da sessão ou do.</span><span class="sxs-lookup"><span data-stu-id="9e20f-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="9e20f-189">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="9e20f-189">To get data from a new source:</span></span>

* <span data-ttu-id="9e20f-190">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="9e20f-191">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="9e20f-192">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="9e20f-193">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="9e20f-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="9e20f-194">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="9e20f-195">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="9e20f-196">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="9e20f-197">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="9e20f-197">No source for a model property</span></span>

<span data-ttu-id="9e20f-198">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="9e20f-199">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="9e20f-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="9e20f-200">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="9e20f-201">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="9e20f-202">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="9e20f-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="9e20f-203">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="9e20f-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="9e20f-204">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="9e20f-205">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="9e20f-206">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="9e20f-207">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="9e20f-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="9e20f-208">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="9e20f-208">Type conversion errors</span></span>

<span data-ttu-id="9e20f-209">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="9e20f-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="9e20f-210">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="9e20f-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="9e20f-211">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="9e20f-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="9e20f-212">Em uma Razor página, reexiba a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="9e20f-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="9e20f-213">A validação no lado do cliente captura dados mais inválidos que, de outra forma, seriam enviados a um Razor formulário de páginas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="9e20f-214">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="9e20f-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="9e20f-215">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="9e20f-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="9e20f-216">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="9e20f-217">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="9e20f-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="9e20f-218">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="9e20f-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="9e20f-219">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="9e20f-220">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="9e20f-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="9e20f-221">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="9e20f-222">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="9e20f-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="9e20f-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="9e20f-223">Simple types</span></span>

<span data-ttu-id="9e20f-224">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="9e20f-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="9e20f-225">Booliano</span><span class="sxs-lookup"><span data-stu-id="9e20f-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="9e20f-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="9e20f-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="9e20f-227">º</span><span class="sxs-lookup"><span data-stu-id="9e20f-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="9e20f-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="9e20f-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="9e20f-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="9e20f-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="9e20f-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="9e20f-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="9e20f-231">Double</span><span class="sxs-lookup"><span data-stu-id="9e20f-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="9e20f-232">Enumeração</span><span class="sxs-lookup"><span data-stu-id="9e20f-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="9e20f-233">Volume</span><span class="sxs-lookup"><span data-stu-id="9e20f-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="9e20f-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="9e20f-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="9e20f-235">Single</span><span class="sxs-lookup"><span data-stu-id="9e20f-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="9e20f-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="9e20f-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="9e20f-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="9e20f-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="9e20f-238">Uri</span><span class="sxs-lookup"><span data-stu-id="9e20f-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="9e20f-239">Versão</span><span class="sxs-lookup"><span data-stu-id="9e20f-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="9e20f-240">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="9e20f-240">Complex types</span></span>

<span data-ttu-id="9e20f-241">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="9e20f-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="9e20f-242">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="9e20f-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="9e20f-243">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="9e20f-244">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="9e20f-245">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="9e20f-246">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="9e20f-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="9e20f-247">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="9e20f-248">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="9e20f-249">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="9e20f-249">Prefix = parameter name</span></span>

<span data-ttu-id="9e20f-250">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="9e20f-251">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="9e20f-252">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="9e20f-253">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="9e20f-253">Prefix = property name</span></span>

<span data-ttu-id="9e20f-254">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="9e20f-255">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="9e20f-256">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="9e20f-257">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="9e20f-257">Custom prefix</span></span>

<span data-ttu-id="9e20f-258">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="9e20f-259">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="9e20f-260">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="9e20f-261">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="9e20f-261">Attributes for complex type targets</span></span>

<span data-ttu-id="9e20f-262">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="9e20f-263">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="9e20f-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="9e20f-264">Eles ***não*** afetam os formatadores de entrada, que processam os corpos de solicitação JSON e XML lançados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="9e20f-265">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="9e20f-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="9e20f-266">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="9e20f-266">[Bind] attribute</span></span>

<span data-ttu-id="9e20f-267">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="9e20f-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="9e20f-268">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="9e20f-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="9e20f-269">`[Bind]` Não ***afeta os*** formatadores de entrada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="9e20f-270">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="9e20f-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="9e20f-271">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="9e20f-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="9e20f-272">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="9e20f-273">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="9e20f-274">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="9e20f-275">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="9e20f-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="9e20f-276">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="9e20f-276">[BindRequired] attribute</span></span>

<span data-ttu-id="9e20f-277">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="9e20f-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="9e20f-278">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="9e20f-279">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="9e20f-280">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="9e20f-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="9e20f-281">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="9e20f-281">[BindNever] attribute</span></span>

<span data-ttu-id="9e20f-282">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="9e20f-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="9e20f-283">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="9e20f-284">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="9e20f-285">Coleções</span><span class="sxs-lookup"><span data-stu-id="9e20f-285">Collections</span></span>

<span data-ttu-id="9e20f-286">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="9e20f-287">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="9e20f-288">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-288">For example:</span></span>

* <span data-ttu-id="9e20f-289">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="9e20f-290">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="9e20f-291">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="9e20f-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="9e20f-292">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="9e20f-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="9e20f-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="9e20f-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="9e20f-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="9e20f-295">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="9e20f-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="9e20f-296">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="9e20f-297">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="9e20f-298">Dicionários</span><span class="sxs-lookup"><span data-stu-id="9e20f-298">Dictionaries</span></span>

<span data-ttu-id="9e20f-299">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="9e20f-300">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="9e20f-301">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-301">For example:</span></span>

* <span data-ttu-id="9e20f-302">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="9e20f-303">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e20f-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="9e20f-304">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="9e20f-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="9e20f-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="9e20f-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="9e20f-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="9e20f-307">Associação de construtor e tipos de registro</span><span class="sxs-lookup"><span data-stu-id="9e20f-307">Constructor binding and record types</span></span>

<span data-ttu-id="9e20f-308">A associação de modelo requer que os tipos complexos tenham um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="9e20f-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="9e20f-309">Os `System.Text.Json` `Newtonsoft.Json` formatadores de entrada e com base dão suporte à desserialização de classes que não têm um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="9e20f-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="9e20f-310">O C# 9 apresenta tipos de registro, que são uma ótima maneira de representar de forma sucinta os dados pela rede.</span><span class="sxs-lookup"><span data-stu-id="9e20f-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="9e20f-311">ASP.NET Core adiciona suporte para associação de modelo e validação de tipos de registro com um único Construtor:</span><span class="sxs-lookup"><span data-stu-id="9e20f-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="9e20f-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="9e20f-313">Ao validar tipos de registro, o tempo de execução pesquisa os metadados de validação especificamente em parâmetros em vez de em Propriedades.</span><span class="sxs-lookup"><span data-stu-id="9e20f-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="9e20f-314">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="9e20f-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="9e20f-315">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="9e20f-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="9e20f-316">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="9e20f-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="9e20f-317">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="9e20f-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="9e20f-318">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="9e20f-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="9e20f-319">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="9e20f-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="9e20f-320">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="9e20f-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="9e20f-321">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="9e20f-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="9e20f-322">Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="9e20f-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="9e20f-323">Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="9e20f-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="9e20f-324">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="9e20f-325">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="9e20f-325">Special data types</span></span>

<span data-ttu-id="9e20f-326">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="9e20f-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="9e20f-327">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="9e20f-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="9e20f-328">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="9e20f-329">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="9e20f-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="9e20f-330">CancellationToken</span></span>

<span data-ttu-id="9e20f-331">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="9e20f-332">FormCollection</span><span class="sxs-lookup"><span data-stu-id="9e20f-332">FormCollection</span></span>

<span data-ttu-id="9e20f-333">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="9e20f-334">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="9e20f-334">Input formatters</span></span>

<span data-ttu-id="9e20f-335">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="9e20f-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="9e20f-336">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="9e20f-337">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="9e20f-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="9e20f-338">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="9e20f-339">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="9e20f-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="9e20f-340">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="9e20f-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="9e20f-341">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="9e20f-342">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="9e20f-343">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="9e20f-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="9e20f-344">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="9e20f-345">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="9e20f-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="9e20f-346">Personalizar a associação de modelo com formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="9e20f-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="9e20f-347">Um formatador de entrada assume total responsabilidade pela leitura de dados do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="9e20f-348">Para personalizar esse processo, configure as APIs usadas pelo formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="9e20f-349">Esta seção descreve como personalizar o `System.Text.Json` formatador de entrada baseado em dados para entender um tipo personalizado chamado `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="9e20f-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="9e20f-350">Considere o seguinte modelo, que contém uma `ObjectId` propriedade personalizada chamada `Id` :</span><span class="sxs-lookup"><span data-stu-id="9e20f-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="9e20f-351">Para personalizar o processo de associação de modelo ao usar o `System.Text.Json` , crie uma classe derivada de <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="9e20f-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="9e20f-352">Para usar um conversor personalizado, aplique o <xref:System.Text.Json.Serialization.JsonConverterAttribute> atributo ao tipo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="9e20f-353">No exemplo a seguir, o `ObjectId` tipo é configurado com `ObjectIdConverter` como seu conversor personalizado:</span><span class="sxs-lookup"><span data-stu-id="9e20f-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="9e20f-354">Para obter mais informações, consulte [como escrever conversores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="9e20f-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="9e20f-355">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="9e20f-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="9e20f-356">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="9e20f-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="9e20f-357">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="9e20f-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="9e20f-358">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="9e20f-359">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e20f-360">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="9e20f-361">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e20f-362">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="9e20f-363">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="9e20f-363">Custom model binders</span></span>

<span data-ttu-id="9e20f-364">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="9e20f-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="9e20f-365">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="9e20f-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="9e20f-366">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="9e20f-366">Manual model binding</span></span> 

<span data-ttu-id="9e20f-367">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9e20f-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="9e20f-368">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="9e20f-369">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="9e20f-370">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="9e20f-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="9e20f-371">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="9e20f-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  usa provedores de valor para obter dados do corpo do formulário, Cadeia de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9e20f-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="9e20f-373">`TryUpdateModelAsync` normalmente é:</span><span class="sxs-lookup"><span data-stu-id="9e20f-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="9e20f-374">Usado com Razor páginas e aplicativos MVC usando controladores e exibições para evitar o excesso de postagens.</span><span class="sxs-lookup"><span data-stu-id="9e20f-374">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="9e20f-375">Não usado com uma API da Web, a menos que consumido de dados de formulário, cadeias de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9e20f-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="9e20f-376">Os pontos de extremidade da API Web que consomem JSON usam [formatadores de entrada](#input-formatters) para desserializar o corpo da solicitação em um objeto.</span><span class="sxs-lookup"><span data-stu-id="9e20f-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="9e20f-377">Para obter mais informações, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="9e20f-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="9e20f-378">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="9e20f-378">[FromServices] attribute</span></span>

<span data-ttu-id="9e20f-379">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="9e20f-380">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="9e20f-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="9e20f-381">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9e20f-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9e20f-382">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e20f-383">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9e20f-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e20f-384">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="9e20f-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="9e20f-385">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9e20f-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="9e20f-386">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="9e20f-386">What is Model binding</span></span>

<span data-ttu-id="9e20f-387">Os controladores e Razor as páginas funcionam com dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-387">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="9e20f-388">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="9e20f-389">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="9e20f-390">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-390">Model binding automates this process.</span></span> <span data-ttu-id="9e20f-391">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="9e20f-391">The model binding system:</span></span>

* <span data-ttu-id="9e20f-392">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="9e20f-393">Fornece os dados para controladores e Razor páginas em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-393">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="9e20f-394">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="9e20f-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="9e20f-395">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="9e20f-396">Exemplo</span><span class="sxs-lookup"><span data-stu-id="9e20f-396">Example</span></span>

<span data-ttu-id="9e20f-397">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e20f-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="9e20f-398">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="9e20f-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="9e20f-399">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="9e20f-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="9e20f-400">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="9e20f-401">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9e20f-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="9e20f-402">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="9e20f-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="9e20f-403">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="9e20f-404">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="9e20f-405">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="9e20f-406">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="9e20f-407">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="9e20f-408">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="9e20f-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="9e20f-409">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="9e20f-410">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="9e20f-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="9e20f-411">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="9e20f-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="9e20f-412">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="9e20f-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="9e20f-413">Destinos</span><span class="sxs-lookup"><span data-stu-id="9e20f-413">Targets</span></span>

<span data-ttu-id="9e20f-414">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="9e20f-415">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="9e20f-416">Parâmetros do Razor método de manipulador Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-416">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="9e20f-417">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="9e20f-418">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="9e20f-418">[BindProperty] attribute</span></span>

<span data-ttu-id="9e20f-419">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="9e20f-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="9e20f-420">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="9e20f-420">[BindProperties] attribute</span></span>

<span data-ttu-id="9e20f-421">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="9e20f-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="9e20f-422">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="9e20f-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="9e20f-423">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="9e20f-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="9e20f-424">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9e20f-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="9e20f-425">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="9e20f-426">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="9e20f-427">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="9e20f-428">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="9e20f-429">Origens</span><span class="sxs-lookup"><span data-stu-id="9e20f-429">Sources</span></span>

<span data-ttu-id="9e20f-430">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="9e20f-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="9e20f-431">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="9e20f-431">Form fields</span></span>
1. <span data-ttu-id="9e20f-432">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="9e20f-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="9e20f-433">Rotear dados</span><span class="sxs-lookup"><span data-stu-id="9e20f-433">Route data</span></span>
1. <span data-ttu-id="9e20f-434">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="9e20f-434">Query string parameters</span></span>
1. <span data-ttu-id="9e20f-435">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="9e20f-435">Uploaded files</span></span>

<span data-ttu-id="9e20f-436">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="9e20f-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="9e20f-437">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="9e20f-437">There are a few exceptions:</span></span>

* <span data-ttu-id="9e20f-438">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="9e20f-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="9e20f-439">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="9e20f-440">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="9e20f-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="9e20f-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="9e20f-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9e20f-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="9e20f-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="9e20f-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="9e20f-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="9e20f-446">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-446">These attributes:</span></span>

* <span data-ttu-id="9e20f-447">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e20f-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="9e20f-448">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="9e20f-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="9e20f-449">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="9e20f-450">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e20f-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="9e20f-451">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="9e20f-451">[FromBody] attribute</span></span>

<span data-ttu-id="9e20f-452">Aplique o `[FromBody]` atributo a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="9e20f-453">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="9e20f-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="9e20f-454">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="9e20f-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="9e20f-455">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="9e20f-456">Por exemplo, a ação a seguir `Create` especifica que seu `pet` parâmetro é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="9e20f-457">A `Pet` classe especifica que sua `Breed` propriedade é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="9e20f-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="9e20f-458">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9e20f-458">In the preceding example:</span></span>

* <span data-ttu-id="9e20f-459">O `[FromQuery]` atributo é ignorado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="9e20f-460">A `Breed` propriedade não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9e20f-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="9e20f-461">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="9e20f-462">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a `Breed` propriedade.</span><span class="sxs-lookup"><span data-stu-id="9e20f-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="9e20f-463">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="9e20f-464">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para ligar outros `[FromBody]` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="9e20f-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="9e20f-465">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="9e20f-465">Additional sources</span></span>

<span data-ttu-id="9e20f-466">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-466">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="9e20f-467">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="9e20f-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="9e20f-468">Por exemplo, talvez você queira dados do cookie estado da sessão ou do.</span><span class="sxs-lookup"><span data-stu-id="9e20f-468">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="9e20f-469">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="9e20f-469">To get data from a new source:</span></span>

* <span data-ttu-id="9e20f-470">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="9e20f-471">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="9e20f-472">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="9e20f-473">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="9e20f-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="9e20f-474">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="9e20f-475">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="9e20f-476">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-476">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="9e20f-477">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="9e20f-477">No source for a model property</span></span>

<span data-ttu-id="9e20f-478">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="9e20f-479">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="9e20f-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="9e20f-480">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="9e20f-481">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="9e20f-482">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="9e20f-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="9e20f-483">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="9e20f-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="9e20f-484">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="9e20f-485">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="9e20f-486">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="9e20f-487">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="9e20f-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="9e20f-488">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="9e20f-488">Type conversion errors</span></span>

<span data-ttu-id="9e20f-489">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="9e20f-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="9e20f-490">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="9e20f-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="9e20f-491">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="9e20f-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="9e20f-492">Em uma Razor página, reexiba a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="9e20f-492">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="9e20f-493">A validação no lado do cliente captura dados mais inválidos que, de outra forma, seriam enviados a um Razor formulário de páginas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-493">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="9e20f-494">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="9e20f-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="9e20f-495">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="9e20f-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="9e20f-496">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="9e20f-497">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="9e20f-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="9e20f-498">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="9e20f-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="9e20f-499">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="9e20f-500">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="9e20f-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="9e20f-501">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="9e20f-502">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="9e20f-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="9e20f-503">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="9e20f-503">Simple types</span></span>

<span data-ttu-id="9e20f-504">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="9e20f-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="9e20f-505">Booliano</span><span class="sxs-lookup"><span data-stu-id="9e20f-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="9e20f-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="9e20f-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="9e20f-507">º</span><span class="sxs-lookup"><span data-stu-id="9e20f-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="9e20f-508">DateTime</span><span class="sxs-lookup"><span data-stu-id="9e20f-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="9e20f-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="9e20f-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="9e20f-510">Decimal</span><span class="sxs-lookup"><span data-stu-id="9e20f-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="9e20f-511">Double</span><span class="sxs-lookup"><span data-stu-id="9e20f-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="9e20f-512">Enumeração</span><span class="sxs-lookup"><span data-stu-id="9e20f-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="9e20f-513">Volume</span><span class="sxs-lookup"><span data-stu-id="9e20f-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="9e20f-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="9e20f-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="9e20f-515">Single</span><span class="sxs-lookup"><span data-stu-id="9e20f-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="9e20f-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="9e20f-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="9e20f-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="9e20f-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="9e20f-518">Uri</span><span class="sxs-lookup"><span data-stu-id="9e20f-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="9e20f-519">Versão</span><span class="sxs-lookup"><span data-stu-id="9e20f-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="9e20f-520">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="9e20f-520">Complex types</span></span>

<span data-ttu-id="9e20f-521">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="9e20f-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="9e20f-522">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="9e20f-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="9e20f-523">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="9e20f-524">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="9e20f-525">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="9e20f-526">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="9e20f-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="9e20f-527">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9e20f-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="9e20f-528">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="9e20f-529">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="9e20f-529">Prefix = parameter name</span></span>

<span data-ttu-id="9e20f-530">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="9e20f-531">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="9e20f-532">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="9e20f-533">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="9e20f-533">Prefix = property name</span></span>

<span data-ttu-id="9e20f-534">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="9e20f-535">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="9e20f-536">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="9e20f-537">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="9e20f-537">Custom prefix</span></span>

<span data-ttu-id="9e20f-538">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="9e20f-539">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="9e20f-540">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="9e20f-541">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="9e20f-541">Attributes for complex type targets</span></span>

<span data-ttu-id="9e20f-542">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="9e20f-543">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="9e20f-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="9e20f-544">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="9e20f-545">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="9e20f-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="9e20f-546">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="9e20f-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="9e20f-547">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="9e20f-547">[BindRequired] attribute</span></span>

<span data-ttu-id="9e20f-548">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="9e20f-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="9e20f-549">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="9e20f-550">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="9e20f-551">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="9e20f-551">[BindNever] attribute</span></span>

<span data-ttu-id="9e20f-552">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="9e20f-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="9e20f-553">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="9e20f-554">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="9e20f-555">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="9e20f-555">[Bind] attribute</span></span>

<span data-ttu-id="9e20f-556">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="9e20f-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="9e20f-557">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="9e20f-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="9e20f-558">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="9e20f-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="9e20f-559">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="9e20f-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="9e20f-560">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="9e20f-561">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="9e20f-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="9e20f-562">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="9e20f-563">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="9e20f-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="9e20f-564">Coleções</span><span class="sxs-lookup"><span data-stu-id="9e20f-564">Collections</span></span>

<span data-ttu-id="9e20f-565">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="9e20f-566">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="9e20f-567">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-567">For example:</span></span>

* <span data-ttu-id="9e20f-568">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="9e20f-569">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-569">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="9e20f-570">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="9e20f-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="9e20f-571">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="9e20f-572">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="9e20f-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="9e20f-573">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="9e20f-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="9e20f-574">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="9e20f-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="9e20f-575">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="9e20f-576">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="9e20f-577">Dicionários</span><span class="sxs-lookup"><span data-stu-id="9e20f-577">Dictionaries</span></span>

<span data-ttu-id="9e20f-578">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="9e20f-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="9e20f-579">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="9e20f-580">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-580">For example:</span></span>

* <span data-ttu-id="9e20f-581">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="9e20f-582">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="9e20f-582">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="9e20f-583">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="9e20f-584">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="9e20f-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="9e20f-585">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="9e20f-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="9e20f-586">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="9e20f-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="9e20f-587">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="9e20f-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="9e20f-588">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="9e20f-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="9e20f-589">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="9e20f-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="9e20f-590">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="9e20f-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="9e20f-591">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="9e20f-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="9e20f-592">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="9e20f-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="9e20f-593">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="9e20f-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="9e20f-594">Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="9e20f-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="9e20f-595">Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="9e20f-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="9e20f-596">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="9e20f-597">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="9e20f-597">Special data types</span></span>

<span data-ttu-id="9e20f-598">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="9e20f-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="9e20f-599">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="9e20f-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="9e20f-600">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="9e20f-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="9e20f-601">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="9e20f-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="9e20f-602">CancellationToken</span></span>

<span data-ttu-id="9e20f-603">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="9e20f-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="9e20f-604">FormCollection</span><span class="sxs-lookup"><span data-stu-id="9e20f-604">FormCollection</span></span>

<span data-ttu-id="9e20f-605">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="9e20f-606">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="9e20f-606">Input formatters</span></span>

<span data-ttu-id="9e20f-607">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="9e20f-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="9e20f-608">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="9e20f-609">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="9e20f-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="9e20f-610">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="9e20f-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="9e20f-611">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="9e20f-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="9e20f-612">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="9e20f-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="9e20f-613">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="9e20f-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="9e20f-614">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="9e20f-615">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="9e20f-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="9e20f-616">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9e20f-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="9e20f-617">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="9e20f-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="9e20f-618">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="9e20f-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="9e20f-619">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="9e20f-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="9e20f-620">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="9e20f-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="9e20f-621">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="9e20f-622">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e20f-623">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="9e20f-624">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e20f-625">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="9e20f-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="9e20f-626">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="9e20f-626">Custom model binders</span></span>

<span data-ttu-id="9e20f-627">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="9e20f-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="9e20f-628">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="9e20f-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="9e20f-629">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="9e20f-629">Manual model binding</span></span>

<span data-ttu-id="9e20f-630">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9e20f-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="9e20f-631">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="9e20f-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="9e20f-632">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="9e20f-633">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="9e20f-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="9e20f-634">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="9e20f-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="9e20f-635">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="9e20f-635">[FromServices] attribute</span></span>

<span data-ttu-id="9e20f-636">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="9e20f-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="9e20f-637">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="9e20f-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="9e20f-638">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9e20f-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9e20f-639">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="9e20f-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e20f-640">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9e20f-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
