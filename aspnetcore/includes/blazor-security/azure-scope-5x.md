<span data-ttu-id="12251-101">Ao trabalhar com uma API de servidor registrada com o AAD e o registro do AAD do aplicativo está em um locatário que depende de um [domínio do Publicador não verificado](/azure/active-directory/develop/howto-configure-publisher-domain), o URI da ID do aplicativo do seu aplicativo de API do servidor não está `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` , mas sim no formato `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` .</span><span class="sxs-lookup"><span data-stu-id="12251-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="12251-102">Se esse for o caso, o escopo de token de acesso padrão em `Program.Main` ( `Program.cs` ) do *`Client`* aplicativo será semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="12251-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="12251-103">Para configurar o aplicativo de API do servidor para um público correspondente, defina o `Audience` no *`Server`* arquivo de configurações do aplicativo de API ( `appsettings.json` ) para corresponder ao público do aplicativo fornecido pelo portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="12251-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="12251-104">Na configuração anterior, o final do valor não `Audience` inclui o **not** escopo padrão `/{DEFAULT SCOPE}` .</span><span class="sxs-lookup"><span data-stu-id="12251-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="12251-105">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="12251-105">Example:</span></span>

<span data-ttu-id="12251-106">`Program.Main` ( `Program.cs` ) do *`Client`* aplicativo:</span><span class="sxs-lookup"><span data-stu-id="12251-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="12251-107">Configure o *`Server`* arquivo de configurações do aplicativo de API ( `appsettings.json` ) com um público correspondente ( `Audience` ):</span><span class="sxs-lookup"><span data-stu-id="12251-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="12251-108">No exemplo anterior, o final do valor não `Audience` inclui o **not** escopo padrão `/API.Access` .</span><span class="sxs-lookup"><span data-stu-id="12251-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
