---
title: Injeção de dependência em controladores no ASP.NET Core
author: ardalis
description: Saiba como os controladores do ASP.NET Core MVC solicitam suas dependências explicitamente por meio de seus construtores com injeção de dependência no ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
no-loc:
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 1282cd984584be423fba755e64e5d2f1afd2af89
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060606"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="f7505-103">Injeção de dependência em controladores no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7505-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7505-104">Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="f7505-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="f7505-105">Controladores do ASP.NET Core MVC solicitam dependências explicitamente por meio de construtores.</span><span class="sxs-lookup"><span data-stu-id="f7505-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="f7505-106">O ASP.NET Core tem suporte interno para [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f7505-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f7505-107">A injeção de dependência torna mais fácil testar e manter aplicativos.</span><span class="sxs-lookup"><span data-stu-id="f7505-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="f7505-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7505-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="f7505-109">Injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="f7505-109">Constructor Injection</span></span>

<span data-ttu-id="f7505-110">Os serviços são adicionados como um parâmetro de construtor e o runtime resolve o serviço de contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="f7505-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="f7505-111">Os serviços são normalmente definidos usando interfaces.</span><span class="sxs-lookup"><span data-stu-id="f7505-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="f7505-112">Por exemplo, considere um aplicativo que exige a hora atual.</span><span class="sxs-lookup"><span data-stu-id="f7505-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="f7505-113">A interface a seguir expõe o serviço `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="f7505-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="f7505-114">O código a seguir implementa a interface `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="f7505-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="f7505-115">Adicione o serviço ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="f7505-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="f7505-116">Para obter mais informações sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, veja [tempos de vida do serviço DI](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f7505-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="f7505-117">O código a seguir exibe uma saudação ao usuário com base na hora do dia:</span><span class="sxs-lookup"><span data-stu-id="f7505-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="f7505-118">Execute o aplicativo e será exibida uma mensagem com base no horário.</span><span class="sxs-lookup"><span data-stu-id="f7505-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="f7505-119">Injeção de ação com FromServices</span><span class="sxs-lookup"><span data-stu-id="f7505-119">Action injection with FromServices</span></span>

<span data-ttu-id="f7505-120">O <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> habilita a injeção de um serviço diretamente em um método de ação sem usar a injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="f7505-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="f7505-121">Acessar configurações de um controlador</span><span class="sxs-lookup"><span data-stu-id="f7505-121">Access settings from a controller</span></span>

<span data-ttu-id="f7505-122">Acessar definições de configuração ou do aplicativo de dentro de um controlador é um padrão comum.</span><span class="sxs-lookup"><span data-stu-id="f7505-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="f7505-123">O *padrão de opções* descrito em <xref:fundamentals/configuration/options> é a abordagem preferencial para gerenciar as configurações.</span><span class="sxs-lookup"><span data-stu-id="f7505-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="f7505-124">Em geral, não convém injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> diretamente em um controlador.</span><span class="sxs-lookup"><span data-stu-id="f7505-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="f7505-125">Crie uma classe que representa as opções.</span><span class="sxs-lookup"><span data-stu-id="f7505-125">Create a class that represents the options.</span></span> <span data-ttu-id="f7505-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f7505-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="f7505-127">Adicione a classe de configuração à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="f7505-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="f7505-128">Configure o aplicativo para ler as configurações de um arquivo formatado em JSON:</span><span class="sxs-lookup"><span data-stu-id="f7505-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="f7505-129">O código a seguir solicita as configurações `IOptions<SampleWebSettings>` do contêiner de serviço e usa-as no método `Index`:</span><span class="sxs-lookup"><span data-stu-id="f7505-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="f7505-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7505-130">Additional resources</span></span>

* <span data-ttu-id="f7505-131">Confira <xref:mvc/controllers/testing> para facilitar o teste do código ao solicitar dependências explicitamente em controladores.</span><span class="sxs-lookup"><span data-stu-id="f7505-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="f7505-132">[Substitui o contêiner de injeção de dependência padrão por uma implementação de terceiros](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="f7505-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7505-133">Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="f7505-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="f7505-134">Controladores do ASP.NET Core MVC solicitam dependências explicitamente por meio de construtores.</span><span class="sxs-lookup"><span data-stu-id="f7505-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="f7505-135">O ASP.NET Core tem suporte interno para [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f7505-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f7505-136">A injeção de dependência torna mais fácil testar e manter aplicativos.</span><span class="sxs-lookup"><span data-stu-id="f7505-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="f7505-137">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7505-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="f7505-138">Injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="f7505-138">Constructor Injection</span></span>

<span data-ttu-id="f7505-139">Os serviços são adicionados como um parâmetro de construtor e o runtime resolve o serviço de contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="f7505-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="f7505-140">Os serviços são normalmente definidos usando interfaces.</span><span class="sxs-lookup"><span data-stu-id="f7505-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="f7505-141">Por exemplo, considere um aplicativo que exige a hora atual.</span><span class="sxs-lookup"><span data-stu-id="f7505-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="f7505-142">A interface a seguir expõe o serviço `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="f7505-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="f7505-143">O código a seguir implementa a interface `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="f7505-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="f7505-144">Adicione o serviço ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="f7505-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="f7505-145">Para obter mais informações sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, veja [tempos de vida do serviço DI](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f7505-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="f7505-146">O código a seguir exibe uma saudação ao usuário com base na hora do dia:</span><span class="sxs-lookup"><span data-stu-id="f7505-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="f7505-147">Execute o aplicativo e será exibida uma mensagem com base no horário.</span><span class="sxs-lookup"><span data-stu-id="f7505-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="f7505-148">Injeção de ação com FromServices</span><span class="sxs-lookup"><span data-stu-id="f7505-148">Action injection with FromServices</span></span>

<span data-ttu-id="f7505-149">O <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> habilita a injeção de um serviço diretamente em um método de ação sem usar a injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="f7505-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="f7505-150">Acessar configurações de um controlador</span><span class="sxs-lookup"><span data-stu-id="f7505-150">Access settings from a controller</span></span>

<span data-ttu-id="f7505-151">Acessar definições de configuração ou do aplicativo de dentro de um controlador é um padrão comum.</span><span class="sxs-lookup"><span data-stu-id="f7505-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="f7505-152">O *padrão de opções* descrito em <xref:fundamentals/configuration/options> é a abordagem preferencial para gerenciar as configurações.</span><span class="sxs-lookup"><span data-stu-id="f7505-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="f7505-153">Em geral, não convém injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> diretamente em um controlador.</span><span class="sxs-lookup"><span data-stu-id="f7505-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="f7505-154">Crie uma classe que representa as opções.</span><span class="sxs-lookup"><span data-stu-id="f7505-154">Create a class that represents the options.</span></span> <span data-ttu-id="f7505-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f7505-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="f7505-156">Adicione a classe de configuração à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="f7505-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="f7505-157">Configure o aplicativo para ler as configurações de um arquivo formatado em JSON:</span><span class="sxs-lookup"><span data-stu-id="f7505-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="f7505-158">O código a seguir solicita as configurações `IOptions<SampleWebSettings>` do contêiner de serviço e usa-as no método `Index`:</span><span class="sxs-lookup"><span data-stu-id="f7505-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="f7505-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7505-159">Additional resources</span></span>

* <span data-ttu-id="f7505-160">Confira <xref:mvc/controllers/testing> para facilitar o teste do código ao solicitar dependências explicitamente em controladores.</span><span class="sxs-lookup"><span data-stu-id="f7505-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="f7505-161">[Substitui o contêiner de injeção de dependência padrão por uma implementação de terceiros](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="f7505-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end