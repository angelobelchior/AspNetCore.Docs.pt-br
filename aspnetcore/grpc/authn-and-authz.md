---
title: Autenticação e autorização no gRPC para ASP.NET Core
author: jamesnk
description: Saiba como usar a autenticação e a autorização no gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673972"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="83103-103">Autenticação e autorização no gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83103-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="83103-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="83103-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="83103-105">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="83103-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="83103-106">Autenticar usuários chamando um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="83103-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="83103-107">gRPC pode ser usado com a [autenticação ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada chamada.</span><span class="sxs-lookup"><span data-stu-id="83103-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="83103-108">Veja a seguir um exemplo de `Startup.Configure` que usa a autenticação gRPC e ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="83103-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="83103-109">A ordem na qual você registra o ASP.NET Core de middleware de autenticação é importante.</span><span class="sxs-lookup"><span data-stu-id="83103-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="83103-110">Sempre chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` de e antes `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="83103-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="83103-111">O mecanismo de autenticação que seu aplicativo usa durante uma chamada precisa ser configurado.</span><span class="sxs-lookup"><span data-stu-id="83103-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="83103-112">A configuração de autenticação é adicionada no `Startup.ConfigureServices` e será diferente dependendo do mecanismo de autenticação usado pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="83103-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="83103-113">Para obter exemplos de como proteger aplicativos ASP.NET Core, consulte [exemplos de autenticação](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="83103-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="83103-114">Depois que a autenticação tiver sido configurada, o usuário poderá ser acessado em um método de serviço gRPC por meio do `ServerCallContext` .</span><span class="sxs-lookup"><span data-stu-id="83103-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="83103-115">Autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="83103-115">Bearer token authentication</span></span>

<span data-ttu-id="83103-116">O cliente pode fornecer um token de acesso para autenticação.</span><span class="sxs-lookup"><span data-stu-id="83103-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="83103-117">O servidor valida o token e o usa para identificar o usuário.</span><span class="sxs-lookup"><span data-stu-id="83103-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="83103-118">No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="83103-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="83103-119">No cliente .NET gRPC, o token pode ser enviado com chamadas usando a `Metadata` coleção.</span><span class="sxs-lookup"><span data-stu-id="83103-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="83103-120">As entradas na `Metadata` coleção são enviadas com uma chamada gRPC como cabeçalhos http:</span><span class="sxs-lookup"><span data-stu-id="83103-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="83103-121">Configurar `ChannelCredentials` o em um canal é uma maneira alternativa de enviar o token para o serviço com chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="83103-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="83103-122">Um `ChannelCredentials` pode incluir `CallCredentials` , que fornece uma maneira de definir automaticamente `Metadata` .</span><span class="sxs-lookup"><span data-stu-id="83103-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="83103-123">`CallCredentials` é executado cada vez que uma chamada gRPC é feita, o que evita a necessidade de escrever código em vários locais para passar o token por conta própria.</span><span class="sxs-lookup"><span data-stu-id="83103-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="83103-124">Observe que `CallCredentials` serão aplicadas somente se o canal estiver protegido com TLS.</span><span class="sxs-lookup"><span data-stu-id="83103-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="83103-125">`CallCredentials` Não são aplicadas em canais não TLS não seguros.</span><span class="sxs-lookup"><span data-stu-id="83103-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="83103-126">A credencial no exemplo a seguir configura o canal para enviar o token com cada chamada gRPC:</span><span class="sxs-lookup"><span data-stu-id="83103-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="83103-127">Autenticação de certificado do cliente</span><span class="sxs-lookup"><span data-stu-id="83103-127">Client certificate authentication</span></span>

<span data-ttu-id="83103-128">Um cliente pode, opcionalmente, fornecer um certificado de cliente para autenticação.</span><span class="sxs-lookup"><span data-stu-id="83103-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="83103-129">A [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83103-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="83103-130">Quando a solicitação entra ASP.NET Core, o [pacote de autenticação de certificado de cliente](xref:security/authentication/certauth) permite que você resolva o certificado para um `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="83103-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="83103-131">Configure o servidor para aceitar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="83103-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="83103-132">Para obter informações sobre como aceitar certificados de cliente no Kestrel, no IIS e no Azure, consulte <xref:security/authentication/certauth#configure-your-server-to-require-certificates> .</span><span class="sxs-lookup"><span data-stu-id="83103-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="83103-133">No cliente .NET gRPC, o certificado do cliente é adicionado a `HttpClientHandler` que é usado para criar o cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="83103-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="83103-134">Outros mecanismos de autenticação</span><span class="sxs-lookup"><span data-stu-id="83103-134">Other authentication mechanisms</span></span>

<span data-ttu-id="83103-135">Muitos ASP.NET Core mecanismos de autenticação com suporte funcionam com o gRPC:</span><span class="sxs-lookup"><span data-stu-id="83103-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="83103-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="83103-136">Azure Active Directory</span></span>
* <span data-ttu-id="83103-137">Certificado do Cliente</span><span class="sxs-lookup"><span data-stu-id="83103-137">Client Certificate</span></span>
* <span data-ttu-id="83103-138">IdentityServidor</span><span class="sxs-lookup"><span data-stu-id="83103-138">IdentityServer</span></span>
* <span data-ttu-id="83103-139">JWT Token</span><span class="sxs-lookup"><span data-stu-id="83103-139">JWT Token</span></span>
* <span data-ttu-id="83103-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="83103-140">OAuth 2.0</span></span>
* <span data-ttu-id="83103-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="83103-141">OpenID Connect</span></span>
* <span data-ttu-id="83103-142">O certificado do provedor de identidade do Web Services Federation</span><span class="sxs-lookup"><span data-stu-id="83103-142">WS-Federation</span></span>

<span data-ttu-id="83103-143">Para obter mais informações sobre como configurar a autenticação no servidor, consulte [ASP.NET Core autenticação](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="83103-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="83103-144">Configurar o cliente gRPC para usar a autenticação dependerá do mecanismo de autenticação que você está usando.</span><span class="sxs-lookup"><span data-stu-id="83103-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="83103-145">Os exemplos anteriores de token de portador e certificado de cliente mostram duas maneiras que o cliente gRPC pode ser configurado para enviar metadados de autenticação com chamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="83103-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="83103-146">Clientes gRPC fortemente tipados usam `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="83103-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="83103-147">A autenticação pode ser configurada em [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou adicionando instâncias de [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas ao `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="83103-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="83103-148">Cada chamada gRPC tem um `CallOptions` argumento opcional.</span><span class="sxs-lookup"><span data-stu-id="83103-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="83103-149">Cabeçalhos personalizados podem ser enviados usando a coleção de cabeçalhos da opção.</span><span class="sxs-lookup"><span data-stu-id="83103-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="83103-150">A autenticação do Windows (NTLM/Kerberos/Negotiate) não pode ser usada com gRPC.</span><span class="sxs-lookup"><span data-stu-id="83103-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="83103-151">gRPC requer HTTP/2, e HTTP/2 não dá suporte à autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="83103-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="83103-152">Autorizar usuários a acessar os serviços e métodos de serviço</span><span class="sxs-lookup"><span data-stu-id="83103-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="83103-153">Por padrão, todos os métodos em um serviço podem ser chamados por usuários não autenticados.</span><span class="sxs-lookup"><span data-stu-id="83103-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="83103-154">Para exigir autenticação, aplique o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo ao serviço:</span><span class="sxs-lookup"><span data-stu-id="83103-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="83103-155">Você pode usar os argumentos do construtor e as propriedades do `[Authorize]` atributo para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas.</span><span class="sxs-lookup"><span data-stu-id="83103-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="83103-156">Por exemplo, se você tiver uma política de autorização personalizada chamada `MyAuthorizationPolicy` , certifique-se de que somente os usuários que correspondem a essa política possam acessar o serviço usando o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="83103-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="83103-157">Os métodos de serviço individuais também podem ter o `[Authorize]` atributo aplicado.</span><span class="sxs-lookup"><span data-stu-id="83103-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="83103-158">Se o usuário atual não **corresponder às políticas aplicadas ao método** e à classe, um erro será retornado para o chamador:</span><span class="sxs-lookup"><span data-stu-id="83103-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="83103-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="83103-159">Additional resources</span></span>

* [<span data-ttu-id="83103-160">Autenticação de token de portador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83103-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="83103-161">Configurar a autenticação de certificado de cliente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83103-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
