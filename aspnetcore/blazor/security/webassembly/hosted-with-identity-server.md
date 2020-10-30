---
title: 'Proteger um aplicativo ASP.NET Core hospedado :::no-loc(Blazor WebAssembly)::: com o :::no-loc(Identity)::: servidor'
author: guardrex
description: 'Saiba como proteger um aplicativo ASP.NET Core hospedado :::no-loc(Blazor WebAssembly)::: com o :::no-loc(Identity)::: servidor.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 147f1d6cdea0b9992b8be333db4cb06e30c7feaf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055211"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="380f5-103">Proteger um :::no-loc(Blazor WebAssembly)::: aplicativo ASP.NET Core hospedado com o :::no-loc(Identity)::: servidor</span><span class="sxs-lookup"><span data-stu-id="380f5-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="380f5-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="380f5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="380f5-105">Este artigo explica como criar um [ :::no-loc(Blazor WebAssembly)::: aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa o [ :::no-loc(Identity)::: servidor do](https://identityserver.io/) para autenticar usuários e chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="380f5-105">This article explains how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [:::no-loc(Identity):::Server](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="380f5-106">Para configurar um aplicativo autônomo ou hospedado :::no-loc(Blazor WebAssembly)::: para usar uma instância de servidor externa existente :::no-loc(Identity)::: , siga as orientações em <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="380f5-106">To configure a standalone or hosted :::no-loc(Blazor WebAssembly)::: app to use an existing, external :::no-loc(Identity)::: Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="380f5-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="380f5-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="380f5-108">Para criar um novo :::no-loc(Blazor WebAssembly)::: projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="380f5-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="380f5-109">Depois de escolher o modelo de **:::no-loc(Blazor WebAssembly)::: aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="380f5-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

1. <span data-ttu-id="380f5-110">Selecione **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** para armazenar usuários no aplicativo usando o sistema do ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="380f5-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="380f5-111">Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .</span><span class="sxs-lookup"><span data-stu-id="380f5-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="380f5-112">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="380f5-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="380f5-113">Para criar um novo :::no-loc(Blazor WebAssembly)::: projeto com um mecanismo de autenticação em uma pasta vazia, especifique o `Individual` mecanismo de autenticação com a `-au|--auth` opção de armazenar usuários no aplicativo usando o [:::no-loc(Identity):::](xref:security/authentication/identity) sistema do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="380f5-113">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="380f5-114">Espaço reservado</span><span class="sxs-lookup"><span data-stu-id="380f5-114">Placeholder</span></span>  | <span data-ttu-id="380f5-115">Exemplo</span><span class="sxs-lookup"><span data-stu-id="380f5-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="380f5-116">A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="380f5-117">Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="380f5-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="380f5-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="380f5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="380f5-119">Para criar um novo :::no-loc(Blazor WebAssembly)::: projeto com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="380f5-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="380f5-120">Na etapa **configurar seu novo :::no-loc(Blazor WebAssembly)::: aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .</span><span class="sxs-lookup"><span data-stu-id="380f5-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="380f5-121">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="380f5-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="380f5-122">Marque a caixa de seleção **ASP.NET Core hospedado** .</span><span class="sxs-lookup"><span data-stu-id="380f5-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="380f5-123">*`Server`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-123">*`Server`* app configuration</span></span>

<span data-ttu-id="380f5-124">As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.</span><span class="sxs-lookup"><span data-stu-id="380f5-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="380f5-125">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="380f5-125">Startup class</span></span>

<span data-ttu-id="380f5-126">A `Startup` classe tem as seguintes adições.</span><span class="sxs-lookup"><span data-stu-id="380f5-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="380f5-127">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="380f5-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="380f5-128">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="380f5-128">:::no-loc(ASP.NET Core Identity)::::</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="380f5-129">:::no-loc(Identity):::Servidor com um <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura as convenções de ASP.NET Core padrão na parte superior do :::no-loc(Identity)::: servidor:</span><span class="sxs-lookup"><span data-stu-id="380f5-129">:::no-loc(Identity):::Server with an additional <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="380f5-130">Autenticação com um <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> método auxiliar adicional que configura o aplicativo para validar tokens JWT produzidos pelo :::no-loc(Identity)::: servidor:</span><span class="sxs-lookup"><span data-stu-id="380f5-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="380f5-131">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="380f5-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="380f5-132">O :::no-loc(Identity)::: middleware do servidor expõe os pontos de extremidade do OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="380f5-132">The :::no-loc(Identity):::Server middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

  * <span data-ttu-id="380f5-133">O middleware de autenticação é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:</span><span class="sxs-lookup"><span data-stu-id="380f5-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="380f5-134">O middleware de autorização habilita os recursos de autorização:</span><span class="sxs-lookup"><span data-stu-id="380f5-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="380f5-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="380f5-135">AddApiAuthorization</span></span>

<span data-ttu-id="380f5-136">O <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura o [ :::no-loc(Identity)::: servidor](https://identityserver.io/) para cenários de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="380f5-136">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [:::no-loc(Identity):::Server](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="380f5-137">:::no-loc(Identity):::O servidor é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-137">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="380f5-138">:::no-loc(Identity):::O servidor expõe complexidade desnecessária para os cenários mais comuns.</span><span class="sxs-lookup"><span data-stu-id="380f5-138">:::no-loc(Identity):::Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="380f5-139">Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="380f5-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="380f5-140">Depois que a autenticação precisar ser alterada, todo o poder do :::no-loc(Identity)::: servidor estará disponível para personalizar a autenticação de acordo com os requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-140">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="380f5-141">Adicionar :::no-loc(Identity)::: ServerJwt</span><span class="sxs-lookup"><span data-stu-id="380f5-141">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="380f5-142">O <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="380f5-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="380f5-143">A política está configurada para permitir que o :::no-loc(Identity)::: manipule todas as solicitações roteadas para qualquer subcaminho no :::no-loc(Identity)::: espaço de URL `/:::no-loc(Identity):::` .</span><span class="sxs-lookup"><span data-stu-id="380f5-143">The policy is configured to allow :::no-loc(Identity)::: to handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space `/:::no-loc(Identity):::`.</span></span> <span data-ttu-id="380f5-144">O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="380f5-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="380f5-145">Além disso, esse método:</span><span class="sxs-lookup"><span data-stu-id="380f5-145">Additionally, this method:</span></span>

* <span data-ttu-id="380f5-146">Registra um `{APPLICATION NAME}API` recurso de API com :::no-loc(Identity)::: o servidor com um escopo padrão de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="380f5-146">Registers an `{APPLICATION NAME}API` API resource with :::no-loc(Identity):::Server with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="380f5-147">Configura o middleware do token de portador JWT para validar tokens emitidos pelo :::no-loc(Identity)::: servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-147">Configures the JWT Bearer Token Middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="380f5-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="380f5-148">WeatherForecastController</span></span>

<span data-ttu-id="380f5-149">No `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é aplicado à classe.</span><span class="sxs-lookup"><span data-stu-id="380f5-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="380f5-150">O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="380f5-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="380f5-151">A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="380f5-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>.</span></span> <span data-ttu-id="380f5-152">O método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> é configurado como o manipulador padrão para solicitações ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="380f5-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="380f5-153">ApplicationDbContext</span></span>

<span data-ttu-id="380f5-154">No `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> estende- <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> se para incluir o esquema para o :::no-loc(Identity)::: servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> to include the schema for :::no-loc(Identity):::Server.</span></span> <span data-ttu-id="380f5-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> é derivado de <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> .</span><span class="sxs-lookup"><span data-stu-id="380f5-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span></span>

<span data-ttu-id="380f5-156">Para obter controle total do esquema de banco de dados, herde de uma das :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponíveis e configure o contexto para incluir o :::no-loc(Identity)::: esquema chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.</span><span class="sxs-lookup"><span data-stu-id="380f5-156">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="380f5-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="380f5-157">OidcConfigurationController</span></span>

<span data-ttu-id="380f5-158">No `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.</span><span class="sxs-lookup"><span data-stu-id="380f5-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="380f5-159">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-159">App settings</span></span>

<span data-ttu-id="380f5-160">No arquivo de configurações do aplicativo ( `:::no-loc(appsettings.json):::` ) na raiz do projeto, a `:::no-loc(Identity):::Server` seção descreve a lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="380f5-160">In the app settings file (`:::no-loc(appsettings.json):::`) at the project root, the `:::no-loc(Identity):::Server` section describes the list of configured clients.</span></span> <span data-ttu-id="380f5-161">No exemplo a seguir, há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="380f5-161">In the following example, there's a single client.</span></span> <span data-ttu-id="380f5-162">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o `ClientId` parâmetro OAuth.</span><span class="sxs-lookup"><span data-stu-id="380f5-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="380f5-163">O perfil indica o tipo de aplicativo que está sendo configurado.</span><span class="sxs-lookup"><span data-stu-id="380f5-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="380f5-164">O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

<span data-ttu-id="380f5-165">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="380f5-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="380f5-166">*`Client`* configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="380f5-167">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="380f5-167">Authentication package</span></span>

<span data-ttu-id="380f5-168">Quando um aplicativo é criado para usar contas de usuário individuais ( `Individual` ), o aplicativo recebe automaticamente uma referência de pacote para o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="380f5-169">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="380f5-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="380f5-170">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="380f5-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="380f5-171">Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="380f5-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="380f5-172">`HttpClient` configuração</span><span class="sxs-lookup"><span data-stu-id="380f5-172">`HttpClient` configuration</span></span>

<span data-ttu-id="380f5-173">Em `Program.Main` ( `Program.cs` ), um nome <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) é configurado para fornecer <xref:System.Net.Http.HttpClient> instâncias que incluem tokens de acesso ao fazer solicitações para a API do servidor:</span><span class="sxs-lookup"><span data-stu-id="380f5-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="380f5-174">Se você estiver configurando um :::no-loc(Blazor WebAssembly)::: aplicativo para usar uma :::no-loc(Identity)::: instância de servidor existente que não faz parte de uma solução hospedada :::no-loc(Blazor)::: , altere o <xref:System.Net.Http.HttpClient> registro do endereço base de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) para a URL do ponto de extremidade de autorização da API do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-174">If you're configuring a :::no-loc(Blazor WebAssembly)::: app to use an existing :::no-loc(Identity)::: Server instance that isn't part of a hosted :::no-loc(Blazor)::: solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="380f5-175">Suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="380f5-175">API authorization support</span></span>

<span data-ttu-id="380f5-176">O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote.</span><span class="sxs-lookup"><span data-stu-id="380f5-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="380f5-177">Esse método configura os serviços exigidos pelo aplicativo para interagir com o sistema de autorização existente.</span><span class="sxs-lookup"><span data-stu-id="380f5-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="380f5-178">Por padrão, a configuração para o aplicativo é carregada por convenção de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="380f5-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="380f5-179">Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="380f5-180">Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.</span><span class="sxs-lookup"><span data-stu-id="380f5-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="380f5-181">Arquivo de importações</span><span class="sxs-lookup"><span data-stu-id="380f5-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="380f5-182">Página de índice</span><span class="sxs-lookup"><span data-stu-id="380f5-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="380f5-183">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="380f5-184">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="380f5-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="380f5-185">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="380f5-185">LoginDisplay component</span></span>

<span data-ttu-id="380f5-186">O `LoginDisplay` componente ( `Shared/LoginDisplay.razor` ) é renderizado no `MainLayout` componente ( `Shared/MainLayout.razor` ) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="380f5-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="380f5-187">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="380f5-187">For authenticated users:</span></span>
  * <span data-ttu-id="380f5-188">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="380f5-188">Displays the current user name.</span></span>
  * <span data-ttu-id="380f5-189">Oferece um link para a página de perfil do usuário no :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="380f5-189">Offers a link to the user profile page in :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="380f5-190">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="380f5-191">Para usuários anônimos:</span><span class="sxs-lookup"><span data-stu-id="380f5-191">For anonymous users:</span></span>
  * <span data-ttu-id="380f5-192">Oferece a opção de se registrar.</span><span class="sxs-lookup"><span data-stu-id="380f5-192">Offers the option to register.</span></span>
  * <span data-ttu-id="380f5-193">Oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="380f5-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.:::no-loc(Identity):::.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="380f5-194">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="380f5-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="380f5-195">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="380f5-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="380f5-196">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-196">Run the app</span></span>

<span data-ttu-id="380f5-197">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-197">Run the app from the Server project.</span></span> <span data-ttu-id="380f5-198">Ao usar o Visual Studio, seja:</span><span class="sxs-lookup"><span data-stu-id="380f5-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="380f5-199">Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .</span><span class="sxs-lookup"><span data-stu-id="380f5-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="380f5-200">Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="380f5-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="380f5-201">Nome e declaração de função com autorização de API</span><span class="sxs-lookup"><span data-stu-id="380f5-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="380f5-202">Fábrica de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="380f5-202">Custom user factory</span></span>

<span data-ttu-id="380f5-203">No *`Client`* aplicativo, crie uma fábrica de usuário personalizada.</span><span class="sxs-lookup"><span data-stu-id="380f5-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="380f5-204">:::no-loc(Identity)::: O servidor envia várias funções como uma matriz JSON em uma única `role` declaração.</span><span class="sxs-lookup"><span data-stu-id="380f5-204">:::no-loc(Identity)::: Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="380f5-205">Uma única função é enviada como um valor de cadeia de caracteres na declaração.</span><span class="sxs-lookup"><span data-stu-id="380f5-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="380f5-206">A fábrica cria uma `role` declaração individual para cada uma das funções do usuário.</span><span class="sxs-lookup"><span data-stu-id="380f5-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="380f5-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="380f5-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            var identity = (Claims:::no-loc(Identity):::)user.:::no-loc(Identity):::;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="380f5-208">No *`Client`* aplicativo, registre a fábrica em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="380f5-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="380f5-209">No *`Server`* aplicativo, chame <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> no :::no-loc(Identity)::: Construtor, que adiciona serviços relacionados a funções:</span><span class="sxs-lookup"><span data-stu-id="380f5-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> on the :::no-loc(Identity)::: builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::;

...

services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<:::no-loc(Identity):::Role>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="380f5-210">Configurar :::no-loc(Identity)::: servidor</span><span class="sxs-lookup"><span data-stu-id="380f5-210">Configure :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="380f5-211">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="380f5-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="380f5-212">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="380f5-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="380f5-213">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="380f5-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="380f5-214">Opções de autorização de API</span><span class="sxs-lookup"><span data-stu-id="380f5-214">API authorization options</span></span>

<span data-ttu-id="380f5-215">No *`Server`* aplicativo:</span><span class="sxs-lookup"><span data-stu-id="380f5-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="380f5-216">Configure :::no-loc(Identity)::: o servidor para colocar o `name` e as `role` declarações no token de ID e no token de acesso.</span><span class="sxs-lookup"><span data-stu-id="380f5-216">Configure :::no-loc(Identity)::: Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="380f5-217">Impedir o mapeamento padrão para funções no manipulador de tokens JWT.</span><span class="sxs-lookup"><span data-stu-id="380f5-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Linq;

...

services.Add:::no-loc(Identity):::Server()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="380f5-218">Serviço de perfil</span><span class="sxs-lookup"><span data-stu-id="380f5-218">Profile Service</span></span>

<span data-ttu-id="380f5-219">No *`Server`* aplicativo, crie uma `ProfileService` implementação.</span><span class="sxs-lookup"><span data-stu-id="380f5-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="380f5-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="380f5-220">`ProfileService.cs`:</span></span>

```csharp
using :::no-loc(Identity):::Model;
using :::no-loc(Identity):::Server4.Models;
using :::no-loc(Identity):::Server4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="380f5-221">No *`Server`* aplicativo, registre o serviço de perfil em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="380f5-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using :::no-loc(Identity):::Server4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="380f5-222">Usar mecanismos de autorização</span><span class="sxs-lookup"><span data-stu-id="380f5-222">Use authorization mechanisms</span></span>

<span data-ttu-id="380f5-223">No *`Client`* aplicativo, as abordagens de autorização de componente são funcionais neste ponto.</span><span class="sxs-lookup"><span data-stu-id="380f5-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="380f5-224">Qualquer um dos mecanismos de autorização nos componentes pode usar uma função para autorizar o usuário:</span><span class="sxs-lookup"><span data-stu-id="380f5-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="380f5-225">[ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) (exemplo: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="380f5-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="380f5-226">[ `[Authorize]` diretiva de atributo](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemplo: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="380f5-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="380f5-227">[Lógica de procedimento](xref:blazor/security/index#procedural-logic) (exemplo: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="380f5-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="380f5-228">Há suporte para vários testes de função:</span><span class="sxs-lookup"><span data-stu-id="380f5-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="380f5-229">`User.:::no-loc(Identity):::.Name` é populado no *`Client`* aplicativo com o nome de usuário do usuário, que geralmente é seu endereço de email de entrada.</span><span class="sxs-lookup"><span data-stu-id="380f5-229">`User.:::no-loc(Identity):::.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="380f5-230">Hospedar no serviço de Azure App com um domínio personalizado</span><span class="sxs-lookup"><span data-stu-id="380f5-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="380f5-231">As diretrizes a seguir explicam como implantar um :::no-loc(Blazor WebAssembly)::: aplicativo hospedado com o :::no-loc(Identity)::: servidor do para [Azure app serviço](https://azure.microsoft.com/services/app-service/) com um domínio personalizado.</span><span class="sxs-lookup"><span data-stu-id="380f5-231">The following guidance explains how to deploy a hosted :::no-loc(Blazor WebAssembly)::: app with :::no-loc(Identity)::: Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="380f5-232">Para esse cenário de hospedagem, **não use o** mesmo certificado para a [ :::no-loc(Identity)::: chave de assinatura de token do servidor](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) e a comunicação https segura do site com navegadores:</span><span class="sxs-lookup"><span data-stu-id="380f5-232">For this hosting scenario, do **not** use the same certificate for [:::no-loc(Identity)::: Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="380f5-233">Usar certificados diferentes para esses dois requisitos é uma boa prática de segurança porque isola chaves privadas para cada finalidade.</span><span class="sxs-lookup"><span data-stu-id="380f5-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="380f5-234">Os certificados TLS para comunicação com navegadores são gerenciados de forma independente sem afetar a :::no-loc(Identity)::: assinatura de token do servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-234">TLS certificates for communication with browsers is managed independently without affecting :::no-loc(Identity)::: Server's token signing.</span></span>
* <span data-ttu-id="380f5-235">Quando [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fornece um certificado a um aplicativo do serviço de aplicativo para associação de domínio personalizada, :::no-loc(Identity)::: o servidor não pode obter o mesmo certificado de Azure Key Vault para assinatura de token.</span><span class="sxs-lookup"><span data-stu-id="380f5-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, :::no-loc(Identity)::: Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="380f5-236">Embora :::no-loc(Identity)::: seja possível configurar o servidor para usar o mesmo certificado TLS de um caminho físico, colocar os certificados de segurança no controle do código-fonte é uma **prática inadequada e deve ser evitado na maioria dos cenários** .</span><span class="sxs-lookup"><span data-stu-id="380f5-236">Although configuring :::no-loc(Identity)::: Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios** .</span></span>

<span data-ttu-id="380f5-237">Nas diretrizes a seguir, um certificado autoassinado é criado em Azure Key Vault exclusivamente para :::no-loc(Identity)::: assinatura de token do servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="380f5-238">A :::no-loc(Identity)::: configuração do servidor usa o certificado do Key Vault por meio do repositório de certificados do aplicativo `My`  >  `CurrentUser` .</span><span class="sxs-lookup"><span data-stu-id="380f5-238">The :::no-loc(Identity)::: Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="380f5-239">Outros certificados usados para tráfego HTTPS com domínios personalizados são criados e configurados separadamente do :::no-loc(Identity)::: certificado de autenticação do servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the :::no-loc(Identity)::: Server signing certificate.</span></span>

<span data-ttu-id="380f5-240">Para configurar um aplicativo, Azure App serviço e Azure Key Vault para hospedar com um domínio personalizado e HTTPS:</span><span class="sxs-lookup"><span data-stu-id="380f5-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="380f5-241">Crie um [plano do serviço de aplicativo](/azure/app-service/overview-hosting-plans) com um nível de plano `Basic B1` ou superior.</span><span class="sxs-lookup"><span data-stu-id="380f5-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="380f5-242">O serviço de aplicativo requer uma `Basic B1` camada de serviço ou mais alta para usar domínios personalizados.</span><span class="sxs-lookup"><span data-stu-id="380f5-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="380f5-243">Crie um certificado PFX para a comunicação segura do navegador do site (protocolo HTTPS) com um nome comum do FQDN (nome de domínio totalmente qualificado) do site que sua organização controla (por exemplo, `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="380f5-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="380f5-244">Crie o certificado com:</span><span class="sxs-lookup"><span data-stu-id="380f5-244">Create the certificate with:</span></span>
   * <span data-ttu-id="380f5-245">Usos de chave</span><span class="sxs-lookup"><span data-stu-id="380f5-245">Key uses</span></span>
     * <span data-ttu-id="380f5-246">Validação de assinatura digital ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="380f5-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="380f5-247">Codificação de chave ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="380f5-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="380f5-248">Uso avançado/estendido de chave</span><span class="sxs-lookup"><span data-stu-id="380f5-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="380f5-249">Autenticação de cliente (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="380f5-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="380f5-250">Autenticação de servidor (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="380f5-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="380f5-251">Para criar o certificado, use uma das abordagens a seguir ou qualquer outra ferramenta ou serviço online adequado:</span><span class="sxs-lookup"><span data-stu-id="380f5-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="380f5-252">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="380f5-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="380f5-253">MakeCert no Windows</span><span class="sxs-lookup"><span data-stu-id="380f5-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="380f5-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="380f5-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="380f5-255">Anote a senha, que será usada posteriormente para importar o certificado para o Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="380f5-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="380f5-256">Para obter mais informações sobre certificados Azure Key Vault, consulte [Azure Key Vault: certificados](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="380f5-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="380f5-257">Crie um novo Azure Key Vault ou use um cofre de chaves existente em sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="380f5-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="380f5-258">Na área **certificados** do Key Vault, importe o certificado do site PFX.</span><span class="sxs-lookup"><span data-stu-id="380f5-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="380f5-259">Registre a impressão digital do certificado, que será usada na configuração do aplicativo mais tarde.</span><span class="sxs-lookup"><span data-stu-id="380f5-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="380f5-260">No Azure Key Vault, gere um novo certificado autoassinado para :::no-loc(Identity)::: assinatura de token do servidor.</span><span class="sxs-lookup"><span data-stu-id="380f5-260">In Azure Key Vault, generate a new self-signed certificate for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="380f5-261">Dê ao certificado um **nome de certificado** e **assunto** .</span><span class="sxs-lookup"><span data-stu-id="380f5-261">Give the certificate a **Certificate Name** and **Subject** .</span></span> <span data-ttu-id="380f5-262">O **assunto** é especificado como `CN={COMMON NAME}` , em que o `{COMMON NAME}` espaço reservado é o nome comum do certificado.</span><span class="sxs-lookup"><span data-stu-id="380f5-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="380f5-263">O nome comum pode ser qualquer cadeia de caracteres alfanumérica.</span><span class="sxs-lookup"><span data-stu-id="380f5-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="380f5-264">Por exemplo, `CN=:::no-loc(Identity):::ServerSigning` é uma **entidade** de certificado válida.</span><span class="sxs-lookup"><span data-stu-id="380f5-264">For example, `CN=:::no-loc(Identity):::ServerSigning` is a valid certificate **Subject** .</span></span> <span data-ttu-id="380f5-265">Use as definições de **configuração de política avançada** padrão.</span><span class="sxs-lookup"><span data-stu-id="380f5-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="380f5-266">Registre a impressão digital do certificado, que será usada na configuração do aplicativo mais tarde.</span><span class="sxs-lookup"><span data-stu-id="380f5-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="380f5-267">Navegue até Azure App serviço no portal do Azure e crie um novo serviço de aplicativo com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="380f5-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="380f5-268">**Publicar** conjunto como `Code` .</span><span class="sxs-lookup"><span data-stu-id="380f5-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="380f5-269">Conjunto de **pilhas em tempo de execução** para o tempo de execução do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="380f5-270">Para **SKU e tamanho** , confirme se a camada do serviço de aplicativo é `Basic B1` ou superior.</span><span class="sxs-lookup"><span data-stu-id="380f5-270">For **Sku and size** , confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="380f5-271">O serviço de aplicativo requer uma `Basic B1` camada de serviço ou mais alta para usar domínios personalizados.</span><span class="sxs-lookup"><span data-stu-id="380f5-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="380f5-272">Depois que o Azure criar o serviço de aplicativo, abra a **configuração** do aplicativo e adicione uma nova configuração de aplicativo especificando as impressões digitais do certificado registradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="380f5-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="380f5-273">A chave de configuração do aplicativo é `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="380f5-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="380f5-274">Separe as impressões digitais do certificado no valor de configuração do aplicativo com uma vírgula, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="380f5-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="380f5-275">Chave: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="380f5-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="380f5-276">Valor: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="380f5-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="380f5-277">No portal do Azure, salvar as configurações do aplicativo é um processo de duas etapas: Salve a `WEBSITE_LOAD_CERTIFICATES` configuração chave-valor e, em seguida, selecione o botão **salvar** na parte superior da folha.</span><span class="sxs-lookup"><span data-stu-id="380f5-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="380f5-278">Selecione as configurações de **TLS/SSL** do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-278">Select the app's **TLS/SSL settings** .</span></span> <span data-ttu-id="380f5-279">Selecione **certificados de chave privada (. pfx)** .</span><span class="sxs-lookup"><span data-stu-id="380f5-279">Select **Private Key Certificates (.pfx)** .</span></span> <span data-ttu-id="380f5-280">Use o processo **importar Key Vault certificado** duas vezes para importar o certificado do site para comunicação HTTPS e o :::no-loc(Identity)::: certificado de autenticação de tokens do servidor autoassinado do site.</span><span class="sxs-lookup"><span data-stu-id="380f5-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed :::no-loc(Identity)::: Server token signing certificate.</span></span>
1. <span data-ttu-id="380f5-281">Navegue até a folha **domínios personalizados** .</span><span class="sxs-lookup"><span data-stu-id="380f5-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="380f5-282">No site do registrador de domínio, use o **endereço IP** e a **ID de verificação de domínio personalizado** para configurar o domínio.</span><span class="sxs-lookup"><span data-stu-id="380f5-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="380f5-283">Uma configuração de domínio típica inclui:</span><span class="sxs-lookup"><span data-stu-id="380f5-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="380f5-284">Um **registro a** com um **host** de `@` e um valor do endereço IP do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="380f5-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="380f5-285">Um **registro txt** com um **host** de `asuid` e o valor da ID de verificação gerado pelo Azure e fornecidos pelo portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="380f5-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="380f5-286">Certifique-se de salvar as alterações no site do registrador de domínio corretamente.</span><span class="sxs-lookup"><span data-stu-id="380f5-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="380f5-287">Alguns sites do registrador exigem um processo de duas etapas para salvar registros de domínio: um ou mais registros são salvos individualmente, atualizando o registro do domínio com um botão separado.</span><span class="sxs-lookup"><span data-stu-id="380f5-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="380f5-288">Retorne para a folha **domínios personalizados** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="380f5-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="380f5-289">Selecione **Adicionar domínio personalizado** .</span><span class="sxs-lookup"><span data-stu-id="380f5-289">Select **Add custom domain** .</span></span> <span data-ttu-id="380f5-290">Selecione a opção **um registro** .</span><span class="sxs-lookup"><span data-stu-id="380f5-290">Select the **A Record** option.</span></span> <span data-ttu-id="380f5-291">Forneça o domínio e selecione **validar** .</span><span class="sxs-lookup"><span data-stu-id="380f5-291">Provide the domain and select **Validate** .</span></span> <span data-ttu-id="380f5-292">Se os registros de domínio estiverem corretos e propagados pela Internet, o portal permitirá que você selecione o botão **Adicionar domínio personalizado** .</span><span class="sxs-lookup"><span data-stu-id="380f5-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="380f5-293">Pode levar alguns dias para que as alterações no registro do domínio se propaguem entre os servidores de nomes de domínio da Internet (DNS) depois que eles forem processados pelo seu registrador de domínio.</span><span class="sxs-lookup"><span data-stu-id="380f5-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="380f5-294">Se os registros de domínio não forem atualizados dentro de três dias úteis, confirme se os registros estão definidos corretamente com o registrador de domínios e entre em contato com o atendimento ao cliente.</span><span class="sxs-lookup"><span data-stu-id="380f5-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="380f5-295">Na folha **domínios personalizados** , o **estado do SSL** para o domínio é marcado `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="380f5-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="380f5-296">Selecione o link **Adicionar Associação** .</span><span class="sxs-lookup"><span data-stu-id="380f5-296">Select the **Add binding** link.</span></span> <span data-ttu-id="380f5-297">Selecione o certificado HTTPS do site do cofre de chaves para a associação de domínio personalizada.</span><span class="sxs-lookup"><span data-stu-id="380f5-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="380f5-298">No Visual Studio, abra o arquivo de configurações do aplicativo do projeto do *servidor* ( `:::no-loc(appsettings.json):::` ou `appsettings.Production.json` ).</span><span class="sxs-lookup"><span data-stu-id="380f5-298">In Visual Studio, open the *Server* project's app settings file (`:::no-loc(appsettings.json):::` or `appsettings.Production.json`).</span></span> <span data-ttu-id="380f5-299">Na :::no-loc(Identity)::: configuração do servidor, adicione a seção a seguir `Key` .</span><span class="sxs-lookup"><span data-stu-id="380f5-299">In the :::no-loc(Identity)::: Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="380f5-300">Especifique a **entidade** do certificado autoassinado para a `Name` chave.</span><span class="sxs-lookup"><span data-stu-id="380f5-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="380f5-301">No exemplo a seguir, o nome comum do certificado atribuído no cofre de chaves é `:::no-loc(Identity):::ServerSigning` , que gera um **assunto** de `CN=:::no-loc(Identity):::ServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="380f5-301">In the following example, the certificate's common name assigned in the key vault is `:::no-loc(Identity):::ServerSigning`, which yields a **Subject** of `CN=:::no-loc(Identity):::ServerSigning`:</span></span>

   ```json
   ":::no-loc(Identity):::Server": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=:::no-loc(Identity):::ServerSigning"
     }
   },
   ```

1. <span data-ttu-id="380f5-302">No Visual Studio, crie um perfil de [publicação](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) de serviço Azure app para o projeto de *servidor* .</span><span class="sxs-lookup"><span data-stu-id="380f5-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="380f5-303">Na barra de menus, selecione: **Compilar**  >  **publicar**  >  **novo**  >  **Azure**  >  **serviço de Azure app** do Azure (Windows ou Linux).</span><span class="sxs-lookup"><span data-stu-id="380f5-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="380f5-304">Quando o Visual Studio está conectado a uma assinatura do Azure, você pode definir a **exibição** de recursos do Azure por **tipo de recurso** .</span><span class="sxs-lookup"><span data-stu-id="380f5-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type** .</span></span> <span data-ttu-id="380f5-305">Navegue dentro da lista de **aplicativos Web** para localizar o serviço de aplicativo para o aplicativo e selecioná-lo.</span><span class="sxs-lookup"><span data-stu-id="380f5-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="380f5-306">Selecione **Concluir** .</span><span class="sxs-lookup"><span data-stu-id="380f5-306">Select **Finish** .</span></span>
1. <span data-ttu-id="380f5-307">Quando o Visual Studio retorna para a janela de **publicação** , as dependências do cofre de chaves e do serviço de banco de dados SQL Server são detectadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="380f5-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="380f5-308">Nenhuma alteração de configuração nas configurações padrão é necessária para o serviço de cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="380f5-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="380f5-309">Para fins de teste, o banco de dados [SQLite](https://www.sqlite.org/index.html) local de um aplicativo, que é configurado por padrão pelo :::no-loc(Blazor)::: modelo, pode ser implantado com o aplicativo sem configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="380f5-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the :::no-loc(Blazor)::: template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="380f5-310">A configuração de um banco de dados diferente para o :::no-loc(Identity)::: servidor em produção está além do escopo deste artigo.</span><span class="sxs-lookup"><span data-stu-id="380f5-310">Configuring a different database for :::no-loc(Identity)::: Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="380f5-311">Para obter mais informações, consulte os recursos de banco de dados nos seguintes conjuntos de documentação:</span><span class="sxs-lookup"><span data-stu-id="380f5-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="380f5-312">Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="380f5-313">:::no-loc(Identity)::: Servidor</span><span class="sxs-lookup"><span data-stu-id="380f5-313">:::no-loc(Identity)::: Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="380f5-314">Selecione o link **Editar** sob o nome do perfil de implantação na parte superior da janela.</span><span class="sxs-lookup"><span data-stu-id="380f5-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="380f5-315">Altere a URL de destino para a URL de domínio personalizado do site (por exemplo, `https://www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="380f5-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="380f5-316">Salve as configurações.</span><span class="sxs-lookup"><span data-stu-id="380f5-316">Save the settings.</span></span>
1. <span data-ttu-id="380f5-317">Publique o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="380f5-317">Publish the app.</span></span> <span data-ttu-id="380f5-318">O Visual Studio abre uma janela do navegador e solicita o site em seu domínio personalizado.</span><span class="sxs-lookup"><span data-stu-id="380f5-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="380f5-319">A documentação do Azure contém detalhes adicionais sobre como usar os serviços do Azure e domínios personalizados com a associação TLS no serviço de aplicativo, incluindo informações sobre como usar registros CNAME em vez de registros.</span><span class="sxs-lookup"><span data-stu-id="380f5-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="380f5-320">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="380f5-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="380f5-321">Documentação do serviço de aplicativo</span><span class="sxs-lookup"><span data-stu-id="380f5-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="380f5-322">Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="380f5-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="380f5-323">Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="380f5-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="380f5-324">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="380f5-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="380f5-325">É recomendável usar uma nova janela de navegador em particular ou Incognito para cada execução de teste de aplicativo após uma alteração no aplicativo, configuração de aplicativo ou serviços do Azure no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="380f5-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="380f5-326">:::no-loc(cookie):::Os s remanescentes de uma execução de teste anterior podem resultar em falha na autenticação ou autorização ao testar o site mesmo quando a configuração do site está correta.</span><span class="sxs-lookup"><span data-stu-id="380f5-326">Lingering :::no-loc(cookie):::s from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="380f5-327">Para obter mais informações sobre como configurar o Visual Studio para abrir uma nova janela de navegador em particular ou Incognito para cada execução de teste, consulte a seção [ :::no-loc(Cookie)::: s e dados do site](#:::no-loc(cookie):::s-and-site-data) .</span><span class="sxs-lookup"><span data-stu-id="380f5-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [:::no-loc(Cookie):::s and site data](#:::no-loc(cookie):::s-and-site-data) section.</span></span>

<span data-ttu-id="380f5-328">Quando a configuração do serviço de aplicativo é alterada na portal do Azure, as atualizações geralmente entram em vigor rapidamente, mas não são instantâneas.</span><span class="sxs-lookup"><span data-stu-id="380f5-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="380f5-329">Às vezes, você deve aguardar um curto período para que um serviço de aplicativo seja reiniciado para que uma alteração de configuração entre em vigor.</span><span class="sxs-lookup"><span data-stu-id="380f5-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="380f5-330">Se estiver solucionando um problema de carregamento de certificado, execute o seguinte comando em um shell de comando portal do Azure [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell.</span><span class="sxs-lookup"><span data-stu-id="380f5-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="380f5-331">O comando fornece uma lista de certificados que o aplicativo pode acessar do `My`  >  `CurrentUser` repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="380f5-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="380f5-332">A saída inclui entidades de certificado e impressões digitais úteis ao depurar um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="380f5-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="380f5-333">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="380f5-333">Additional resources</span></span>

* [<span data-ttu-id="380f5-334">Implantação no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="380f5-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="380f5-335">Importar um certificado do Key Vault (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="380f5-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="380f5-336">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="380f5-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
