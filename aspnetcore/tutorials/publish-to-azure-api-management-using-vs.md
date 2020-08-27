---
title: Publicar uma API da Web do ASP.NET Core no gerenciamento de API do Azure com o Visual Studio
author: codemillmatt
description: Saiba como publicar uma API da Web do ASP.NET Core no gerenciamento de API do Azure usando o Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945633"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Publicar uma API da Web do ASP.NET Core no gerenciamento de API do Azure com o Visual Studio

Por [Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>Configuração

- Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.
- [Crie uma nova instância de gerenciamento de API do Azure](/azure/api-management/get-started-create-service-instance) , se ainda não tiver feito isso.
- [Crie um projeto de aplicativo de API Web](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Configurar o aplicativo

Adicionar definições de Swagger à API Web do ASP.NET Core permite que o gerenciamento de API do Azure Leia as definições de API do aplicativo. Conclua as etapas a seguir.

### <a name="add-swagger"></a>Adicionar Swagger

1. Adicione o pacote NuGet [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) ao projeto de API Web ASP.NET Core:

    ![Captura de tela para configurar a caixa de diálogo NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Adicione a seguinte linha ao método `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Adicione a seguinte linha ao método `Startup.Configure`:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Alterar o roteamento de API

Em seguida, você alterará a estrutura de URL necessária para acessar a `Get` ação do `WeatherForecastController` . Concluir as seguintes etapas:

1. Abra o arquivo *WeatherForecastController.cs* .
1. Exclua o `[Route("[controller]")]` atributo de nível de classe. A definição de classe se parecerá com o seguinte:

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Adicione um `[Route("/")]` atributo à `Get()` ação. A definição da função será parecida com a seguinte:

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Publicar a API da Web para o serviço Azure App

Conclua as etapas a seguir para publicar a API Web do ASP.NET Core no gerenciamento de API do Azure:

1. Publique o aplicativo de API para Azure App serviço.
1. Adicione uma API em branco à instância do serviço de gerenciamento de API do Azure.
1. Publique o aplicativo de API Web do ASP.NET Core na instância do serviço de gerenciamento de API do Azure.

### <a name="publish-the-api-app-to-azure-app-service"></a>Publicar o aplicativo de API no serviço Azure App

Conclua as etapas a seguir para publicar a API Web do ASP.NET Core no gerenciamento de API do Azure:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **publicar**:

    ![Menu contextual aberto com o link Publicar realçado](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. Na caixa de diálogo **publicar** , selecione **Azure** e selecione o botão **Avançar** :

    ![Caixa de diálogo Publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Selecione **serviço de Azure app (Windows)** e selecione o botão **Avançar** :

    ![Diálogo de publicação: selecione serviço de aplicativo](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Selecione **criar um novo serviço de Azure app**.

    ![Diálogo de publicação: selecione instância de serviço do Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    A caixa de diálogo **Criar Serviço de Aplicativo** é exibida. Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados. Você pode manter esses nomes ou alterá-los.
1. Selecione o botão **Criar**.

    ![Criar caixa de diálogo do Serviço de Aplicativo](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Após a conclusão da criação, a caixa de diálogo é fechada automaticamente e a caixa de diálogo **publicar** fica em foco novamente. A instância que foi criada é selecionada automaticamente.

![Diálogo de publicação: selecione instância do serviço de aplicativo](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

Neste ponto, você precisa adicionar uma API ao serviço de gerenciamento de API do Azure. Deixe o Visual Studio aberto enquanto você conclui as tarefas a seguir.

### <a name="add-an-api-to-azure-api-management"></a>Adicionar uma API ao gerenciamento de API do Azure

1. Abra a instância do serviço de gerenciamento de API criada anteriormente no portal do Azure. Selecione a folha **APIs** :

    ![Folha de APIs selecionada na instância do serviço de gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. No painel **Adicionar uma nova API** , selecione o bloco **API em branco** :

    ![Tela mostrando o bloco de API em branco realçado](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Insira os seguintes valores na caixa de diálogo **criar uma API em branco** que aparece:    

    - **Nome de exibição**: *WeatherForecasts*
    - **Nome**: *WeatherForecasts*
    - **Sufixo de URL da API**: *v1*
    - Deixe o campo **URL do serviço Web** vazio.

1. Selecione o botão **Criar**.

    ![Captura de tela da caixa de diálogo criar uma API em branco concluída](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

A API em branco é criada.

![Captura de tela de uma API em branco no portal de gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Publicar a API Web do ASP.NET Core no gerenciamento de API do Azure

1. Retorne ao Visual Studio. A caixa de diálogo de **publicação** ainda deve estar aberta onde você parou antes.
1. Selecione o serviço de Azure App publicado recentemente para que ele seja realçado.
1. Selecione o botão **Avançar**.

    ![Captura de tela da caixa de diálogo Publicar com o serviço de aplicativo selecionado](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. A caixa de diálogo agora mostra o serviço de gerenciamento de API do Azure criado anteriormente. Expanda-o e a pasta *APIs* para ver a API em branco que você criou.
1. Selecione o nome da API em branco e selecione o botão **concluir** .

    ![Captura de tela da API em branco do gerenciamento de API do Azure recém-criada selecionada na caixa de diálogo publicar](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. A caixa de diálogo é fechada e uma tela de resumo é exibida com informações sobre a publicação. Clique no botão **Publicar**.

    ![Captura de tela do Visual Studio com o perfil de publicação exibido](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    A API da Web será publicada no serviço de Azure App e no gerenciamento de API do Azure. Uma nova janela do navegador será exibida e mostrará a API em execução no serviço Azure App. Você pode fechar essa janela.

1. Volte para a instância de gerenciamento de API do Azure no portal do Azure.
1. Atualize a janela do navegador.
1. Selecione a API em branco que você criou nas etapas anteriores. Agora ele está populado e você pode explorar.

    ![Captura de tela da API preenchida no gerenciamento de API do Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Configurar o nome da API publicada

Observe que o nome da API é diferente do que você o nomeou. A API publicada é denominada *WeatherAPI*; no entanto, você o nomeou como *WeatherForecasts* quando a criou. Conclua as seguintes etapas para corrigir o nome:

![Captura de tela da API preenchida com nomes diferentes realçadas](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Exclua a seguinte linha do `Startup.ConfigureServices` método:
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Adicione o seguinte código ao método `Startup.ConfigureServices`:
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. Abra o perfil de publicação recém-criado. Ele pode ser encontrado em **Gerenciador de soluções** na pasta *Properties/PublishProfiles* .

    ![Captura de tela mostrando o local do arquivo de perfil de publicação realçado](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Altere o `<OpenAPIDocumentName>` valor do elemento de `v1` para `WeatherForecasts` .

    ![captura de tela das alterações necessárias para o perfil de publicação](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Republique a API Web do ASP.NET Core e abra a instância do gerenciamento de API do Azure no portal do Azure.
1. Atualize a página em seu navegador. Você verá o nome da API agora está correto.

    ![Captura de tela da API concluída no portal](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Verifique se a API Web está funcionando

Você pode testar a API da Web ASP.NET Core implantada no gerenciamento de API do Azure por meio do portal do Azure com as seguintes etapas:

1. Abra a guia **Testar**.
1. Selecione **/** ou a operação **Get** .
1. Selecione **Enviar**.

    ![Captura de tela do portal antes do teste](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Uma resposta bem-sucedida se parecerá com o seguinte:

![Captura de tela de uma resposta bem-sucedida do gerenciamento de API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Limpeza

Quando você terminar de testar o aplicativo, vá para a [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.

1. Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.

    ![Portal do Azure: Grupos de Recursos no menu da barra lateral](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. Na página **Grupos de recursos**, selecione **Excluir**.

    ![Portal do Azure: página Grupos de Recursos](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Insira o nome do grupo de recursos e selecione **Excluir**. O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.

## <a name="next-steps"></a>Próximas etapas

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciamento de API do Azure](/azure/api-management/api-management-key-concepts)
- [Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-overview)
