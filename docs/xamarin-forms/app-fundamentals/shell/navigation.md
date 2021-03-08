---
title: Navegación en Xamarin.Forms Shell
description: Las aplicaciones de Xamarin.Forms Shell pueden usar una experiencia de navegación basada en el URI que permite desplazarse a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida.
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/23/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7d714b38dd838ef0779802dc7ca8c050dbd4464b
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757542"
---
# <a name="xamarinforms-shell-navigation"></a>Navegación en Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell incluye una experiencia de navegación basada en el URI que emplea rutas para navegar a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida. También ofrece la posibilidad de navegar hacia atrás sin tener que visitar todas las páginas de la pila de navegación.

La clase [`Shell`](xref:Xamarin.Forms.Shell) define las siguientes propiedades relacionadas con la navegación:

- [`BackButtonBehavior`](xref:Xamarin.Forms.Shell.BackButtonBehaviorProperty), de tipo [`BackButtonBehavior`](xref:Xamarin.Forms.BackButtonBehavior), propiedad adjunta que define el comportamiento del botón Atrás.
- [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem), de tipo [`ShellItem`](xref:Xamarin.Forms.ShellItem), el valor seleccionado actualmente.
- [`CurrentPage`](xref:Xamarin.Forms.Shell.CurrentPage), de tipo [`Page`](xref:Xamarin.Forms.Page), la página presentada actualmente.
- [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState), de tipo [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState), el estado de navegación actual del objeto [`Shell`](xref:Xamarin.Forms.Shell).
- [`Current`](xref:Xamarin.Forms.Shell.Current), de tipo [`Shell`](xref:Xamarin.Forms.Shell), alias con el tipo convertido para `Application.Current.MainPage`.

Las propiedades [`BackButtonBehavior`](xref:Xamarin.Forms.Shell.BackButtonBehaviorProperty), [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) y [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState) están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que estas propiedades pueden ser destinos de los enlaces de datos.

La navegación se realiza mediante la invocación del método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*), desde la clase [`Shell`](xref:Xamarin.Forms.Shell). Cuando la navegación está a punto de realizarse, se activa un evento [`Navigating`](xref:Xamarin.Forms.Shell.Navigating) y, cuando finaliza, se activa un evento [`Navigated`](xref:Xamarin.Forms.Shell.Navigated).

> [!NOTE]
> La navegación todavía se puede realizar en una aplicación de Shell mediante la propiedad [Navigation](xref:Xamarin.Forms.NavigableElement.Navigation). Para más información, consulte [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="routes"></a>Rutas

La navegación se realiza en una aplicación de Shell mediante la especificación de un URI al que navegar. Los URI de navegación pueden tener tres componentes:

- Una *ruta*, que define la ruta de acceso al contenido que existe como parte de la jerarquía visual de Shell.
- Una *página*. Las páginas que no existen en la jerarquía visual de Shell se pueden insertar en la pila de navegación desde cualquier lugar dentro de una aplicación de Shell. Por ejemplo, una página de detalles no se definirá en la jerarquía visual de Shell, pero se puede insertar en la pila de navegación si es necesario.
- Uno o varios *parámetros de consulta*. Los parámetros de consulta son parámetros que se pueden pasar a la página de destino durante la navegación.

Cuando un URI de navegación incluye los tres componentes, la estructura es: //route/page?queryParameters

### <a name="register-routes"></a>Registro de rutas

Se pueden definir rutas en objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem), [`TabBar`](xref:Xamarin.Forms.TabBar), [`Tab`](xref:Xamarin.Forms.Tab) y [`ShellContent`](xref:Xamarin.Forms.ShellContent) mediante sus propiedades `Route`:

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
> Todos los elementos de la jerarquía de Shell tienen asociada una ruta. Si no se establece una ruta, se genera una en tiempo de ejecución. Sin embargo, no se garantiza que las rutas generadas sean coherentes entre distintas sesiones de aplicación.

En el ejemplo anterior se crea la siguiente jerarquía de ruta, que se puede usar en la navegación mediante programación:

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

Para desplazarse al objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) de la ruta `dogs`, el URI de la ruta absoluta es `//animals/domestic/dogs`. Igualmente, para desplazarse al objeto `ShellContent` de la ruta `about`, el URL de la ruta absoluta es `//about`.

> [!WARNING]
> Si se detecta una ruta duplicada, se produce una excepción `ArgumentException` al inicio de la aplicación. También se producirá esta excepción si dos o más rutas del mismo nivel de la jerarquía comparten el nombre de ruta.

### <a name="register-detail-page-routes"></a>Registro de rutas de la página de detalles

En el constructor de subclases de [`Shell`](xref:Xamarin.Forms.Shell), o en cualquier otra ubicación que se ejecute antes de invocar una ruta, se pueden registrar explícitamente rutas adicionales para cualquier página que no esté representada en la jerarquía visual de Shell. Esto se realiza mediante el método [`Routing.RegisterRoute`](xref:Xamarin.Forms.Routing.RegisterRoute*):

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

En este ejemplo se registran como rutas páginas de detalles que no están definidas en la subclase [`Shell`](xref:Xamarin.Forms.Shell). Se puede navegar después a estas páginas desde cualquier lugar de la aplicación con la navegación basada en URI. Las rutas de estas páginas se conocen como *rutas globales*.

> [!WARNING]
> Se producirá una excepción `ArgumentException` si el método [`Routing.RegisterRoute`](xref:Xamarin.Forms.Routing.RegisterRoute*) intenta registrar la misma ruta en dos o más tipos diferentes.

Como alternativa, se pueden registrar las páginas en diferentes jerarquías de ruta:

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

En este ejemplo se habilita la navegación contextual por las páginas, donde la navegación a la ruta `details` desde la página de la ruta `monkeys` muestra `MonkeyDetailPage`. De forma similar, al desplazarse a la ruta `details` desde la página de la ruta `elephants` se muestra `ElephantDetailPage`. Para obtener más información, consulte [Navegación contextual](#contextual-navigation).

> [!NOTE]
> Si es necesario, el registro de las páginas cuyas rutas se han registrado con el método [`Routing.RegisterRoute`](xref:Xamarin.Forms.Routing.RegisterRoute*) se puede cancelar con el método [`Routing.UnRegisterRoute`](xref:Xamarin.Forms.Routing.UnRegisterRoute*).

## <a name="perform-navigation"></a>Realización de la navegación

Para realizar la navegación, se debe obtener primero una referencia a la subclase [`Shell`](xref:Xamarin.Forms.Shell). Esta referencia se puede obtener mediante la conversión de la propiedad `App.Current.MainPage` en un objeto `Shell` o por medio de la propiedad [`Shell.Current`](xref:Xamarin.Forms.Shell.Current). La navegación se puede realizar entonces llamando al método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) en el objeto `Shell`. Este método lleva a una clase [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) y devuelve un objeto `Task` que se completa una vez completada la animación de navegación. El objeto `ShellNavigationState` se construye mediante el método `GoToAsync`, desde una propiedad `string` o `Uri`, y tiene su propiedad `Location` establecida en el argumento `string` o `Uri`.

> [!IMPORTANT]
> Cuando se navega a una ruta de la jerarquía visual de Shell, no se crea una pila de navegación. Sin embargo, cuando se navega a una página que no está en la jerarquía visual de Shell, se crea una pila de navegación.

El estado actual de navegación del objeto [`Shell`](xref:Xamarin.Forms.Shell) se puede recuperar mediante la propiedad [`Shell.Current.CurrentState`](xref:Xamarin.Forms.Shell.CurrentState), que incluye el URI de la ruta mostrada en la propiedad `Location`.

### <a name="absolute-routes"></a>Rutas absolutas

La navegación se puede realizar mediante la especificación de un URI absoluto válido como argumento del método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*):

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

En este ejemplo se navega a la página de la ruta `monkeys`, donde dicha ruta se define en un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent). El objeto `ShellContent` que representa la ruta `monkeys` es un elemento secundario de un objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem), cuya ruta es `animals`.

### <a name="relative-routes"></a>Rutas relativas

La navegación se puede realizar también mediante la especificación de un URI relativo válido como argumento del método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*). El sistema de enrutamiento intenta hacer coincidir el URI con un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent). Por lo tanto, si todas las rutas de una aplicación son únicas, la navegación se puede realizar con solo especificar el nombre de ruta único como un URI relativo.

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

Cuando se muestra la página registrada para la ruta `monkeys`, al navegar a la ruta `details` se mostrará la página registrada para la ruta `monkeys/details`. Igualmente, cuando se muestra la página registrada para la ruta `bears`, al navegar a la ruta `details` se mostrará la página registrada para la ruta `bears/details`. Para información sobre cómo registrar las rutas en este ejemplo, consulte [Registro de rutas de página](#register-detail-page-routes).

### <a name="backwards-navigation"></a>Navegación hacia atrás

La navegación hacia atrás se puede llevar a cabo especificando ".." como argumento en el método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*):

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
| //*page* o ///*page* | Actualmente, las rutas globales no pueden ser la única página en la pila de navegación. Por lo tanto, no se admite el enrutamiento absoluto a rutas globales. |

El uso de estos formatos de ruta dará como resultado una excepción `Exception`.

> [!WARNING]
> Al intentar navegar a una ruta inexistente, se producirá una excepción `ArgumentException`.

### <a name="debugging-navigation"></a>Depuración de la navegación

Algunas de las clases de Shell se representan con `DebuggerDisplayAttribute`, que especifica cómo el depurador muestra una clase o campo. Esto puede ayudar a depurar las solicitudes de navegación puesto que se muestran los datos relacionados con la solicitud de navegación. Por ejemplo, en la captura de pantalla siguiente se muestran las propiedades [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) y [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState) del objeto [`Shell.Current`](xref:Xamarin.Forms.Shell.Current):

![Captura de pantalla del depurador](navigation-images/debugger.png "instantáneas")

En este ejemplo, la propiedad [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem), de tipo [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem), muestra el título y la ruta de acceso del objeto `FlyoutItem`. Igualmente, la propiedad [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState), de tipo [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState), muestra el URI de la ruta mostrada dentro de la aplicación de Shell.

### <a name="navigation-stack"></a>Pila de navegación

La clase [`Tab`](xref:Xamarin.Forms.Tab) define una propiedad [`Stack`](xref:Xamarin.Forms.ShellSection.Stack), de tipo `IReadOnlyList<Page>`, que representa la pila de navegación actual en el objeto `Tab`. La clase también proporciona los siguientes métodos de navegación reemplazables:

- [`GetNavigationStack`](xref:Xamarin.Forms.ShellSection.GetNavigationStack*), que devuelve `IReadOnlyList<Page`>, la pila de navegación actual.
- [`OnInsertPageBefore`](xref:Xamarin.Forms.ShellSection.OnInsertPageBefore*), que se llama cuando se llama a `INavigation.InsertPageBefore`.
- [`OnPopAsync`](xref:Xamarin.Forms.ShellSection.OnPopAsync*), que devuelve `Task<Page>` y se llama cuando se llama a `INavigation.PopAsync`.
- [`OnPopToRootAsync`](xref:Xamarin.Forms.ShellSection.OnPopToRootAsync*), que devuelve `Task` y se llama cuando se llama a `INavigation.OnPopToRootAsync`.
- [`OnPushAsync`](xref:Xamarin.Forms.ShellSection.OnPushAsync*), que devuelve `Task` y se llama cuando se llama a `INavigation.PushAsync`.
- [`OnRemovePage`](xref:Xamarin.Forms.ShellSection.OnRemovePage*), que se llama cuando se llama a `INavigation.RemovePage`.

En el ejemplo siguiente se muestra cómo se invalida el método [`OnRemovePage`](xref:Xamarin.Forms.ShellSection.OnRemovePage*):

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

En este ejemplo, los objetos `MyTab` se deben consumir en la jerarquía visual de Shell en lugar de los objetos [`Tab`](xref:Xamarin.Forms.Tab).

## <a name="navigation-events"></a>Eventos de navegación

La clase [`Shell`](xref:Xamarin.Forms.Shell) define un evento [`Navigating`](xref:Xamarin.Forms.Shell.Navigating), que se desencadena cuando está a punto de realizarse la navegación, ya sea debido a la navegación mediante programación o a la interacción del usuario. El objeto [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs) que acompaña al evento `Navigating` proporciona las siguientes propiedades:

| Propiedad | Tipo | Descripción |
|---|---|---|
| [`Current`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Current) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | Identificador URI de la página actual. |
| [`Source`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Source) | [`ShellNavigationSource`](xref:Xamarin.Forms.ShellNavigationSource) | El tipo de navegación que se ha producido. |
| [`Target`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Target) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | Identificador URI que representa el destino de la navegación. |
| [`CanCancel`](xref:Xamarin.Forms.ShellNavigatingEventArgs.CanCancel)  | `bool` | Valor que indica si es posible cancelar la navegación. |
| [`Cancelled`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Cancelled)  | `bool` | Valor que indica si la navegación se ha cancelado. |

Además, la clase [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs) proporciona un método [`Cancel`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Cancel*), que se puede usar para cancelar la navegación, y un método [`GetDeferral`](xref:Xamarin.Forms.ShellNavigatingEventArgs.GetDeferral*), que devuelve un token de [`ShellNavigatingDeferral`](xref:Xamarin.Forms.ShellNavigatingDeferral) que se puede usar para completar la navegación. Para obtener más información sobre el aplazamiento de la navegación, vea [Aplazamiento de navegación](#navigation-deferral).

La clase [`Shell`](xref:Xamarin.Forms.Shell) también define un evento [`Navigated`](xref:Xamarin.Forms.Shell.Navigated), que se desencadena cuando se ha completado la navegación. El objeto [`ShellNavigatedEventArgs`](xref:Xamarin.Forms.ShellNavigatedEventArgs) que acompaña al evento `Navigated` proporciona las siguientes propiedades:

| Propiedad | Tipo | Descripción |
|---|---|---|
| [`Current`](xref:Xamarin.Forms.ShellNavigatedEventArgs.Current)  | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | Identificador URI de la página actual. |
| [`Previous`](xref:Xamarin.Forms.ShellNavigatedEventArgs.Previous) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | Identificador URI de la página anterior. |
| [`Source`](xref:Xamarin.Forms.ShellNavigatedEventArgs.Source) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | El tipo de navegación que se ha producido. |

> [!IMPORTANT]
> Se llama al método `OnNavigating` cuando se genera el evento [`Navigating`](xref:Xamarin.Forms.Shell.Navigating). Del mismo modo, se llama al método `OnNavigated` cuando se genera el evento [`Navigated`](xref:Xamarin.Forms.Shell.Navigated). Ambos métodos se pueden invalidar en la subclase [`Shell`](xref:Xamarin.Forms.Shell) para interceptar las solicitudes de navegación.

Ambas clases, [`ShellNavigatedEventArgs`](xref:Xamarin.Forms.ShellNavigatedEventArgs) y [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs), tienen propiedades `Source`, de tipo [`ShellNavigationSource`](xref:Xamarin.Forms.ShellNavigationSource). Esta enumeración proporciona los valores siguientes:

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

Por lo tanto, se puede interceptar la navegación con una invalidación de `OnNavigating` y se pueden realizar acciones en función del origen de navegación. Por ejemplo, el código siguiente muestra cómo cancelar la navegación hacia atrás si no se han guardado los datos de la página:

```csharp
protected override void OnNavigating(ShellNavigatingEventArgs args)
{
    base.OnNavigating(args);

    // Cancel any back navigation.
    if (args.Source == ShellNavigationSource.Pop)
    {
        args.Cancel();
    }
// }
```

## <a name="navigation-deferral"></a>Aplazamiento de navegación

La navegación de shell se puede interceptar y completar o cancelar según lo que elija el usuario. Esto se puede conseguir invalidando el método `OnNavigating` de la subclase [`Shell`](xref:Xamarin.Forms.Shell) y llamando al método [`GetDeferral`](xref:Xamarin.Forms.ShellNavigatingEventArgs.GetDeferral*) en el objeto [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs). Este método devuelve un token de [`ShellNavigatingDeferral`](xref:Xamarin.Forms.ShellNavigatingDeferral) que tiene un método [`Complete`](xref:Xamarin.Forms.ShellNavigatingDeferral.Complete*), que se puede usar para completar la solicitud de navegación:

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

En este ejemplo, se muestra una hoja de acción que invita al usuario a completar la solicitud de navegación o cancelarla. La navegación se cancela invocando el método [`Cancel`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Cancel*) en el objeto [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs). La navegación se completa invocando el método [`Complete`](xref:Xamarin.Forms.ShellNavigatingDeferral.Complete*) en el token de [`ShellNavigatingDeferral`](xref:Xamarin.Forms.ShellNavigatingDeferral) obtenido mediante el método `GetDeferral` en el objeto `ShellNavigatingEventArgs`.

> [!WARNING]
> El método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) generará una excepción `InvalidOperationException` si un usuario intenta navegar mientras hay un aplazamiento de navegación pendiente.

## <a name="pass-data"></a>Pasar datos

Al realizar la navegación mediante programación basada en URI, los datos pueden pasarse como parámetros de consulta. Esto se consigue anexando `?` después de una ruta, seguido de un identificador de parámetro de consulta, `=`, y un valor. Por ejemplo, el código siguiente se ejecuta en la aplicación de ejemplo cuando un usuario selecciona un elefante en `ElephantsPage`:

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"elephantdetails?name={elephantName}");
}
```

En este ejemplo de código se recupera el elefante actualmente seleccionado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) y se navega a la ruta `elephantdetails`; además, se pasa `elephantName` como parámetro de consulta.

Para recibir datos, la clase que representa la página a la que se navega, o la clase correspondiente al objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página, se debe decorar con un elemento [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) para cada parámetro de consulta:

```csharp
[QueryProperty(nameof(Name), "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            LoadAnimal(value);
        }
    }
    ...

    void LoadAnimal(string name)
    {
        try
        {
            Animal animal = ElephantData.Elephants.FirstOrDefault(a => a.Name == name);
            BindingContext = animal;
        }
        catch (Exception)
        {
            Console.WriteLine("Failed to load animal.");
        }
    }    
}
```

El primer argumento de [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) especifica el nombre de la propiedad que recibirá los datos, mientras que el segundo argumento especifica el identificador del parámetro de consulta. Por tanto, el elemento `QueryPropertyAttribute` del ejemplo anterior especifica que la propiedad `Name` recibirá los datos pasados al parámetro de consulta `name` del URI en la llamada al método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*). El establecedor de la propiedad `Name` llama al método `LoadAnimal` para recuperar el objeto `Animal` para el objeto `name` y lo establece como la propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página.

> [!IMPORTANT]
> Los valores de los parámetros de consulta se descodifican automáticamente como dirección URL.

### <a name="pass-multiple-query-parameters"></a>Paso de varios parámetros de consulta

Se pueden pasar varios parámetros de consulta conectándolos con `&`. Por ejemplo, el código siguiente pasa dos elementos de datos:

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    string elephantLocation = (e.CurrentSelection.FirstOrDefault() as Animal).Location;
    await Shell.Current.GoToAsync($"elephantdetails?name={elephantName}&location={elephantLocation}");
}
```

En este ejemplo de código se recupera el elefante actualmente seleccionado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) y se navega a la ruta `elephantdetails`; además, se pasan `elephantName` y `elephantLocation` como parámetros de consulta.

Para recibir datos, la clase que representa la página a la que se navega, o la clase correspondiente al objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página, se debe decorar con un elemento [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) para cada parámetro de consulta:

```csharp
[QueryProperty(nameof(Name), "name")]
[QueryProperty(nameof(Location), "location")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            // Custom logic
        }
    }

    public string Location
    {
        set
        {
            // Custom logic
        }
    }
    ...    
}
```

En este ejemplo, la clase se decora con un objeto [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) para cada parámetro de consulta. El primer objeto `QueryPropertyAttribute` especifica que la propiedad `Name` recibirá los datos pasados en el parámetro de consulta `name`, mientras que el segundo objeto `QueryPropertyAttribute` especifica que la propiedad `Location` recibirá los datos pasado en el parámetro de consulta `location`. En ambos casos, los valores de los parámetros de consulta se especifican en el URI en la llamada al método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*).

## <a name="back-button-behavior"></a>Comportamiento del botón Atrás

La apariencia y el comportamiento del botón Atrás se pueden volver a definir estableciendo la propiedad adjunta [`BackButtonBehavior`](xref:Xamarin.Forms.Shell.BackButtonBehaviorProperty) en un objeto [`BackButtonBehavior`](xref:Xamarin.Forms.BackButtonBehavior). La clase [`BackButtonBehavior`](xref:Xamarin.Forms.BackButtonBehavior) define las propiedades siguientes:

- [`Command`](xref:Xamarin.Forms.BackButtonBehavior.Command), de tipo `ICommand`, que se ejecuta cuando se presiona el botón Atrás.
- [`CommandParameter`](xref:Xamarin.Forms.BackButtonBehavior.CommandParameter), de tipo `object`, que es el parámetro que se pasa al objeto `Command`.
- [`IconOverride`](xref:Xamarin.Forms.BackButtonBehavior.IconOverride), de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono usado para el botón Atrás.
- [`IsEnabled`](xref:Xamarin.Forms.BackButtonBehavior.IsEnabled), de tipo `boolean`, indica si se ha habilitado el botón Atrás. El valor predeterminado es `true`.
- [`TextOverride`](xref:Xamarin.Forms.BackButtonBehavior.TextOverride), de tipo `string`, el texto usado para el botón Atrás.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

En el código siguiente se muestra un ejemplo de cómo volver a definir la apariencia y el comportamiento del botón Atrás:

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

La propiedad [`Command`](xref:Xamarin.Forms.BackButtonBehavior.Command) se establece en `ICommand` para ejecutarse cuando se presiona el botón Atrás y la propiedad `IconOverride` se establece en el icono que se usa para el botón Atrás:

[![Captura de pantalla de la invalidación de un icono de botón Atrás de Shell en iOS y Android](navigation-images/back-button.png)](navigation-images/back-button-large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
