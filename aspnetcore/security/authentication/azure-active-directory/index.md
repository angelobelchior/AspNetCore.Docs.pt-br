---
title: Plataforma de identidade da Microsoft e Azure Active Directory com ASP.NET Core
author: rick-anderson
description: Descubra tópicos relacionados à autenticação com o Azure Active Directory da plataforma de identidade da Microsoft para aplicativos Web e APIs no ASP.NET Core.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061399"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="d28e3-103">Azure Active Directory com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d28e3-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="d28e3-104">Esses tutoriais e exemplos demonstram a autenticação no ASP.NET Core usando a plataforma de identidade da Microsoft e o Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d28e3-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="d28e3-105">Para obter tutoriais e exemplos adicionais usando ASP.NET Core com o Azure AD, consulte [plataforma de identidade da Microsoft](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="d28e3-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="d28e3-106">Cenários de aplicativo</span><span class="sxs-lookup"><span data-stu-id="d28e3-106">Application Scenarios</span></span>

* [<span data-ttu-id="d28e3-107">Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d28e3-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="d28e3-108">Aplicativo Web que conecta os usuários</span><span class="sxs-lookup"><span data-stu-id="d28e3-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="d28e3-109">Aplicativo Web que chama as APIs Web</span><span class="sxs-lookup"><span data-stu-id="d28e3-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="d28e3-110">API Web protegida</span><span class="sxs-lookup"><span data-stu-id="d28e3-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="d28e3-111">API Web que faz chamadas a outras APIs Web</span><span class="sxs-lookup"><span data-stu-id="d28e3-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="d28e3-112">Aplicativo Web que conecta usuários com Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d28e3-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="d28e3-113">Exemplos</span><span class="sxs-lookup"><span data-stu-id="d28e3-113">Samples</span></span>

* <span data-ttu-id="d28e3-114">[Habilite seu aplicativo ASP.NET Core para conectar usuários e chamar APIs da Web usando o Azure ad v2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="d28e3-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="d28e3-115">Consulte [este vídeo associado](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="d28e3-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="d28e3-116">[Chamando uma API Web do ASP.NET Core 2,0 de um aplicativo WPF usando o Azure ad v2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="d28e3-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="d28e3-117">Consulte [este vídeo associado](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="d28e3-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="d28e3-118">Um aplicativo Web ASP.NET Core com o Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d28e3-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
