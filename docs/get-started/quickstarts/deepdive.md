---
title: Análisis detallado de inicio rápido de Xamarin.Forms
description: En este artículo se examinan los aspectos fundamentales del desarrollo de aplicaciones con Xamarin.Forms Shell. Los temas que se han tratado incluyen la anatomía de una aplicación de Xamarin.Forms Shell, los aspectos fundamentales de la arquitectura y la aplicación, y la interfaz de usuario.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: F65C83B7-BC9F-425F-8662-931B652A2946
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/25/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8e7a5a196dbbd55f8d10d1e1960f6b4c989021dc
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757648"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Análisis detallado de inicio rápido de Xamarin.Forms

En [Inicio rápido de Xamarin.Forms](~/get-started/index.yml) se ha creado la aplicación Notes. En este artículo se revisa lo que se ha compilado para comprender los aspectos fundamentales del funcionamiento de las aplicaciones de Xamarin.Forms Shell.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introducción a Visual Studio

Visual Studio organiza el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación, una biblioteca auxiliar o una aplicación de prueba, entre otros. La aplicación Notes consta de una solución que contiene tres proyectos, como se muestra en la captura de pantalla siguiente:

![Explorador de soluciones de Visual Studio](deepdive-images/vs/solution.png)

Los proyectos son:

- Notes: este proyecto es el de la biblioteca de .NET Standard que incluye todo el código compartido y la interfaz de usuario compartida.
- Notes.Android: este proyecto incluye el código específico de Android y es el punto de entrada de la aplicación Android.
- Notes.iOS: este proyecto incluye el código específico de iOS y es el punto de entrada de la aplicación iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomía de una aplicación de Xamarin.Forms

En la captura de pantalla siguiente se muestra el contenido del proyecto de biblioteca de .NET Standard Notes en Visual Studio:

![Contenido del proyecto Phoneword de .NET Standard](deepdive-images/vs/net-standard-project.png)

El proyecto tiene un nodo **Dependencias** que contiene los nodos **NuGet** y **SDK**:

- **NuGet**: paquetes NuGet Xamarin.Forms, Xamarin.Essentials, Newtonsoft.Json y sqlite-net-pcl que se han agregado al proyecto.
- **SDK**: el metapaquete `NETStandard.Library` que hace referencia al conjunto completo de paquetes NuGet que definen .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

[Visual Studio para Mac](/visualstudio/mac/) sigue la práctica de Visual Studio consistente en organizar el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación, una biblioteca auxiliar o una aplicación de prueba, entre otros. La aplicación Notes consta de una solución que contiene tres proyectos, como se muestra en la captura de pantalla siguiente:

![Panel Solución de Visual Studio para Mac](deepdive-images/vsmac/solution.png)

Los proyectos son:

- Notes: este proyecto es el de la biblioteca de .NET Standard que incluye todo el código compartido y la interfaz de usuario compartida.
- Notes.Android: este proyecto incluye el código específico de Android y es el punto de entrada de las aplicaciones Android.
- Notes.iOS: este proyecto incluye el código específico de iOS y es el punto de entrada de las aplicaciones iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomía de una aplicación de Xamarin.Forms

En la captura de pantalla siguiente se muestra el contenido del proyecto de biblioteca de .NET Standard Notes en Visual Studio para Mac:

![Contenido del proyecto de biblioteca de .NET Standard Phoneword](deepdive-images/vsmac/net-standard-project.png)

El proyecto tiene un nodo **Dependencias** que contiene los nodos **NuGet** y **SDK**:

- **NuGet**: paquetes NuGet Xamarin.Forms, Xamarin.Essentials, Newtonsoft.Json y sqlite-net-pcl que se han agregado al proyecto.
- **SDK**: el metapaquete `NETStandard.Library` que hace referencia al conjunto completo de paquetes NuGet que definen .NET Standard.

::: zone-end

El proyecto también consta de varios archivos:

- **Data\NoteDatabase.cs**: esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminarlos.
- **Models\Note.cs**: esta clase define un modelo `Note` cuyas instancias almacenarán datos sobre cada nota en la aplicación.
- **Views\AboutPage.xaml**: el marcado XAML para la clase `AboutPage`, que define la interfaz de usuario para la página de información.
- **Views\AboutPage.xaml.cs**: el código subyacente para la clase `AboutPage`, que contiene la lógica de negocios que se ejecuta cuando el usuario interactúa con la página.
- **Views\NotesPage.xaml**: el marcado XAML para la clase `NotesPage`, que define la interfaz de usuario para la página que se muestra al iniciar la aplicación.
- **Views\NotesPage.xaml.cs**: el código subyacente para la clase `NotesPage`, que contiene la lógica de negocios que se ejecuta cuando el usuario interactúa con la página.
- **Views\NoteEntryPage.xaml**: el marcado XAML para la clase `NoteEntryPage`, que define la interfaz de usuario para la página que se muestra cuando el usuario escribe una nota.
- **Views\NoteEntryPage.xaml.cs**: el código subyacente para la clase `NoteEntryPage`, que contiene la lógica de negocios que se ejecuta cuando el usuario interactúa con la página.
- **App.xaml**: el marcado XAML para la clase `App`, que define un diccionario de recursos para la aplicación.
- **App.xaml.cs**: el código subyacente para la clase `App`, que es el responsable de crear instancias de la aplicación de Shell y para controlar los eventos del ciclo de vida de la aplicación.
- **AppShell.xaml**: el marcado XAML para la clase `AppShell`, que define la jerarquía visual de la aplicación.
- **AppShell.xaml.cs**: el código subyacente para la clase `AppShell`, que crea una ruta para `NoteEntryPage`, a fin de que se pueda acceder mediante programación.
- **AssemblyInfo.cs**: este archivo contiene un atributo de aplicación sobre el proyecto, que se aplica en el nivel de ensamblado.

Para obtener más información sobre la anatomía de una aplicación de Xamarin.iOS, consulte [Anatomía de una aplicación de Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Para obtener más información sobre la anatomía de una aplicación de Xamarin.Android, consulte [Anatomía de una aplicación de Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Arquitectura y aspectos básicos de la aplicación

Una aplicación de Xamarin.Forms tiene la misma arquitectura que una aplicación multiplataforma tradicional. El código compartido normalmente se coloca en una biblioteca de .NET Standard, y las aplicaciones específicas de la plataforma consumen el código compartido. En el diagrama siguiente se muestra información general de esta relación para la aplicación Notes:

![Arquitectura de Notes](deepdive-images/architecture.png)

Para maximizar la reutilización del código de inicio, las aplicaciones de Xamarin.Forms tienen una clase única denominada `App`, que es responsable de crear instancias de la aplicación en cada plataforma, como se muestra en el ejemplo de código siguiente:

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        // ...
    }
}
```

Este código establece la propiedad `MainPage` de la clase `App` en el objeto `AppShell`. La clase `AppShell` define la jerarquía visual de la aplicación. Shell toma esta jerarquía visual y genera la interfaz de usuario para ella. Para más información sobre cómo definir la jerarquía visual de la aplicación, consulte [Jerarquía visual de la aplicación](#application-visual-hierarchy).

Además, el archivo **AssemblyInfo.cs** contiene un único atributo de aplicación, que se aplica en el nivel de ensamblado:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

El atributo [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) activa el compilador XAML, para que XAML se compile directamente en lenguaje intermedio. Para obtener más información, consulte [Compilación XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launch-the-application-on-each-platform"></a>Inicio de la aplicación en cada plataforma

La forma en que se inicia la aplicación en cada plataforma es específica de la plataforma.

### <a name="ios"></a>iOS

Para abrir la página inicial de Xamarin.Forms en iOS, en el proyecto Notes.iOS se define la clase `AppDelegate`, que hereda de la clase `FormsApplicationDelegate`:

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

El reemplazo `FinishedLaunching` inicializa el marco de Xamarin.Forms mediante una llamada al método `Init`. Esto hace que la implementación específica de iOS de Xamarin.Forms se cargue en la aplicación antes de que se establezca el controlador de vista raíz mediante la llamada al método `LoadApplication`.

### <a name="android"></a>Android

Para iniciar la página de inicio de Xamarin.Forms en Android, el proyecto Notes.Android incluye código para crear un elemento `Activity` con el atributo `MainLauncher`, y la actividad se hereda de la clase `FormsAppCompatActivity`:

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

El reemplazo `OnCreate` inicializa el marco de Xamarin.Forms mediante una llamada al método `Init`. Esto provoca que la implementación específica de Android de Xamarin.Forms se cargue en la aplicación antes de que lo haga la aplicación de Xamarin.Forms.

## <a name="application-visual-hierarchy"></a>Jerarquía visual de la aplicación

Las aplicaciones de Xamarin.Forms Shell definen la jerarquía visual de la aplicación en una clase que genera subclases de la clase `Shell`. En la aplicación de notas, se trata de la clase `Appshell`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Notes.Views"
       x:Class="Notes.AppShell">
    <TabBar>
        <ShellContent Title="Notes"
                      Icon="icon_feed.png"
                      ContentTemplate="{DataTemplate views:NotesPage}" />
        <ShellContent Title="About"
                      Icon="icon_about.png"
                      ContentTemplate="{DataTemplate views:AboutPage}" />
    </TabBar>
</Shell>
```

Este XAML consta de dos objetos principales:

- `TabBar`. El `TabBar` representa la barra de pestañas de la parte inferior y debe usarse cuando el patrón de navegación de la aplicación usa pestañas en la parte inferior. El objeto `TabBar` es un elemento secundario del objeto `Shell`.
- `ShellContent`, que representa los objetos `ContentPage` para cada pestaña de `TabBar`. Cada objeto `ShellContent` es un elemento secundario del objeto `TabBar`.

Estos objetos no representan ninguna interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario de navegación del contenido. Por lo tanto, la clase `AppShell` define dos páginas que se pueden navegar desde las pestañas inferiores. Las páginas se crean a petición, en respuesta a la navegación.

Para más información sobre las aplicaciones de Shell, consulte [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="user-interface"></a>Interfaz de usuario

Existen varios grupos de controles que se usan para crear la interfaz de usuario de una aplicación de Xamarin.Forms:

1. **Páginas**: las páginas de Xamarin.Forms representan pantallas de aplicaciones móviles multiplataforma. En la aplicación Notes se usa la clase [`ContentPage`](xref:Xamarin.Forms.ContentPage) para mostrar pantallas únicas. Para obtener más información sobre las páginas de códigos, vea [Páginas de Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Vistas**: las vistas de Xamarin.Forms son los controles que se muestran en la interfaz de usuario, como etiquetas, botones y cuadros de entrada de texto. En la aplicación Notes terminada se usan las vistas [`CollectionView`](xref:Xamarin.Forms.CollectionView), [`Editor`](xref:Xamarin.Forms.Editor) y [`Button`](xref:Xamarin.Forms.Button). Para obtener más información sobre las vistas, consulte [Vistas de Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Diseños**: los diseños de Xamarin.Forms son contenedores que se usan para crear vistas en estructuras lógicas. En la aplicación Notes se usa la clase [`StackLayout`](xref:Xamarin.Forms.StackLayout) para organizar las vistas en una pila vertical y la clase [`Grid`](xref:Xamarin.Forms.Grid) para organizar los botones de forma horizontal. Para obtener más información sobre los diseños, consulte [Diseños de Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).

En tiempo de ejecución, cada control se asignará a su equivalente nativo, que es lo que se representará.

### <a name="layout"></a>Diseño

En la aplicación Notes se usa [`StackLayout`](xref:Xamarin.Forms.StackLayout) para simplificar el desarrollo de aplicaciones multiplataforma mediante la disposición automática de las vistas en la pantalla, independientemente del tamaño de esta. Cada elemento secundario se coloca uno detrás del otro, ya sea horizontal o verticalmente, en el orden en el que se ha agregado. La cantidad de espacio que usará la clase `StackLayout` depende de cómo se establezcan las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), pero `StackLayout` intentará usar toda la pantalla de forma predeterminada.

En el código XAML siguiente se muestra un ejemplo de uso de una clase [`StackLayout`](xref:Xamarin.Forms.StackLayout) para organizar el control `NoteEntryPage`:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.Views.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

De forma predeterminada, [`StackLayout`](xref:Xamarin.Forms.StackLayout) asume una orientación vertical. Pero se puede cambiar a una orientación horizontal si se establece la propiedad [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) en el miembro de enumeración [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

> [!NOTE]
> El tamaño de las vistas se puede establecer a través de las propiedades `HeightRequest` y `WidthRequest`.

Para más información sobre la clase [`StackLayout`](xref:Xamarin.Forms.StackLayout), consulte [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md).

### <a name="responding-to-user-interaction"></a>Responder a la interacción del usuario

Un objeto que se ha definido en XAML puede desencadenar un evento que se controla en el archivo de código subyacente. En el ejemplo de código siguiente se muestra el método `OnSaveButtonClicked` del código subyacente de la clase `NoteEntryPage`, que se ejecuta en respuesta al evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) que se desencadena en el botón *Guardar*.

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    if (!string.IsNullOrWhiteSpace(note.Text))
    {
        await App.Database.SaveNoteAsync(note);
    }
    await Shell.Current.GoToAsync("..");
}
```

El método `OnSaveButtonClicked` guarda la nota en la base de datos y regresa a la página anterior. Para más información sobre la navegación, consulte [Navegación](#navigation).

> [!NOTE]
> El archivo de código subyacente de una clase XAML puede tener acceso a un objeto que se ha definido en XAML con el nombre asignado a él con el atributo `x:Name`. El valor que se ha asignado a este atributo tiene las mismas reglas que las variables de C#, ya que debe comenzar con una letra o guion bajo y no contener espacios incrustados.

La conexión del botón Guardar con el método `OnSaveButtonClicked` se realiza en el marcado XAML de la clase `NoteEntryPage`:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listas

[`CollectionView`](xref:Xamarin.Forms.CollectionView) es responsable de mostrar una colección de elementos en una lista. De manera predeterminada, los elementos de la lista se muestran verticalmente y cada elemento se muestra en una sola fila.

En el ejemplo de código siguiente se muestra el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) de `NotesPage`:

```xaml
<CollectionView x:Name="collectionView"
                Margin="{StaticResource PageMargin}"
                SelectionMode="Single"
                SelectionChanged="OnSelectionChanged">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="10" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Label Text="{Binding Text}"
                       FontSize="Medium" />
                <Label Text="{Binding Date}"
                       TextColor="{StaticResource TertiaryColor}"
                       FontSize="Small" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El diseño de cada fila de [`CollectionView`](xref:Xamarin.Forms.CollectionView) se define dentro del elemento [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) y se usa el enlace de datos para mostrar las notas recuperadas por la aplicación. La propiedad [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) está establecida en el origen de datos, en **NotesPage.xaml.cs**:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    collectionView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Este código rellena [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todas las notas almacenadas en la base de datos y se ejecuta cuando aparece la página.

Cuando se selecciona un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView), se desencadena el evento `SelectionChanged`. Cuando se desencadena el evento, se ejecuta un controlador de eventos denominado `OnSelectionChanged`:

```csharp
async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (e.CurrentSelection != null)
    {
        // ...
    }
}
```

El evento `SelectionChanged` puede acceder al objeto asociado al elemento a través de la propiedad `e.CurrentSelection`.

Para más información sobre la clase [`CollectionView`](xref:Xamarin.Forms.CollectionView), consulte [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="navigation"></a>Navegación

La navegación se realiza en una aplicación de Shell mediante la especificación de un URI al que navegar. Los URI de navegación tienen tres componentes:

- Una *ruta*, que define la ruta de acceso al contenido que existe como parte de la jerarquía visual de Shell.
- Una *página*. Las páginas que no existen en la jerarquía visual de Shell se pueden insertar en la pila de navegación desde cualquier lugar dentro de una aplicación de Shell. Por ejemplo, `NoteEntryPage` no se define en la jerarquía visual de Shell, pero se puede insertar en la pila de navegación si es necesario.
- Uno o varios *parámetros de consulta*. Los parámetros de consulta son parámetros que se pueden pasar a la página de destino durante la navegación.

No es necesario que un URI de navegación incluya los tres componentes pero, cuando lo hace, la estructura es: //route/page?queryParameters

> [!NOTE]
> Las rutas se pueden definir en los elementos de la jerarquía visual de Shell a través de la propiedad `Route`. Sin embargo, si la propiedad `Route` no está establecida, como en la aplicación de notas, se genera una ruta en tiempo de ejecución.

Para más información sobre la navegación de Shell, consulte [Navegación de Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

### <a name="register-routes"></a>Registro de rutas

Para ir a una página que no existe en la jerarquía visual de Shell, es necesario que se registre primero con el sistema de enrutamiento de Shell. con el método `Routing.RegisterRoute`. En la aplicación de notas, esto ocurre en el constructor `AppShell`:

```csharp
public partial class AppShell : Shell
{
    public AppShell()
    {
        // ...
        Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
    }
}
```

En este ejemplo, se registra una ruta denominada `NoteEntryPage` con respecto al tipo `NoteEntryPage`. Luego, es posible navegar a esta página desde cualquier lugar de la aplicación mediante la navegación basada en el URI.

### <a name="perform-navigation"></a>Realización de la navegación

La navegación se realiza mediante el método `GoToAsync`, que acepta un argumento que representa la ruta a la que navegar:

```csharp
await Shell.Current.GoToAsync("NoteEntryPage");
```

En este ejemplo, se navega a `NoteEntryPage`.

> [!IMPORTANT]
> Se crea una pila de navegación cuando se navega a una página que no está en la jerarquía visual de Shell.

Al navegar a una página, los datos se pueden pasar a la página como un parámetro de consulta:

```csharp
async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (e.CurrentSelection != null)
    {
        // Navigate to the NoteEntryPage, passing the ID as a query parameter.
        Note note = (Note)e.CurrentSelection.FirstOrDefault();
        await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
    }
}
```

En este ejemplo, se recupera el elemento actualmente seleccionado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) y navega a `NoteEntryPage`, con el valor de propiedad `ID` del objeto `Note` que se pasa como parámetro de consulta a la propiedad `NoteEntryPage.ItemId`.

Para recibir los datos pasados, la clase `NoteEntryPage` se decora con el atributo `QueryPropertyAttribute`.

```csharp
[QueryProperty(nameof(ItemId), nameof(ItemId))]
public partial class NoteEntryPage : ContentPage
{
    public string ItemId
    {
        set
        {
            LoadNote(value);
        }
    }
    // ...
}
```

El primer argumento de `QueryPropertyAttribute` especifica el nombre de la propiedad `ItemId` que recibirá los datos, mientras que el segundo argumento especifica el identificador del parámetro de consulta. Por tanto, el elemento `QueryPropertyAttribute` del ejemplo anterior especifica que la propiedad `ItemId` recibirá los datos pasados al parámetro de consulta `ItemId` del URI en la llamada al método `GoToAsync`. Luego, la propiedad `ItemId` llama al método `LoadNote` para recuperar la nota del dispositivo.

La navegación hacia atrás se puede llevar a cabo si especifica ".." como el argumento para el método `GoToAsync`:

```csharp
await Shell.Current.GoToAsync("..");
```

Para más información sobe la navegación hacia atrás, consulte [Navegación hacia atrás](~/xamarin-forms/app-fundamentals/shell/navigation.md#backwards-navigation).

## <a name="data-binding"></a>Enlace de datos

El enlace de datos se usa para simplificar la forma en que una aplicación de Xamarin.Forms muestra sus datos e interactúa con ellos. Establece una conexión entre la interfaz de usuario y la aplicación subyacente. La clase [`BindableObject`](xref:Xamarin.Forms.BindableObject) contiene gran parte de la infraestructura para admitir el enlace de datos.

El enlace de datos conecta dos objetos, denominados *origen* y *destino*. El objeto de *origen* proporciona los datos. El objeto de *destino* usa (y, a menudo, muestra) los datos del objeto de origen. Por ejemplo, un control [`Editor`](xref:Xamarin.Forms.Editor) (objeto de *destino*) normalmente enlazará su propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) a una propiedad `string` pública en un objeto de *origen*. En el diagrama siguiente se muestra la relación de enlace:

![Enlace de datos](deepdive-images/data-binding.png)

El principal beneficio del enlace de datos es que ya no tiene que preocuparse de sincronizar los datos entre las vistas y el origen de datos. Los cambios en el objeto de *origen* se insertan automáticamente en el objeto de *destino* en segundo plano por medio del marco de enlace, mientras que los cambios en el objeto de destino pueden insertarse de manera opcional en el objeto de *origen*.

El establecimiento del enlace de datos es un proceso de dos pasos:

- La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del objeto de *destino* se debe establecer en el de *origen*.
- Es necesario establecer un enlace entre el *destino* y el *origen*. En XAML, esto se consigue mediante la extensión de marcado [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

En la aplicación Notes, el destino de enlace es el elemento [`Editor`](xref:Xamarin.Forms.Editor) que muestra una nota, mientras que la instancia de `Note` establecida como [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` es el origen de enlace. Inicialmente, `BindingContext` de `NoteEntryPage` se establece cuando se ejecuta el constructor de página:

```csharp
public NoteEntryPage()
{
    // ...
    BindingContext = new Note();
}
```

En este ejemplo, el `BindingContext` de la página está establecido en una `Note` nueva cuando se crea `NoteEntryPage`. Esto controla el escenario en que se agrega una nota nueva a la aplicación.

Además, el `BindingContext` de la página también se establece cuando se produce la navegación a `NoteEntryPage`, siempre que haya una nota existente seleccionada en `NotesPage`:

```csharp
[QueryProperty(nameof(ItemId), nameof(ItemId))]
public partial class NoteEntryPage : ContentPage
{
    public string ItemId
    {
        set
        {
            LoadNote(value);
        }

        async void LoadNote(string itemId)
        {
            try
            {
                int id = Convert.ToInt32(itemId);
                // Retrieve the note and set it as the BindingContext of the page.
                Note note = await App.Database.GetNoteAsync(id);
                BindingContext = note;
            }
            catch (Exception)
            {
                Console.WriteLine("Failed to load note.");
            }
        }    
        // ...    
    }
}
```

En este ejemplo, cuando se produce la navegación por la página, el `BindingContext`se establece en el objeto `Note` seleccionado una vez que se recupera de la base de datos.

> [!IMPORTANT]
> Aunque la propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto de *destino* se puede establecer de manera individual, no es necesario hacerlo. `BindingContext` es una propiedad especial que heredan todos sus elementos secundarios. Por tanto, cuando la propiedad `BindingContext` de [`ContentPage`](xref:Xamarin.Forms.ContentPage) se establece en una instancia de `Note`, todos los elementos secundarios de `ContentPage` tienen la misma propiedad `BindingContext` y se pueden enlazar a propiedades públicas del objeto `Note`.

Después, el objeto [`Editor`](xref:Xamarin.Forms.Editor) de `NoteEntryPage` se enlaza a la propiedad `Text` del objeto `Note`:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}" />
```

Se establece un enlace entre la propiedad [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) y la propiedad `Text` del objeto de *origen*. Los cambios realizados en `Editor` se propagarán de forma automática al objeto `Note`. De forma similar, si se realizan cambios en la propiedad `Note.Text`, el motor de enlace de Xamarin.Forms también actualizará el contenido de `Editor`. Esto se conoce como *enlace bidireccional*.

Para obtener más información sobre el enlace de datos, consulte [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Aplicación de estilos

Las aplicaciones de Xamarin.Forms suelen contener varios elementos visuales que tienen un aspecto idéntico. Establecer la apariencia de cada elemento visual puede ser repetitivo y propenso a errores. En su lugar, se pueden crear estilos que definan el aspecto y, después, aplicarlos a los elementos visuales necesarios.

La clase [`Style`](xref:Xamarin.Forms.Style) agrupa una colección de valores de propiedad en un objeto que después se puede aplicar a varias instancias de elementos visuales. Los estilos se almacenan en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), ya sea en el nivel de la aplicación, de la página o de la vista. La elección de dónde se puede definir un elemento `Style` afecta a dónde se puede usar:

- Las instancias de [`Style`](xref:Xamarin.Forms.Style) definidas en el nivel de aplicación se pueden aplicar en toda la aplicación.
- Las instancias de [`Style`](xref:Xamarin.Forms.Style) definidas en el nivel de página se pueden aplicar a la página y a sus elementos secundarios.
- Las instancias de [`Style`](xref:Xamarin.Forms.Style) definidas en el nivel de vista se pueden aplicar a la vista y a sus elementos secundarios.

> [!IMPORTANT]
> Todos los estilos que se usen en la aplicación se almacenan en el diccionario de recursos de la aplicación para evitar la duplicación. Pero el código de XAML que es específico de una página no debería incluirse en el diccionario de recursos de la aplicación, dado que los recursos se analizarán en el inicio de la aplicación en lugar de cuando los solicite una página. Para más información, consulte [Reducción del tamaño del diccionario de recursos de aplicación](~/xamarin-forms/deploy-test/performance.md#reduce-the-application-resource-dictionary-size).

Cada instancia de [`Style`](xref:Xamarin.Forms.Style) contiene una colección de uno o varios objetos [`Setter`](xref:Xamarin.Forms.Setter), donde cada objeto `Setter` tiene un elemento [`Property`](xref:Xamarin.Forms.Setter.Property) y un elemento [`Value`](xref:Xamarin.Forms.Setter.Value). `Property` es el nombre de la propiedad enlazable del elemento al que se aplica el estilo y `Value` es el valor que se aplica a la propiedad. En el ejemplo de código siguiente se muestra un estilo de `NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.Views.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

Este estilo se aplica a cualquier instancia de [`Editor`](xref:Xamarin.Forms.Editor) de la página.

Al crear un elemento [`Style`](xref:Xamarin.Forms.Style), siempre se requiere la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType).

> [!NOTE]
> Normalmente, la aplicación de estilo a una aplicación Xamarin.Forms se realiza mediante estilos XAML. Aun así, Xamarin.Forms también admite aplicar estilo a los elementos visuales mediante hojas de estilo CSS (CSS). Para obtener más información, vea [Aplicación de estilos para aplicaciones Xamarin.Forms con hojas de estilo (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Para obtener más información sobre los estilos XAML, vea [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="test-and-deployment"></a>Prueba e implementación

Tanto Visual Studio para Mac como Visual Studio ofrecen numerosas opciones para probar e implementar una aplicación. Depurar aplicaciones es una parte común del ciclo de vida del desarrollo de la aplicación y ayuda a diagnosticar problemas de código. Para obtener más información, consulte [Set a Breakpoint (Establecer un punto de interrupción)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Step Through Code (Recorrer el código paso a paso)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) y [Output Information to the Log Window (Información de salida para la ventana Registro)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Los simuladores son un buen lugar para comenzar a implementar y probar una aplicación, y cuentan con una funcionalidad que resulta útil a la hora de probar las aplicaciones. Sin embargo, los usuarios no usarán la aplicación final en un simulador, por lo que las aplicaciones deben probarse en dispositivos reales desde el primer momento y con frecuencia. Para obtener más información sobre el aprovisionamiento de dispositivos de iOS, consulte [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md). Para obtener más información sobre el aprovisionamiento de dispositivos de Android, consulte [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Pasos siguientes

En este análisis detallado se han examinado los aspectos fundamentales del desarrollo de aplicaciones con Xamarin.Forms Shell. Se recomienda que, como paso siguiente, lea sobre las funcionalidades que se indican a continuación:

- Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones móviles. Para obtener más información, consulte [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).
- Existen varios grupos de controles que se usan para crear la interfaz de usuario de una aplicación de Xamarin.Forms. Para obtener más información, vea [Referencia de controles](~/xamarin-forms/user-interface/controls/index.md).
- El enlace de datos es la técnica que consiste en vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejen automáticamente en la otra propiedad. Para obtener más información, vea [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms ofrece varias experiencias de navegación por páginas, en función del tipo de página que se use. Para obtener más información, consulte [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md) (Navegación).
- Los estilos permiten reducir el uso de marcado repetitivo y conseguir que la apariencia de las aplicaciones pueda cambiarse con mayor facilidad. Para obtener más información, consulte [Aplicación de estilos a aplicaciones Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Las plantillas de datos permiten definir la presentación de los datos en las vistas admitidas. Para obtener más información, consulte [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Plantillas de datos).
- Los efectos también permiten personalizar los controles nativos de cada plataforma. Los efectos se crean en proyectos específicos de la plataforma mediante la creación de subclases de la clase [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2). Para usarlos, se adjuntan a un control adecuado de Xamarin.Forms. Para obtener más información, consulte [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Efectos).
- Cada página, diseño y control se representan de forma diferente en cada plataforma mediante una clase `Renderer` que, a su vez, crea un control nativo, lo organiza en la pantalla y agrega el comportamiento especificado al código compartido. Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Para obtener más información, consulte [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Representadores personalizados).
- El código compartido puede tener acceso a la funcionalidad nativa mediante la clase [`DependencyService`](xref:Xamarin.Forms.DependencyService). Para obtener más información, consulte [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Acceso a características nativas con DependencyService).

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Lenguaje XAML (eXtensible Application Markup Language)](~/xamarin-forms/xaml/index.yml)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Controls Reference](~/xamarin-forms/user-interface/controls/index.md) (Referencia de controles)
- [Ejemplos de introducción](/samples/browse/?products=xamarin&term=Xamarin.Forms%2bget%2bstarted)
- [Ejemplos de Xamarin.Forms](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Referencia de API de Xamarin.Forms](xref:Xamarin.Forms)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
