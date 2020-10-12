<span data-ttu-id="d8507-101">O portal do Azure fornece um dos seguintes formatos de URI de ID de aplicativo com base no fato de o locatário do AAD ter ou não um [domínio de Publicador verificado ou não verificado](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="d8507-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="d8507-102">Quando o último dos dois URIs de ID do aplicativo anterior for usado no aplicativo cliente para formar o URI do escopo e a autorização não for bem-sucedida, tente fornecer o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="d8507-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="d8507-103">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d8507-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
