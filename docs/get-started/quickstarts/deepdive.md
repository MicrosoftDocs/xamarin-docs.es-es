---
title: 'title: "Xamarin.Forms: Análisis detallado de inicio rápido" description: "En este artículo se examinan los aspectos fundamentales del desarrollo de aplicaciones con Xamarin.Forms."'
description: 'Los temas que se han tratado incluyen la anatomía de una aplicación de Xamarin.Forms, la arquitectura y los elementos fundamentales, y la interfaz de usuario." zone_pivot_groups: platform ms.topic: quickstart ms.prod: xamarin ms.custom: video ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 11/27/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1bfb76f71a2ac9d8bc9ae84152501909000b9623
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84132527"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Análisis detallado de inicio rápido de Xamarin.Forms

En [Inicio rápido de Xamarin.Forms](~/get-started/index.yml) se ha creado la aplicación Notes. En este artículo se revisa lo que se ha compilado para comprender los aspectos fundamentales del funcionamiento de las aplicaciones de Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introducción a Visual Studio

Visual Studio organiza el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación, una biblioteca auxiliar o una aplicación de prueba, entre otros. La aplicación Notes consta de una solución que contiene cuatro proyectos, como se muestra en la captura de pantalla siguiente:

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

Los proyectos son:

- Notes: este proyecto es el de la biblioteca de .NET Standard que incluye todo el código compartido y la interfaz de usuario compartida.
- Notes.Android: este proyecto incluye el código específico de Android y es el punto de entrada de la aplicación Android.
- Notes.iOS: este proyecto incluye el código específico de iOS y es el punto de entrada de la aplicación iOS.
- Notes.UWP: este proyecto incluye el código específico de Plataforma universal de Windows (UWP) y es el punto de entrada de la aplicación para UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomía de una aplicación de Xamarin.Forms

En la captura de pantalla siguiente se muestra el contenido del proyecto de biblioteca de .NET Standard Notes en Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

El proyecto tiene un nodo **Dependencias** que contiene los nodos **NuGet** y **SDK**:

- **NuGet**: paquetes NuGet Xamarin.Forms y sqlite-net-pcl que se han agregado al proyecto.
- **SDK**: el metapaquete `NETStandard.Library` que hace referencia al conjunto completo de paquetes NuGet que definen .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

[Visual Studio para Mac](/visualstudio/mac/) sigue la práctica de Visual Studio consistente en organizar el código en *soluciones* y *proyectos*. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación, una biblioteca auxiliar o una aplicación de prueba, entre otros. La aplicación Notes consta de una solución que contiene tres proyectos, como se muestra en la captura de pantalla siguiente:

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

Los proyectos son:

- Notes: este proyecto es el de la biblioteca de .NET Standard que incluye todo el código compartido y la interfaz de usuario compartida.
- Notes.Android: este proyecto incluye el código específico de Android y es el punto de entrada de las aplicaciones Android.
- Notes.iOS: este proyecto incluye el código específico de iOS y es el punto de entrada de las aplicaciones iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomía de una aplicación de Xamarin.Forms

En la captura de pantalla siguiente se muestra el contenido del proyecto de biblioteca de .NET Standard Notes en Visual Studio para Mac:

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

El proyecto tiene un nodo **Dependencias** que contiene los nodos **NuGet** y **SDK**:

- **NuGet**: paquetes NuGet Xamarin.Forms y sqlite-net-pcl que se han agregado al proyecto.
- **SDK**: el metapaquete `NETStandard.Library` que hace referencia al conjunto completo de paquetes NuGet que definen .NET Standard.

::: zone-end

El proyecto también consta de varios archivos:

- **Data\NoteDatabase.cs**: esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminarlos.
- **Models\Note.cs**: esta clase define un modelo `Note` cuyas instancias almacenarán datos sobre cada nota en la aplicación.
- **App.xaml**: el marcado XAML para la clase `App`, que define un diccionario de recursos para la aplicación.
- **App.xaml.cs**: el código subyacente para la clase `App`, que es el responsable de crear instancias de la primera página que se mostrarán mediante la aplicación en cada plataforma, y para controlar los eventos del ciclo de vida de la aplicación.
- **AssemblyInfo.cs**: este archivo contiene un atributo de aplicación sobre el proyecto, que se aplica en el nivel de ensamblado.
- **NotesPage.xaml**: el marcado XAML para la clase `NotesPage`, que define la interfaz de usuario para la página que se muestra al iniciar la aplicación.
- **NotesPage.xaml.cs**: el código subyacente para la clase `NotesPage`, que contiene la lógica de negocios que se ejecuta cuando el usuario interactúa con la página.
- **NoteEntryPage.xaml**: el marcado XAML para la clase `NoteEntryPage`, que define la interfaz de usuario para la página que se muestra cuando el usuario escribe una nota.
- **NoteEntryPage.xaml.cs**: el código subyacente para la clase `NoteEntryPage`, que contiene la lógica de negocios que se ejecuta cuando el usuario interactúa con la página.

Para obtener más información sobre la anatomía de una aplicación de Xamarin.iOS, consulte [Anatomía de una aplicación de Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Para obtener más información sobre la anatomía de una aplicación de Xamarin.Android, consulte [Anatomía de una aplicación de Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Arquitectura y aspectos básicos de la aplicación

Una aplicación de Xamarin.Forms tiene la misma arquitectura que una aplicación multiplataforma tradicional. El código compartido normalmente se coloca en una biblioteca de .NET Standard, y las aplicaciones específicas de la plataforma consumen el código compartido. En el diagrama siguiente se muestra información general de esta relación para la aplicación Notes:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

Para maximizar la reutilización del código de inicio, las aplicaciones de Xamarin.Forms tienen una clase única denominada `App` que es responsable de crear instancias de la primera página que mostrará la aplicación en cada plataforma, como se muestra en el siguiente ejemplo de código:

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

En este código se establece la propiedad `MainPage` de la clase `App` en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) cuyo contenido es una instancia de `NotesPage`.

Además, el archivo **AssemblyInfo.cs** contiene un único atributo de aplicación, que se aplica en el nivel de ensamblado:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

El atributo [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) activa el compilador XAML, para que XAML se compile directamente en lenguaje intermedio. Para obtener más información, consulte [Compilación XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Inicio de la aplicación en cada plataforma

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

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En las aplicaciones de Plataforma universal de Windows (UWP), el método `Init` que inicializa el marco de Xamarin.Forms se invoca desde la clase `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Esto hace que la implementación específica de UWP de Xamarin.Forms se cargue en la aplicación. La página inicial de Xamarin.Forms se inicia mediante la clase `MainPage`:

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

La aplicación de Xamarin.Forms se carga con el método `LoadApplication`.

> [!NOTE]
> Las aplicaciones para la Plataforma universal de Windows se pueden compilar con Xamarin.Forms, pero solo mediante Visual Studio en Windows.

::: zone-end

## <a name="user-interface"></a>Interfaz de usuario

Existen cuatro grupos de controles principales que se usan para crear la interfaz de usuario de una aplicación Xamarin.Forms:

1. **Páginas**: las páginas de Xamarin.Forms representan pantallas de aplicaciones móviles multiplataforma. En la aplicación Notes se usa la clase [`ContentPage`](xref:Xamarin.Forms.ContentPage) para mostrar pantallas únicas. Para obtener más información sobre las páginas de códigos, vea [Páginas de Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Vistas**: las vistas de Xamarin.Forms son los controles que se muestran en la interfaz de usuario, como etiquetas, botones y cuadros de entrada de texto. En la aplicación Notes terminada se usan las vistas [`ListView`](xref:Xamarin.Forms.ListView), [`Editor`](xref:Xamarin.Forms.Editor) y [`Button`](xref:Xamarin.Forms.Button). Para obtener más información sobre las vistas, consulte [Vistas de Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Diseños**: los diseños de Xamarin.Forms son contenedores que se usan para crear vistas en estructuras lógicas. En la aplicación Notes se usa la clase [`StackLayout`](xref:Xamarin.Forms.StackLayout) para organizar las vistas en una pila vertical y la clase [`Grid`](xref:Xamarin.Forms.Grid) para organizar los botones de forma horizontal. Para obtener más información sobre los diseños, consulte [Diseños de Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Celdas**: las celdas de Xamarin.Forms son elementos especializados que se usan para los elementos de una lista, y describen cómo debe dibujarse cada elemento de una lista. En la aplicación Notes se usa [`TextCell`](xref:Xamarin.Forms.TextCell) para mostrar dos elementos para cada fila de la lista. Para obtener más información sobre las celdas, consulte [Celdas de Xamarin.Forms](~/xamarin-forms/user-interface/controls/cells.md).

En tiempo de ejecución, cada control se asignará a su equivalente nativo, que es lo que se representará.

### <a name="layout"></a>Diseño

En la aplicación Notes se usa [`StackLayout`](xref:Xamarin.Forms.StackLayout) para simplificar el desarrollo de aplicaciones multiplataforma mediante la disposición automática de las vistas en la pantalla, independientemente del tamaño de esta. Cada elemento secundario se coloca uno detrás del otro, ya sea horizontal o verticalmente, en el orden en el que se ha agregado. La cantidad de espacio que usará la clase `StackLayout` depende de cómo se establezcan las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), pero `StackLayout` intentará usar toda la pantalla de forma predeterminada.

En el código XAML siguiente se muestra un ejemplo de uso de una clase [`StackLayout`](xref:Xamarin.Forms.StackLayout) para organizar el control `NoteEntryPage`:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
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

Para obtener más información sobre la clase [`StackLayout`](xref:Xamarin.Forms.StackLayout), consulte [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md).

### <a name="responding-to-user-interaction"></a>Responder a la interacción del usuario

Un objeto que se ha definido en XAML puede desencadenar un evento que se controla en el archivo de código subyacente. En el ejemplo de código siguiente se muestra el método `OnSaveButtonClicked` del código subyacente de la clase `NoteEntryPage`, que se ejecuta en respuesta al evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) que se desencadena en el botón *Guardar*.

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

El método `OnSaveButtonClicked` guarda la nota en la base de datos y regresa a la página anterior.

> [!NOTE]
> El archivo de código subyacente de una clase XAML puede tener acceso a un objeto que se ha definido en XAML con el nombre asignado a él con el atributo `x:Name`. El valor que se ha asignado a este atributo tiene las mismas reglas que las variables de C#, ya que debe comenzar con una letra o guion bajo y no contener espacios incrustados.

La conexión del botón Guardar con el método `OnSaveButtonClicked` se realiza en el marcado XAML de la clase `NoteEntryPage`:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listas

[`ListView`](xref:Xamarin.Forms.ListView) es responsable de mostrar una colección de elementos de forma vertical en una lista. Cada elemento de `ListView` se incluirá en una sola celda.

En el ejemplo de código siguiente se muestra el elemento [`ListView`](xref:Xamarin.Forms.ListView) de `NotesPage`:

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

El diseño de cada fila de [`ListView`](xref:Xamarin.Forms.ListView) se define dentro del elemento [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) y se usa el enlace de datos para mostrar las notas recuperadas por la aplicación. La propiedad [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) está establecida en el origen de datos, en `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Este código rellena el elemento [`ListView`](xref:Xamarin.Forms.ListView) con todas las notas almacenadas en la base de datos.

Cuando se selecciona una fila en [`ListView`](xref:Xamarin.Forms.ListView), se desencadena el evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected). Cuando se desencadena el evento, se ejecuta un controlador de eventos denominado `OnListViewItemSelected`:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

El evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) puede acceder al objeto asociado a la celda a través de la propiedad [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem).

Para más información sobre la clase [`ListView`](xref:Xamarin.Forms.ListView), vea [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navegación

Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo [`Page`](xref:Xamarin.Forms.Page) que se use. Para las instancias de [`ContentPage`](xref:Xamarin.Forms.ContentPage), la navegación puede ser jerárquica o modal. Para obtener información sobre la navegación modal, consulte [Páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Las clases [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) y [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) proporcionan experiencias de navegación alternativas. Para obtener más información, consulte [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md) (Navegación).

En la navegación jerárquica, se usa la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para navegar por una pila de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage), hacia delante y atrás, como se prefiera. La clase implementa la navegación como una pila de objetos [`Page`](xref:Xamarin.Forms.Page) en la que el último en entrar es el primero en salir (LIFO). Para pasar de una página a otra, una aplicación insertará una nueva página en la pila de navegación, donde se convertirá en la página activa. Para volver a la página anterior, la aplicación mostrará la página actual de la pila de navegación y la nueva página de nivel superior se convertirá en la página activa.

La clase `NavigationPage` también agregará una barra de navegación en la parte superior de la página que muestra un título y un botón **Atrás** adecuado para la plataforma para volver a la página anterior.

La primera página que se agrega a una pila de navegación se denomina página *raíz* de la aplicación y, en el ejemplo de código siguiente, se muestra cómo se consigue esto en la aplicación Notes:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Todas las instancias [`ContentPage`](xref:Xamarin.Forms.ContentPage) tienen una propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) que expone métodos para modificar la pila de la página. Solo se deberían invocar estos métodos si la aplicación incluye una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para navegar a la `NoteEntryPage`, es necesario invocar el método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Esto hace que el nuevo objeto `NoteEntryPage` se inserte en la pila de navegación, donde se convierte en la página activa.

La página activa se puede extraer de la pila de navegación. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla. Para volver mediante programación a la página original, el objeto `NoteEntryPage` debe invocar el método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PopAsync();
```

Para obtener más información sobre la navegación jerárquica, consulte [Hierarchical Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Navegación jerárquica).

## <a name="data-binding"></a>Enlace de datos

El enlace de datos se usa para simplificar la forma en que una aplicación de Xamarin.Forms muestra sus datos e interactúa con ellos. Establece una conexión entre la interfaz de usuario y la aplicación subyacente. La clase [`BindableObject`](xref:Xamarin.Forms.BindableObject) contiene gran parte de la infraestructura para admitir el enlace de datos.

El enlace de datos conecta dos objetos, denominados *origen* y *destino*. El objeto de *origen* proporciona los datos. El objeto de *destino* usa (y, a menudo, muestra) los datos del objeto de origen. Por ejemplo, un control [`Editor`](xref:Xamarin.Forms.Editor) (objeto de *destino*) normalmente enlazará su propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) a una propiedad `string` pública en un objeto de *origen*. En el diagrama siguiente se muestra la relación de enlace:

![](deepdive-images/data-binding.png "Data Binding")

El principal beneficio del enlace de datos es que ya no tiene que preocuparse de sincronizar los datos entre las vistas y el origen de datos. Los cambios en el objeto de *origen* se insertan automáticamente en el objeto de *destino* en segundo plano por medio del marco de enlace, mientras que los cambios en el objeto de destino pueden insertarse de manera opcional en el objeto de *origen*.

El establecimiento del enlace de datos es un proceso de dos pasos:

- La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del objeto de *destino* se debe establecer en el de *origen*.
- Es necesario establecer un enlace entre el *destino* y el *origen*. En XAML, esto se consigue mediante la extensión de marcado [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

En la aplicación Notes, el destino de enlace es el elemento [`Editor`](xref:Xamarin.Forms.Editor) que muestra una nota, mientras que la instancia de `Note` establecida como [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` es el origen de enlace.

El objeto `BindingContext` de `NoteEntryPage` se establece durante la navegación de la página, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

En el método `OnNoteAddedClicked`, que se ejecuta cuando se agrega una nueva nota a la aplicación, el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` se establece en una nueva instancia de `Note`. En el método `OnListViewItemSelected`, que se ejecuta cuando se selecciona una nota existente en el elemento [`ListView`](xref:Xamarin.Forms.ListView), el objeto `BindingContext` de `NoteEntryPage` se establece en la instancia de `Note` seleccionada, a la que se accede a través de la propiedad [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem).

> [!IMPORTANT]
> Aunque la propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto de *destino* se puede establecer de manera individual, no es necesario hacerlo. `BindingContext` es una propiedad especial que heredan todos sus elementos secundarios. Por tanto, cuando la propiedad `BindingContext` de [`ContentPage`](xref:Xamarin.Forms.ContentPage) se establece en una instancia de `Note`, todos los elementos secundarios de `ContentPage` tienen la misma propiedad `BindingContext` y se pueden enlazar a propiedades públicas del objeto `Note`.

Después, el objeto [`Editor`](xref:Xamarin.Forms.Editor) de `NoteEntryPage` se enlaza a la propiedad `Text` del objeto `Note`:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
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
> Todos los estilos que se usen en la aplicación se almacenan en el diccionario de recursos de la aplicación para evitar la duplicación. Pero el código de XAML que es específico de una página no debería incluirse en el diccionario de recursos de la aplicación, dado que los recursos se analizarán en el inicio de la aplicación en lugar de cuando los solicite una página.

Cada instancia de [`Style`](xref:Xamarin.Forms.Style) contiene una colección de uno o varios objetos [`Setter`](xref:Xamarin.Forms.Setter), donde cada objeto `Setter` tiene un elemento [`Property`](xref:Xamarin.Forms.Setter.Property) y un elemento [`Value`](xref:Xamarin.Forms.Setter.Value). `Property` es el nombre de la propiedad enlazable del elemento al que se aplica el estilo y `Value` es el valor que se aplica a la propiedad. En el ejemplo de código siguiente se muestra un estilo de `NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
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

### <a name="providing-platform-specific-styles"></a>Proporcionar estilos específicos de la plataforma

Las extensiones de marcado `OnPlatform` permiten personalizar la apariencia de la interfaz de usuario para cada plataforma:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

Este elemento [`Style`](xref:Xamarin.Forms.Style) establece otros valores [`Color`](xref:Xamarin.Forms.Color) para las propiedades [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) y [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), en función de la plataforma que se use.

Para más información sobre las extensiones de marcado, vea [Extensiones de marcado para el lenguaje XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Para obtener información sobre de la extensión de marcado `OnPlatform`, vea [Extensión de marcado OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Pruebas e implementación

Tanto Visual Studio para Mac como Visual Studio ofrecen numerosas opciones para probar e implementar una aplicación. Depurar aplicaciones es una parte común del ciclo de vida del desarrollo de la aplicación y ayuda a diagnosticar problemas de código. Para obtener más información, consulte [Set a Breakpoint (Establecer un punto de interrupción)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Step Through Code (Recorrer el código paso a paso)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) y [Output Information to the Log Window (Información de salida para la ventana Registro)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Los simuladores son un buen lugar para comenzar a implementar y probar una aplicación, y cuentan con una funcionalidad que resulta útil a la hora de probar las aplicaciones. Sin embargo, los usuarios no usarán la aplicación final en un simulador, por lo que las aplicaciones deben probarse en dispositivos reales desde el primer momento y con frecuencia. Para obtener más información sobre el aprovisionamiento de dispositivos de iOS, consulte [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md). Para obtener más información sobre el aprovisionamiento de dispositivos de Android, consulte [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Pasos siguientes

En este análisis detallado se han examinado los aspectos fundamentales del desarrollo de aplicaciones con Xamarin.Forms. Se recomienda que, como paso siguiente, lea sobre las funcionalidades que se indican a continuación:

- Existen cuatro grupos de controles principales que se usan para crear la interfaz de usuario de una aplicación Xamarin.Forms. Para obtener más información, vea [Referencia de controles](~/xamarin-forms/user-interface/controls/index.md).
- El enlace de datos es la técnica que consiste en vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejen automáticamente en la otra propiedad. Para obtener más información, vea [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de página que se use. Para obtener más información, consulte [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md) (Navegación).
- Los estilos permiten reducir el uso de marcado repetitivo y conseguir que la apariencia de las aplicaciones pueda cambiarse con mayor facilidad. Para obtener más información, consulte [Aplicación de estilos a aplicaciones Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Las extensiones de marcado XAML extienden las funciones avanzadas y la flexibilidad de XAML al permitir establecer atributos de elementos desde orígenes distintos de cadenas de texto literales. Para obtener más información, vea [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Las plantillas de datos permiten definir la presentación de los datos en las vistas admitidas. Para obtener más información, consulte [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Plantillas de datos).
- Cada página, diseño y control se representan de forma diferente en cada plataforma mediante una clase `Renderer` que, a su vez, crea un control nativo, lo organiza en la pantalla y agrega el comportamiento especificado al código compartido. Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Para obtener más información, consulte [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Representadores personalizados).
- Los efectos también permiten personalizar los controles nativos de cada plataforma. Los efectos se crean en proyectos específicos de la plataforma mediante la creación de subclases de la clase [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2). Para usarlos, se adjuntan a un control adecuado de Xamarin.Forms. Para obtener más información, consulte [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Efectos).
- El código compartido puede tener acceso a la funcionalidad nativa mediante la clase [`DependencyService`](xref:Xamarin.Forms.DependencyService). Para obtener más información, consulte [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Acceso a características nativas con DependencyService).

También es recomendable el libro de Charles Petzold titulado [_Creating Mobile Apps with Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) (Creación de aplicaciones móviles con Xamarin.Forms) para obtener más información sobre Xamarin.Forms. El libro está disponible como un archivo PDF o en una variedad de formatos de libro electrónico.

## <a name="related-links"></a>Vínculos relacionados

- [Lenguaje XAML (eXtensible Application Markup Language)](~/xamarin-forms/xaml/index.yml)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Controls Reference](~/xamarin-forms/user-interface/controls/index.md) (Referencia de controles)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Ejemplos de Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Getting Started Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started) (Ejemplos de introducción)
- [Referencia de la API Xamarin.Forms](xref:Xamarin.Forms)
- [Aprendizaje autoguiado gratuito (vídeo)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
