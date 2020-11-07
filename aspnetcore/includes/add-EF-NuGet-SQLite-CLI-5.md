<span data-ttu-id="ceb76-101">Execute os seguintes comandos da CLI do .NET:</span><span class="sxs-lookup"><span data-stu-id="ceb76-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef -v 5.0.0-rc.2.20475.6
dotnet tool install --global dotnet-aspnet-codegenerator -v 5.0.0-rc.2.20516.1
dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
dotnet add package Microsoft.Extensions.Logging.Debug -v 5.0.0-*
```

<span data-ttu-id="ceb76-102">Os comandos anteriores adicionam:</span><span class="sxs-lookup"><span data-stu-id="ceb76-102">The preceding commands add:</span></span>

* <span data-ttu-id="ceb76-103">A [ferramenta ASPNET-CodeGenerator scaffolding](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="ceb76-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="ceb76-104">As ferramentas de EF Core para a CLI do .NET.</span><span class="sxs-lookup"><span data-stu-id="ceb76-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="ceb76-105">O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="ceb76-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="ceb76-106">Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="ceb76-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="ceb76-107">Para obter orientação sobre a configuração de vários ambientes que permite que um aplicativo Configure seus contextos de banco de dados por ambiente, consulte <xref:fundamentals/environments#environment-based-startup-class-and-methods> .</span><span class="sxs-lookup"><span data-stu-id="ceb76-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
