---
title: 'Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core'
author: bradygaster
description: 'Adicione o protocolo Hub MessagePack a ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058162"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="1075c-103">Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1075c-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1075c-104">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="1075c-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="1075c-105">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="1075c-105">What is MessagePack?</span></span>

<span data-ttu-id="1075c-106">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="1075c-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="1075c-107">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="1075c-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="1075c-108">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="1075c-109">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="1075c-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="1075c-110">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="1075c-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="1075c-111">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1075c-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="1075c-112">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-113">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="1075c-113">JSON is enabled by default.</span></span> <span data-ttu-id="1075c-114">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="1075c-115">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="1075c-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="1075c-116">Nesse delegado, a `SerializerOptions` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="1075c-117">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="1075c-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="1075c-118">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="1075c-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="1075c-119">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="1075c-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="1075c-120">Por exemplo, chamar `.WithSecurity(MessagePackSecurity.UntrustedData)` ao substituir o `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="1075c-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="1075c-121">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="1075c-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-122">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="1075c-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="1075c-123">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="1075c-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="1075c-124">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="1075c-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="1075c-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1075c-125">.NET client</span></span>

<span data-ttu-id="1075c-126">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1075c-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="1075c-127">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="1075c-128">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-128">JavaScript client</span></span>

<span data-ttu-id="1075c-129">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="1075c-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="1075c-130">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1075c-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="1075c-131">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="1075c-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="1075c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="1075c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="1075c-133">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="1075c-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="1075c-134">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="1075c-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="1075c-135">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="1075c-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-136">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="1075c-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="1075c-137">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js* , ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="1075c-138">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="1075c-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="1075c-139">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="1075c-140">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1075c-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="1075c-141">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="1075c-141">MessagePack quirks</span></span>

<span data-ttu-id="1075c-142">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="1075c-143">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="1075c-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="1075c-144">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1075c-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="1075c-145">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="1075c-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="1075c-146">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="1075c-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="1075c-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1075c-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="1075c-148">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="1075c-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="1075c-149">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="1075c-150">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="1075c-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="1075c-151">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="1075c-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="1075c-152">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="1075c-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="1075c-153">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack será convertido para o formato UTC se, `DateTime.Kind` `DateTimeKind.Local` caso contrário, ele não tocará o tempo e o passará como está.</span><span class="sxs-lookup"><span data-stu-id="1075c-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="1075c-154">Se você estiver trabalhando com `DateTime` valores, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="1075c-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="1075c-155">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="1075c-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="1075c-156">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="1075c-157">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="1075c-158">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="1075c-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="1075c-159">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="1075c-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="1075c-160">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1075c-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="1075c-161">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="1075c-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="1075c-162">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="1075c-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="1075c-163">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="1075c-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="1075c-164">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="1075c-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="1075c-165">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="1075c-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="1075c-166">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="1075c-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="1075c-167">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="1075c-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="1075c-168">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="1075c-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="1075c-169">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="1075c-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="1075c-170">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="1075c-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="1075c-171">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="1075c-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="1075c-172">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="1075c-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="1075c-173">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="1075c-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="1075c-174">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="1075c-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="1075c-175">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="1075c-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="1075c-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="1075c-176">Related resources</span></span>

* [<span data-ttu-id="1075c-177">Introdução</span><span class="sxs-lookup"><span data-stu-id="1075c-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1075c-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1075c-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1075c-179">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1075c-180">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="1075c-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="1075c-181">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="1075c-181">What is MessagePack?</span></span>

<span data-ttu-id="1075c-182">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="1075c-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="1075c-183">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="1075c-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="1075c-184">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="1075c-185">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="1075c-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="1075c-186">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="1075c-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="1075c-187">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1075c-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="1075c-188">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-189">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="1075c-189">JSON is enabled by default.</span></span> <span data-ttu-id="1075c-190">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="1075c-191">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="1075c-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="1075c-192">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="1075c-193">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="1075c-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="1075c-194">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="1075c-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="1075c-195">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="1075c-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="1075c-196">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="1075c-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="1075c-197">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="1075c-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="1075c-198">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="1075c-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="1075c-199">`MessagePack` a versão 2. x introduziu alterações significativas e é incompatível com SignalR as versões 3,1 e anteriores.</span><span class="sxs-lookup"><span data-stu-id="1075c-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="1075c-200">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="1075c-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="1075c-201">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="1075c-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="1075c-202">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="1075c-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-203">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="1075c-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="1075c-204">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="1075c-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="1075c-205">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="1075c-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="1075c-206">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1075c-206">.NET client</span></span>

<span data-ttu-id="1075c-207">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1075c-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="1075c-208">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="1075c-209">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-209">JavaScript client</span></span>

<span data-ttu-id="1075c-210">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="1075c-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="1075c-211">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1075c-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="1075c-212">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="1075c-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="1075c-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="1075c-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="1075c-214">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="1075c-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="1075c-215">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="1075c-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="1075c-216">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="1075c-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-217">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="1075c-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="1075c-218">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js* , ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="1075c-219">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="1075c-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="1075c-220">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="1075c-221">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1075c-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="1075c-222">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="1075c-222">MessagePack quirks</span></span>

<span data-ttu-id="1075c-223">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="1075c-224">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="1075c-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="1075c-225">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1075c-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="1075c-226">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="1075c-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="1075c-227">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="1075c-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="1075c-228">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1075c-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="1075c-229">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="1075c-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="1075c-230">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="1075c-231">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="1075c-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="1075c-232">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="1075c-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="1075c-233">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="1075c-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="1075c-234">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="1075c-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="1075c-235">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="1075c-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="1075c-236">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="1075c-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="1075c-237">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="1075c-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="1075c-238">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="1075c-239">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="1075c-240">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="1075c-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="1075c-241">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="1075c-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="1075c-242">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1075c-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="1075c-243">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="1075c-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="1075c-244">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="1075c-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="1075c-245">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="1075c-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="1075c-246">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="1075c-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="1075c-247">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="1075c-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="1075c-248">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="1075c-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="1075c-249">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="1075c-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="1075c-250">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="1075c-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="1075c-251">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="1075c-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="1075c-252">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="1075c-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="1075c-253">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="1075c-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="1075c-254">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="1075c-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="1075c-255">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="1075c-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="1075c-256">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="1075c-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="1075c-257">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="1075c-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="1075c-258">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="1075c-258">Related resources</span></span>

* [<span data-ttu-id="1075c-259">Introdução</span><span class="sxs-lookup"><span data-stu-id="1075c-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1075c-260">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1075c-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1075c-261">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1075c-262">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="1075c-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="1075c-263">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="1075c-263">What is MessagePack?</span></span>

<span data-ttu-id="1075c-264">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="1075c-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="1075c-265">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="1075c-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="1075c-266">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="1075c-267">SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="1075c-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="1075c-268">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="1075c-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="1075c-269">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1075c-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="1075c-270">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-271">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="1075c-271">JSON is enabled by default.</span></span> <span data-ttu-id="1075c-272">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="1075c-273">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="1075c-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="1075c-274">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="1075c-275">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="1075c-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="1075c-276">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="1075c-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="1075c-277">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="1075c-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="1075c-278">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="1075c-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="1075c-279">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="1075c-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="1075c-280">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="1075c-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="1075c-281">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="1075c-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="1075c-282">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="1075c-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="1075c-283">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="1075c-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-284">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="1075c-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="1075c-285">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="1075c-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="1075c-286">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="1075c-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="1075c-287">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1075c-287">.NET client</span></span>

<span data-ttu-id="1075c-288">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1075c-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="1075c-289">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="1075c-290">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-290">JavaScript client</span></span>

<span data-ttu-id="1075c-291">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="1075c-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="1075c-292">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1075c-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="1075c-293">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="1075c-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="1075c-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="1075c-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="1075c-295">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="1075c-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="1075c-296">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="1075c-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="1075c-297">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="1075c-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="1075c-298">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="1075c-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="1075c-299">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js* , ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="1075c-300">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="1075c-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="1075c-301">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="1075c-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="1075c-302">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1075c-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="1075c-303">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="1075c-303">MessagePack quirks</span></span>

<span data-ttu-id="1075c-304">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="1075c-305">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="1075c-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="1075c-306">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1075c-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="1075c-307">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="1075c-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="1075c-308">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="1075c-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="1075c-309">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1075c-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="1075c-310">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="1075c-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="1075c-311">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="1075c-312">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="1075c-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="1075c-313">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="1075c-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="1075c-314">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="1075c-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="1075c-315">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="1075c-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="1075c-316">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="1075c-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="1075c-317">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="1075c-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="1075c-318">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="1075c-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="1075c-319">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="1075c-320">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="1075c-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="1075c-321">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="1075c-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="1075c-322">O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="1075c-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="1075c-323">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1075c-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="1075c-324">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="1075c-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="1075c-325">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="1075c-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="1075c-326">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="1075c-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="1075c-327">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="1075c-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="1075c-328">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="1075c-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="1075c-329">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="1075c-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="1075c-330">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="1075c-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="1075c-331">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="1075c-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="1075c-332">Para obter mais informações, consulte [a documentação do MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="1075c-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="1075c-333">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="1075c-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="1075c-334">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="1075c-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="1075c-335">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="1075c-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="1075c-336">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="1075c-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="1075c-337">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="1075c-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="1075c-338">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="1075c-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="1075c-339">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="1075c-339">Related resources</span></span>

* [<span data-ttu-id="1075c-340">Introdução</span><span class="sxs-lookup"><span data-stu-id="1075c-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1075c-341">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1075c-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1075c-342">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1075c-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
