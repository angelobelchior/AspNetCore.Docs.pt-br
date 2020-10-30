---
title: Por que migrar o WCF para o ASP.NET Core gRPC
author: markrendle
description: Este artigo fornece um resumo de por que ASP.NET Core gRPC é uma boa opção para desenvolvedores de Windows Communication Foundation (WCF) que desejam migrar para arquiteturas e plataformas modernas.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058682"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC para desenvolvedores de Windows Communication Foundation (WCF)

Este artigo fornece um resumo de por que ASP.NET Core gRPC é uma boa opção para desenvolvedores de Windows Communication Foundation (WCF) que desejam migrar para arquiteturas e plataformas modernas.

## <a name="comparison-to-wcf"></a>Comparação com o WCF

Embora a implementação e a abordagem sejam diferentes para gRPC, a experiência de desenvolvimento e consumo de serviços com o gRPC deve ser intuitiva para desenvolvedores do WCF. O WCF e o gRPC são estruturas RPC (chamada de procedimento remoto) com os mesmos objetivos:

* Torne possível codificar como se o cliente e o servidor estivessem na mesma plataforma.
* Forneça uma API de rede portátil simplificada.

Ambas as plataformas compartilham a necessidade de declarar e implementar uma interface, embora o processo para declarar a interface seja diferente. Os muitos tipos de chamadas RPC que o gRPC dá suporte ao MAP bem para as associações disponíveis para os serviços WCF. Para obter mais informações e exemplos, consulte [migrar uma solução WCF para gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Benefícios do gRPC

o gRPC fornece uma estrutura melhor do que outras abordagens pelos seguintes motivos.

### <a name="performance"></a>Desempenho

gRPC usa HTTP/2. Em contraste com HTTP/1.1, HTTP/2:

* É um protocolo binário menor e mais rápido.
* É mais eficiente para os computadores analisarem.
* Dá suporte à multiplexação de solicitações em uma única conexão. A multiplexação permite que várias solicitações sejam enviadas em uma conexão sem que as solicitações sejam bloqueadas. No HTTP/1.1, o bloqueio é conhecido como "bloqueio de cabeçote de linha (Chol)".

gRPC usa Protobuf, um formato binário eficiente, para serializar mensagens. As mensagens Protobuf são:
* Rápido de serializar e desserializar.
* Use menos largura de banda do que os formatos baseados em texto. 

o gRPC é uma boa solução para dispositivos móveis e redes com restrições de largura de banda.

### <a name="interoperability"></a>Interoperabilidade

Há ferramentas e bibliotecas de gRPC para todas as principais linguagens de programação e plataformas, incluindo .NET, Java, Python, go, C++, Node.js, Swift, Dart, Ruby e PHP. Graças ao formato de conexão binária Protobuf e à geração de código eficiente para cada plataforma, os desenvolvedores podem criar aplicativos de multiplataforma e com desempenho cruzado.

### <a name="usability-and-productivity"></a>Usabilidade e produtividade

gRPC é uma solução RPC abrangente. Ele funciona consistentemente em vários idiomas e plataformas. Ele também fornece ferramentas excelentes, com grande parte do código clichê gerado automaticamente. Como o WCF, o gRPC gera automaticamente mensagens e um cliente com rigidez de tipos. O tempo do desenvolvedor é liberado para se concentrar na lógica de negócios.

### <a name="streaming"></a>Streaming

o gRPC tem streaming bidirecional completo, que fornece funcionalidade semelhante aos serviços Full duplex do WCF. o streaming gRPC pode operar em conexões de Internet regulares, balanceadores de carga e malhas de serviço.

### <a name="deadlines-timeouts-and-cancellation"></a>Prazos, tempos limite e cancelamento

gRPC permite que os clientes especifiquem um tempo máximo para que um RPC seja concluído. Se o prazo especificado for excedido, o servidor poderá cancelar a operação independentemente do cliente. Os prazos finais e cancelamentos podem ser propagados por meio de chamadas gRPC subsequentes para ajudar a impor limites de uso de recursos. Os clientes podem parar as operações quando um prazo é excedido ou antes, se necessário. Por exemplo, os clientes podem parar as operações devido a uma interação do usuário.

### <a name="security"></a>Segurança

gRPC pode usar TLS e HTTP/2 para fornecer uma conexão criptografada de ponta a ponta entre o cliente e o servidor. O suporte para autenticação de certificado de cliente aumenta ainda mais a segurança e a confiança entre o cliente e o servidor.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC como um caminho de migração para WCF para .NET Core e .NET 5

O .NET Core e o .NET 5 marcam uma mudança no modo como a Microsoft oferece soluções de comunicação remota para os desenvolvedores que desejam fornecer serviços em uma variedade de plataformas. O .NET Core e o .NET 5 oferecem suporte à [chamada de serviços WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), mas não oferecem suporte ao lado do servidor para hospedar o WCF.

Há dois caminhos recomendados para modernizar aplicativos WCF:

* o gRPC é criado em tecnologias modernas e surgiu como a escolha mais popular em toda a comunidade de desenvolvedores para aplicativos RPC. A partir do .NET Core 3,0, as plataformas .NET modernas têm suporte excelente para gRPC. Migrar serviços WCF para usar o gRPC ajuda a fornecer os recursos de RPC, o desempenho, uma interoperabilidade necessária em aplicativos modernos.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) é um esforço da Comunidade para dar suporte à Hospedagem de serviços WCF para .NET Core e .NET 5. Uma versão de visualização está disponível e o projeto está funcionando para ser pronto para produção. O CoreWCF só dá suporte a um subconjunto dos recursos do WCF e .NET Framework aplicativos que migram para usá-lo precisarão de alterações de código e testes para serem bem-sucedidos. CoreWCF é uma boa opção se um aplicativo precisa manter a compatibilidade com os clientes existentes que chamam os serviços WCF.

## <a name="get-started"></a>Introdução

Para obter orientações detalhadas sobre a criação de serviços gRPCs em ASP.NET Core para desenvolvedores do WCF, consulte [ASP.NET Core gRPC para desenvolvedores do WCF](/dotnet/architecture/grpc-for-wcf-developers)
