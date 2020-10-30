---
title: Documentação da API Web do ASP.NET Core com o Swagger/OpenAPI
author: RicoSuter
description: Este tutorial fornece uma explicação de como adicionar o Swagger para gerar documentação e páginas de ajuda para um aplicativo de API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062448"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="b180a-103">Documentação da API Web do ASP.NET Core com o Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="b180a-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="b180a-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="b180a-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="b180a-105">O Swagger (OpenAPI) é uma especificação independente de linguagem para descrever as APIs REST.</span><span class="sxs-lookup"><span data-stu-id="b180a-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="b180a-106">Ele permite que computadores e seres humanos entendam os recursos de uma API REST sem acesso direto ao código-fonte.</span><span class="sxs-lookup"><span data-stu-id="b180a-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="b180a-107">Suas principais metas são:</span><span class="sxs-lookup"><span data-stu-id="b180a-107">Its main goals are to:</span></span>

* <span data-ttu-id="b180a-108">Minimize a quantidade de trabalho necessária para conectar serviços dissociados.</span><span class="sxs-lookup"><span data-stu-id="b180a-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="b180a-109">Reduza a quantidade de tempo necessária para documentar com precisão um serviço.</span><span class="sxs-lookup"><span data-stu-id="b180a-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="b180a-110">As duas implementações principais do OpenAPI para .NET são [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag), consulte:</span><span class="sxs-lookup"><span data-stu-id="b180a-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="b180a-111">Introdução com swashbuckle</span><span class="sxs-lookup"><span data-stu-id="b180a-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="b180a-112">Introdução com NSwag</span><span class="sxs-lookup"><span data-stu-id="b180a-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="b180a-113">OpenApi vs. Swagger</span><span class="sxs-lookup"><span data-stu-id="b180a-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="b180a-114">O projeto do Swagger foi donate à iniciativa OpenAPI em 2015 e já foi chamado de OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b180a-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="b180a-115">Ambos os nomes são usados de forma intercambiável.</span><span class="sxs-lookup"><span data-stu-id="b180a-115">Both names are used interchangeably.</span></span> <span data-ttu-id="b180a-116">No entanto, "OpenAPI" refere-se à especificação.</span><span class="sxs-lookup"><span data-stu-id="b180a-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="b180a-117">"Swagger" refere-se à família de produtos de software livre e comerciais do Ready smartbear que funcionam com a especificação OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b180a-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="b180a-118">Os produtos de código aberto subsequentes, como [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), também se enquadram no nome da família Swagger, apesar de não serem liberados pelo Ready smartbear.</span><span class="sxs-lookup"><span data-stu-id="b180a-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="b180a-119">Em suma:</span><span class="sxs-lookup"><span data-stu-id="b180a-119">In short:</span></span>

* <span data-ttu-id="b180a-120">OpenAPI é uma especificação.</span><span class="sxs-lookup"><span data-stu-id="b180a-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="b180a-121">O Swagger é uma ferramenta que usa a especificação OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b180a-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="b180a-122">Por exemplo, OpenAPIGenerator e SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="b180a-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="b180a-123">Especificação OpenAPI (openapi.jsem)</span><span class="sxs-lookup"><span data-stu-id="b180a-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="b180a-124">A especificação OpenAPI é um documento que descreve os recursos de sua API.</span><span class="sxs-lookup"><span data-stu-id="b180a-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="b180a-125">O documento é baseado nas anotações XML e de atributo dentro dos controladores e modelos.</span><span class="sxs-lookup"><span data-stu-id="b180a-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="b180a-126">É a parte principal do fluxo de OpenAPI e é usada para direcionar ferramentas como SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="b180a-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="b180a-127">Por padrão, ele tem o nome *openapi.jsem* .</span><span class="sxs-lookup"><span data-stu-id="b180a-127">By default, it's named *openapi.json* .</span></span> <span data-ttu-id="b180a-128">Aqui está um exemplo de uma especificação OpenAPI, reduzida para fins de brevidade:</span><span class="sxs-lookup"><span data-stu-id="b180a-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="b180a-129">IU do Swagger</span><span class="sxs-lookup"><span data-stu-id="b180a-129">Swagger UI</span></span>

<span data-ttu-id="b180a-130">A [interface do usuário do Swagger](https://swagger.io/swagger-ui/) oferece uma interface do usuário baseada na Web que fornece informações sobre o serviço, usando a especificação openapi gerada.</span><span class="sxs-lookup"><span data-stu-id="b180a-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="b180a-131">O Swashbuckle e o NSwag incluem uma versão incorporada da interface do usuário do Swagger, para que ele possa ser hospedado em seu aplicativo ASP.NET Core usando uma chamada de registro de middleware.</span><span class="sxs-lookup"><span data-stu-id="b180a-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="b180a-132">A interface do usuário da Web tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="b180a-132">The web UI looks like this:</span></span>

![IU do Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="b180a-134">Todo método de ação pública nos controladores pode ser testado da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b180a-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="b180a-135">Selecione um nome de método para expandir a seção.</span><span class="sxs-lookup"><span data-stu-id="b180a-135">Select a method name to expand the section.</span></span> <span data-ttu-id="b180a-136">Adicione todos os parâmetros necessários e selecione **experimentar!** .</span><span class="sxs-lookup"><span data-stu-id="b180a-136">Add any necessary parameters, and select **Try it out!** .</span></span>

![Teste GET de Swagger de exemplo](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="b180a-138">A versão da interface do usuário do Swagger usada para as capturas de tela é a versão 2.</span><span class="sxs-lookup"><span data-stu-id="b180a-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="b180a-139">Para obter um exemplo da versão 3, confira [Exemplo Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="b180a-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b180a-140">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b180a-140">Next steps</span></span>

* [<span data-ttu-id="b180a-141">Introdução ao Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="b180a-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="b180a-142">Introdução ao NSwag</span><span class="sxs-lookup"><span data-stu-id="b180a-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
