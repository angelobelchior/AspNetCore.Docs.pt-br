---
title: 'Usar filtros de Hub no ASP.NET Core SignalR'
author: brecon
description: 'Saiba como usar filtros de Hub no ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052715"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="fc1cc-103">Usar filtros de Hub no ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="fc1cc-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="fc1cc-104">Filtros de Hub:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-104">Hub filters:</span></span>

* <span data-ttu-id="fc1cc-105">Estão disponíveis no ASP.NET Core 5,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="fc1cc-106">Permitir que a lógica seja executada antes e depois dos métodos de Hub serem invocados pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="fc1cc-107">Este artigo fornece diretrizes para escrever e usar filtros de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="fc1cc-108">Configurar filtros de Hub</span><span class="sxs-lookup"><span data-stu-id="fc1cc-108">Configure hub filters</span></span>

<span data-ttu-id="fc1cc-109">Os filtros de Hub podem ser aplicados globalmente ou por tipo de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="fc1cc-110">A ordem na qual os filtros são adicionados é a ordem na qual os filtros são executados.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="fc1cc-111">Filtros de Hub globais são executados antes dos filtros de Hub locais.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="fc1cc-112">Um filtro de Hub pode ser adicionado de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="fc1cc-113">Adicionar um filtro por tipo concreto:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="fc1cc-114">Isso será resolvido da injeção de dependência (DI) ou do tipo ativado.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="fc1cc-115">Adicionar um filtro por tipo de tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="fc1cc-116">Isso será resolvido a partir de DI ou do tipo ativado.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="fc1cc-117">Adicionar um filtro por instância:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="fc1cc-118">Essa instância será usada como um singleton.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="fc1cc-119">Todas as invocações de método de Hub usarão a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="fc1cc-120">Os filtros de Hub são criados e descartados por invocação de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="fc1cc-121">Se você quiser armazenar o estado global no filtro ou nenhum estado, adicione o tipo de filtro hub a DI como um singleton para melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="fc1cc-122">Como alternativa, adicione o filtro como uma instância, se possível.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="fc1cc-123">Criar filtros de Hub</span><span class="sxs-lookup"><span data-stu-id="fc1cc-123">Create hub filters</span></span>

<span data-ttu-id="fc1cc-124">Crie um filtro declarando uma classe que herda de `IHubFilter` e adicione o `InvokeMethodAsync` método.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="fc1cc-125">Também há `OnConnectedAsync` e `OnDisconnectedAsync` isso pode, opcionalmente, ser implementado para encapsular os `OnConnectedAsync` métodos de Hub e, `OnDisconnectedAsync` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="fc1cc-126">Os filtros são muito semelhantes ao middleware.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="fc1cc-127">O `next` método invoca o próximo filtro.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="fc1cc-128">O filtro final invocará o método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="fc1cc-129">Os filtros também podem armazenar o resultado de esperar `next` e executar a lógica após o método de Hub ser chamado antes de retornar o resultado de `next` .</span><span class="sxs-lookup"><span data-stu-id="fc1cc-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="fc1cc-130">Para ignorar uma invocação de método de Hub em um filtro, lance uma exceção do tipo `HubException` em vez de chamar `next` .</span><span class="sxs-lookup"><span data-stu-id="fc1cc-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="fc1cc-131">O cliente receberá um erro se ele estiver esperando um resultado.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="fc1cc-132">Usar filtros de Hub</span><span class="sxs-lookup"><span data-stu-id="fc1cc-132">Use hub filters</span></span>

<span data-ttu-id="fc1cc-133">Ao escrever a lógica de filtro, tente torná-la genérica usando atributos em métodos de Hub em vez de verificar nomes de métodos de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="fc1cc-134">Considere um filtro que verificará um argumento de método de Hub para frases banidas e substituirá as frases que encontrar `***` .</span><span class="sxs-lookup"><span data-stu-id="fc1cc-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="fc1cc-135">Para este exemplo, suponha que uma `LanguageFilterAttribute` classe seja definida.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="fc1cc-136">A classe tem uma propriedade chamada `FilterArgument` que pode ser definida ao usar o atributo.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="fc1cc-137">Coloque o atributo no método de Hub que tem um argumento de cadeia de caracteres a ser limpo:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="fc1cc-138">Defina um filtro de Hub para verificar o atributo e substituir as frases banidas em um argumento de método de Hub por `**_` :</span><span class="sxs-lookup"><span data-stu-id="fc1cc-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="fc1cc-139">Registre o filtro de Hub no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fc1cc-140">Para evitar a reinicialização da lista de frases proibidas para cada invocação, o filtro de Hub é registrado como um singleton:</span><span class="sxs-lookup"><span data-stu-id="fc1cc-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="fc1cc-141">O objeto HubInvocationContext</span><span class="sxs-lookup"><span data-stu-id="fc1cc-141">The HubInvocationContext object</span></span>

<span data-ttu-id="fc1cc-142">O `HubInvocationContext` contém informações para a invocação do método de Hub atual.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="fc1cc-143">Propriedade</span><span class="sxs-lookup"><span data-stu-id="fc1cc-143">Property</span></span> | <span data-ttu-id="fc1cc-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="fc1cc-144">Description</span></span> | <span data-ttu-id="fc1cc-145">Type</span><span class="sxs-lookup"><span data-stu-id="fc1cc-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="fc1cc-146">O `HubCallerContext` contém informações sobre a conexão.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="fc1cc-147">A instância do Hub que está sendo usada para esta invocação de método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="fc1cc-148">O nome do método de Hub que está sendo invocado.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="fc1cc-149">A lista de argumentos que estão sendo passados para o método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="fc1cc-150">O provedor de serviço com escopo para esta invocação de método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="fc1cc-151">As informações do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="fc1cc-152">O objeto HubLifetimeContext</span><span class="sxs-lookup"><span data-stu-id="fc1cc-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="fc1cc-153">O `HubLifetimeContext` contém informações para os `OnConnectedAsync` `OnDisconnectedAsync` métodos de Hub e.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="fc1cc-154">Propriedade</span><span class="sxs-lookup"><span data-stu-id="fc1cc-154">Property</span></span> | <span data-ttu-id="fc1cc-155">Descrição</span><span class="sxs-lookup"><span data-stu-id="fc1cc-155">Description</span></span> | <span data-ttu-id="fc1cc-156">Type</span><span class="sxs-lookup"><span data-stu-id="fc1cc-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="fc1cc-157">O `HubCallerContext` contém informações sobre a conexão.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="fc1cc-158">A instância do Hub que está sendo usada para esta invocação de método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="fc1cc-159">O provedor de serviço com escopo para esta invocação de método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="fc1cc-160">Autorização e filtros</span><span class="sxs-lookup"><span data-stu-id="fc1cc-160">Authorization and filters</span></span>

<span data-ttu-id="fc1cc-161">[Autorize os atributos nos métodos de Hub](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) executados antes dos filtros de Hub.</span><span class="sxs-lookup"><span data-stu-id="fc1cc-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
