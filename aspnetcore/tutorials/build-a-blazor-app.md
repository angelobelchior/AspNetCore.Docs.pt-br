---
title: Compilar um Blazor aplicativo de lista de tarefas pendentes
author: guardrex
description: Crie um Blazor aplicativo passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 42c4f94869586bfcd4e8b27e4bdf4ef226094072
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022492"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="9f818-103">Compilar um Blazor aplicativo de lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="9f818-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="9f818-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9f818-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9f818-105">Este tutorial mostra como criar e modificar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="9f818-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="9f818-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f818-107">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="9f818-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="9f818-108">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="9f818-108">Modify Razor components</span></span>
> * <span data-ttu-id="9f818-109">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="9f818-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="9f818-110">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="9f818-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="9f818-111">No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="9f818-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f818-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="9f818-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="9f818-113">Criar um aplicativo de lista de tarefas pendentes Blazor</span><span class="sxs-lookup"><span data-stu-id="9f818-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="9f818-114">Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="9f818-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="9f818-115">O comando anterior cria uma pasta chamada `TodoList` para manter o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="9f818-116">Altere os diretórios para a `TodoList` pasta com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="9f818-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="9f818-117">Adicione um novo `Todo` Razor componente ao aplicativo na `Pages` pasta usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="9f818-117">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="9f818-118">Razoros nomes de arquivo de componente exigem uma primeira letra em maiúscula, portanto, confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` .</span><span class="sxs-lookup"><span data-stu-id="9f818-118">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="9f818-119">No, `Pages/Todo.razor` forneça a marcação inicial para o componente:</span><span class="sxs-lookup"><span data-stu-id="9f818-119">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="9f818-120">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="9f818-120">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="9f818-121">O `NavMenu` componente ( `Shared/NavMenu.razor` ) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-121">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="9f818-122">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-122">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="9f818-123">Adicione um `<NavLink>` elemento para o `Todo` componente adicionando a seguinte marcação de item de lista abaixo dos itens de lista existentes no `Shared/NavMenu.razor` arquivo:</span><span class="sxs-lookup"><span data-stu-id="9f818-123">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="9f818-124">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-124">Rebuild and run the app.</span></span> <span data-ttu-id="9f818-125">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.</span><span class="sxs-lookup"><span data-stu-id="9f818-125">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="9f818-126">Adicione um `TodoItem.cs` arquivo à raiz do projeto para conter uma classe que representa um item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="9f818-126">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="9f818-127">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9f818-127">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="9f818-128">Retornar ao `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="9f818-128">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="9f818-129">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="9f818-129">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="9f818-130">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="9f818-130">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="9f818-131">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="9f818-131">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="9f818-132">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="9f818-132">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="9f818-133">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="9f818-133">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="9f818-134">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-134">Rebuild and run the app.</span></span> <span data-ttu-id="9f818-135">Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="9f818-135">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="9f818-136">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="9f818-136">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="9f818-137">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="9f818-137">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="9f818-138">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="9f818-138">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="9f818-139">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="9f818-139">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="9f818-140">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="9f818-140">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="9f818-141">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-141">Rebuild and run the app.</span></span> <span data-ttu-id="9f818-142">Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="9f818-142">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="9f818-143">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="9f818-143">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="9f818-144">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="9f818-144">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="9f818-145">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="9f818-145">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="9f818-146">Para verificar se esses valores estão associados, atualize o cabeçalho `<h3>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="9f818-146">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="9f818-147">O `Todo` componente concluído ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="9f818-147">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="9f818-148">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9f818-148">Rebuild and run the app.</span></span> <span data-ttu-id="9f818-149">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="9f818-149">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9f818-150">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="9f818-150">Next steps</span></span>

<span data-ttu-id="9f818-151">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="9f818-151">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f818-152">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="9f818-152">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="9f818-153">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="9f818-153">Modify Razor components</span></span>
> * <span data-ttu-id="9f818-154">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="9f818-154">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="9f818-155">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="9f818-155">Use routing in a Blazor app</span></span>

<span data-ttu-id="9f818-156">Saiba mais sobre as ferramentas para ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="9f818-156">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
