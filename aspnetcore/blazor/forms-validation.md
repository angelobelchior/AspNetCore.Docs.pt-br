---
title: ASP.NET Core Blazor formulários e validação
author: guardrex
description: Saiba como usar formatos e cenários de validação de campo no Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/18/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 6fde5800a6a791c4a5923c13964c34977a59c017
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865304"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="19d51-103">ASP.NET Core Blazor formulários e validação</span><span class="sxs-lookup"><span data-stu-id="19d51-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="19d51-104">Por [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="19d51-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="19d51-105">Há suporte para formulários e validação no Blazor uso de [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="19d51-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="19d51-106">O tipo a seguir `ExampleModel` define a lógica de validação usando as anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="19d51-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="19d51-107">Um formulário é definido usando o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="19d51-108">O formulário a seguir demonstra elementos, componentes e código típicos Razor :</span><span class="sxs-lookup"><span data-stu-id="19d51-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="19d51-109">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="19d51-109">In the preceding example:</span></span>

* <span data-ttu-id="19d51-110">O formulário valida a entrada do usuário no `name` campo usando a validação definida no `ExampleModel` tipo.</span><span class="sxs-lookup"><span data-stu-id="19d51-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="19d51-111">O modelo é criado no bloco do componente `@code` e mantido em um campo privado ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="19d51-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="19d51-112">O campo é atribuído ao `Model` atributo do `<EditForm>` elemento.</span><span class="sxs-lookup"><span data-stu-id="19d51-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="19d51-113">As <xref:Microsoft.AspNetCore.Components.Forms.InputText> ligações do componente `@bind-Value` :</span><span class="sxs-lookup"><span data-stu-id="19d51-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="19d51-114">A propriedade do modelo ( `exampleModel.Name` ) para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `Value` .</span><span class="sxs-lookup"><span data-stu-id="19d51-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="19d51-115">Para obter mais informações sobre associação de propriedades, consulte <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="19d51-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="19d51-116">Um delegado de evento de alteração para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="19d51-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="19d51-117">O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [componente do validador](#validator-components) anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="19d51-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="19d51-118">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="19d51-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="19d51-119">`HandleValidSubmit` é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="19d51-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="19d51-120">Componentes de formulários internos</span><span class="sxs-lookup"><span data-stu-id="19d51-120">Built-in forms components</span></span>

<span data-ttu-id="19d51-121">Um conjunto de componentes internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="19d51-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="19d51-122">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="19d51-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="19d51-123">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="19d51-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="19d51-124">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="19d51-124">Input component</span></span> | <span data-ttu-id="19d51-125">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="19d51-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="19d51-126">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="19d51-126">Input component</span></span> | <span data-ttu-id="19d51-127">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="19d51-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

<span data-ttu-id="19d51-128">Todos os componentes de entrada, incluindo <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="19d51-128">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="19d51-129">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="19d51-129">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="19d51-130">Os componentes de entrada fornecem o comportamento padrão para validar quando um campo é alterado, incluindo a atualização da classe CSS de campo para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-130">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="19d51-131">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="19d51-131">Some components include useful parsing logic.</span></span> <span data-ttu-id="19d51-132">Por exemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> e <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> manipule valores não analisáveis normalmente registrando valores não analisáveis como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="19d51-132">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="19d51-133">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="19d51-133">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="19d51-134">O tipo a seguir `Starship` define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que as anteriores `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="19d51-134">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="19d51-135">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="19d51-135">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="19d51-136">O formulário a seguir valida a entrada do usuário usando a validação definida no `Starship` modelo:</span><span class="sxs-lookup"><span data-stu-id="19d51-136">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="19d51-137">O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> cria um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como um [valor em cascata](xref:blazor/components/cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="19d51-137">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="19d51-138">Atribua **um** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **ou** um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> a um <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="19d51-138">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="19d51-139">A atribuição de ambos não tem suporte e gera um **erro de tempo de execução**.</span><span class="sxs-lookup"><span data-stu-id="19d51-139">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="19d51-140">O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> fornece eventos convenientes para envio de formulário válido e inválido:</span><span class="sxs-lookup"><span data-stu-id="19d51-140">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="19d51-141">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para usar o código personalizado para disparar a validação e verificar valores de campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-141">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="19d51-142">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="19d51-142">In the following example:</span></span>

* <span data-ttu-id="19d51-143">O `HandleSubmit` método é executado quando o **`Submit`** botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="19d51-143">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="19d51-144">O formulário é validado chamando <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="19d51-144">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="19d51-145">O código adicional é executado dependendo do resultado da validação.</span><span class="sxs-lookup"><span data-stu-id="19d51-145">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="19d51-146">Coloque a lógica de negócios no método atribuído a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .</span><span class="sxs-lookup"><span data-stu-id="19d51-146">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="19d51-147">A API do Framework não existe para limpar as mensagens de validação diretamente de um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="19d51-147">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="19d51-148">Portanto, geralmente não recomendamos adicionar mensagens de validação a um novo <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> em um formulário.</span><span class="sxs-lookup"><span data-stu-id="19d51-148">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="19d51-149">Para gerenciar mensagens de validação, use um [componente de validador](#validator-components) com seu [código de validação de lógica de negócios](#business-logic-validation), conforme descrito neste artigo.</span><span class="sxs-lookup"><span data-stu-id="19d51-149">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="19d51-150">Suporte ao nome de exibição</span><span class="sxs-lookup"><span data-stu-id="19d51-150">Display name support</span></span>

<span data-ttu-id="19d51-151">*Esta seção se aplica ao .NET 5 Release Candidate 1 (RC1) ou posterior, que será lançado em meados de setembro.*</span><span class="sxs-lookup"><span data-stu-id="19d51-151">*This section applies to .NET 5 Release Candidate 1 (RC1) or later, which will be released in mid-September.*</span></span>

<span data-ttu-id="19d51-152">Os seguintes componentes internos dão suporte à exibição de nomes com o `DisplayName` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="19d51-152">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="19d51-153">No exemplo de `InputDate` componente a seguir:</span><span class="sxs-lookup"><span data-stu-id="19d51-153">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="19d51-154">O nome de exibição ( `DisplayName` ) é definido como `birthday` .</span><span class="sxs-lookup"><span data-stu-id="19d51-154">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="19d51-155">O componente está associado à `BirthDate` propriedade como um `DateTime` tipo.</span><span class="sxs-lookup"><span data-stu-id="19d51-155">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="19d51-156">Se o usuário não fornecer um valor de data, o erro de validação será exibido como:</span><span class="sxs-lookup"><span data-stu-id="19d51-156">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="19d51-157">Componentes do validador</span><span class="sxs-lookup"><span data-stu-id="19d51-157">Validator components</span></span>

<span data-ttu-id="19d51-158">Os componentes do validador dão suporte à validação de formulário gerenciando um <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> para um formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="19d51-158">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="19d51-159">A Blazor estrutura fornece o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente para anexar suporte de validação a formulários baseados em [atributos de validação (anotações de dados)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="19d51-159">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="19d51-160">Crie componentes personalizados do validador para processar mensagens de validação para diferentes formulários na mesma página ou o mesmo formulário em etapas diferentes de processamento de formulário, por exemplo, validação do lado do cliente seguida pela validação do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="19d51-160">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="19d51-161">O exemplo de componente do validador mostrado nesta seção, `CustomValidator` , é usado nas seguintes seções deste artigo:</span><span class="sxs-lookup"><span data-stu-id="19d51-161">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="19d51-162">Validação da lógica de negócios</span><span class="sxs-lookup"><span data-stu-id="19d51-162">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="19d51-163">Validação do servidor</span><span class="sxs-lookup"><span data-stu-id="19d51-163">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="19d51-164">Os atributos personalizados de validação de anotação de dados podem ser usados em vez de componentes personalizados do validador em muitos casos.</span><span class="sxs-lookup"><span data-stu-id="19d51-164">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="19d51-165">Os atributos personalizados aplicados ao modelo do formulário são ativados com o uso do <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-165">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="19d51-166">Quando usado com a validação do lado do servidor, todos os atributos personalizados aplicados ao modelo devem ser executáveis no servidor.</span><span class="sxs-lookup"><span data-stu-id="19d51-166">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="19d51-167">Para obter mais informações, consulte <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="19d51-167">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="19d51-168">Criar um componente de validador de <xref:Microsoft.AspNetCore.Components.ComponentBase> :</span><span class="sxs-lookup"><span data-stu-id="19d51-168">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="19d51-169">O formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> é um [parâmetro em cascata](xref:blazor/components/cascading-values-and-parameters) do componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-169">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="19d51-170">Quando o componente do validador é inicializado, um novo <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> é criado para manter uma lista atual de erros de formulário.</span><span class="sxs-lookup"><span data-stu-id="19d51-170">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="19d51-171">O repositório de mensagens recebe erros quando o código do desenvolvedor no componente do formulário chama o `DisplayErrors` método.</span><span class="sxs-lookup"><span data-stu-id="19d51-171">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="19d51-172">Os erros são passados para o `DisplayErrors` método em um [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) .</span><span class="sxs-lookup"><span data-stu-id="19d51-172">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="19d51-173">No dicionário, a chave é o nome do campo de formulário que tem um ou mais erros.</span><span class="sxs-lookup"><span data-stu-id="19d51-173">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="19d51-174">O valor é a lista de erros.</span><span class="sxs-lookup"><span data-stu-id="19d51-174">The value is the error list.</span></span>
* <span data-ttu-id="19d51-175">As mensagens são limpas quando ocorre um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="19d51-175">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="19d51-176">A validação é solicitada em <xref:Microsoft.AspNetCore.Components.Forms.EditContext> quando o <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> evento é gerado.</span><span class="sxs-lookup"><span data-stu-id="19d51-176">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="19d51-177">Todos os erros são apagados.</span><span class="sxs-lookup"><span data-stu-id="19d51-177">All of the errors are cleared.</span></span>
  * <span data-ttu-id="19d51-178">Um campo é alterado no formulário quando o <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> evento é gerado.</span><span class="sxs-lookup"><span data-stu-id="19d51-178">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="19d51-179">Somente os erros do campo são apagados.</span><span class="sxs-lookup"><span data-stu-id="19d51-179">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="19d51-180">O `ClearErrors` método é chamado pelo código do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="19d51-180">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="19d51-181">Todos os erros são apagados.</span><span class="sxs-lookup"><span data-stu-id="19d51-181">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
    public class CustomValidator : ComponentBase
    {
        private ValidationMessageStore messageStore;

        [CascadingParameter]
        private EditContext CurrentEditContext { get; set; }

        protected override void OnInitialized()
        {
            if (CurrentEditContext == null)
            {
                throw new InvalidOperationException(
                    $"{nameof(CustomValidator)} requires a cascading " +
                    $"parameter of type {nameof(EditContext)}. " +
                    $"For example, you can use {nameof(CustomValidator)} " +
                    $"inside an {nameof(EditForm)}.");
            }

            messageStore = new ValidationMessageStore(CurrentEditContext);

            CurrentEditContext.OnValidationRequested += (s, e) => 
                messageStore.Clear();
            CurrentEditContext.OnFieldChanged += (s, e) => 
                messageStore.Clear(e.FieldIdentifier);
        }

        public void DisplayErrors(Dictionary<string, List<string>> errors)
        {
            foreach (var err in errors)
            {
                messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
            }

            CurrentEditContext.NotifyValidationStateChanged();
        }

        public void ClearErrors()
        {
            messageStore.Clear();
            CurrentEditContext.NotifyValidationStateChanged();
        }
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="19d51-182">Validação da lógica de negócios</span><span class="sxs-lookup"><span data-stu-id="19d51-182">Business logic validation</span></span>

<span data-ttu-id="19d51-183">A validação da lógica de negócios pode ser realizada com um [componente de validador](#validator-components) que recebe erros de formulário em um dicionário.</span><span class="sxs-lookup"><span data-stu-id="19d51-183">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="19d51-184">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="19d51-184">In the following example:</span></span>

* <span data-ttu-id="19d51-185">O `CustomValidator` componente da seção [componentes do validador](#validator-components) deste artigo é usado.</span><span class="sxs-lookup"><span data-stu-id="19d51-185">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="19d51-186">A validação requer um valor para a descrição do envio ( `Description` ) se o usuário selecionar a `Defense` classificação de remessa ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="19d51-186">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="19d51-187">Quando as mensagens de validação são definidas no componente, elas são adicionadas ao validador <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> e mostradas no <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :</span><span class="sxs-lookup"><span data-stu-id="19d51-187">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```csharp
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="19d51-188">Como alternativa ao uso de [componentes de validação](#validator-components), os atributos de validação de anotação de dados podem ser usados.</span><span class="sxs-lookup"><span data-stu-id="19d51-188">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="19d51-189">Os atributos personalizados aplicados ao modelo do formulário são ativados com o uso do <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-189">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="19d51-190">Quando usado com a validação do lado do servidor, os atributos devem ser executáveis no servidor.</span><span class="sxs-lookup"><span data-stu-id="19d51-190">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="19d51-191">Para obter mais informações, consulte <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="19d51-191">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="19d51-192">Validação do servidor</span><span class="sxs-lookup"><span data-stu-id="19d51-192">Server validation</span></span>

<span data-ttu-id="19d51-193">A validação do servidor pode ser realizada com um [componente de validador](#validator-components)de servidor:</span><span class="sxs-lookup"><span data-stu-id="19d51-193">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="19d51-194">Processe a validação do lado do cliente no formulário com o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-194">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="19d51-195">Quando o formulário passar na validação do lado do cliente ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> é chamado), envie o <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> para uma API de servidor de back-end para processamento de formulário.</span><span class="sxs-lookup"><span data-stu-id="19d51-195">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="19d51-196">Validação do modelo de processo no servidor.</span><span class="sxs-lookup"><span data-stu-id="19d51-196">Process model validation on the server.</span></span>
* <span data-ttu-id="19d51-197">A API do servidor inclui a validação interna das anotações de dados da estrutura e a lógica de validação personalizada fornecida pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="19d51-197">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="19d51-198">Se a validação passar no servidor, processe o formulário e envie de volta um código de status de êxito (*200-OK*).</span><span class="sxs-lookup"><span data-stu-id="19d51-198">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="19d51-199">Se a validação falhar, retorne um código de status de falha (*400-solicitação inadequada*) e os erros de validação de campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-199">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="19d51-200">Desabilite o formulário em caso de êxito ou exiba os erros.</span><span class="sxs-lookup"><span data-stu-id="19d51-200">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="19d51-201">O exemplo a seguir se baseia em:</span><span class="sxs-lookup"><span data-stu-id="19d51-201">The following example is based on:</span></span>

* <span data-ttu-id="19d51-202">Uma solução hospedada Blazor criada pelo [ Blazor modelo de projeto hospedado](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="19d51-202">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="19d51-203">O exemplo pode ser usado com qualquer uma das soluções hospedadas seguras Blazor descritas na [ Identity documentação e segurança](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="19d51-203">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="19d51-204">O exemplo de formulário de *banco de dados Starfleet Starship* na seção [componentes de formulários internos](#built-in-forms-components) anteriores.</span><span class="sxs-lookup"><span data-stu-id="19d51-204">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="19d51-205">O Blazor componente da estrutura <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="19d51-205">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="19d51-206">O `CustomValidator` componente mostrado na seção [componentes do validador](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="19d51-206">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="19d51-207">No exemplo a seguir, a API do servidor valida que um valor é fornecido para a descrição do navio ( `Description` ) se o usuário selecionar a `Defense` classificação de remessa ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="19d51-207">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="19d51-208">Coloque o `Starship` modelo no projeto da solução `Shared` para que os aplicativos cliente e servidor possam usar o modelo.</span><span class="sxs-lookup"><span data-stu-id="19d51-208">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="19d51-209">Como o modelo requer anotações de dados, adicione uma referência de pacote para [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) o `Shared` arquivo de projeto do projeto:</span><span class="sxs-lookup"><span data-stu-id="19d51-209">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="19d51-210">Para determinar a versão mais recente de não visualização do pacote, consulte o **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="19d51-210">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="19d51-211">No projeto de API do servidor, adicione um controlador para processar solicitações de validação Starship ( `Controllers/StarshipValidation.cs` ) e retorne as mensagens de validação com falha:</span><span class="sxs-lookup"><span data-stu-id="19d51-211">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {MESSAGE}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="19d51-212">Quando ocorre um erro de validação de associação de modelo no servidor, um [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) normalmente retorna uma [resposta de solicitação inadequada padrão](xref:web-api/index#default-badrequest-response) com um <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="19d51-212">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="19d51-213">A resposta contém mais dados do que apenas os erros de validação, conforme mostrado no exemplo a seguir quando todos os campos do formulário de banco de dados *Starfleet Starship* não são enviados e o formulário falha na validação:</span><span class="sxs-lookup"><span data-stu-id="19d51-213">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="19d51-214">Se a API do servidor retornar a resposta JSON padrão anterior, é possível para o cliente analisar a resposta para obter os filhos do `errors` nó.</span><span class="sxs-lookup"><span data-stu-id="19d51-214">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="19d51-215">No entanto, é inconveniente analisar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="19d51-215">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="19d51-216">A análise do JSON requer código adicional após <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> a chamada para produzir um [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) erro de processamento de erros de validação de formulários.</span><span class="sxs-lookup"><span data-stu-id="19d51-216">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="19d51-217">O ideal é que a API do servidor retorne apenas os erros de validação:</span><span class="sxs-lookup"><span data-stu-id="19d51-217">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="19d51-218">Para modificar a resposta da API do servidor para fazer com que ela retorne apenas os erros de validação, altere o delegado que é invocado em ações que são anotadas <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="19d51-218">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="19d51-219">Para o ponto de extremidade da API ( `/StarshipValidation` ), retorne um <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> com o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> .</span><span class="sxs-lookup"><span data-stu-id="19d51-219">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="19d51-220">Para qualquer outro ponto de extremidade de API, preserve o comportamento padrão retornando o resultado do objeto com um novo <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="19d51-220">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="19d51-221">Para obter mais informações, consulte <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="19d51-221">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="19d51-222">No projeto do cliente, adicione o componente do validador mostrado na seção [componentes do validador](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="19d51-222">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="19d51-223">No projeto cliente, o formulário de *banco de dados Starfleet Starship* é atualizado para mostrar erros de validação de servidor com a ajuda do `CustomValidator` componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-223">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="19d51-224">Quando a API do servidor retorna mensagens de validação, elas são adicionadas ao `CustomValidator` componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="19d51-224">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="19d51-225">Os erros estão disponíveis no formato da <xref:Microsoft.AspNetCore.Components.Forms.EditContext> exibição do formulário <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :</span><span class="sxs-lookup"><span data-stu-id="19d51-225">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```csharp
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {MESSAGE}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="19d51-226">Como alternativa aos [componentes de validação](#validator-components), os atributos de validação de anotação de dados podem ser usados.</span><span class="sxs-lookup"><span data-stu-id="19d51-226">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="19d51-227">Os atributos personalizados aplicados ao modelo do formulário são ativados com o uso do <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-227">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="19d51-228">Quando usado com a validação do lado do servidor, os atributos devem ser executáveis no servidor.</span><span class="sxs-lookup"><span data-stu-id="19d51-228">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="19d51-229">Para obter mais informações, consulte <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="19d51-229">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="19d51-230">A abordagem de validação no lado do servidor nesta seção é adequada para qualquer um dos Blazor WebAssembly exemplos de solução hospedada neste conjunto de documentação:</span><span class="sxs-lookup"><span data-stu-id="19d51-230">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="19d51-231">AAD (Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="19d51-231">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="19d51-232">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="19d51-232">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="19d51-233">Identity Servidor</span><span class="sxs-lookup"><span data-stu-id="19d51-233">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="19d51-234">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="19d51-234">InputText based on the input event</span></span>

<span data-ttu-id="19d51-235">Use o <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente para criar um componente personalizado que usa o `input` evento em vez do `change` evento.</span><span class="sxs-lookup"><span data-stu-id="19d51-235">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="19d51-236">No exemplo a seguir, o `CustomInputText` componente herda o componente da estrutura `InputText` e define a associação de evento ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) para o `oninput` evento.</span><span class="sxs-lookup"><span data-stu-id="19d51-236">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="19d51-237">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="19d51-237">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="19d51-238">O `CustomInputText` componente pode ser usado em qualquer lugar que <xref:Microsoft.AspNetCore.Components.Forms.InputText> for usado:</span><span class="sxs-lookup"><span data-stu-id="19d51-238">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="19d51-239">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="19d51-239">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="19d51-240">Botões de opção</span><span class="sxs-lookup"><span data-stu-id="19d51-240">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="19d51-241">Use `InputRadio` componentes com o `InputRadioGroup` componente para criar um grupo de botões de opção.</span><span class="sxs-lookup"><span data-stu-id="19d51-241">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="19d51-242">No exemplo a seguir, as propriedades são adicionadas ao `Starship` modelo descrito na seção [componentes de formulários internos](#built-in-forms-components) :</span><span class="sxs-lookup"><span data-stu-id="19d51-242">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="19d51-243">Adicione o seguinte `enums` ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="19d51-243">Add the following `enums` to the app.</span></span> <span data-ttu-id="19d51-244">Crie um novo arquivo para armazenar o `enums` ou adicione o `enums` ao `Starship.cs` arquivo.</span><span class="sxs-lookup"><span data-stu-id="19d51-244">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="19d51-245">Torne o `enums` acessível ao `Starship` modelo e o formulário de *banco de dados Starfleet Starship* :</span><span class="sxs-lookup"><span data-stu-id="19d51-245">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="19d51-246">Atualize o formulário de *banco de dados Starfleet Starship* descrito na seção [componentes de formulários internos](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="19d51-246">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="19d51-247">Adicione os componentes a serem produzidos:</span><span class="sxs-lookup"><span data-stu-id="19d51-247">Add the components to produce:</span></span>

* <span data-ttu-id="19d51-248">Um grupo de botões de opção para o fabricante do envio.</span><span class="sxs-lookup"><span data-stu-id="19d51-248">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="19d51-249">Um grupo de botões de opção aninhados para a cor e o mecanismo de remessa.</span><span class="sxs-lookup"><span data-stu-id="19d51-249">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="19d51-250">Se `Name` for omitido, `InputRadio` os componentes serão agrupados por seu ancestral mais recente.</span><span class="sxs-lookup"><span data-stu-id="19d51-250">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="19d51-251">Ao trabalhar com botões de opção em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de opção são avaliados como um grupo.</span><span class="sxs-lookup"><span data-stu-id="19d51-251">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="19d51-252">O valor de cada botão de opção é fixo, mas o valor do grupo de botões de opção é o valor do botão de opção selecionado.</span><span class="sxs-lookup"><span data-stu-id="19d51-252">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="19d51-253">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="19d51-253">The following example shows how to:</span></span>

* <span data-ttu-id="19d51-254">Manipular a vinculação de dados para um grupo de botões de opção.</span><span class="sxs-lookup"><span data-stu-id="19d51-254">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="19d51-255">Suporte à validação usando um `InputRadio` componente personalizado.</span><span class="sxs-lookup"><span data-stu-id="19d51-255">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="19d51-256">O seguinte <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa o `InputRadio` componente anterior para obter e validar uma classificação do usuário:</span><span class="sxs-lookup"><span data-stu-id="19d51-256">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="19d51-257">Associando `<select>` Opções de elemento a valores de objeto C# `null`</span><span class="sxs-lookup"><span data-stu-id="19d51-257">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="19d51-258">Não há uma maneira sensata de representar um `<select>` valor de opção de elemento como um valor de objeto C# `null` , porque:</span><span class="sxs-lookup"><span data-stu-id="19d51-258">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="19d51-259">Atributos HTML não podem ter `null` valores.</span><span class="sxs-lookup"><span data-stu-id="19d51-259">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="19d51-260">O equivalente mais próximo de `null` em HTML é a ausência do `value` atributo HTML do `<option>` elemento.</span><span class="sxs-lookup"><span data-stu-id="19d51-260">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="19d51-261">Ao selecionar um `<option>` sem `value` atributo, o navegador trata o valor como o *conteúdo de texto* do `<option>` elemento.</span><span class="sxs-lookup"><span data-stu-id="19d51-261">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="19d51-262">A Blazor estrutura não tenta suprimir o comportamento padrão porque ele envolveria:</span><span class="sxs-lookup"><span data-stu-id="19d51-262">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="19d51-263">Criar uma cadeia de soluções alternativas de caso especial no Framework.</span><span class="sxs-lookup"><span data-stu-id="19d51-263">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="19d51-264">Alterações recentes no comportamento da estrutura atual.</span><span class="sxs-lookup"><span data-stu-id="19d51-264">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="19d51-265">O equivalente mais plausível `null` em HTML é uma *cadeia de caracteres vazia* `value` .</span><span class="sxs-lookup"><span data-stu-id="19d51-265">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="19d51-266">A Blazor estrutura manipula `null` conversões de cadeia de caracteres vazias para a associação bidirecional a um `<select>` valor de.</span><span class="sxs-lookup"><span data-stu-id="19d51-266">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="19d51-267">A Blazor estrutura não lida automaticamente `null` com conversões de cadeia de caracteres vazias ao tentar fazer a ligação bidirecional com um `<select>` valor de.</span><span class="sxs-lookup"><span data-stu-id="19d51-267">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="19d51-268">Para obter mais informações, consulte [corrigir Associação `<select>` a um valor nulo (dotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="19d51-268">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="19d51-269">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="19d51-269">Validation support</span></span>

<span data-ttu-id="19d51-270">O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente anexa o suporte à validação usando anotações de dados para o em cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="19d51-270">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="19d51-271">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="19d51-271">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="19d51-272">Para usar um sistema de validação diferente de anotações de dados, substitua-o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="19d51-272">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="19d51-273">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="19d51-273">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="19d51-274">Os links anteriores para a fonte de referência fornecem o código do `master` Branch do repositório, que representa o desenvolvimento atual da unidade do produto para a próxima versão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="19d51-274">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="19d51-275">Para selecionar a ramificação de uma versão diferente, use o seletor de ramificações do GitHub (por exemplo, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="19d51-275">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="19d51-276">Blazor executa dois tipos de validação:</span><span class="sxs-lookup"><span data-stu-id="19d51-276">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="19d51-277">A *validação de campo* é executada quando o usuário faz a Tabulação de um campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-277">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="19d51-278">Durante a validação de campo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa todos os resultados de validação relatados ao campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-278">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="19d51-279">A *validação do modelo* é executada quando o usuário envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="19d51-279">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="19d51-280">Durante a validação do modelo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta determinar o campo com base no nome do membro que o resultado da validação relata.</span><span class="sxs-lookup"><span data-stu-id="19d51-280">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="19d51-281">Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-281">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="19d51-282">Resumo de validação e componentes de mensagem de validação</span><span class="sxs-lookup"><span data-stu-id="19d51-282">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="19d51-283">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="19d51-283">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="19d51-284">Mensagens de validação de saída para um modelo específico com o `Model` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="19d51-284">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="19d51-285">O <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="19d51-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="19d51-286">Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="19d51-286">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="19d51-287">Os <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componentes e oferecem suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="19d51-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="19d51-288">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento gerado ou `<ul>` .</span><span class="sxs-lookup"><span data-stu-id="19d51-288">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="19d51-289">Controle o estilo das mensagens de validação na folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="19d51-289">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="19d51-290">A `validation-message` classe padrão define a cor do texto das mensagens de validação como vermelho:</span><span class="sxs-lookup"><span data-stu-id="19d51-290">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="19d51-291">Atributos de validação personalizados</span><span class="sxs-lookup"><span data-stu-id="19d51-291">Custom validation attributes</span></span>

<span data-ttu-id="19d51-292">Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o contexto de validação <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="19d51-292">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="19d51-293"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> é `null`.</span><span class="sxs-lookup"><span data-stu-id="19d51-293"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="19d51-294">Não há suporte para injetar serviços para validação no `IsValid` método.</span><span class="sxs-lookup"><span data-stu-id="19d51-294">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="19d51-295">Blazor pacote de validação de anotações de dados</span><span class="sxs-lookup"><span data-stu-id="19d51-295">Blazor data annotations validation package</span></span>

<span data-ttu-id="19d51-296">O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.</span><span class="sxs-lookup"><span data-stu-id="19d51-296">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="19d51-297">O pacote está *experimental*no momento.</span><span class="sxs-lookup"><span data-stu-id="19d51-297">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="19d51-298">O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) pacote tem uma versão mais recente do *release candidate* em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue a usar o pacote do Release Candidate *experimental* no momento.</span><span class="sxs-lookup"><span data-stu-id="19d51-298">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="19d51-299">O assembly do pacote pode ser movido para a estrutura ou o tempo de execução em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="19d51-299">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="19d51-300">Assista ao [repositório GitHub de anúncios](https://github.com/aspnet/Announcements), no [repositório GitHub do dotnet/aspnetcore](https://github.com/dotnet/aspnetcore)ou nesta seção do tópico para obter mais atualizações.</span><span class="sxs-lookup"><span data-stu-id="19d51-300">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="19d51-301">Atributo [compareproperty]</span><span class="sxs-lookup"><span data-stu-id="19d51-301">[CompareProperty] attribute</span></span>

<span data-ttu-id="19d51-302">O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente porque não associa o resultado da validação a um membro específico.</span><span class="sxs-lookup"><span data-stu-id="19d51-302">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="19d51-303">Isso pode resultar em comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio.</span><span class="sxs-lookup"><span data-stu-id="19d51-303">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="19d51-304">O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) pacote *experimental* introduz um atributo de validação adicional, `ComparePropertyAttribute` que funciona em relação a essas limitações.</span><span class="sxs-lookup"><span data-stu-id="19d51-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="19d51-305">Em um Blazor aplicativo, `[CompareProperty]` é uma substituição direta para o [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="19d51-305">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="19d51-306">Modelos aninhados, tipos de coleção e tipos complexos</span><span class="sxs-lookup"><span data-stu-id="19d51-306">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="19d51-307">Blazor fornece suporte para validar a entrada de formulário usando anotações de dados com o interno <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="19d51-307">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="19d51-308">No entanto, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.</span><span class="sxs-lookup"><span data-stu-id="19d51-308">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="19d51-309">Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo pacote *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="19d51-309">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="19d51-310">Anote as propriedades do modelo com `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="19d51-310">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="19d51-311">Nas classes de modelo a seguir, a `ShipDescription` classe contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:</span><span class="sxs-lookup"><span data-stu-id="19d51-311">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="19d51-312">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="19d51-312">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="19d51-313">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="19d51-313">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="19d51-314">Habilitar o botão enviar com base na validação do formulário</span><span class="sxs-lookup"><span data-stu-id="19d51-314">Enable the submit button based on form validation</span></span>

<span data-ttu-id="19d51-315">Para habilitar e desabilitar o botão enviar com base na validação do formulário:</span><span class="sxs-lookup"><span data-stu-id="19d51-315">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="19d51-316">Use o formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> para atribuir o modelo quando o componente for inicializado.</span><span class="sxs-lookup"><span data-stu-id="19d51-316">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="19d51-317">Valide o formulário no retorno de chamada do contexto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> para habilitar e desabilitar o botão enviar.</span><span class="sxs-lookup"><span data-stu-id="19d51-317">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="19d51-318">Desvincule o manipulador de eventos no `Dispose` método.</span><span class="sxs-lookup"><span data-stu-id="19d51-318">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="19d51-319">Para obter mais informações, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="19d51-319">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="19d51-320">Ao usar um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , não atribua também um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ao <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="19d51-320">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="19d51-321">No exemplo anterior, defina `formInvalid` como `false` se:</span><span class="sxs-lookup"><span data-stu-id="19d51-321">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="19d51-322">O formulário é pré-carregado com valores padrão válidos.</span><span class="sxs-lookup"><span data-stu-id="19d51-322">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="19d51-323">Você deseja que o botão enviar seja habilitado quando o formulário é carregado.</span><span class="sxs-lookup"><span data-stu-id="19d51-323">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="19d51-324">Um efeito colateral da abordagem anterior é que um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente é preenchido com campos inválidos depois que o usuário interage com um campo.</span><span class="sxs-lookup"><span data-stu-id="19d51-324">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="19d51-325">Esse cenário pode ser resolvido de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="19d51-325">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="19d51-326">Não use um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente no formulário.</span><span class="sxs-lookup"><span data-stu-id="19d51-326">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="19d51-327">Torne o <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente visível quando o botão enviar estiver selecionado (por exemplo, em um `HandleValidSubmit` método).</span><span class="sxs-lookup"><span data-stu-id="19d51-327">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="19d51-328">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="19d51-328">Troubleshoot</span></span>

> <span data-ttu-id="19d51-329">InvalidOperationException: EditForm requer um parâmetro de modelo ou um parâmetro EditContext, mas não ambos.</span><span class="sxs-lookup"><span data-stu-id="19d51-329">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="19d51-330">Confirme se o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tem um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **ou** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="19d51-330">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="19d51-331">Não use ambos para a mesma forma.</span><span class="sxs-lookup"><span data-stu-id="19d51-331">Don't use both for the same form.</span></span>

<span data-ttu-id="19d51-332">Ao atribuir um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ao formulário, confirme se o tipo de modelo é instanciado, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="19d51-332">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
