---
title: Navegación en Xamarin.Forms Shell
description: Las aplicaciones de Xamarin.Forms Shell pueden usar una experiencia de navegación basada en el URI que permite desplazarse a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida.
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5fb215ea92035965b48fff85ef4ccc70edc65fdf
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939191"
---
# <a name="no-locxamarinforms-shell-navigation"></a>Navegación en Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell incluye una experiencia de navegación basada en el URI que emplea rutas para navegar a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida. También ofrece la posibilidad de navegar hacia atrás sin tener que visitar todas las páginas de la pila de navegación.

`Shell` define las siguientes propiedades relacionadas con la navegación:

- `BackButtonBehavior`, de tipo `BackButtonBehavior`, una propiedad adjunta que define el comportamiento del botón Atrás.
- `CurrentItem`, de tipo `FlyoutItem`, el valor de `FlyoutItem` seleccionado actualmente.
- `CurrentPage`, de tipo `Page`, la página presentada actualmente.
- `CurrentState`, de tipo `ShellNavigationState`, el estado de navegación actual de `Shell`.
- `Current`, de tipo `Shell`, un alias con el tipo convertido para `Application.Current.MainPage`.

Las propiedades `BackButtonBehavior`, `CurrentItem` y `CurrentState` están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que estas propiedades pueden ser destinos de los enlaces de datos.

La navegación se realiza mediante la invocación del método `GoToAsync`, desde la clase `Shell`. Cuando la navegación está a punto de realizarse, se activa un evento `Navigating` y cuando finaliza, se activa un evento `Navigated`.

> [!NOTE]
> La navegación todavía se puede realizar en una aplicación de Xamarin.Forms Shell mediante la propiedad [Navigation](xref:Xamarin.Forms.NavigableElement.Navigation). Para más información, consulte [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="routes"></a>Rutas

La navegación se realiza en una aplicación de Shell mediante la especificación de un URI al que navegar. Los URI de navegación pueden tener tres componentes:

- Una *ruta*, que define la ruta de acceso al contenido que existe como parte de la jerarquía visual de Shell.
- Una *página*. Las páginas que no existen en la jerarquía visual de Shell se pueden insertar en la pila de navegación desde cualquier lugar dentro de una aplicación de Shell. Por ejemplo, una página de detalles de elementos no se definirá en la jerarquía visual de Shell, pero se puede insertar en la pila de navegación si es necesario.
- Uno o varios *parámetros de consulta*. Los parámetros de consulta son parámetros que se pueden pasar a la página de destino durante la navegación.

Cuando un URI de navegación incluye los tres componentes, la estructura es: //route/page?queryParameters

### <a name="register-routes"></a>Registro de rutas

Las rutas se pueden definir en objetos `FlyoutItem`, `Tab` y `ShellContent` mediante sus propiedades `Route`:

```xaml
<Shell ...>
    <FlyoutItem ...
                Route="animals">
        <Tab ...
             Route="domestic">
            <ShellContent ...
                          Route="cats" />
            <ShellContent ...
                          Route="dogs" />
        </Tab>
        <ShellContent ...
                      Route="monkeys" />
        <ShellContent ...
                      Route="elephants" />  
        <ShellContent ...
                      Route="bears" />
    </FlyoutItem>
    <ShellContent ...
                  Route="about" />                  
    ...
</Shell>
```

> [!NOTE]
> Todos los elementos de la jerarquía de Shell tienen asociada una ruta. Si el desarrollador no establece una ruta, esta se genera en tiempo de ejecución. Sin embargo, no se garantiza que las rutas generadas sean coherentes entre distintas sesiones de aplicación.

En este ejemplo se crea la siguiente jerarquía de ruta desde la misma aplicación, que se puede usar en la navegación mediante programación:

```
animals
  domestic
    cats
    dogs
  monkeys
  elephants
  bears
about
```

Para desplazarse al objeto `ShellContent` de la ruta `dogs`, el URI de la ruta absoluta es `//animals/domestic/dogs`. Igualmente, para desplazarse al objeto `ShellContent` de la ruta `about`, el URL de la ruta absoluta es `//about`.

> [!IMPORTANT]
> Si se detecta una ruta duplicada, se produce una excepción `ArgumentException` al inicio de la aplicación. También se producirá esta excepción si dos o más rutas del mismo nivel de la jerarquía comparten el nombre de ruta.

#### <a name="register-page-routes"></a>Registro de rutas de página

En el constructor de subclases de Shell, o en cualquier otra ubicación que se ejecute antes de invocar una ruta, se pueden registrar explícitamente rutas adicionales para cualquier página que no esté representada en la jerarquía visual de Shell:

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

En este ejemplo se registran como rutas páginas de detalles de elementos que no están definidas en la subclase de Shell. Luego, es posible desplazarse por estas páginas desde cualquier lugar de la aplicación mediante la navegación basada en el URI. Las rutas de estas páginas se conocen como *rutas globales*.

> [!NOTE]
> Si es necesario, se puede cancelar el registro de las páginas cuyas rutas se han registrado con el método `Routing.RegisterRoute`, con el método `Routing.UnRegisterRoute`.

Como alternativa, se pueden registrar las páginas en diferentes jerarquías de ruta:

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

En este ejemplo se habilita la navegación contextual por las páginas, donde la navegación a la ruta `details` desde la página de la ruta `monkeys` muestra `MonkeyDetailPage`. De forma similar, al desplazarse a la ruta `details` desde la página de la ruta `elephants` se muestra `ElephantDetailPage`.

> [!IMPORTANT]
> Se producirá una excepción `ArgumentException` si el método `Routing.RegisterRoute` intenta registrar la misma ruta en dos o más tipos diferentes.

## <a name="perform-navigation"></a>Realización de la navegación

Para realizar la navegación, se debe obtener primero una referencia a la subclase `Shell`. Esta referencia se puede obtener mediante la conversión de la propiedad `App.Current.MainPage` en un objeto `Shell`, por medio de la propiedad `Shell.Current`. Luego, se puede realizar la navegación mediante la llamada al método `GoToAsync` en el objeto `Shell`. Este método lleva a `ShellNavigationState` y devuelve un objeto `Task` que se completa una vez completada la animación de navegación. El objeto `ShellNavigationState` se construye mediante el método `GoToAsync`, desde una propiedad `string` o `Uri`, y tiene su propiedad `Location` establecida en el argumento `string` o `Uri`.

Cuando se navega a una ruta de la jerarquía visual de Shell, no se crea una pila de navegación. Sin embargo, cuando se navega a una página que no está en la jerarquía visual de Shell, se crea una pila de navegación.

> [!NOTE]
> El estado actual de navegación de `Shell` se puede recuperar mediante la propiedad `Shell.Current.CurrentState`, que incluye el URI de la ruta mostrada en la propiedad `Location`.

### <a name="absolute-routes"></a>Rutas absolutas

Se puede realizar la navegación mediante la especificación de un URI absoluto válido como argumento del método `GoToAsync`:

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

En este ejemplo se navega a la página de la ruta `monkeys`, donde dicha ruta se define en un objeto `ShellContent`. El objeto `ShellContent` que representa la ruta `monkeys` es un elemento secundario de un objeto `FlyoutItem`, cuya ruta es `animals`.

### <a name="relative-routes"></a>Rutas relativas

La navegación se puede realizar también mediante la especificación de un URI relativo válido como argumento del método `GoToAsync`. El sistema de enrutamiento intenta hacer coincidir el URI con un objeto `ShellContent`. Por lo tanto, si todas las rutas de una aplicación son únicas, la navegación se puede realizar con solo especificar el nombre de ruta único como un URI relativo.

Se admiten los formatos siguientes de ruta relativa:

| Formato | Descripción |
| --- | --- |
| *route* | En la jerarquía de ruta se buscará la ruta especificada, hacia arriba desde la posición actual. La página que coincida se insertará en la pila de navegación. |
| /*route* | La jerarquía de ruta se buscará a partir de la ruta especificada, hacia abajo desde la posición actual. La página que coincida se insertará en la pila de navegación. |
| //*route* | En la jerarquía de ruta se buscará la ruta especificada, hacia arriba desde la posición actual. La página que coincida reemplazará la pila de navegación. |
| ///*route* | En la jerarquía de ruta se buscará la ruta especificada, hacia abajo desde la posición actual. La página que coincida reemplazará la pila de navegación. |

En el ejemplo siguiente se navega a la página de la ruta `monkeydetails`:

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

En este ejemplo, la ruta `monkeyDetails` se busca hacia arriba en la jerarquía hasta que se encuentra la página que coincide. Cuando se encuentra, se inserta en la pila de navegación.

#### <a name="contextual-navigation"></a>Navegación contextual

Las rutas relativas permiten la navegación contextual. Por ejemplo, considere la siguiente jerarquía de ruta:

```
monkeys
  details
bears
  details
```

Cuando se muestra la página registrada para la ruta `monkeys`, al navegar a la ruta `details` se mostrará la página registrada para la ruta `monkeys/details`. Igualmente, cuando se muestra la página registrada para la ruta `bears`, al navegar a la ruta `details` se mostrará la página registrada para la ruta `bears/details`. Para información sobre cómo registrar las rutas en este ejemplo, consulte [Registro de rutas de página](#register-page-routes).

### <a name="backwards-navigation"></a>Navegación hacia atrás

La navegación hacia atrás se puede llevar a cabo especificando ".." como argumento en el método `GotoAsync`:

```csharp
await Shell.Current.GoToAsync("..");
```

La navegación hacia atrás con ".." también se puede combinar con una ruta:

```csharp
await Shell.Current.GoToAsync("../route");
```

En este ejemplo, se realiza la navegación hacia atrás y, después, se navega a la ruta especificada.

> [!IMPORTANT]
> Desplazarse hacia atrás y luego a una ruta especificada solo es posible si la navegación hacia atrás sitúa al usuario en la ubicación actual en la jerarquía de rutas para navegar a la ruta especificada.

Del mismo modo, es posible desplazarse hacia atrás varias veces y, seguidamente, navegar a una ruta especificada:

```csharp
await Shell.Current.GoToAsync("../../route");
```

En este ejemplo, la navegación hacia atrás se realiza dos veces y, después, se navega a la ruta especificada.

Además, los datos se pueden pasar a través de las propiedades de consulta al navegar hacia atrás:

```csharp
await Shell.Current.GoToAsync($"..?parameterToPassBack={parameterValueToPassBack}");
```

En este ejemplo, se realiza la navegación hacia atrás y el valor del parámetro de consulta se pasa al parámetro de consulta en la página anterior.

> [!NOTE]
> Los parámetros de consulta se pueden anexar a cualquier solicitud de navegación hacia atrás.

Para obtener más información sobre cómo pasar datos al navegar, vea [Pasar datos](#pass-data).

### <a name="invalid-routes"></a>Rutas no válidas

Los siguientes formatos de ruta no son válidos:

| Formato | Explicación |
| --- | --- |
| *route* o /*route* | Las rutas de la jerarquía visual no se pueden insertar en la pila de navegación. |
| //*page* o ///*page* | Actualmente, las rutas globales no pueden ser la única página en la pila de navegación. Por lo tanto, no se admite el enrutamiento absoluto a rutas globales. |

El uso de cualquiera de estos formatos de ruta dará como resultado una excepción `Exception`.

> [!IMPORTANT]
> Al intentar navegar a una ruta inexistente, se producirá una excepción `ArgumentException`.

### <a name="debugging-navigation"></a>Depuración de la navegación

Algunas de las clases de Shell se representan con `DebuggerDisplayAttribute`, que especifica cómo el depurador muestra una clase o campo. Esto puede ayudar a depurar las solicitudes de navegación puesto que se muestran los datos relacionados con la solicitud de navegación. Por ejemplo, en la captura de pantalla siguiente se muestran las propiedades `CurrentItem` y `CurrentState` del objeto `Shell.Current`:

![Captura de pantalla del depurador](navigation-images/debugger.png "instantáneas")

En este ejemplo, la propiedad `CurrentItem`, de tipo `FlyoutItem`, muestra el título y la ruta del objeto `FlyoutItem`. Igualmente, la propiedad `CurrentState`, de tipo `ShellNavigationState`, muestra el URI de la ruta mostrada dentro de la aplicación de Shell.

### <a name="tab-class"></a>Clase Tab

La clase `Tab` define una propiedad `Stack`, de tipo `IReadOnlyList<Page>`, que representa la pila de navegación actual insertada en `Tab`. La clase también proporciona los siguientes métodos de navegación reemplazables:

- `GetNavigationStack`, devuelve `IReadOnlyList<Page`>, la pila de navegación actual.
- `OnInsertPageBefore`, que se llama cuando se llama a `INavigation.InsertPageBefore`.
- `OnPopAsync`, devuelve `Task<Page>`, y se llama cuando se llama a `INavigation.PopAsync`.
- `OnPopToRootAsync`, devuelve `Task`, y se llama cuando se llama a `INavigation.OnPopToRootAsync`.
- `OnPushAsync`, devuelve `Task`, y se llama cuando se llama a `INavigation.PushAsync`.
- `OnRemovePage`, que se llama cuando se llama a `INavigation.RemovePage`.

Por ejemplo, en el código siguiente de ejemplo se indica cómo invalidar el método `OnRemovePage`:

```csharp
public class MyTab : Tab
{
    protected override void OnRemovePage(Page page)
    {
        base.OnRemovePage(page);

        // Custom logic
    }
}
```

Después, los objetos `MyTab` se pueden consumir en la jerarquía visual de Shell en lugar de los objetos `Tab`.

## <a name="navigation-events"></a>Eventos de navegación

La clase `Shell` define un evento `Navigating`, que se desencadena cuando está a punto de realizarse la navegación, ya sea debido a la navegación mediante programación o a la interacción del usuario. El objeto `ShellNavigatingEventArgs` que acompaña al evento `Navigating` proporciona las siguientes propiedades:

| Propiedad. | Tipo | Descripción |
|---|---|---|
| `Current` | `ShellNavigationState` | Identificador URI de la página actual. |
| `Source` | `ShellNavigationSource` | El tipo de navegación que se ha producido. |
| `Target` | `ShellNavigationState`  | Identificador URI que representa el destino de la navegación. |
| `CanCancel`  | `bool` | Valor que indica si es posible cancelar la navegación. |
| `Cancelled`  | `bool` | Valor que indica si la navegación se ha cancelado. |

Además, la clase `ShellNavigatingEventArgs` proporciona un método `Cancel`, que se puede usar para cancelar la navegación, y un método `GetDeferral`, que devuelve un token de `ShellNavigatingDeferral` que se puede usar para completar la navegación. Para obtener más información sobre el aplazamiento de la navegación, vea [Aplazamiento de navegación](#navigation-deferral).

La clase `Shell` también define un evento `Navigated`, que se desencadena cuando se ha completado la navegación. El objeto `ShellNavigatedEventArgs` que acompaña al evento `Navigating` proporciona las siguientes propiedades:

| Propiedad. | Tipo | Descripción |
|---|---|---|
| `Current` | `ShellNavigationState` | Identificador URI de la página actual. |
| `Previous`| `ShellNavigationState` | Identificador URI de la página anterior. |
| `Source`  | `ShellNavigationSource` | El tipo de navegación que se ha producido. |

Las clases `ShellNavigatedEventArgs` y `ShellNavigatingEventArgs` tienen ambas propiedades `Source`, de tipo `ShellNavigationSource`. Esta enumeración proporciona los valores siguientes:

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

Por lo tanto, en un controlador del evento `Navigating`, es posible interceptar la navegación y realizar acciones en función del origen de navegación. Por ejemplo, el código siguiente muestra cómo cancelar la navegación hacia atrás si no se han guardado los datos de la página:

```csharp
void OnNavigating(object sender, ShellNavigatingEventArgs e)
{
    // Cancel back navigation if data is unsaved
    if (e.Source == ShellNavigationSource.Pop && !dataSaved)
    {
        e.Cancel();
    }
}
```

## <a name="navigation-deferral"></a>Aplazamiento de navegación

La navegación de shell se puede interceptar y completar o cancelar según lo que elija el usuario. Esto se puede conseguir invalidando el método `OnNavigating` de la subclase `Shell` y, después, llamando al método `GetDeferral` en el objeto `ShellNavigatingEventArgs`. Este método devuelve un token de `ShellNavigatingDeferral` que tiene un método `Complete`, que se puede usar para completar la solicitud de navegación:

```csharp
public MyShell : Shell
{
    // ...
    protected override async void OnNavigating(ShellNavigatingEventArgs args)
    {
        base.OnNavigating(args);

        ShellNavigatingDeferral token = args.GetDeferral();
        var result = await DisplayActionSheet("Navigate?", "Cancel", "Yes", "No");

        if (result != "Yes")
        {
            args.Cancel();
        }
        token.Complete();
    }    
}
```

En este ejemplo, se muestra una hoja de acción que invita al usuario a completar la solicitud de navegación o cancelarla. La navegación se cancela invocando el método `Cancel` en el objeto `ShellNavigatingEventArgs`. La navegación se completa invocando el método `Complete` en el token de `ShellNavigatingDeferral` obtenido mediante el método `GetDeferral` en el objeto `ShellNavigatingEventArgs`.

> [!IMPORTANT]
> El método `GoToAsync` generará una excepción `InvalidOperationException` si un usuario intenta navegar mientras hay un aplazamiento de navegación pendiente.

## <a name="pass-data"></a>Pasar datos

Al realizar la navegación mediante programación, los datos pueden pasarse como parámetros de consulta. Por ejemplo, el código siguiente se ejecuta en la aplicación de ejemplo cuando un usuario selecciona un elefante en `ElephantsPage`:

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"//animals/elephants/elephantdetails?name={elephantName}");
}
```

En este ejemplo de código se recupera el elefante actualmente seleccionado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) y se navega a la ruta `elephantdetails`; además, se pasa `elephantName` como parámetro de consulta. Tenga en cuenta que los parámetros de consulta van a ser codificados con URL para la navegación, por lo que "Indian Elephant" se convertirá en "Indian%20Elephant".

Para recibir datos, la clase que representa la página a la que se navega, o la clase de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página, se debe decorar con un elemento `QueryPropertyAttribute` para cada parámetro de consulta:

```csharp
[QueryProperty("Name", "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            BindingContext = ElephantData.Elephants.FirstOrDefault(m => m.Name == Uri.UnescapeDataString(value));
        }
    }
    ...
}
```

El primer argumento de `QueryPropertyAttribute` especifica el nombre de la propiedad que recibirá los datos, mientras que el segundo argumento especifica el identificador del parámetro de consulta. Por tanto, el elemento `QueryPropertyAttribute` del ejemplo anterior especifica que la propiedad `Name` recibirá los datos pasados al parámetro de consulta `name` del URI en la llamada al método `GoToAsync`. La propiedad `Name` decodifica entonces el valor del parámetro de consulta en una dirección URL y lo usa para establecer el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página en el objeto que se mostrará.

> [!NOTE]
> Una clase se puede representar con varios objetos `QueryPropertyAttribute`.

## <a name="back-button-behavior"></a>Comportamiento del botón Atrás

La clase `BackButtonBehavior` define las siguientes propiedades que controlan la apariencia y el comportamiento del botón Atrás:

- `Command`, de tipo `ICommand`, que se ejecuta cuando se presiona el botón Atrás.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `IconOverride`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono usado para el botón Atrás.
- `IsEnabled`, de tipo `boolean`, indica si se ha habilitado el botón Atrás. El valor predeterminado es `true`.
- `TextOverride`, de tipo `string`, el texto usado para el botón Atrás.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

La clase `BackButtonBehavior` se puede consumir mediante el establecimiento de la propiedad adjunta `Shell.BackButtonBehavior` a un objeto `BackButtonBehavior`:

```xaml
<ContentPage ...>    
    <Shell.BackButtonBehavior>
        <BackButtonBehavior Command="{Binding BackCommand}"
                            IconOverride="back.png" />   
    </Shell.BackButtonBehavior>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetBackButtonBehavior(this, new BackButtonBehavior
{
    Command = new Command(() =>
    {
        ...
    }),
    IconOverride = "back.png"
});
```

La propiedad `Command` se establece en `ICommand` para ejecutarse cuando se presiona el botón Atrás, y la propiedad `IconOverride` se establece en el icono que se usa para el botón Atrás:

[![Captura de pantalla de la invalidación de un icono de botón Atrás de shell en iOS y Android](navigation-images/back-button.png "Invalidación de icono de botón Atrás de Shell")](navigation-images/back-button-large.png#lightbox "Invalidación de icono de botón Atrás de Shell")

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
