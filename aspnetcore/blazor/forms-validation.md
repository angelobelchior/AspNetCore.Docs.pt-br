---
title: ASP.NET Core Blazor formulários e validação
author: guardrex
description: Saiba como usar formatos e cenários de validação de campo no Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: fe232b40a2255732dd375cc266937576d5b2d5d9
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507818"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor formulários e validação

Por [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)e [Luke Latham](https://github.com/guardrex)

Há suporte para formulários e validação no Blazor uso de [anotações de dados](xref:mvc/models/validation).

O tipo a seguir `ExampleModel` define a lógica de validação usando as anotações de dados:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Um formulário é definido usando o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> componente. O formulário a seguir demonstra elementos, componentes e código típicos Razor :

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

No exemplo anterior:

* O formulário valida a entrada do usuário no `name` campo usando a validação definida no `ExampleModel` tipo. O modelo é criado no bloco do componente `@code` e mantido em um campo privado ( `exampleModel` ). O campo é atribuído ao `Model` atributo do `<EditForm>` elemento.
* As <xref:Microsoft.AspNetCore.Components.Forms.InputText> ligações do componente `@bind-Value` :
  * A propriedade do modelo ( `exampleModel.Name` ) para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `Value` . Para obter mais informações sobre associação de propriedades, consulte <xref:blazor/components/data-binding#binding-with-component-parameters> .
  * Um delegado de evento de alteração para a <xref:Microsoft.AspNetCore.Components.Forms.InputText> Propriedade do componente `ValueChanged` .
* O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [componente do validador](#validator-components) anexa o suporte à validação usando anotações de dados.
* O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume as mensagens de validação.
* `HandleValidSubmit` é disparado quando o formulário é enviado com êxito (aprovado na validação).

## <a name="built-in-forms-components"></a>Componentes de formulários internos

Um conjunto de componentes internos está disponível para receber e validar a entrada do usuário. As entradas são validadas quando são alteradas e quando um formulário é enviado. Os componentes de entrada disponíveis são mostrados na tabela a seguir.

::: moniker range=">= aspnetcore-5.0"

| Componente de entrada | Renderizado como&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Componente de entrada | Renderizado como&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> Os `InputRadio` `InputRadioGroup` componentes e estão disponíveis no ASP.NET Core 5,0 ou posterior. Para obter mais informações, selecione uma versão 5,0 ou posterior deste artigo.

::: moniker-end

Todos os componentes de entrada, incluindo <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , dão suporte a atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.

Os componentes de entrada fornecem o comportamento padrão para validar quando um campo é alterado, incluindo a atualização da classe CSS de campo para refletir o estado do campo. Alguns componentes incluem lógica de análise útil. Por exemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> e <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> manipule valores não analisáveis normalmente registrando valores não analisáveis como erros de validação. Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?` ).

O tipo a seguir `Starship` define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que as anteriores `ExampleModel` :

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

No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.

O formulário a seguir valida a entrada do usuário usando a validação definida no `Starship` modelo:

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

O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> cria um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como um [valor em cascata](xref:blazor/components/cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.

Atribua **um** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **ou** um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> a um <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . A atribuição de ambos não tem suporte e gera um **erro de tempo de execução**.

O <xref:Microsoft.AspNetCore.Components.Forms.EditForm> fornece eventos convenientes para envio de formulário válido e inválido:

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para usar o código personalizado para disparar a validação e verificar valores de campo.

No exemplo a seguir:

* O `HandleSubmit` método é executado quando o **`Submit`** botão é selecionado.
* O formulário é validado chamando <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .
* O código adicional é executado dependendo do resultado da validação. Coloque a lógica de negócios no método atribuído a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

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
> A API do Framework não existe para limpar as mensagens de validação diretamente de um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Portanto, geralmente não recomendamos adicionar mensagens de validação a um novo <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> em um formulário. Para gerenciar mensagens de validação, use um [componente de validador](#validator-components) com seu [código de validação de lógica de negócios](#business-logic-validation), conforme descrito neste artigo.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Suporte ao nome de exibição

*Esta seção se aplica a ASP.NET Core no .NET 5 Release Candidate 1 (RC1) ou posterior.*

Os seguintes componentes internos dão suporte à exibição de nomes com o `DisplayName` parâmetro:

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

No exemplo de `InputDate` componente a seguir:

* O nome de exibição ( `DisplayName` ) é definido como `birthday` .
* O componente está associado à `BirthDate` propriedade como um `DateTime` tipo.

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Se o usuário não fornecer um valor de data, o erro de validação será exibido como:

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Componentes do validador

Os componentes do validador dão suporte à validação de formulário gerenciando um <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> para um formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

A Blazor estrutura fornece o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente para anexar suporte de validação a formulários baseados em [atributos de validação (anotações de dados)](xref:mvc/models/validation#validation-attributes). Crie componentes personalizados do validador para processar mensagens de validação para diferentes formulários na mesma página ou o mesmo formulário em etapas diferentes de processamento de formulário, por exemplo, validação do lado do cliente seguida pela validação do lado do servidor. O exemplo de componente do validador mostrado nesta seção, `CustomValidator` , é usado nas seguintes seções deste artigo:

* [Validação da lógica de negócios](#business-logic-validation)
* [Validação do servidor](#server-validation)

> [!NOTE]
> Os atributos personalizados de validação de anotação de dados podem ser usados em vez de componentes personalizados do validador em muitos casos. Os atributos personalizados aplicados ao modelo do formulário são ativados com o uso do <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente. Quando usado com a validação do lado do servidor, todos os atributos personalizados aplicados ao modelo devem ser executáveis no servidor. Para obter mais informações, consulte <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Criar um componente de validador de <xref:Microsoft.AspNetCore.Components.ComponentBase> :

* O formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> é um [parâmetro em cascata](xref:blazor/components/cascading-values-and-parameters) do componente.
* Quando o componente do validador é inicializado, um novo <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> é criado para manter uma lista atual de erros de formulário.
* O repositório de mensagens recebe erros quando o código do desenvolvedor no componente do formulário chama o `DisplayErrors` método. Os erros são passados para o `DisplayErrors` método em um [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) . No dicionário, a chave é o nome do campo de formulário que tem um ou mais erros. O valor é a lista de erros.
* As mensagens são limpas quando ocorre um dos seguintes:
  * A validação é solicitada em <xref:Microsoft.AspNetCore.Components.Forms.EditContext> quando o <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> evento é gerado. Todos os erros são apagados.
  * Um campo é alterado no formulário quando o <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> evento é gerado. Somente os erros do campo são apagados.
  * O `ClearErrors` método é chamado pelo código do desenvolvedor. Todos os erros são apagados.

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

## <a name="business-logic-validation"></a>Validação da lógica de negócios

A validação da lógica de negócios pode ser realizada com um [componente de validador](#validator-components) que recebe erros de formulário em um dicionário.

No exemplo a seguir:

* O `CustomValidator` componente da seção [componentes do validador](#validator-components) deste artigo é usado.
* A validação requer um valor para a descrição do envio ( `Description` ) se o usuário selecionar a `Defense` classificação de remessa ( `Classification` ).

Quando as mensagens de validação são definidas no componente, elas são adicionadas ao validador <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> e mostradas no <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :

```razor
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
> Como alternativa ao uso de [componentes de validação](#validator-components), os atributos de validação de anotação de dados podem ser usados. Os atributos personalizados aplicados ao modelo do formulário são ativados com o uso do <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente. Quando usado com a validação do lado do servidor, os atributos devem ser executáveis no servidor. Para obter mais informações, consulte <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Validação do servidor

A validação do servidor pode ser realizada com um [componente de validador](#validator-components)de servidor:

* Processe a validação do lado do cliente no formulário com o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente.
* Quando o formulário passar na validação do lado do cliente ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> é chamado), envie o <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> para uma API de servidor de back-end para processamento de formulário.
* Validação do modelo de processo no servidor.
* A API do servidor inclui a validação interna das anotações de dados da estrutura e a lógica de validação personalizada fornecida pelo desenvolvedor. Se a validação passar no servidor, processe o formulário e envie de volta um código de status de êxito ( *200-OK* ). Se a validação falhar, retorne um código de status de falha ( *400-solicitação inadequada* ) e os erros de validação de campo.
* Desabilite o formulário em caso de êxito ou exiba os erros.

O exemplo a seguir se baseia em:

* Uma solução hospedada Blazor criada pelo [ Blazor modelo de projeto hospedado](xref:blazor/hosting-models#blazor-webassembly). O exemplo pode ser usado com qualquer uma das soluções hospedadas seguras Blazor descritas na [ Identity documentação e segurança](xref:blazor/security/webassembly/index#implementation-guidance).
* O exemplo de formulário de *banco de dados Starfleet Starship* na seção [componentes de formulários internos](#built-in-forms-components) anteriores.
* O Blazor componente da estrutura <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .
* O `CustomValidator` componente mostrado na seção [componentes do validador](#validator-components) .

No exemplo a seguir, a API do servidor valida que um valor é fornecido para a descrição do navio ( `Description` ) se o usuário selecionar a `Defense` classificação de remessa ( `Classification` ).

Coloque o `Starship` modelo no projeto da solução `Shared` para que os aplicativos cliente e servidor possam usar o modelo. Como o modelo requer anotações de dados, adicione uma referência de pacote para [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) o `Shared` arquivo de projeto do projeto:

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Para determinar a versão mais recente de não visualização do pacote, consulte o **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).

No projeto de API do servidor, adicione um controlador para processar solicitações de validação Starship ( `Controllers/StarshipValidation.cs` ) e retorne as mensagens de validação com falha:

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
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

Quando ocorre um erro de validação de associação de modelo no servidor, um [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) normalmente retorna uma [resposta de solicitação inadequada padrão](xref:web-api/index#default-badrequest-response) com um <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . A resposta contém mais dados do que apenas os erros de validação, conforme mostrado no exemplo a seguir quando todos os campos do formulário de banco de dados *Starfleet Starship* não são enviados e o formulário falha na validação:

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

Se a API do servidor retornar a resposta JSON padrão anterior, é possível para o cliente analisar a resposta para obter os filhos do `errors` nó. No entanto, é inconveniente analisar o arquivo. A análise do JSON requer código adicional após <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> a chamada para produzir um [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) erro de processamento de erros de validação de formulários. O ideal é que a API do servidor retorne apenas os erros de validação:

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Para modificar a resposta da API do servidor para fazer com que ela retorne apenas os erros de validação, altere o delegado que é invocado em ações que são anotadas <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> no `Startup.ConfigureServices` . Para o ponto de extremidade da API ( `/StarshipValidation` ), retorne um <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> com o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> . Para qualquer outro ponto de extremidade de API, preserve o comportamento padrão retornando o resultado do objeto com um novo <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :

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

Para obter mais informações, consulte <xref:web-api/handle-errors#validation-failure-error-response>.

No projeto do cliente, adicione o componente do validador mostrado na seção [componentes do validador](#validator-components) .

No projeto cliente, o formulário de *banco de dados Starfleet Starship* é atualizado para mostrar erros de validação de servidor com a ajuda do `CustomValidator` componente. Quando a API do servidor retorna mensagens de validação, elas são adicionadas ao `CustomValidator` componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> . Os erros estão disponíveis no formato da <xref:Microsoft.AspNetCore.Components.Forms.EditContext> exibição do formulário <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :

```razor
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
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> Como alternativa aos [componentes de validação](#validator-components), os atributos de validação de anotação de dados podem ser usados. Os atributos personalizados aplicados ao modelo do formulário são ativados com o uso do <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente. Quando usado com a validação do lado do servidor, os atributos devem ser executáveis no servidor. Para obter mais informações, consulte <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> A abordagem de validação no lado do servidor nesta seção é adequada para qualquer um dos Blazor WebAssembly exemplos de solução hospedada neste conjunto de documentação:
>
> * [AAD (Azure Active Directory)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity Servidor](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>InputText com base no evento de entrada

Use o <xref:Microsoft.AspNetCore.Components.Forms.InputText> componente para criar um componente personalizado que usa o `input` evento em vez do `change` evento.

No exemplo a seguir, o `CustomInputText` componente herda o componente da estrutura `InputText` e define a associação de evento ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) para o `oninput` evento.

`Shared/CustomInputText.razor`:

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

O `CustomInputText` componente pode ser usado em qualquer lugar que <xref:Microsoft.AspNetCore.Components.Forms.InputText> for usado:

`Pages/TestForm.razor`:

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

## <a name="radio-buttons"></a>Botões de opção

::: moniker range=">= aspnetcore-5.0"

Use `InputRadio` componentes com o `InputRadioGroup` componente para criar um grupo de botões de opção. No exemplo a seguir, as propriedades são adicionadas ao `Starship` modelo descrito na seção [componentes de formulários internos](#built-in-forms-components) :

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

Adicione o seguinte `enums` ao aplicativo. Crie um novo arquivo para armazenar o `enums` ou adicione o `enums` ao `Starship.cs` arquivo. Torne o `enums` acessível ao `Starship` modelo e o formulário de *banco de dados Starfleet Starship* :

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

Atualize o formulário de *banco de dados Starfleet Starship* descrito na seção [componentes de formulários internos](#built-in-forms-components) . Adicione os componentes a serem produzidos:

* Um grupo de botões de opção para o fabricante do envio.
* Um grupo de botões de opção aninhados para a cor e o mecanismo de remessa.

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
> Se `Name` for omitido, `InputRadio` os componentes serão agrupados por seu ancestral mais recente.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Ao trabalhar com botões de opção em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de opção são avaliados como um grupo. O valor de cada botão de opção é fixo, mas o valor do grupo de botões de opção é o valor do botão de opção selecionado. O exemplo a seguir mostra como:

* Manipular a vinculação de dados para um grupo de botões de opção.
* Suporte à validação usando um `InputRadio` componente personalizado.

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

O seguinte <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa o `InputRadio` componente anterior para obter e validar uma classificação do usuário:

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

## <a name="binding-select-element-options-to-c-object-null-values"></a>Associando `<select>` Opções de elemento a valores de objeto C# `null`

Não há uma maneira sensata de representar um `<select>` valor de opção de elemento como um valor de objeto C# `null` , porque:

* Atributos HTML não podem ter `null` valores. O equivalente mais próximo de `null` em HTML é a ausência do `value` atributo HTML do `<option>` elemento.
* Ao selecionar um `<option>` sem `value` atributo, o navegador trata o valor como o *conteúdo de texto* do `<option>` elemento.

A Blazor estrutura não tenta suprimir o comportamento padrão porque ele envolveria:

* Criar uma cadeia de soluções alternativas de caso especial no Framework.
* Alterações recentes no comportamento da estrutura atual.

::: moniker range=">= aspnetcore-5.0"

O equivalente mais plausível `null` em HTML é uma *cadeia de caracteres vazia* `value` . A Blazor estrutura manipula `null` conversões de cadeia de caracteres vazias para a associação bidirecional a um `<select>` valor de.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

A Blazor estrutura não lida automaticamente `null` com conversões de cadeia de caracteres vazias ao tentar fazer a ligação bidirecional com um `<select>` valor de. Para obter mais informações, consulte [corrigir Associação `<select>` a um valor nulo (dotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Suporte à validação

O <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente anexa o suporte à validação usando anotações de dados para o em cascata <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito. Para usar um sistema de validação diferente de anotações de dados, substitua-o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por uma implementação personalizada. A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Os links anteriores para a fonte de referência fornecem o código do `master` Branch do repositório, que representa o desenvolvimento atual da unidade do produto para a próxima versão do ASP.NET Core. Para selecionar a ramificação de uma versão diferente, use o seletor de ramificações do GitHub (por exemplo, `release/3.1` ).

Blazor executa dois tipos de validação:

* A *validação de campo* é executada quando o usuário faz a Tabulação de um campo. Durante a validação de campo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente associa todos os resultados de validação relatados ao campo.
* A *validação do modelo* é executada quando o usuário envia o formulário. Durante a validação do modelo, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente tenta determinar o campo com base no nome do membro que o resultado da validação relata. Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.

### <a name="validation-summary-and-validation-message-components"></a>Resumo de validação e componentes de mensagem de validação

O <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Mensagens de validação de saída para um modelo específico com o `Model` parâmetro:
  
```razor
<ValidationSummary Model="@starship" />
```

O <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Os <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componentes e oferecem suporte a atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento gerado ou `<ul>` .

Controle o estilo das mensagens de validação na folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ). A `validation-message` classe padrão define a cor do texto das mensagens de validação como vermelho:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Atributos de validação personalizados

Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o contexto de validação <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

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
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> é `null`. Não há suporte para injetar serviços para validação no `IsValid` método.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>Atributos da classe de validação personalizada

Os nomes de classe de validação personalizada são úteis na integração com estruturas CSS, como [Bootstrap](https://getbootstrap.com/). Para especificar nomes de classe de validação personalizados, crie uma classe derivada de `FieldCssClassProvider` e defina a classe na <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instância:

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor pacote de validação de anotações de dados

O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente. O pacote está *experimental* no momento.

> [!NOTE]
> O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) pacote tem uma versão mais recente do *release candidate* em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue a usar o pacote do Release Candidate *experimental* no momento. O assembly do pacote pode ser movido para a estrutura ou o tempo de execução em uma versão futura. Assista ao [repositório GitHub de anúncios](https://github.com/aspnet/Announcements), no [repositório GitHub do dotnet/aspnetcore](https://github.com/dotnet/aspnetcore)ou nesta seção do tópico para obter mais atualizações.

### <a name="compareproperty-attribute"></a>Atributo [compareproperty]

O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> componente porque não associa o resultado da validação a um membro específico. Isso pode resultar em comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio. O [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) pacote *experimental* introduz um atributo de validação adicional, `ComparePropertyAttribute` que funciona em relação a essas limitações. Em um Blazor aplicativo, `[CompareProperty]` é uma substituição direta para o [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atributo.

### <a name="nested-models-collection-types-and-complex-types"></a>Modelos aninhados, tipos de coleção e tipos complexos

Blazor fornece suporte para validar a entrada de formulário usando anotações de dados com o interno <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . No entanto, o <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.

Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo pacote *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Anote as propriedades do modelo com `[ValidateComplexType]` . Nas classes de modelo a seguir, a `ShipDescription` classe contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

`ShipDescription.cs`:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Habilitar o botão enviar com base na validação do formulário

Para habilitar e desabilitar o botão enviar com base na validação do formulário:

* Use o formulário <xref:Microsoft.AspNetCore.Components.Forms.EditContext> para atribuir o modelo quando o componente for inicializado.
* Valide o formulário no retorno de chamada do contexto <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> para habilitar e desabilitar o botão enviar.
* Desvincule o manipulador de eventos no `Dispose` método. Para obter mais informações, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> Ao usar um <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , não atribua também um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ao <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .

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

No exemplo anterior, defina `formInvalid` como `false` se:

* O formulário é pré-carregado com valores padrão válidos.
* Você deseja que o botão enviar seja habilitado quando o formulário é carregado.

Um efeito colateral da abordagem anterior é que um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente é preenchido com campos inválidos depois que o usuário interage com um campo. Esse cenário pode ser resolvido de uma das seguintes maneiras:

* Não use um <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente no formulário.
* Torne o <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> componente visível quando o botão enviar estiver selecionado (por exemplo, em um `HandleValidSubmit` método).

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

## <a name="troubleshoot"></a>Solucionar problemas

> InvalidOperationException: EditForm requer um parâmetro de modelo ou um parâmetro EditContext, mas não ambos.

Confirme se o <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tem um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **ou** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Não use ambos para a mesma forma.

Ao atribuir um <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> ao formulário, confirme se o tipo de modelo é instanciado, como mostra o exemplo a seguir:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/file-uploads>

::: moniker-end
