---
title: Documentação da API Web do ASP.NET Core com o Swagger/OpenAPI
author: RicoSuter
description: Este tutorial fornece uma explicação de como adicionar o Swagger para gerar documentação e páginas de ajuda para um aplicativo de API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062448"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Documentação da API Web do ASP.NET Core com o Swagger/OpenAPI

Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)

O Swagger (OpenAPI) é uma especificação independente de linguagem para descrever as APIs REST. Ele permite que computadores e seres humanos entendam os recursos de uma API REST sem acesso direto ao código-fonte. Suas principais metas são:

* Minimize a quantidade de trabalho necessária para conectar serviços dissociados.
* Reduza a quantidade de tempo necessária para documentar com precisão um serviço.

As duas implementações principais do OpenAPI para .NET são [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag), consulte:

* [Introdução com swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introdução com NSwag](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi vs. Swagger

O projeto do Swagger foi donate à iniciativa OpenAPI em 2015 e já foi chamado de OpenAPI. Ambos os nomes são usados de forma intercambiável. No entanto, "OpenAPI" refere-se à especificação. "Swagger" refere-se à família de produtos de software livre e comerciais do Ready smartbear que funcionam com a especificação OpenAPI. Os produtos de código aberto subsequentes, como [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), também se enquadram no nome da família Swagger, apesar de não serem liberados pelo Ready smartbear.

Em suma:

* OpenAPI é uma especificação.
* O Swagger é uma ferramenta que usa a especificação OpenAPI. Por exemplo, OpenAPIGenerator e SwaggerUI.

## <a name="openapi-specification-openapijson"></a>Especificação OpenAPI (openapi.jsem)

A especificação OpenAPI é um documento que descreve os recursos de sua API. O documento é baseado nas anotações XML e de atributo dentro dos controladores e modelos. É a parte principal do fluxo de OpenAPI e é usada para direcionar ferramentas como SwaggerUI. Por padrão, ele tem o nome *openapi.jsem* . Aqui está um exemplo de uma especificação OpenAPI, reduzida para fins de brevidade:

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

## <a name="swagger-ui"></a>IU do Swagger

A [interface do usuário do Swagger](https://swagger.io/swagger-ui/) oferece uma interface do usuário baseada na Web que fornece informações sobre o serviço, usando a especificação openapi gerada. O Swashbuckle e o NSwag incluem uma versão incorporada da interface do usuário do Swagger, para que ele possa ser hospedado em seu aplicativo ASP.NET Core usando uma chamada de registro de middleware. A interface do usuário da Web tem esta aparência:

![IU do Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Todo método de ação pública nos controladores pode ser testado da interface do usuário. Selecione um nome de método para expandir a seção. Adicione todos os parâmetros necessários e selecione **experimentar!** .

![Teste GET de Swagger de exemplo](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> A versão da interface do usuário do Swagger usada para as capturas de tela é a versão 2. Para obter um exemplo da versão 3, confira [Exemplo Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introdução ao NSwag](xref:tutorials/get-started-with-nswag)
