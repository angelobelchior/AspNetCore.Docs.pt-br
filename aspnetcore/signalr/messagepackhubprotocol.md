---
title: Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core
author: bradygaster
description: Adicione o protocolo Hub MessagePack a ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8e590c87f75d35cbafde1adbc87dea9c45eac92d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022544"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="054fb-103">Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="054fb-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="054fb-104">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="054fb-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="054fb-105">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="054fb-105">What is MessagePack?</span></span>

<span data-ttu-id="054fb-106">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="054fb-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="054fb-107">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="054fb-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="054fb-108">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="054fb-109">SignalRtem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="054fb-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="054fb-110">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="054fb-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="054fb-111">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="054fb-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="054fb-112">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-113">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="054fb-113">JSON is enabled by default.</span></span> <span data-ttu-id="054fb-114">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="054fb-115">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="054fb-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="054fb-116">Nesse delegado, a `SerializerOptions` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="054fb-117">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="054fb-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="054fb-118">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="054fb-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="054fb-119">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="054fb-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="054fb-120">Por exemplo, chamar `.WithSecurity(MessagePackSecurity.UntrustedData)` ao substituir o `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="054fb-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="054fb-121">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="054fb-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-122">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="054fb-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="054fb-123">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="054fb-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="054fb-124">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="054fb-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="054fb-125">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="054fb-125">.NET client</span></span>

<span data-ttu-id="054fb-126">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="054fb-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="054fb-127">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="054fb-128">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-128">JavaScript client</span></span>

<span data-ttu-id="054fb-129">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="054fb-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="054fb-130">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="054fb-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="054fb-131">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="054fb-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="054fb-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="054fb-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="054fb-133">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="054fb-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="054fb-134">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="054fb-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="054fb-135">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="054fb-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-136">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="054fb-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="054fb-137">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="054fb-138">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="054fb-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="054fb-139">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="054fb-140">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="054fb-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="054fb-141">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="054fb-141">MessagePack quirks</span></span>

<span data-ttu-id="054fb-142">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="054fb-143">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="054fb-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="054fb-144">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="054fb-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="054fb-145">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="054fb-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="054fb-146">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="054fb-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="054fb-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="054fb-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="054fb-148">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="054fb-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="054fb-149">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="054fb-150">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="054fb-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="054fb-151">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="054fb-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="054fb-152">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="054fb-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="054fb-153">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack será convertido para o formato UTC se, `DateTime.Kind` `DateTimeKind.Local` caso contrário, ele não tocará o tempo e o passará como está.</span><span class="sxs-lookup"><span data-stu-id="054fb-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="054fb-154">Se você estiver trabalhando com `DateTime` valores, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="054fb-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="054fb-155">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="054fb-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="054fb-156">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="054fb-157">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="054fb-158">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="054fb-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="054fb-159">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="054fb-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="054fb-160">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="054fb-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="054fb-161">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="054fb-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="054fb-162">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="054fb-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="054fb-163">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="054fb-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="054fb-164">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="054fb-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="054fb-165">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="054fb-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="054fb-166">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="054fb-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="054fb-167">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="054fb-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="054fb-168">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="054fb-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="054fb-169">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="054fb-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="054fb-170">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="054fb-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="054fb-171">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="054fb-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="054fb-172">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="054fb-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="054fb-173">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="054fb-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="054fb-174">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="054fb-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="054fb-175">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="054fb-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="054fb-176">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="054fb-176">Related resources</span></span>

* [<span data-ttu-id="054fb-177">Introdução</span><span class="sxs-lookup"><span data-stu-id="054fb-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="054fb-178">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="054fb-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="054fb-179">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="054fb-180">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="054fb-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="054fb-181">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="054fb-181">What is MessagePack?</span></span>

<span data-ttu-id="054fb-182">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="054fb-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="054fb-183">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="054fb-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="054fb-184">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="054fb-185">SignalRtem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="054fb-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="054fb-186">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="054fb-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="054fb-187">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="054fb-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="054fb-188">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-189">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="054fb-189">JSON is enabled by default.</span></span> <span data-ttu-id="054fb-190">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="054fb-191">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="054fb-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="054fb-192">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="054fb-193">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="054fb-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="054fb-194">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="054fb-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="054fb-195">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="054fb-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="054fb-196">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="054fb-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="054fb-197">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="054fb-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="054fb-198">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="054fb-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="054fb-199">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="054fb-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="054fb-200">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="054fb-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="054fb-201">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="054fb-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-202">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="054fb-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="054fb-203">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="054fb-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="054fb-204">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="054fb-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="054fb-205">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="054fb-205">.NET client</span></span>

<span data-ttu-id="054fb-206">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="054fb-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="054fb-207">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="054fb-208">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-208">JavaScript client</span></span>

<span data-ttu-id="054fb-209">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="054fb-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="054fb-210">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="054fb-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="054fb-211">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="054fb-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="054fb-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="054fb-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="054fb-213">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="054fb-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="054fb-214">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="054fb-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="054fb-215">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="054fb-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-216">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="054fb-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="054fb-217">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="054fb-218">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="054fb-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="054fb-219">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="054fb-220">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="054fb-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="054fb-221">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="054fb-221">MessagePack quirks</span></span>

<span data-ttu-id="054fb-222">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="054fb-223">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="054fb-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="054fb-224">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="054fb-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="054fb-225">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="054fb-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="054fb-226">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="054fb-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="054fb-227">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="054fb-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="054fb-228">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="054fb-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="054fb-229">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="054fb-230">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="054fb-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="054fb-231">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="054fb-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="054fb-232">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="054fb-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="054fb-233">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="054fb-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="054fb-234">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="054fb-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="054fb-235">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="054fb-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="054fb-236">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="054fb-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="054fb-237">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="054fb-238">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="054fb-239">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="054fb-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="054fb-240">O valor de `DateTime.MinValue` é `January 1, 0001` , que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="054fb-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="054fb-241">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="054fb-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="054fb-242">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="054fb-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="054fb-243">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="054fb-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="054fb-244">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="054fb-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="054fb-245">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="054fb-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="054fb-246">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="054fb-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="054fb-247">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="054fb-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="054fb-248">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="054fb-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="054fb-249">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="054fb-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="054fb-250">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="054fb-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="054fb-251">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="054fb-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="054fb-252">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="054fb-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="054fb-253">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="054fb-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="054fb-254">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="054fb-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="054fb-255">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="054fb-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="054fb-256">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="054fb-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="054fb-257">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="054fb-257">Related resources</span></span>

* [<span data-ttu-id="054fb-258">Introdução</span><span class="sxs-lookup"><span data-stu-id="054fb-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="054fb-259">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="054fb-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="054fb-260">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="054fb-261">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="054fb-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="054fb-262">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="054fb-262">What is MessagePack?</span></span>

<span data-ttu-id="054fb-263">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária rápida e compacto.</span><span class="sxs-lookup"><span data-stu-id="054fb-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="054fb-264">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="054fb-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="054fb-265">As mensagens binárias são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="054fb-266">SignalRtem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="054fb-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="054fb-267">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="054fb-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="054fb-268">Para habilitar o protocolo de Hub do MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="054fb-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="054fb-269">No `Startup.ConfigureServices` método, adicione `AddMessagePackProtocol` à `AddSignalR` chamada para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-270">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="054fb-270">JSON is enabled by default.</span></span> <span data-ttu-id="054fb-271">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="054fb-272">Para personalizar como o MessagePack formatará seus dados, o `AddMessagePackProtocol` usa um delegado para configurar opções.</span><span class="sxs-lookup"><span data-stu-id="054fb-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="054fb-273">Nesse delegado, a `FormatterResolvers` propriedade pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="054fb-274">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="054fb-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="054fb-275">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="054fb-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="054fb-276">É altamente recomendável revisar [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) e aplicar os patches recomendados.</span><span class="sxs-lookup"><span data-stu-id="054fb-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="054fb-277">Por exemplo, definir a `MessagePackSecurity.Active` propriedade estática como `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="054fb-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="054fb-278">Definir o `MessagePackSecurity.Active` requer a instalação manual [de uma versão 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="054fb-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="054fb-279">`MessagePack`A instalação das atualizações 1.9. x é usada pela versão SignalR .</span><span class="sxs-lookup"><span data-stu-id="054fb-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="054fb-280">Quando `MessagePackSecurity.Active` não está definido como `MessagePackSecurity.UntrustedData` , um cliente mal-intencionado pode causar uma negação de serviço.</span><span class="sxs-lookup"><span data-stu-id="054fb-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="054fb-281">Defina `MessagePackSecurity.Active` em `Program.Main` , conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="054fb-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="054fb-282">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="054fb-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-283">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="054fb-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="054fb-284">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="054fb-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="054fb-285">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="054fb-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="054fb-286">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="054fb-286">.NET client</span></span>

<span data-ttu-id="054fb-287">Para habilitar o MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame-o `AddMessagePackProtocol` em `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="054fb-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="054fb-288">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="054fb-289">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-289">JavaScript client</span></span>

<span data-ttu-id="054fb-290">O suporte do MessagePack para o cliente JavaScript é fornecido pelo [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="054fb-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="054fb-291">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="054fb-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="054fb-292">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="054fb-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="054fb-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="054fb-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="054fb-294">Em um navegador, a `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="054fb-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="054fb-295">Use uma `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="054fb-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="054fb-296">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="054fb-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="054fb-297">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="054fb-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="054fb-298">Se *signalr-protocol-msgpack.js* for referenciado antes de *msgpack5.js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="054fb-299">*signalr.js* também é necessário antes de *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="054fb-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="054fb-300">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="054fb-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="054fb-301">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="054fb-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="054fb-302">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="054fb-302">MessagePack quirks</span></span>

<span data-ttu-id="054fb-303">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="054fb-304">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="054fb-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="054fb-305">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="054fb-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="054fb-306">Por exemplo, considere a seguinte classe C#:</span><span class="sxs-lookup"><span data-stu-id="054fb-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="054fb-307">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e minúsculas devem corresponder exatamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="054fb-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="054fb-308">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="054fb-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="054fb-309">O uso de `camelCased` nomes não se associará corretamente à classe C#.</span><span class="sxs-lookup"><span data-stu-id="054fb-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="054fb-310">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="054fb-311">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="054fb-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="054fb-312">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="054fb-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="054fb-313">O protocolo MessagePack não fornece uma maneira de codificar o `Kind` valor de a `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="054fb-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="054fb-314">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="054fb-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="054fb-315">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="054fb-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="054fb-316">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="054fb-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="054fb-317">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="054fb-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="054fb-318">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="054fb-319">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo SignalR cliente JavaScript não dá suporte ao `timestamp96` tipo em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="054fb-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="054fb-320">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="054fb-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="054fb-321">O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="054fb-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="054fb-322">Por isso, `DateTime.MinValue` não há suporte para enviar para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="054fb-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="054fb-323">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="054fb-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="054fb-324">Normalmente, `DateTime.MinValue` é usado para codificar um "ausente" ou um `null` valor.</span><span class="sxs-lookup"><span data-stu-id="054fb-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="054fb-325">Se você precisar codificar esse valor em MessagePack, use um valor Anulável `DateTime` ( `DateTime?` ) ou codifique um valor separado `bool` indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="054fb-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="054fb-326">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="054fb-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="054fb-327">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="054fb-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="054fb-328">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="054fb-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="054fb-329">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="054fb-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="054fb-330">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="054fb-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="054fb-331">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="054fb-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="054fb-332">Depois de gerar os serializadores gerados previamente, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="054fb-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="054fb-333">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="054fb-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="054fb-334">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="054fb-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="054fb-335">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número ( `{ foo: 42 }` ), mas a propriedade na classe .NET for do tipo `string` , o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="054fb-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="054fb-336">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="054fb-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="054fb-337">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="054fb-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="054fb-338">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="054fb-338">Related resources</span></span>

* [<span data-ttu-id="054fb-339">Introdução</span><span class="sxs-lookup"><span data-stu-id="054fb-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="054fb-340">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="054fb-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="054fb-341">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="054fb-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
