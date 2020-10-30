---
title: Criar mensagens Protobuf para aplicativos .NET
author: jamesnk
description: Saiba como criar mensagens Protobuf para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: b70a5ee00405eecfce900b86dc631a54682dce1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058890"
---
# <a name="create-protobuf-messages-for-net-apps"></a>Criar mensagens Protobuf para aplicativos .NET

Por [James Newton – King](https://twitter.com/jamesnk) e [Mark Rendle](https://twitter.com/markrendle)

gRPC usa [Protobuf](https://developers.google.com/protocol-buffers) como sua IDL (linguagem de definição de interface). Protobuf IDL é um formato de linguagem neutra para especificar as mensagens enviadas e recebidas pelos serviços do gRPC. As mensagens Protobuf são definidas em `.proto` arquivos. Este documento explica como os conceitos do Protobuf são mapeados para o .NET.

## <a name="protobuf-messages"></a>Mensagens de Protobuf

As mensagens são o objeto principal de transferência de dados no Protobuf. Elas são conceitualmente semelhantes às classes do .NET.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

A definição de mensagem anterior especifica três campos como pares de nome-valor. Como as propriedades em tipos .NET, cada campo tem um nome e um tipo. O tipo de campo pode ser um [tipo de valor escalar Protobuf](#scalar-value-types), por exemplo `int32` , ou outra mensagem.

Além de um nome, cada campo na definição de mensagem tem um número exclusivo. Os números de campo são usados para identificar campos quando a mensagem é serializada para Protobuf. A serialização de um pequeno número é mais rápida do que a serialização do nome inteiro do campo. Como os números de campo identificam um campo, é importante tomar cuidado ao alterá-los. Para obter mais informações sobre como alterar mensagens Protobuf, consulte <xref:grpc/versioning> .

Quando um aplicativo é criado, as ferramentas Protobuf geram tipos .NET de `.proto` arquivos. A `Person` mensagem gera uma classe .net:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Para obter mais informações sobre mensagens Protobuf, consulte o [Guia de idiomas do Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Tipos de valor escalar

Protobuf dá suporte a um intervalo de tipos de valor escalar nativos. A tabela a seguir lista todos eles com seu tipo C# equivalente:

| Tipo de Protobuf | Tipo de C#      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

Os valores escalares sempre têm um valor padrão e não podem ser definidos como `null` . Essa restrição inclui `string` e `ByteString` quais são classes C#. `string` o padrão é um valor de cadeia de caracteres vazia e `ByteString` o padrão é um valor de bytes vazio. A tentativa de defini-las para `null` gera um erro.

[Tipos de wrapper anuláveis](#nullable-types) podem ser usados para dar suporte a valores nulos.

### <a name="dates-and-times"></a>Datas e horas

Os tipos escalares nativos não fornecem valores de data e hora, equivalentes a. NET <xref:System.DateTimeOffset> , <xref:System.DateTime> , e <xref:System.TimeSpan> . Esses tipos podem ser especificados usando algumas das extensões de *tipos bem conhecidas* do Protobuf. Essas extensões fornecem geração de código e suporte a tempo de execução para tipos de campo complexos em plataformas com suporte.

A tabela a seguir mostra os tipos de data e hora:

| Tipo .NET        | Tipo de Well-Known Protobuf    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

As propriedades geradas na classe C# não são os tipos de data e hora do .NET. As propriedades usam as `Timestamp` `Duration` classes e no `Google.Protobuf.WellKnownTypes` namespace. Essas classes fornecem métodos para converter de e para `DateTimeOffset` , `DateTime` e `TimeSpan` .

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> O `Timestamp` tipo funciona com horas UTC. `DateTimeOffset` os valores sempre têm um deslocamento de zero e a `DateTime.Kind` propriedade é sempre `DateTimeKind.Utc` .

### <a name="nullable-types"></a>Tipos anuláveis

A geração de código Protobuf para C# usa os tipos nativos, como `int` para `int32` . Portanto, os valores são sempre incluídos e não podem ser `null` .

Para valores que exigem explícito `null` , como o uso `int?` de em código c#, os tipos de Well-Known do Protobuf incluem wrappers que são compilados para tipos de C# anuláveis. Para usá-los, importe `wrappers.proto` para o `.proto` arquivo, como o código a seguir:

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

`wrappers.proto` os tipos não são expostos em Propriedades geradas. O Protobuf os mapeia automaticamente para os tipos anuláveis do .NET apropriados em mensagens C#. Por exemplo, um `google.protobuf.Int32Value` campo gera uma `int?` propriedade. Propriedades de tipo de referência como `string` e `ByteString` são inalteradas, exceto `null` que podem ser atribuídas a elas sem erro.

A tabela a seguir mostra a lista completa de tipos de wrapper com seu tipo C# equivalente:

| Tipo de C#      | Wrapper de tipo de Well-Known       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a>Bytes

Há suporte para cargas binárias em Protobuf com o `bytes` tipo de valor escalar. Uma propriedade gerada em C# usa `ByteString` como o tipo de propriedade.

Use `ByteString.CopyFrom(byte[] data)` para criar uma nova instância de uma matriz de bytes:

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

`ByteString` os dados são acessados diretamente usando o `ByteString.Span` ou o `ByteString.Memory` . Ou chame `ByteString.ToByteArray()` para converter uma instância de volta em uma matriz de bytes:

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>Decimais

O Protobuf não dá suporte nativo ao `decimal` tipo .net, apenas `double` e `float` . Há uma discussão em andamento no projeto Protobuf sobre a possibilidade de adicionar um tipo decimal padrão aos tipos de Well-Known, com suporte de plataforma para linguagens e estruturas que dão suporte a ele. Nada foi implementado ainda.

É possível criar uma definição de mensagem para representar o `decimal` tipo que funciona para a serialização segura entre clientes e servidores .net. Mas os desenvolvedores de outras plataformas teriam que entender o formato que está sendo usado e implementar sua própria manipulação.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Criando um tipo decimal personalizado para Protobuf

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

O `nanos` campo representa valores de `0.999_999_999` a `-0.999_999_999` . Por exemplo, o `decimal` valor `1.5m` seria representado como `{ units = 1, nanos = 500_000_000 }` . É por isso que o `nanos` campo neste exemplo usa o `sfixed32` tipo, que codifica com mais eficiência do que `int32` para valores maiores. Se o `units` campo for negativo, o `nanos` campo também deverá ser negativo.

> [!NOTE]
> Há vários outros algoritmos para codificar `decimal` valores como cadeias de caracteres de byte, mas essa mensagem é mais fácil de entender do que qualquer um deles. Os valores não são afetados por big-endian ou little-endian em plataformas diferentes.

A conversão entre esse tipo e o `decimal` tipo de BCL pode ser implementada em C# da seguinte maneira:

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a>Coleções

### <a name="lists"></a>Listas

As listas em Protobuf são especificadas usando a `repeated` palavra-chave prefix em um campo. O exemplo a seguir mostra como criar uma lista:

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

No código gerado, `repeated` os campos são representados pelo `Google.Protobuf.Collections.RepeatedField<T>` tipo genérico.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` implementa <xref:System.Collections.Generic.IList%601>. Portanto, você pode usar consultas LINQ ou convertê-la em uma matriz ou em uma lista. `RepeatedField<T>` as propriedades não têm um setter público. Os itens devem ser adicionados à coleção existente.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Dicionários

O <xref:System.Collections.Generic.IDictionary%602> tipo .net é representado em Protobuf usando `map<key_type, value_type>` .

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

No código .NET gerado, `map` os campos são representados pelo `Google.Protobuf.Collections.MapField<TKey, TValue>` tipo genérico. `MapField<TKey, TValue>` implementa <xref:System.Collections.Generic.IDictionary%602>. Como `repeated` Propriedades, `map` as propriedades não têm um setter público. Os itens devem ser adicionados à coleção existente.

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a>Mensagens não estruturadas e condicionais

Protobuf é um formato de mensagens do primeiro contrato. As mensagens de um aplicativo, incluindo seus campos e tipos, devem ser especificadas em `.proto` arquivos quando o aplicativo é compilado. O primeiro design de contrato do Protobuf é ótimo na imposição do conteúdo da mensagem, mas pode limitar os cenários em que um contrato estrito não é necessário:

* Mensagens com cargas desconhecidas. Por exemplo, uma mensagem com um campo que pode conter qualquer mensagem.
* Mensagens condicionais. Por exemplo, uma mensagem retornada de um serviço gRPC pode ser um resultado de êxito ou um resultado de erro.
* Valores dinâmicos. Por exemplo, uma mensagem com um campo que contém uma coleção não estruturada de valores, semelhante ao JSON.

O Protobuf oferece recursos e tipos de linguagem para dar suporte a esses cenários.

### <a name="any"></a>Qualquer

O `Any` tipo permite que você use mensagens como tipos inseridos sem ter sua `.proto` definição. Para usar o `Any` tipo, importe `any.proto` .

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a>Oneof

`oneof` os campos são um recurso de idioma. O compilador manipula a `oneof` palavra-chave quando gera a classe da mensagem. Usando `oneof` para especificar uma mensagem de resposta que poderia retornar `Person` ou `Error` pode ter esta aparência:

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

Os campos dentro do `oneof` conjunto devem ter números de campo exclusivos na declaração de mensagem geral.

Ao usar `oneof` o, o código C# gerado inclui uma enumeração que especifica quais dos campos foram definidos. Você pode testar a enumeração para localizar qual campo está definido. Campos que não são definidos como Return `null` ou o valor padrão, em vez de lançar uma exceção.

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a>Valor

O `Value` tipo representa um valor digitado dinamicamente. Pode ser `null` , um número, uma cadeia de caracteres, um booliano, um dicionário de valores ( `Struct` ) ou uma lista de valores ( `ValueList` ). `Value` é um tipo de Well-Known Protobuf que usa o recurso discutido anteriormente `oneof` . Para usar o `Value` tipo, importe `struct.proto` .

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

Usar `Value` diretamente pode ser detalhado. Uma maneira alternativa de usar o `Value` é com o suporte interno do Protobuf para mapear mensagens para JSON. `JsonFormatter` `JsonWriter` Os tipos e Protobuf podem ser usados com qualquer mensagem Protobuf. `Value` é particularmente adequado para ser convertido de e para JSON.

Esse é o equivalente JSON do código anterior:

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>Recursos adicionais

* [Guia de linguagem Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
