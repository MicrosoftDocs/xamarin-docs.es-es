---
title: Creación de una aplicación de Xamarin.Forms Shell
description: El proceso para crear una aplicación de Xamarin.Forms Shell consiste en crear un archivo XAML que sirva de subclase de la clase Shell, establecer la propiedad MainPage de la clase App de la aplicación en el objeto Shell con subclases y, después, describir la jerarquía visual de la aplicación en la clase Shell con subclases.
ms.prod: xamarin
ms.assetid: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f02deecbd372e7125fe13aaed37ad08f2cc4faf6
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757604"
---
# <a name="create-a-xamarinforms-shell-application"></a>Creación de una aplicación de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

El proceso para crear una aplicación de Xamarin.Forms Shell es el siguiente:

1. Cree una aplicación de Xamarin.Forms o cargue una aplicación existente que quiera convertir en una aplicación de Shell.
1. Agregue un archivo XAML al proyecto de código compartido que sirva de subclase de la clase [`Shell`](xref:Xamarin.Forms.Shell). Para obtener más información, consulte [Subclass the Shell class](#subclass-the-shell-class) (Establecimiento de subclases en la clase Shell).
1. Establezca la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App` de la aplicación en el objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Para obtener más información, consulte [Bootstrap the Shell application](#bootstrap-the-shell-application) (Arranque de la aplicación Shell).
1. Describa la jerarquía visual de la aplicación en la clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Para obtener más información, consulte [Describe the visual hierarchy of the application](#describe-the-visual-hierarchy-of-the-application) (Descripción de la jerarquía visual de la aplicación).

Para ver un tutorial paso a paso sobre cómo crear una aplicación de Shell, consulte [Inicio rápido de la creación de una aplicación de Xamarin.Forms](~/get-started/quickstarts/app.md).

## <a name="subclass-the-shell-class"></a>Creación de subclases de la clase de Shell

El primer paso para crear una aplicación de Xamarin.Forms Shell es agregar un archivo XAML al proyecto de código compartido que genera subclases de la clase [`Shell`](xref:Xamarin.Forms.Shell). Este archivo puede tener cualquier nombre, pero se recomienda **AppShell**. El siguiente código de ejemplo muestra un archivo **AppShell.xaml** recién creado:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="MyApp.AppShell">

</Shell>
```

En el ejemplo siguiente se muestra el código subyacente, **AppShell.xaml.cs**:

```csharp
using Xamarin.Forms;

namespace MyApp
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

Después de crear el archivo XAML que crea subclases en el objeto [`Shell`](xref:Xamarin.Forms.Shell), la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App` debe establecerse en el objeto `Shell` con subclases:

```csharp
namespace MyApp
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

En este ejemplo, la clase `AppShell` es el archivo XAML que se deriva de la clase [`Shell`](xref:Xamarin.Forms.Shell).

> [!WARNING]
> Mientras se compila una aplicación de Shell en blanco, si intenta ejecutarla, se producirá una `InvalidOperationException` excepción.

## <a name="describe-the-visual-hierarchy-of-the-application"></a>Descripción de la jerarquía visual de la aplicación

El último paso para crear una aplicación de Xamarin.Forms Shell consiste en describir la jerarquía visual de la aplicación en la clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Una clase `Shell` con subclases consta de tres objetos jerárquicos principales:

1. [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o [`TabBar`](xref:Xamarin.Forms.TabBar). Un objeto `FlyoutItem` representa uno o varios elementos en el control flotante y debe usarse cuando el modelo de navegación de la aplicación incluye un control flotante. Un objeto `TabBar` representa la barra de pestañas inferior y debe usarse cuando el modelo de navegación de la aplicación comienza con pestañas en la parte inferior y no requiere control flotante. Cada objeto `FlyoutItem` o `TabBar` es un elemento secundario del objeto [`Shell`](xref:Xamarin.Forms.Shell).
1. [`Tab`](xref:Xamarin.Forms.Tab), que representa contenido agrupado, navegable mediante pestañas en la parte inferior. Cada objeto `Tab` es un elemento secundario de un objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o un objeto [`TabBar`](xref:Xamarin.Forms.TabBar).
1. [`ShellContent`](xref:Xamarin.Forms.ShellContent), que representa los objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) de cada pestaña. Cada objeto `ShellContent` es un elemento secundario de un objeto [`Tab`](xref:Xamarin.Forms.Tab). Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, los objetos serán navegables mediante las pestañas superiores.

Estos objetos no representan ninguna interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario de navegación del contenido.

El siguiente XAML muestra un ejemplo de una clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <!--
        Shell has implicit conversion operators that enable the Shell visual hierarchy to be simplified.
        This is possible because a subclassed Shell object can only ever contain a FlyoutItem object or a TabBar object,
        which can only ever contain Tab objects, which can only ever contain ShellContent objects.

        The implicit conversion automatically wraps the ShellContent objects below in Tab objects.
        -->
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>
    ...
</Shell>
```

Cuando se ejecuta, este XAML muestra el objeto `CatsPage`, porque es el primer elemento de contenido declarado en la clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases:

[![Captura de pantalla de una aplicación de Shell en iOS y Android](create-images/cats.png)](create-images/cats-large.png#lightbox)

Al presionar el icono de tres barras, o al deslizar el dedo desde la izquierda, se muestra el control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](create-images/flyout.png)](create-images/flyout-large.png#lightbox)

Se muestran varios elementos en el control flotante porque la propiedad [`FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions) está establecida en `AsMultipleItems`. Para obtener más información, consulte [Opciones de presentación de control flotante](flyout.md#flyout-display-options).

> [!IMPORTANT]
> En una aplicación de Shell, las páginas se crean a petición en respuesta a la navegación. Esto se logra mediante el uso de la extensión de marcado [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) para establecer la propiedad [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) de cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Inicio rápido de la creación de una aplicación de Xamarin.Forms](~/get-started/quickstarts/app.md)
