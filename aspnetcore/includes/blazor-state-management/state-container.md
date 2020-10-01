Os componentes aninhados normalmente associam dados usando *Associação encadeada* , conforme descrito em <xref:blazor/components/data-binding> . Componentes aninhados e não aninhados podem compartilhar o acesso a dados usando um contêiner de estado na memória registrado. Uma classe de contêiner de estado personalizado pode usar um atribuível <xref:System.Action> para notificar componentes em diferentes partes do aplicativo de alterações de estado. No exemplo a seguir:

* Um par de componentes usa um contêiner de estado para rastrear uma propriedade.
* Os componentes do exemplo são aninhados, mas o aninhamento não é necessário para que essa abordagem funcione.

`StateContainer.cs`:

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

Em `Program.Main` (Webassembly mais incrivelmente):

```csharp
builder.Services.AddSingleton<StateContainer>();
```

Em `Startup.ConfigureServices` (servidor mais incrivelmente):

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

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

`Shared/Component2.razor`:

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
