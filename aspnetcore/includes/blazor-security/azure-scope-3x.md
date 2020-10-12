O portal do Azure fornece um dos seguintes formatos de URI de ID de aplicativo com base no fato de o locatário do AAD ter ou não um [domínio de Publicador verificado ou não verificado](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Quando o último dos dois URIs de ID do aplicativo anterior for usado no aplicativo cliente para formar o URI do escopo e a autorização não for bem-sucedida, tente fornecer o URI do escopo sem o esquema e o host:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Exemplo:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
