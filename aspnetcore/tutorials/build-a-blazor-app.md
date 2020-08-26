---
title: Compilar um Blazor aplicativo de lista de tarefas pendentes
author: guardrex
description: Crie um Blazor aplicativo passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 7335b68ad06b9d2b8d7e056cfc1a6d8214119b21
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865424"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="d8741-103">Compilar um Blazor aplicativo de lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d8741-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="d8741-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8741-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8741-105">Este tutorial mostra como criar e modificar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8741-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="d8741-106">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="d8741-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8741-107">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="d8741-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="d8741-108">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="d8741-108">Modify Razor components</span></span>
> * <span data-ttu-id="d8741-109">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="d8741-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="d8741-110">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="d8741-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="d8741-111">No final deste tutorial, você terá um aplicativo de lista de tarefas em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="d8741-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8741-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d8741-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="d8741-113">Criar um aplicativo de lista de tarefas pendentes Blazor</span><span class="sxs-lookup"><span data-stu-id="d8741-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="d8741-114">Crie um novo Blazor aplicativo chamado `TodoList` em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="d8741-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="d8741-115">O comando anterior cria uma pasta chamada `TodoList` para manter o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8741-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="d8741-116">A `TodoList` pasta é a *pasta raiz* do projeto.</span><span class="sxs-lookup"><span data-stu-id="d8741-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="d8741-117">Altere os diretórios para a `TodoList` pasta com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8741-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="d8741-118">Adicione um novo `Todo` Razor componente ao aplicativo na `Pages` pasta usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8741-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="d8741-119">Razor os nomes de arquivo de componente exigem uma primeira letra em maiúscula.</span><span class="sxs-lookup"><span data-stu-id="d8741-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="d8741-120">Abra a `Pages` pasta e confirme se o `Todo` nome do arquivo de componente começa com uma letra maiúscula `T` .</span><span class="sxs-lookup"><span data-stu-id="d8741-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="d8741-121">O nome do arquivo deve ser `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="d8741-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="d8741-122">No, `Pages/Todo.razor` forneça a marcação inicial para o componente:</span><span class="sxs-lookup"><span data-stu-id="d8741-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="d8741-123">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="d8741-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="d8741-124">O `NavMenu` componente ( `Shared/NavMenu.razor` ) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8741-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="d8741-125">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8741-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="d8741-126">Adicione um `<NavLink>` elemento para o `Todo` componente adicionando a seguinte marcação de item de lista abaixo dos itens de lista existentes no `Shared/NavMenu.razor` arquivo:</span><span class="sxs-lookup"><span data-stu-id="d8741-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="d8741-127">Compile e execute o aplicativo executando o `dotnet run` comando no Shell de comando da `TodoList` pasta.</span><span class="sxs-lookup"><span data-stu-id="d8741-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="d8741-128">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.</span><span class="sxs-lookup"><span data-stu-id="d8741-128">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="d8741-129">Adicione um `TodoItem.cs` arquivo à raiz do projeto (a `TodoList` pasta) para manter uma classe que representa um item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="d8741-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="d8741-130">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d8741-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="d8741-131">Retornar ao `Todo` componente ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="d8741-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="d8741-132">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="d8741-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="d8741-133">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="d8741-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d8741-134">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="d8741-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="d8741-135">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="d8741-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="d8741-136">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="d8741-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="d8741-137">Pare o aplicativo em execução no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d8741-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="d8741-138">Muitos shells de comando aceitam o comando de teclado <kbd>Ctrl</kbd> + <kbd>c</kbd> para interromper um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8741-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="d8741-139">Recompile e execute o aplicativo com o `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="d8741-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="d8741-140">Quando o **`Add todo`** botão é selecionado, nada acontece porque um manipulador de eventos não está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="d8741-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="d8741-141">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="d8741-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="d8741-142">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="d8741-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="d8741-143">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="d8741-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="d8741-144">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="d8741-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d8741-145">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="d8741-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="d8741-146">Pare o aplicativo em execução no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d8741-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="d8741-147">Recompile e execute o aplicativo com o `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="d8741-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="d8741-148">Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="d8741-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="d8741-149">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="d8741-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d8741-150">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="d8741-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d8741-151">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="d8741-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="d8741-152">Para verificar se esses valores estão associados, atualize o cabeçalho `<h3>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="d8741-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="d8741-153">O `Todo` componente concluído ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="d8741-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="d8741-154">Pare o aplicativo em execução no Shell de comando.</span><span class="sxs-lookup"><span data-stu-id="d8741-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="d8741-155">Recompile e execute o aplicativo com o `dotnet run` comando.</span><span class="sxs-lookup"><span data-stu-id="d8741-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="d8741-156">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="d8741-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d8741-157">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d8741-157">Next steps</span></span>

<span data-ttu-id="d8741-158">Neste tutorial, você aprendeu a:</span><span class="sxs-lookup"><span data-stu-id="d8741-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d8741-159">Criar um projeto de aplicativo de lista de tarefas Blazor</span><span class="sxs-lookup"><span data-stu-id="d8741-159">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="d8741-160">Modificar Razor componentes</span><span class="sxs-lookup"><span data-stu-id="d8741-160">Modify Razor components</span></span>
> * <span data-ttu-id="d8741-161">Usar manipulação de eventos e vinculação de dados em componentes</span><span class="sxs-lookup"><span data-stu-id="d8741-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="d8741-162">Usar o roteamento em um Blazor aplicativo</span><span class="sxs-lookup"><span data-stu-id="d8741-162">Use routing in a Blazor app</span></span>

<span data-ttu-id="d8741-163">Saiba mais sobre as ferramentas para ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="d8741-163">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
