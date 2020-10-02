Os trailers HTTP são semelhantes aos cabeçalhos HTTP, exceto que são enviados depois que o corpo da resposta é enviado. Para o IIS e HTTP.SYS, há suporte apenas para os trailers de resposta HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

No código de exemplo anterior:

* `SupportsTrailers` garante que os trailers tenham suporte para a resposta.
* `DeclareTrailer` Adiciona o nome do trailer fornecido ao `Trailer` cabeçalho de resposta. Declarar os marcadores de resposta é opcional, mas recomendado. Se `DeclareTrailer` for chamado, ele deve ser antes que os cabeçalhos de resposta sejam enviados.
* `AppendTrailer` anexa o trailer.
