<span data-ttu-id="44012-101">A estrutura usa como padrão o modo de logon pop-up e volta a redirecionar o modo de logon se um pop-up não puder ser aberto.</span><span class="sxs-lookup"><span data-stu-id="44012-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="44012-102">Configure MSAL para usar o modo de logon de redirecionamento definindo a `LoginMode` propriedade de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> como `redirect` :</span><span class="sxs-lookup"><span data-stu-id="44012-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="44012-103">A configuração padrão é `popup` , e o valor da cadeia de caracteres não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="44012-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
