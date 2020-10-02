<span data-ttu-id="e5a30-101">Os trailers HTTP são semelhantes aos cabeçalhos HTTP, exceto que são enviados depois que o corpo da resposta é enviado.</span><span class="sxs-lookup"><span data-stu-id="e5a30-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="e5a30-102">Para o IIS e HTTP.SYS, há suporte apenas para os trailers de resposta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e5a30-102">For IIS and HTTP.SYS, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="e5a30-103">No código de exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="e5a30-103">In the preceding example code:</span></span>

* <span data-ttu-id="e5a30-104">`SupportsTrailers` garante que os trailers tenham suporte para a resposta.</span><span class="sxs-lookup"><span data-stu-id="e5a30-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="e5a30-105">`DeclareTrailer` Adiciona o nome do trailer fornecido ao `Trailer` cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="e5a30-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="e5a30-106">Declarar os marcadores de resposta é opcional, mas recomendado.</span><span class="sxs-lookup"><span data-stu-id="e5a30-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="e5a30-107">Se `DeclareTrailer` for chamado, ele deve ser antes que os cabeçalhos de resposta sejam enviados.</span><span class="sxs-lookup"><span data-stu-id="e5a30-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="e5a30-108">`AppendTrailer` anexa o trailer.</span><span class="sxs-lookup"><span data-stu-id="e5a30-108">`AppendTrailer` appends the trailer.</span></span>
