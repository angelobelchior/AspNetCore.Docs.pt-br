* `-F|--no-formatting`

  <span data-ttu-id="2c8a6-101">Um sinalizador cuja presença suprime a formatação da resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="2c8a6-102">Define um cabeçalho para a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-102">Sets an HTTP request header.</span></span> <span data-ttu-id="2c8a6-103">Os dois formatos de valor a seguir são compatíveis:</span><span class="sxs-lookup"><span data-stu-id="2c8a6-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="2c8a6-104">Especifica um arquivo em que o corpo da resposta HTTP deve ser gravado.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="2c8a6-105">Por exemplo, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="2c8a6-106">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="2c8a6-107">Especifica um arquivo em que os cabeçalhos da resposta HTTP devem ser gravados.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="2c8a6-108">Por exemplo, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="2c8a6-109">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="2c8a6-110">Um sinalizador cuja presença habilita o streaming da resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2c8a6-110">A flag whose presence enables streaming of the HTTP response.</span></span>
