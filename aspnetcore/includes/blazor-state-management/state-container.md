<span data-ttu-id="2fd0c-101">Os componentes aninhados normalmente associam dados usando *Associação encadeada* , conforme descrito em <xref:blazor/components/data-binding> .</span><span class="sxs-lookup"><span data-stu-id="2fd0c-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="2fd0c-102">Componentes aninhados e não aninhados podem compartilhar o acesso a dados usando um contêiner de estado na memória registrado.</span><span class="sxs-lookup"><span data-stu-id="2fd0c-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="2fd0c-103">Uma classe de contêiner de estado personalizado pode usar um atribuível <xref:System.Action> para notificar componentes em diferentes partes do aplicativo de alterações de estado.</span><span class="sxs-lookup"><span data-stu-id="2fd0c-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="2fd0c-104">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fd0c-104">In the following example:</span></span>

* <span data-ttu-id="2fd0c-105">Um par de componentes usa um contêiner de estado para rastrear uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="2fd0c-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="2fd0c-106">Os componentes do exemplo são aninhados, mas o aninhamento não é necessário para que essa abordagem funcione.</span><span class="sxs-lookup"><span data-stu-id="2fd0c-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="2fd0c-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="2fd0c-107">`StateContainer.cs`:</span></span>

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="2fd0c-108">Em `Program.Main` (Webassembly mais incrivelmente):</span><span class="sxs-lookup"><span data-stu-id="2fd0c-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="2fd0c-109">Em `Startup.ConfigureServices` (servidor mais incrivelmente):</span><span class="sxs-lookup"><span data-stu-id="2fd0c-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="2fd0c-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="2fd0c-110">`Pages/Component1.razor`:</span></span>

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="2fd0c-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="2fd0c-111">`Shared/Component2.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```
