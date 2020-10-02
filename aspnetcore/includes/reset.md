Redefinir permite que o servidor redefina uma solicitação HTTP/2 com um código de erro especificado. Uma solicitação de redefinição é considerada anulada.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` no exemplo de código anterior, especifica o `INTERNAL_ERROR` código de erro. Para obter mais informações sobre códigos de erro HTTP/2, visite a [seção código de erro de especificação http/2](https://tools.ietf.org/html/rfc7540#page-50).