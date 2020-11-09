---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- 'appsettings.json'
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
uid: mvc/models/model-binding
ms.openlocfilehash: 49300d32096e577db9b13a0510cc310b91ddb51d
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365347"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="95cab-103">Model binding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95cab-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="95cab-104">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="95cab-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="95cab-105">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95cab-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="95cab-106">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="95cab-106">What is Model binding</span></span>

<span data-ttu-id="95cab-107">Os controladores e Razor as páginas funcionam com dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="95cab-108">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="95cab-109">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="95cab-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="95cab-110">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="95cab-110">Model binding automates this process.</span></span> <span data-ttu-id="95cab-111">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="95cab-111">The model binding system:</span></span>

* <span data-ttu-id="95cab-112">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="95cab-113">Fornece os dados para controladores e Razor páginas em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="95cab-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="95cab-114">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="95cab-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="95cab-115">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="95cab-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="95cab-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="95cab-116">Example</span></span>

<span data-ttu-id="95cab-117">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="95cab-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="95cab-118">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="95cab-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="95cab-119">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="95cab-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="95cab-120">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="95cab-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="95cab-121">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="95cab-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="95cab-122">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="95cab-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="95cab-123">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="95cab-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="95cab-124">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="95cab-125">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="95cab-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="95cab-126">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="95cab-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="95cab-127">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="95cab-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="95cab-128">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="95cab-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="95cab-129">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="95cab-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="95cab-130">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="95cab-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="95cab-131">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="95cab-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="95cab-132">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="95cab-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="95cab-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="95cab-133">Targets</span></span>

<span data-ttu-id="95cab-134">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="95cab-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="95cab-135">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="95cab-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="95cab-136">Parâmetros do Razor método de manipulador Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="95cab-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="95cab-137">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="95cab-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="95cab-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="95cab-138">[BindProperty] attribute</span></span>

<span data-ttu-id="95cab-139">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="95cab-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="95cab-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="95cab-140">[BindProperties] attribute</span></span>

<span data-ttu-id="95cab-141">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="95cab-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="95cab-142">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="95cab-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="95cab-143">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="95cab-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="95cab-144">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="95cab-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="95cab-145">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="95cab-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="95cab-146">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="95cab-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="95cab-147">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="95cab-148">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="95cab-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="95cab-149">Origens</span><span class="sxs-lookup"><span data-stu-id="95cab-149">Sources</span></span>

<span data-ttu-id="95cab-150">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="95cab-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="95cab-151">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="95cab-151">Form fields</span></span>
1. <span data-ttu-id="95cab-152">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="95cab-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="95cab-153">Rotear dados</span><span class="sxs-lookup"><span data-stu-id="95cab-153">Route data</span></span>
1. <span data-ttu-id="95cab-154">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="95cab-154">Query string parameters</span></span>
1. <span data-ttu-id="95cab-155">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="95cab-155">Uploaded files</span></span>

<span data-ttu-id="95cab-156">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="95cab-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="95cab-157">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="95cab-157">There are a few exceptions:</span></span>

* <span data-ttu-id="95cab-158">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="95cab-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="95cab-159">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="95cab-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="95cab-160">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="95cab-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="95cab-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="95cab-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="95cab-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="95cab-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="95cab-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="95cab-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="95cab-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="95cab-166">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="95cab-166">These attributes:</span></span>

* <span data-ttu-id="95cab-167">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="95cab-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="95cab-168">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="95cab-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="95cab-169">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="95cab-170">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="95cab-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="95cab-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="95cab-171">[FromBody] attribute</span></span>

<span data-ttu-id="95cab-172">Aplique o `[FromBody]` atributo a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="95cab-173">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="95cab-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="95cab-174">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="95cab-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="95cab-175">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="95cab-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="95cab-176">Por exemplo, a ação a seguir `Create` especifica que seu `pet` parâmetro é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="95cab-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="95cab-177">A `Pet` classe especifica que sua `Breed` propriedade é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="95cab-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="95cab-178">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="95cab-178">In the preceding example:</span></span>

* <span data-ttu-id="95cab-179">O `[FromQuery]` atributo é ignorado.</span><span class="sxs-lookup"><span data-stu-id="95cab-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="95cab-180">A `Breed` propriedade não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="95cab-181">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="95cab-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="95cab-182">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a `Breed` propriedade.</span><span class="sxs-lookup"><span data-stu-id="95cab-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="95cab-183">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="95cab-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="95cab-184">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para ligar outros `[FromBody]` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="95cab-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="95cab-185">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="95cab-185">Additional sources</span></span>

<span data-ttu-id="95cab-186">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="95cab-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="95cab-187">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="95cab-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="95cab-188">Por exemplo, talvez você queira dados do cookie estado da sessão ou do.</span><span class="sxs-lookup"><span data-stu-id="95cab-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="95cab-189">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="95cab-189">To get data from a new source:</span></span>

* <span data-ttu-id="95cab-190">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="95cab-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="95cab-191">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="95cab-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="95cab-192">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="95cab-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="95cab-193">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="95cab-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="95cab-194">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="95cab-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="95cab-195">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="95cab-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="95cab-196">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="95cab-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="95cab-197">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="95cab-197">No source for a model property</span></span>

<span data-ttu-id="95cab-198">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="95cab-199">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="95cab-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="95cab-200">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="95cab-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="95cab-201">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="95cab-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="95cab-202">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="95cab-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="95cab-203">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="95cab-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="95cab-204">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="95cab-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="95cab-205">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="95cab-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="95cab-206">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="95cab-207">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="95cab-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="95cab-208">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="95cab-208">Type conversion errors</span></span>

<span data-ttu-id="95cab-209">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="95cab-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="95cab-210">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="95cab-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="95cab-211">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="95cab-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="95cab-212">Em uma Razor página, reexiba a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="95cab-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="95cab-213">A validação no lado do cliente captura dados mais inválidos que, de outra forma, seriam enviados a um Razor formulário de páginas.</span><span class="sxs-lookup"><span data-stu-id="95cab-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="95cab-214">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="95cab-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="95cab-215">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="95cab-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="95cab-216">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="95cab-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="95cab-217">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="95cab-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="95cab-218">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="95cab-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="95cab-219">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="95cab-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="95cab-220">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="95cab-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="95cab-221">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="95cab-222">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="95cab-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="95cab-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="95cab-223">Simple types</span></span>

<span data-ttu-id="95cab-224">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="95cab-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="95cab-225">Booliano</span><span class="sxs-lookup"><span data-stu-id="95cab-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="95cab-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="95cab-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="95cab-227">º</span><span class="sxs-lookup"><span data-stu-id="95cab-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="95cab-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="95cab-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="95cab-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="95cab-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="95cab-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="95cab-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="95cab-231">Double</span><span class="sxs-lookup"><span data-stu-id="95cab-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="95cab-232">Enumeração</span><span class="sxs-lookup"><span data-stu-id="95cab-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="95cab-233">Volume</span><span class="sxs-lookup"><span data-stu-id="95cab-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="95cab-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="95cab-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="95cab-235">Single</span><span class="sxs-lookup"><span data-stu-id="95cab-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="95cab-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="95cab-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="95cab-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="95cab-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="95cab-238">Uri</span><span class="sxs-lookup"><span data-stu-id="95cab-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="95cab-239">Versão</span><span class="sxs-lookup"><span data-stu-id="95cab-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="95cab-240">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="95cab-240">Complex types</span></span>

<span data-ttu-id="95cab-241">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="95cab-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="95cab-242">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="95cab-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="95cab-243">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="95cab-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="95cab-244">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="95cab-245">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="95cab-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="95cab-246">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="95cab-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="95cab-247">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="95cab-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="95cab-248">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="95cab-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="95cab-249">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="95cab-249">Prefix = parameter name</span></span>

<span data-ttu-id="95cab-250">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="95cab-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="95cab-251">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="95cab-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="95cab-252">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="95cab-253">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="95cab-253">Prefix = property name</span></span>

<span data-ttu-id="95cab-254">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="95cab-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="95cab-255">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="95cab-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="95cab-256">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="95cab-257">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="95cab-257">Custom prefix</span></span>

<span data-ttu-id="95cab-258">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="95cab-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="95cab-259">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="95cab-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="95cab-260">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="95cab-261">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="95cab-261">Attributes for complex type targets</span></span>

<span data-ttu-id="95cab-262">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="95cab-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="95cab-263">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="95cab-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="95cab-264">Eles \* **não** afetam os formatadores de entrada, que processam os corpos de solicitação JSON e XML lançados.</span><span class="sxs-lookup"><span data-stu-id="95cab-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="95cab-265">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="95cab-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="95cab-266">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="95cab-266">[Bind] attribute</span></span>

<span data-ttu-id="95cab-267">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="95cab-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="95cab-268">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="95cab-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="95cab-269">`[Bind]` Não _*_afeta os_*_ formatadores de entrada.</span><span class="sxs-lookup"><span data-stu-id="95cab-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="95cab-270">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="95cab-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="95cab-271">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="95cab-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="95cab-272">O `[Bind]` atributo pode ser usado para proteger contra sobrepostos em cenários _create \*.</span><span class="sxs-lookup"><span data-stu-id="95cab-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="95cab-273">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="95cab-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="95cab-274">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="95cab-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="95cab-275">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="95cab-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="95cab-276">Atributo [ModelBinder]</span><span class="sxs-lookup"><span data-stu-id="95cab-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="95cab-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> pode ser aplicado a tipos, propriedades ou parâmetros.</span><span class="sxs-lookup"><span data-stu-id="95cab-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="95cab-278">Ele permite especificar o tipo de associador de modelo usado para associar a instância ou o tipo específico.</span><span class="sxs-lookup"><span data-stu-id="95cab-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="95cab-279">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="95cab-280">O `[ModelBinder]` atributo também pode ser usado para alterar o nome de uma propriedade ou parâmetro quando ele está sendo associado a um modelo:</span><span class="sxs-lookup"><span data-stu-id="95cab-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="95cab-281">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="95cab-281">[BindRequired] attribute</span></span>

<span data-ttu-id="95cab-282">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="95cab-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="95cab-283">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="95cab-284">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="95cab-285">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="95cab-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="95cab-286">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="95cab-286">[BindNever] attribute</span></span>

<span data-ttu-id="95cab-287">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="95cab-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="95cab-288">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="95cab-289">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="95cab-290">Coleções</span><span class="sxs-lookup"><span data-stu-id="95cab-290">Collections</span></span>

<span data-ttu-id="95cab-291">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="95cab-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="95cab-292">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="95cab-293">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-293">For example:</span></span>

* <span data-ttu-id="95cab-294">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="95cab-295">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="95cab-295">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="95cab-296">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="95cab-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="95cab-297">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="95cab-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="95cab-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="95cab-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="95cab-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="95cab-300">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="95cab-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="95cab-301">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="95cab-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="95cab-302">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="95cab-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="95cab-303">Dicionários</span><span class="sxs-lookup"><span data-stu-id="95cab-303">Dictionaries</span></span>

<span data-ttu-id="95cab-304">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="95cab-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="95cab-305">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="95cab-306">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-306">For example:</span></span>

* <span data-ttu-id="95cab-307">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="95cab-308">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="95cab-308">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="95cab-309">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="95cab-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="95cab-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="95cab-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="95cab-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="95cab-312">Associação de construtor e tipos de registro</span><span class="sxs-lookup"><span data-stu-id="95cab-312">Constructor binding and record types</span></span>

<span data-ttu-id="95cab-313">A associação de modelo requer que os tipos complexos tenham um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="95cab-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="95cab-314">Os `System.Text.Json` `Newtonsoft.Json` formatadores de entrada e com base dão suporte à desserialização de classes que não têm um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="95cab-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="95cab-315">O C# 9 apresenta tipos de registro, que são uma ótima maneira de representar de forma sucinta os dados pela rede.</span><span class="sxs-lookup"><span data-stu-id="95cab-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="95cab-316">ASP.NET Core adiciona suporte para associação de modelo e validação de tipos de registro com um único Construtor:</span><span class="sxs-lookup"><span data-stu-id="95cab-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

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

<span data-ttu-id="95cab-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="95cab-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="95cab-318">Ao validar tipos de registro, o tempo de execução pesquisa os metadados de validação especificamente em parâmetros em vez de em Propriedades.</span><span class="sxs-lookup"><span data-stu-id="95cab-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="95cab-319">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="95cab-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="95cab-320">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="95cab-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="95cab-321">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="95cab-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="95cab-322">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="95cab-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="95cab-323">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="95cab-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="95cab-324">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="95cab-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="95cab-325">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="95cab-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="95cab-326">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="95cab-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="95cab-327">Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="95cab-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="95cab-328">Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="95cab-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="95cab-329">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="95cab-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="95cab-330">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="95cab-330">Special data types</span></span>

<span data-ttu-id="95cab-331">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="95cab-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="95cab-332">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="95cab-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="95cab-333">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="95cab-334">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="95cab-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="95cab-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="95cab-335">CancellationToken</span></span>

<span data-ttu-id="95cab-336">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="95cab-336">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="95cab-337">FormCollection</span><span class="sxs-lookup"><span data-stu-id="95cab-337">FormCollection</span></span>

<span data-ttu-id="95cab-338">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="95cab-338">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="95cab-339">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="95cab-339">Input formatters</span></span>

<span data-ttu-id="95cab-340">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="95cab-340">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="95cab-341">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="95cab-341">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="95cab-342">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="95cab-342">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="95cab-343">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="95cab-343">You can add other formatters for other content types.</span></span>

<span data-ttu-id="95cab-344">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="95cab-344">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="95cab-345">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="95cab-345">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="95cab-346">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="95cab-346">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="95cab-347">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="95cab-347">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="95cab-348">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="95cab-348">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="95cab-349">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-349">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="95cab-350">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="95cab-350">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="95cab-351">Personalizar a associação de modelo com formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="95cab-351">Customize model binding with input formatters</span></span>

<span data-ttu-id="95cab-352">Um formatador de entrada assume total responsabilidade pela leitura de dados do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-352">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="95cab-353">Para personalizar esse processo, configure as APIs usadas pelo formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="95cab-353">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="95cab-354">Esta seção descreve como personalizar o `System.Text.Json` formatador de entrada baseado em dados para entender um tipo personalizado chamado `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="95cab-354">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="95cab-355">Considere o seguinte modelo, que contém uma `ObjectId` propriedade personalizada chamada `Id` :</span><span class="sxs-lookup"><span data-stu-id="95cab-355">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="95cab-356">Para personalizar o processo de associação de modelo ao usar o `System.Text.Json` , crie uma classe derivada de <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="95cab-356">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="95cab-357">Para usar um conversor personalizado, aplique o <xref:System.Text.Json.Serialization.JsonConverterAttribute> atributo ao tipo.</span><span class="sxs-lookup"><span data-stu-id="95cab-357">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="95cab-358">No exemplo a seguir, o `ObjectId` tipo é configurado com `ObjectIdConverter` como seu conversor personalizado:</span><span class="sxs-lookup"><span data-stu-id="95cab-358">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="95cab-359">Para obter mais informações, consulte [como escrever conversores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="95cab-359">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="95cab-360">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="95cab-360">Exclude specified types from model binding</span></span>

<span data-ttu-id="95cab-361">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="95cab-361">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="95cab-362">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="95cab-362">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="95cab-363">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="95cab-363">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="95cab-364">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="95cab-364">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="95cab-365">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="95cab-365">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="95cab-366">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="95cab-366">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="95cab-367">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="95cab-367">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="95cab-368">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="95cab-368">Custom model binders</span></span>

<span data-ttu-id="95cab-369">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="95cab-369">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="95cab-370">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="95cab-370">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="95cab-371">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="95cab-371">Manual model binding</span></span> 

<span data-ttu-id="95cab-372">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="95cab-372">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="95cab-373">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="95cab-373">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="95cab-374">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="95cab-374">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="95cab-375">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="95cab-375">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="95cab-376">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-376">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="95cab-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  usa provedores de valor para obter dados do corpo do formulário, Cadeia de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="95cab-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="95cab-378">`TryUpdateModelAsync` normalmente é:</span><span class="sxs-lookup"><span data-stu-id="95cab-378">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="95cab-379">Usado com Razor páginas e aplicativos MVC usando controladores e exibições para evitar o excesso de postagens.</span><span class="sxs-lookup"><span data-stu-id="95cab-379">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="95cab-380">Não usado com uma API da Web, a menos que consumido de dados de formulário, cadeias de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="95cab-380">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="95cab-381">Os pontos de extremidade da API Web que consomem JSON usam [formatadores de entrada](#input-formatters) para desserializar o corpo da solicitação em um objeto.</span><span class="sxs-lookup"><span data-stu-id="95cab-381">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="95cab-382">Para obter mais informações, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="95cab-382">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="95cab-383">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="95cab-383">[FromServices] attribute</span></span>

<span data-ttu-id="95cab-384">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="95cab-384">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="95cab-385">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="95cab-385">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="95cab-386">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="95cab-386">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="95cab-387">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="95cab-387">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95cab-388">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95cab-388">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95cab-389">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="95cab-389">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="95cab-390">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95cab-390">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="95cab-391">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="95cab-391">What is Model binding</span></span>

<span data-ttu-id="95cab-392">Os controladores e Razor as páginas funcionam com dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-392">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="95cab-393">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-393">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="95cab-394">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="95cab-394">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="95cab-395">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="95cab-395">Model binding automates this process.</span></span> <span data-ttu-id="95cab-396">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="95cab-396">The model binding system:</span></span>

* <span data-ttu-id="95cab-397">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-397">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="95cab-398">Fornece os dados para controladores e Razor páginas em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="95cab-398">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="95cab-399">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="95cab-399">Converts string data to .NET types.</span></span>
* <span data-ttu-id="95cab-400">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="95cab-400">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="95cab-401">Exemplo</span><span class="sxs-lookup"><span data-stu-id="95cab-401">Example</span></span>

<span data-ttu-id="95cab-402">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="95cab-402">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="95cab-403">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="95cab-403">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="95cab-404">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="95cab-404">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="95cab-405">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="95cab-405">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="95cab-406">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="95cab-406">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="95cab-407">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="95cab-407">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="95cab-408">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="95cab-408">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="95cab-409">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-409">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="95cab-410">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="95cab-410">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="95cab-411">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="95cab-411">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="95cab-412">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="95cab-412">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="95cab-413">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="95cab-413">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="95cab-414">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="95cab-414">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="95cab-415">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="95cab-415">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="95cab-416">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="95cab-416">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="95cab-417">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="95cab-417">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="95cab-418">Destinos</span><span class="sxs-lookup"><span data-stu-id="95cab-418">Targets</span></span>

<span data-ttu-id="95cab-419">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="95cab-419">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="95cab-420">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="95cab-420">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="95cab-421">Parâmetros do Razor método de manipulador Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="95cab-421">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="95cab-422">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="95cab-422">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="95cab-423">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="95cab-423">[BindProperty] attribute</span></span>

<span data-ttu-id="95cab-424">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="95cab-424">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="95cab-425">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="95cab-425">[BindProperties] attribute</span></span>

<span data-ttu-id="95cab-426">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="95cab-426">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="95cab-427">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="95cab-427">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="95cab-428">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="95cab-428">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="95cab-429">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="95cab-429">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="95cab-430">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="95cab-430">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="95cab-431">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="95cab-431">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="95cab-432">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-432">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="95cab-433">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="95cab-433">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="95cab-434">Origens</span><span class="sxs-lookup"><span data-stu-id="95cab-434">Sources</span></span>

<span data-ttu-id="95cab-435">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="95cab-435">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="95cab-436">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="95cab-436">Form fields</span></span>
1. <span data-ttu-id="95cab-437">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="95cab-437">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="95cab-438">Rotear dados</span><span class="sxs-lookup"><span data-stu-id="95cab-438">Route data</span></span>
1. <span data-ttu-id="95cab-439">Parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="95cab-439">Query string parameters</span></span>
1. <span data-ttu-id="95cab-440">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="95cab-440">Uploaded files</span></span>

<span data-ttu-id="95cab-441">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="95cab-441">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="95cab-442">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="95cab-442">There are a few exceptions:</span></span>

* <span data-ttu-id="95cab-443">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="95cab-443">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="95cab-444">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="95cab-444">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="95cab-445">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="95cab-445">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="95cab-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="95cab-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="95cab-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="95cab-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="95cab-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="95cab-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="95cab-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="95cab-451">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="95cab-451">These attributes:</span></span>

* <span data-ttu-id="95cab-452">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="95cab-452">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="95cab-453">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="95cab-453">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="95cab-454">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-454">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="95cab-455">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="95cab-455">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="95cab-456">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="95cab-456">[FromBody] attribute</span></span>

<span data-ttu-id="95cab-457">Aplique o `[FromBody]` atributo a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-457">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="95cab-458">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="95cab-458">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="95cab-459">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="95cab-459">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="95cab-460">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="95cab-460">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="95cab-461">Por exemplo, a ação a seguir `Create` especifica que seu `pet` parâmetro é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="95cab-461">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="95cab-462">A `Pet` classe especifica que sua `Breed` propriedade é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="95cab-462">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="95cab-463">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="95cab-463">In the preceding example:</span></span>

* <span data-ttu-id="95cab-464">O `[FromQuery]` atributo é ignorado.</span><span class="sxs-lookup"><span data-stu-id="95cab-464">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="95cab-465">A `Breed` propriedade não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="95cab-465">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="95cab-466">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="95cab-466">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="95cab-467">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a `Breed` propriedade.</span><span class="sxs-lookup"><span data-stu-id="95cab-467">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="95cab-468">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="95cab-468">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="95cab-469">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para ligar outros `[FromBody]` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="95cab-469">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="95cab-470">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="95cab-470">Additional sources</span></span>

<span data-ttu-id="95cab-471">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="95cab-471">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="95cab-472">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="95cab-472">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="95cab-473">Por exemplo, talvez você queira dados do cookie estado da sessão ou do.</span><span class="sxs-lookup"><span data-stu-id="95cab-473">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="95cab-474">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="95cab-474">To get data from a new source:</span></span>

* <span data-ttu-id="95cab-475">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="95cab-475">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="95cab-476">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="95cab-476">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="95cab-477">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="95cab-477">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="95cab-478">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [fábrica](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookie s.</span><span class="sxs-lookup"><span data-stu-id="95cab-478">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="95cab-479">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="95cab-479">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="95cab-480">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="95cab-480">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="95cab-481">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="95cab-481">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="95cab-482">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="95cab-482">No source for a model property</span></span>

<span data-ttu-id="95cab-483">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-483">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="95cab-484">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="95cab-484">The property is set to null or a default value:</span></span>

* <span data-ttu-id="95cab-485">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="95cab-485">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="95cab-486">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="95cab-486">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="95cab-487">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="95cab-487">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="95cab-488">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="95cab-488">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="95cab-489">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="95cab-489">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="95cab-490">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [`[BindRequired]`](#bindrequired-attribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="95cab-490">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="95cab-491">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-491">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="95cab-492">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="95cab-492">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="95cab-493">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="95cab-493">Type conversion errors</span></span>

<span data-ttu-id="95cab-494">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="95cab-494">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="95cab-495">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="95cab-495">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="95cab-496">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="95cab-496">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="95cab-497">Em uma Razor página, reexiba a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="95cab-497">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="95cab-498">A validação no lado do cliente captura dados mais inválidos que, de outra forma, seriam enviados a um Razor formulário de páginas.</span><span class="sxs-lookup"><span data-stu-id="95cab-498">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="95cab-499">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="95cab-499">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="95cab-500">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="95cab-500">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="95cab-501">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="95cab-501">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="95cab-502">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="95cab-502">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="95cab-503">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="95cab-503">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="95cab-504">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="95cab-504">The invalid input does appear in an error message.</span></span> <span data-ttu-id="95cab-505">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="95cab-505">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="95cab-506">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-506">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="95cab-507">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="95cab-507">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="95cab-508">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="95cab-508">Simple types</span></span>

<span data-ttu-id="95cab-509">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="95cab-509">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="95cab-510">Booliano</span><span class="sxs-lookup"><span data-stu-id="95cab-510">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="95cab-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="95cab-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="95cab-512">º</span><span class="sxs-lookup"><span data-stu-id="95cab-512">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="95cab-513">DateTime</span><span class="sxs-lookup"><span data-stu-id="95cab-513">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="95cab-514">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="95cab-514">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="95cab-515">Decimal</span><span class="sxs-lookup"><span data-stu-id="95cab-515">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="95cab-516">Double</span><span class="sxs-lookup"><span data-stu-id="95cab-516">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="95cab-517">Enumeração</span><span class="sxs-lookup"><span data-stu-id="95cab-517">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="95cab-518">Volume</span><span class="sxs-lookup"><span data-stu-id="95cab-518">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="95cab-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="95cab-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="95cab-520">Single</span><span class="sxs-lookup"><span data-stu-id="95cab-520">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="95cab-521">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="95cab-521">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="95cab-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="95cab-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="95cab-523">Uri</span><span class="sxs-lookup"><span data-stu-id="95cab-523">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="95cab-524">Versão</span><span class="sxs-lookup"><span data-stu-id="95cab-524">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="95cab-525">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="95cab-525">Complex types</span></span>

<span data-ttu-id="95cab-526">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="95cab-526">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="95cab-527">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="95cab-527">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="95cab-528">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="95cab-528">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="95cab-529">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-529">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="95cab-530">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="95cab-530">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="95cab-531">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="95cab-531">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="95cab-532">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="95cab-532">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="95cab-533">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="95cab-533">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="95cab-534">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="95cab-534">Prefix = parameter name</span></span>

<span data-ttu-id="95cab-535">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="95cab-535">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="95cab-536">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="95cab-536">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="95cab-537">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-537">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="95cab-538">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="95cab-538">Prefix = property name</span></span>

<span data-ttu-id="95cab-539">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="95cab-539">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="95cab-540">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="95cab-540">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="95cab-541">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-541">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="95cab-542">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="95cab-542">Custom prefix</span></span>

<span data-ttu-id="95cab-543">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="95cab-543">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="95cab-544">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="95cab-544">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="95cab-545">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-545">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="95cab-546">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="95cab-546">Attributes for complex type targets</span></span>

<span data-ttu-id="95cab-547">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="95cab-547">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="95cab-548">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="95cab-548">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="95cab-549">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="95cab-549">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="95cab-550">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="95cab-550">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="95cab-551">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="95cab-551">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="95cab-552">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="95cab-552">[BindRequired] attribute</span></span>

<span data-ttu-id="95cab-553">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="95cab-553">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="95cab-554">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-554">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="95cab-555">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-555">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="95cab-556">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="95cab-556">[BindNever] attribute</span></span>

<span data-ttu-id="95cab-557">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="95cab-557">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="95cab-558">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="95cab-558">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="95cab-559">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-559">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="95cab-560">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="95cab-560">[Bind] attribute</span></span>

<span data-ttu-id="95cab-561">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="95cab-561">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="95cab-562">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="95cab-562">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="95cab-563">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="95cab-563">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="95cab-564">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="95cab-564">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="95cab-565">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="95cab-565">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="95cab-566">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="95cab-566">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="95cab-567">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="95cab-567">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="95cab-568">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="95cab-568">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="95cab-569">Coleções</span><span class="sxs-lookup"><span data-stu-id="95cab-569">Collections</span></span>

<span data-ttu-id="95cab-570">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="95cab-570">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="95cab-571">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="95cab-572">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-572">For example:</span></span>

* <span data-ttu-id="95cab-573">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-573">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="95cab-574">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="95cab-574">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="95cab-575">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="95cab-575">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="95cab-576">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-576">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="95cab-577">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="95cab-577">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="95cab-578">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="95cab-578">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="95cab-579">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="95cab-579">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="95cab-580">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="95cab-580">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="95cab-581">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="95cab-581">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="95cab-582">Dicionários</span><span class="sxs-lookup"><span data-stu-id="95cab-582">Dictionaries</span></span>

<span data-ttu-id="95cab-583">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="95cab-583">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="95cab-584">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="95cab-584">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="95cab-585">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-585">For example:</span></span>

* <span data-ttu-id="95cab-586">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-586">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="95cab-587">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="95cab-587">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="95cab-588">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="95cab-588">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="95cab-589">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="95cab-589">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="95cab-590">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="95cab-590">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="95cab-591">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="95cab-591">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="95cab-592">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="95cab-592">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="95cab-593">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="95cab-593">Treat values as invariant culture.</span></span>
* <span data-ttu-id="95cab-594">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="95cab-594">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="95cab-595">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="95cab-595">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="95cab-596">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="95cab-596">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="95cab-597">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="95cab-597">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="95cab-598">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="95cab-598">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="95cab-599">Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="95cab-599">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="95cab-600">Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="95cab-600">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="95cab-601">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="95cab-601">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="95cab-602">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="95cab-602">Special data types</span></span>

<span data-ttu-id="95cab-603">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="95cab-603">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="95cab-604">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="95cab-604">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="95cab-605">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="95cab-605">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="95cab-606">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="95cab-606">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="95cab-607">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="95cab-607">CancellationToken</span></span>

<span data-ttu-id="95cab-608">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="95cab-608">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="95cab-609">FormCollection</span><span class="sxs-lookup"><span data-stu-id="95cab-609">FormCollection</span></span>

<span data-ttu-id="95cab-610">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="95cab-610">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="95cab-611">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="95cab-611">Input formatters</span></span>

<span data-ttu-id="95cab-612">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="95cab-612">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="95cab-613">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="95cab-613">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="95cab-614">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="95cab-614">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="95cab-615">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="95cab-615">You can add other formatters for other content types.</span></span>

<span data-ttu-id="95cab-616">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="95cab-616">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="95cab-617">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="95cab-617">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="95cab-618">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="95cab-618">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="95cab-619">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="95cab-619">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="95cab-620">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="95cab-620">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="95cab-621">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="95cab-621">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="95cab-622">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="95cab-622">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="95cab-623">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="95cab-623">Exclude specified types from model binding</span></span>

<span data-ttu-id="95cab-624">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="95cab-624">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="95cab-625">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="95cab-625">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="95cab-626">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="95cab-626">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="95cab-627">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="95cab-627">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="95cab-628">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="95cab-628">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="95cab-629">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="95cab-629">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="95cab-630">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="95cab-630">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="95cab-631">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="95cab-631">Custom model binders</span></span>

<span data-ttu-id="95cab-632">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="95cab-632">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="95cab-633">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="95cab-633">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="95cab-634">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="95cab-634">Manual model binding</span></span>

<span data-ttu-id="95cab-635">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="95cab-635">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="95cab-636">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="95cab-636">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="95cab-637">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="95cab-637">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="95cab-638">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="95cab-638">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="95cab-639">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="95cab-639">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="95cab-640">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="95cab-640">[FromServices] attribute</span></span>

<span data-ttu-id="95cab-641">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="95cab-641">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="95cab-642">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="95cab-642">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="95cab-643">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="95cab-643">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="95cab-644">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="95cab-644">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95cab-645">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95cab-645">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
