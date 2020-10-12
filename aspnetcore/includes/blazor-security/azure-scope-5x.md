Ao trabalhar com uma API de servidor registrada com o AAD e o registro do AAD do aplicativo está em um locatário que depende de um [domínio do Publicador não verificado](/azure/active-directory/develop/howto-configure-publisher-domain), o URI da ID do aplicativo do seu aplicativo de API do servidor não está `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` , mas sim no formato `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` . Se esse for o caso, o escopo de token de acesso padrão em `Program.Main` ( `Program.cs` ) do *`Client`* aplicativo será semelhante ao seguinte:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Para configurar o aplicativo de API do servidor para um público correspondente, defina o `Audience` no *`Server`* arquivo de configurações do aplicativo de API ( `appsettings.json` ) para corresponder ao público do aplicativo fornecido pelo portal do Azure:

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

Na configuração anterior, o final do valor não `Audience` inclui o **not** escopo padrão `/{DEFAULT SCOPE}` .

Exemplo:

`Program.Main` ( `Program.cs` ) do *`Client`* aplicativo:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Configure o *`Server`* arquivo de configurações do aplicativo de API ( `appsettings.json` ) com um público correspondente ( `Audience` ):

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

No exemplo anterior, o final do valor não `Audience` inclui o **not** escopo padrão `/API.Access` .
