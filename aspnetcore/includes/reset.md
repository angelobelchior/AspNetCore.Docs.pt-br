<span data-ttu-id="2d69b-101">Redefinir permite que o servidor redefina uma solicitação HTTP/2 com um código de erro especificado.</span><span class="sxs-lookup"><span data-stu-id="2d69b-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="2d69b-102">Uma solicitação de redefinição é considerada anulada.</span><span class="sxs-lookup"><span data-stu-id="2d69b-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="2d69b-103">`Reset` no exemplo de código anterior, especifica o `INTERNAL_ERROR` código de erro.</span><span class="sxs-lookup"><span data-stu-id="2d69b-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="2d69b-104">Para obter mais informações sobre códigos de erro HTTP/2, visite a [seção código de erro de especificação http/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="2d69b-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>