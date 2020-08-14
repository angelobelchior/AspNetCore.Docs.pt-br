A estrutura usa como padrão o modo de logon pop-up e volta a redirecionar o modo de logon se um pop-up não puder ser aberto. Configure MSAL para usar o modo de logon de redirecionamento definindo a `LoginMode` propriedade de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> como `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

A configuração padrão é `popup` , e o valor da cadeia de caracteres não diferencia maiúsculas de minúsculas.
