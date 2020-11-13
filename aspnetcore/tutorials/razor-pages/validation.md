---
title: Parte 8, adicionar validação
author: rick-anderson
description: 'Parte 8 da série de tutoriais em :::no-loc(Razor)::: páginas.'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d69ab3452f4f15e916049e5c772a20fe9f9fac65
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570218"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="7aa2b-103">Parte 8 da série de tutoriais em :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-103">Part 8 of tutorial series on :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="7aa2b-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7aa2b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7aa2b-105">Nesta seção, a lógica de validação é adicionada para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="7aa2b-106">As regras de validação são impostas sempre que um usuário cria ou edita um filme.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="7aa2b-107">Validação</span><span class="sxs-lookup"><span data-stu-id="7aa2b-107">Validation</span></span>

<span data-ttu-id="7aa2b-108">Um princípio-chave do desenvolvimento de software é chamado [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (“ **D** on't **R** epeat **Y** ourself”).</span><span class="sxs-lookup"><span data-stu-id="7aa2b-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="7aa2b-109">:::no-loc(Razor)::: As páginas incentivam o desenvolvimento onde a funcionalidade é especificada uma vez e é refletida em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-109">:::no-loc(Razor)::: Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="7aa2b-110">O DRY pode ajudar a:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-110">DRY can help:</span></span>

* <span data-ttu-id="7aa2b-111">Reduzir a quantidade de código em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="7aa2b-112">Fazer com que o código seja menos propenso a erros e mais fácil de ser testado e mantido.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="7aa2b-113">O suporte de validação fornecido por :::no-loc(Razor)::: páginas e Entity Framework é um bom exemplo do princípio seco:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-113">The validation support provided by :::no-loc(Razor)::: Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="7aa2b-114">As regras de validação são especificadas declarativamente em um único lugar, na classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="7aa2b-115">As regras são impostas em todos os lugares no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="7aa2b-116">Adicionar regras de validação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="7aa2b-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="7aa2b-117">O `DataAnnotations` namespace fornece:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="7aa2b-118">Um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="7aa2b-119">Formatação de atributos como `[DataType]` essa ajuda com a formatação e não fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="7aa2b-120">Atualize a classe `Movie` para aproveitar os atributos de validação `[Required]`, `[StringLength]`, `[RegularExpression]` e `[Range]` internos.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="7aa2b-121">Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="7aa2b-122">Os atributos `[Required]` e `[MinimumLength]` indicam que uma propriedade deve ter um valor.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="7aa2b-123">Nada impede que um usuário insira espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="7aa2b-124">O atributo `[RegularExpression]` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="7aa2b-125">No código anterior, `Genre` :</span><span class="sxs-lookup"><span data-stu-id="7aa2b-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="7aa2b-126">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-126">Must only use letters.</span></span>
  * <span data-ttu-id="7aa2b-127">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="7aa2b-128">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="7aa2b-129">O `RegularExpression` `Rating` :</span><span class="sxs-lookup"><span data-stu-id="7aa2b-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="7aa2b-130">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="7aa2b-131">Permite caracteres especiais e números em espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="7aa2b-132">"PG-13" é válido para uma classificação, mas falha para um `Genre` .</span><span class="sxs-lookup"><span data-stu-id="7aa2b-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="7aa2b-133">O atributo `[Range]` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="7aa2b-134">O `[StringLength]` atributo pode definir um comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="7aa2b-135">Tipos de valor, como `decimal` ,,, `int` `float` `DateTime` , são inerentemente necessários e não precisam do `[Required]` atributo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="7aa2b-136">As regras de validação anteriores são usadas para demonstração, elas não são ideais para um sistema de produção.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="7aa2b-137">Por exemplo, o anterior impede a inserção de um filme com apenas dois caracteres e não permite caracteres especiais no `Genre` .</span><span class="sxs-lookup"><span data-stu-id="7aa2b-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="7aa2b-138">Ter regras de validação automaticamente impostas pelo ASP.NET Core ajuda a:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="7aa2b-139">Ajuda a tornar o aplicativo mais robusto.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="7aa2b-140">Reduza as chances de salvar dados inválidos no banco de dado.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="7aa2b-141">IU de erro de validação em :::no-loc(Razor)::: páginas</span><span class="sxs-lookup"><span data-stu-id="7aa2b-141">Validation Error UI in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="7aa2b-142">Execute o aplicativo e navegue para Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="7aa2b-143">Selecione o link **:::no-loc(Create)::: novo** .</span><span class="sxs-lookup"><span data-stu-id="7aa2b-143">Select the **:::no-loc(Create)::: New** link.</span></span> <span data-ttu-id="7aa2b-144">Preencha o formulário com alguns valores inválidos.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="7aa2b-145">Quando a validação do lado do cliente do jQuery detecta o erro, ela exibe uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="7aa2b-147">Observe como o formulário renderizou automaticamente uma mensagem de erro de validação em cada campo que contém um valor inválido.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="7aa2b-148">Os erros são impostos no lado do cliente, usando JavaScript e jQuery, e no lado do servidor, quando um usuário tem o JavaScript desabilitado.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="7aa2b-149">Um benefício significativo é que **nenhuma** alteração de código era necessária nas :::no-loc(Create)::: páginas ou editar.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-149">A significant benefit is that **no** code changes were necessary in the :::no-loc(Create)::: or Edit pages.</span></span> <span data-ttu-id="7aa2b-150">Depois que as anotações de dados forem aplicadas ao modelo, a interface do usuário de validação foi habilitada.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="7aa2b-151">As :::no-loc(Razor)::: páginas criadas neste tutorial captam automaticamente as regras de validação, usando atributos de validação nas propriedades da `Movie` classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-151">The :::no-loc(Razor)::: Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="7aa2b-152">Validação do teste usando a página Editar: a mesma validação é aplicada.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="7aa2b-153">Os dados de formulário não serão postados no servidor enquanto houver erros de validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="7aa2b-154">Verifique se os dados de formulário não são postados por uma ou mais das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="7aa2b-155">Coloque um ponto de interrupção no método `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="7aa2b-156">Envie o formulário selecionando **:::no-loc(Create):::** ou **salvando**.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-156">Submit the form by selecting **:::no-loc(Create):::** or **Save**.</span></span> <span data-ttu-id="7aa2b-157">O ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-157">The break point is never hit.</span></span>
* <span data-ttu-id="7aa2b-158">Use a [ferramenta Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="7aa2b-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="7aa2b-159">Use as ferramentas do desenvolvedor do navegador para monitorar o tráfego de rede.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="7aa2b-160">Validação do servidor</span><span class="sxs-lookup"><span data-stu-id="7aa2b-160">Server-side validation</span></span>

<span data-ttu-id="7aa2b-161">Quando o JavaScript está desabilitado no navegador, o envio do formulário com erros será postado no servidor.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="7aa2b-162">(Opcional) Teste a validação do servidor:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="7aa2b-163">Desabilite o JavaScript no navegador.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="7aa2b-164">O JavaScript pode ser desabilitado usando as ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="7aa2b-165">Se o JavaScript não puder ser desabilitado no navegador, tente outro navegador.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="7aa2b-166">Defina um ponto de interrupção no `OnPostAsync` método da :::no-loc(Create)::: página ou edição.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-166">Set a break point in the `OnPostAsync` method of the :::no-loc(Create)::: or Edit page.</span></span>
1. <span data-ttu-id="7aa2b-167">Envie um formulário com dados inválidos.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="7aa2b-168">Verifique se o estado do modelo é inválido:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="7aa2b-169">Como alternativa, [desabilite a validação do lado do cliente no servidor](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="7aa2b-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="7aa2b-170">O código a seguir mostra uma parte da página *:::no-loc(Create)::: . cshtml* com Scaffold anteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-170">The following code shows a portion of the *:::no-loc(Create):::.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="7aa2b-171">Ele é usado pelas :::no-loc(Create)::: páginas e editar para:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-171">It's used by the :::no-loc(Create)::: and Edit pages to:</span></span>

* <span data-ttu-id="7aa2b-172">Exibir o formulário inicial.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-172">Display the initial form.</span></span>
* <span data-ttu-id="7aa2b-173">Reexiba o formulário no caso de um erro.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/:::no-loc(Create):::.cshtml?range=14-20)]

<span data-ttu-id="7aa2b-174">O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) usa os atributos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para a Validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="7aa2b-175">O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) exibe erros de validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="7aa2b-176">Consulte [Validação](xref:mvc/models/validation) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="7aa2b-177">As :::no-loc(Create)::: páginas e editar não têm nenhuma regra de validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-177">The :::no-loc(Create)::: and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="7aa2b-178">As regras de validação e as cadeias de caracteres de erro são especificadas somente na classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="7aa2b-179">Essas regras de validação são aplicadas automaticamente a :::no-loc(Razor)::: páginas que editam o `Movie` modelo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-179">These validation rules are automatically applied to :::no-loc(Razor)::: Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="7aa2b-180">Quando a lógica de validação precisa ser alterada, ela é feita apenas no modelo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="7aa2b-181">A validação é aplicada consistentemente em todo o aplicativo, a lógica de validação é definida em um único local.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="7aa2b-182">A validação em um único lugar ajuda a manter o código limpo e facilita sua manutenção e atualização.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="7aa2b-183">Usar atributos de tipo de dados</span><span class="sxs-lookup"><span data-stu-id="7aa2b-183">Use DataType Attributes</span></span>

<span data-ttu-id="7aa2b-184">Examine a classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-184">Examine the `Movie` class.</span></span> <span data-ttu-id="7aa2b-185">O namespace `System.ComponentModel.DataAnnotations` fornece atributos de formatação, além do conjunto interno de atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="7aa2b-186">O atributo `[DataType]` é aplicado às propriedades `ReleaseDate` e `Price`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="7aa2b-187">Os `[DataType]` atributos fornecem:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="7aa2b-188">Dicas para que o mecanismo de exibição formate os dados.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="7aa2b-189">Fornece atributos como `<a>` para URL e `<a href="mailto:EmailAddress.com">` para email.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="7aa2b-190">Use o atributo `[RegularExpression]` para validar o formato dos dados.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="7aa2b-191">O atributo `[DataType]` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="7aa2b-192">`[DataType]` atributos não são atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="7aa2b-193">No aplicativo de exemplo, apenas a data é exibida, sem a hora.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="7aa2b-194">A `DataType` enumeração fornece muitos tipos de dados, como `Date` ,,, `Time` `PhoneNumber` `Currency` , `EmailAddress` e muito mais.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="7aa2b-195">Os `[DataType]` atributos:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="7aa2b-196">Pode permitir que o aplicativo forneça automaticamente recursos específicos de tipo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="7aa2b-197">Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="7aa2b-198">Pode fornecer um seletor `DataType.Date` de data em navegadores que dão suporte a HTML5.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="7aa2b-199">Emitir HTML 5 `data-` , pronunciado "Dash de dados", atributos que o HTML 5 navegadores consomem.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="7aa2b-200">**Não** forneça nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="7aa2b-201">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="7aa2b-202">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas `CultureInfo` do servidor.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="7aa2b-203">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="7aa2b-204">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="7aa2b-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="7aa2b-205">O atributo `[DisplayFormat]` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="7aa2b-206">A `ApplyFormatInEditMode` configuração especifica que a formatação será aplicada quando o valor for exibido para edição.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="7aa2b-207">Esse comportamento pode não ser desejado para alguns campos.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="7aa2b-208">Por exemplo, em valores de moeda, o símbolo de moeda normalmente não é desejado na interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="7aa2b-209">O atributo `[DisplayFormat]` pode ser usado por si só, mas geralmente é uma boa ideia usar o atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="7aa2b-210">O atributo `[DataType]` transmite a semântica dos dados em vez de como renderizá-los em uma tela.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="7aa2b-211">O `[DataType]` atributo fornece os seguintes benefícios que não estão disponíveis com `[DisplayFormat]` :</span><span class="sxs-lookup"><span data-stu-id="7aa2b-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="7aa2b-212">O navegador pode habilitar recursos do HTML5, por exemplo, para mostrar um controle de calendário, o símbolo de moeda apropriado da localidade, links de email, etc.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="7aa2b-213">Por padrão, o navegador renderiza dados usando o formato correto com base em sua localidade.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="7aa2b-214">O atributo `[DataType]` pode permitir que a estrutura ASP.NET Core escolha o modelo de campo correto para renderizar os dados.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="7aa2b-215">O `DisplayFormat` , se usado por si só, usa o modelo de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="7aa2b-216">**Observação:** a validação do jQuery não funciona com o `[Range]` atributo e `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7aa2b-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="7aa2b-217">Por exemplo, o seguinte código sempre exibirá um erro de validação do lado do cliente, mesmo quando a data estiver no intervalo especificado:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="7aa2b-218">É uma prática recomendada evitar a compilação de datas rígidas em modelos, portanto, usando o `[Range]` atributo e `DateTime` não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="7aa2b-219">Use a [configuração](xref:fundamentals/configuration/index) para intervalos de datas e outros valores sujeitos a alterações frequentes em vez de especificá-los no código.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="7aa2b-220">O seguinte código mostra como combinar atributos em uma linha:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="7aa2b-221">[Introdução ao :::no-loc(Razor)::: As páginas e EF Core](xref:data/ef-rp/intro) mostram as operações de EF Core avançadas com :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-221">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with :::no-loc(Razor)::: Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="7aa2b-222">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="7aa2b-222">Apply migrations</span></span>

<span data-ttu-id="7aa2b-223">As anotações de aplicadas à classe alteram o esquema.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="7aa2b-224">Por exemplo, as DataAnnotations aplicadas ao campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="7aa2b-225">Limitam os caracteres a 60.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="7aa2b-226">Não permitem um valor `null`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7aa2b-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7aa2b-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7aa2b-228">Atualmente a tabela `Movie` tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="7aa2b-229">As alterações do esquema anterior não fazem com que o EF lance uma exceção.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="7aa2b-230">No entanto, crie uma migração de forma que o esquema seja consistente com o modelo.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="7aa2b-231"> No menu **Ferramentas** , selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="7aa2b-232">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="7aa2b-233">`Update-Database` executa os métodos `Up` da classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="7aa2b-234">Examine o método `Up`:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="7aa2b-235">A tabela `Movie` atualizada tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="7aa2b-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7aa2b-236">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7aa2b-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7aa2b-237">As migrações não são necessárias para o SQLite.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="7aa2b-238">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="7aa2b-238">Publish to Azure</span></span>

<span data-ttu-id="7aa2b-239">Para obter informações sobre como implantar no Azure, consulte [tutorial: criar um aplicativo ASP.NET Core no Azure com o banco de dados SQL](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="7aa2b-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="7aa2b-240">Obrigado por concluir esta introdução às :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-240">Thanks for completing this introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="7aa2b-241">[Introdução ao :::no-loc(Razor)::: As páginas e EF Core](xref:data/ef-rp/intro) são um excelente acompanhamento deste tutorial.</span><span class="sxs-lookup"><span data-stu-id="7aa2b-241">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7aa2b-242">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7aa2b-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="7aa2b-243">Anterior: adicionar um novo campo</span><span class="sxs-lookup"><span data-stu-id="7aa2b-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
