<span data-ttu-id="e2292-101">Aplicativos de servidor mais poseriais residem na memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="e2292-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="e2292-102">Isso significa que há vários aplicativos hospedados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="e2292-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="e2292-103">Para cada sessão de aplicativo, o mais incrivelmente inicia um circuito com seu próprio escopo de contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="e2292-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="e2292-104">Isso significa que os serviços com escopo são exclusivos por sessão mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="e2292-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="e2292-105">Não recomendamos aplicativos no mesmo estado de compartilhamento do servidor usando serviços singleton, a menos que seja feito um tratamento extremo, pois isso pode introduzir vulnerabilidades de segurança, como o vazamento do estado do usuário entre circuitos.</span><span class="sxs-lookup"><span data-stu-id="e2292-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="e2292-106">Você pode usar serviços singleton com estado em aplicativos mais incrivelmente se eles forem projetados especificamente para ele.</span><span class="sxs-lookup"><span data-stu-id="e2292-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="e2292-107">Por exemplo, é possível usar um cache de memória como um singleton porque ele requer uma chave para acessar uma determinada entrada, supondo que os usuários não tenham controle de quais chaves de cache são usadas.</span><span class="sxs-lookup"><span data-stu-id="e2292-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="e2292-108">**Além disso, por motivos de segurança, você não deve <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> usar em aplicativos mais potentes.**</span><span class="sxs-lookup"><span data-stu-id="e2292-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="e2292-109">Os aplicativos mais bem executados fora do contexto do pipeline de ASP.NET Core e <xref:Microsoft.AspNetCore.Http.HttpContext> não há garantia de que estejam disponíveis no <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nem é garantido que esteja mantendo o contexto que iniciou o aplicativo mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="e2292-109">Blazor apps run outside of the context of the ASP.NET Core pipeline and the <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor it is guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="e2292-110">A maneira recomendada de passar o estado da solicitação para o aplicativo mais fácil é por meio de parâmetros para o componente raiz na renderização inicial do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e2292-110">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="e2292-111">Defina uma classe com todos os dados que você deseja passar para o aplicativo mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="e2292-111">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="e2292-112">Preencha esses dados da página Razor usando o <xref:Microsoft.AspNetCore.Http.HttpContext> disponível naquele momento.</span><span class="sxs-lookup"><span data-stu-id="e2292-112">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="e2292-113">Passe os dados para o aplicativo mais incrivelmente como um parâmetro para o componente raiz (aplicativo).</span><span class="sxs-lookup"><span data-stu-id="e2292-113">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="e2292-114">Defina um parâmetro no componente raiz para manter os dados que estão sendo passados para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2292-114">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="e2292-115">Usar os dados específicos do usuário dentro do aplicativo; ou, como alternativa, copie esses dados em um serviço com escopo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dentro para que ele possa ser usado em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2292-115">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="e2292-116">Para obter mais informações e um código de exemplo, consulte <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="e2292-116">For more information and example code, see <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>