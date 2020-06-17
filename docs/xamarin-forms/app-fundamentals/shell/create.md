---
title: "Creación de una aplicación de Xamarin.Forms Shell" description: "El proceso para crear una aplicación de Xamarin.Forms Shell consiste en crear un archivo XAML que sirva de subclase de la clase Shell, establecer la propiedad MainPage de la clase App de la aplicación en el objeto Shell con subclases y, después, describir la jerarquía visual de la aplicación en la clase Shell con subclases."
ms.prod: xamarin ms.assetid: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 05/24/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="create-a-xamarinforms-shell-application"></a>Creación de una aplicación de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

El proceso para crear una aplicación de Xamarin.Forms Shell es el siguiente:

1. Cree una aplicación de Xamarin.Forms o cargue una aplicación existente que quiera convertir en una aplicación de Shell.
1. Agregue un archivo XAML al proyecto de código compartido que sirva de subclase de la clase `Shell`. Para obtener más información, consulte [Subclass the Shell class](#subclass-the-shell-class) (Establecimiento de subclases en la clase Shell).
1. Establezca la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App` de la aplicación en el objeto `Shell` con subclases. Para obtener más información, consulte [Bootstrap the Shell application](#bootstrap-the-shell-application) (Arranque de la aplicación Shell).
1. Describa la jerarquía visual de la aplicación en la clase `Shell` con subclases. Para obtener más información, consulte [Describe the visual hierarchy of the application](#describe-the-visual-hierarchy-of-the-application) (Descripción de la jerarquía visual de la aplicación).

## <a name="subclass-the-shell-class"></a>Creación de subclases de la clase de Shell

El primer paso para crear una aplicación de Xamarin.Forms Shell es agregar un archivo XAML al proyecto de código compartido que crea subclases en la clase `Shell`. Este archivo puede tener cualquier nombre, pero se recomienda **AppShell**. El siguiente código de ejemplo muestra un archivo **AppShell.xaml** recién creado:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell">

</Shell>
```

En el ejemplo siguiente se muestra el código subyacente, **AppShell.xaml.cs**:

```csharp
using Xamarin.Forms;

namespace Xaminals
{
    public partial class AppShell : Shell
    {
        public AppShell()
        {
            InitializeComponent();
        }
    }
}
```

## <a name="bootstrap-the-shell-application"></a>Arranque de una aplicación de Shell

Después de crear el archivo XAML que crea subclases en el objeto `Shell`, la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App` debe establecerse en el objeto `Shell` con subclases:

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

En este ejemplo, la clase `AppShell` es el archivo XAML que se deriva de la clase `Shell`.

> [!NOTE]
> Mientras se compila una aplicación de Shell en blanco, su ejecución dará como resultado la generación de `InvalidOperationException`.

## <a name="describe-the-visual-hierarchy-of-the-application"></a>Descripción de la jerarquía visual de la aplicación

El último paso para crear una aplicación de Xamarin.Forms Shell consiste en describir la jerarquía visual de la aplicación en la clase `Shell` con subclases. Una clase `Shell` con subclases consta de tres objetos jerárquicos principales:

- `FlyoutItem` o `TabBar`. Un `FlyoutItem` representa uno o varios elementos en el control flotante y debe usarse cuando el modelo de navegación de la aplicación incluye un control flotante. Un `TabBar` representa la barra de pestañas de la parte inferior y debe usarse cuando el patrón de navegación de la aplicación comienza con pestañas en la parte inferior. Cada objeto `FlyoutItem` o `TabBar` es un elemento secundario del objeto `Shell`.
- `Tab`, que representa contenido agrupado, navegable mediante las pestañas inferiores. Cada objeto `Tab` es un elemento secundario de un objeto `FlyoutItem` o un objeto `TabBar`.
- `ShellContent`, que representa el objeto `ContentPage` en la aplicación. Cada objeto `ShellContent` es un elemento secundario de un objeto `Tab`. Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, los objetos serán navegables mediante las pestañas superiores.

Ninguno de estos objetos representa ninguna interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario de navegación del contenido.

El siguiente XAML muestra un ejemplo de una clase `Shell` con subclases:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

Cuando se ejecuta, este XAML muestra el objeto `CatsPage`, porque es el primer elemento de contenido declarado en la clase `Shell` con subclases:

[![Captura de pantalla de una aplicación de shell en iOS y Android](create-images/cats.png "Aplicación de shell")](create-images/cats-large.png#lightbox "Aplicación de shell")

Al presionar el icono de tres barras, o al deslizar el dedo desde la izquierda, se muestra el control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](create-images/flyout-reduced.png "Control flotante de Shell")](create-images/flyout-reduced-large.png#lightbox "Control flotante de Shell")

> [!IMPORTANT]
> En una aplicación de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que es un elemento secundario de un objeto `ShellContent` se crea durante el inicio de la aplicación. Agregar objetos `ShellContent` adicionales mediante esta estrategia dará lugar a la creación de páginas adicionales durante el inicio de la aplicación, lo que puede conducir a una experiencia de inicio deficiente. Sin embargo, Shell también es capaz de crear páginas a petición, en respuesta a la navegación. Para más información, consulte [Carga eficiente de páginas](tabs.md#efficient-page-loading) en la guía [Pestañas de Xamarin.Forms Shell](tabs.md).

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
