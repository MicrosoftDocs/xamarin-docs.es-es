---
title: Xamarin. Forms con Visual Basic.NET
description: La plantilla de proyecto de Xamarin. Forms se puede modificar para usar Visual Basic para el ensamblado principal, lo que le permite compilar aplicaciones móviles multiplataforma con VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 5b042116f6d1fe2832298d09e3d47cba745973ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456878"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms con Visual Basic.NET

Xamarin no admite Visual Basic directamente: siga las instrucciones de esta página para crear una solución de Xamarin. Forms de C# y, a continuación, reemplace el proyecto de C# .NET Standard por Visual Basic.

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![Cree una solución de Xamarin. Forms y reemplace el proyecto .NET Standard por Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Debe usar Visual Studio en Windows para programar con Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Tutorial de Xamarin. Forms con Visual Basic

Siga estos pasos para crear un proyecto sencillo de Xamarin. Forms que usa Visual Basic:

1. En Visual Studio 2019, elija **crear un nuevo proyecto**.

2. En la ventana **crear un nuevo proyecto** , escriba **Xamarin. Forms** para filtrar la lista y seleccione **aplicación móvil (Xamarin. Forms)** y, a continuación, presione **siguiente**.

    [![Filtrar por aplicaciones de Xamarin. Forms](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. En la siguiente pantalla, escriba un nombre para el proyecto y presione **crear**.

4. Elija la plantilla **en blanco** y haga clic en **Aceptar**:

    [![Plantilla de Xamarin. Forms en blanco](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    Esto crea una solución de Xamarin. Forms en Visual Studio, con C#. Los pasos siguientes modifican la solución para utilizar Visual Basic.

5. Haga clic con el botón derecho en la solución y elija **agregar > nuevo proyecto...**

6. Escriba **Visual Basic biblioteca** para filtrar las opciones del proyecto y elija la opción **biblioteca de clases (.net Standard)** con el icono de Visual Basic:

    [![Filtrar por biblioteca de Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. En la siguiente pantalla, escriba un nombre para el proyecto y presione **crear**.

8. Haga clic con el botón derecho en el proyecto de Visual Basic y elija **propiedades**y, a continuación, cambie el **espacio de nombres predeterminado** para que coincida con los proyectos de C# existentes:

    [![Asegúrese de que el espacio de nombres raíz de Visual Basic coincide con la aplicación de Xamarin. Forms](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. Haga clic con el botón derecho en el nuevo proyecto de Visual Basic, elija **administrar paquetes NuGet**, instale **Xamarin. Forms** y cierre la ventana Administrador de paquetes.

    [![Formularios y cierre la ventana del administrador de paquetes](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. Cambie el nombre del archivo **Class1. VB** predeterminado a **app. VB**:

    [![Cambiar el nombre del archivo y la clase de Class1 predeterminados a la aplicación](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. Pegue el código siguiente en el archivo **app. VB** , que se convertirá en el punto inicial de la aplicación de Xamarin. Forms:

    ```vb
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

12. Actualice los proyectos de iOS y Android para que hagan referencia a nuevos Visual Basic proyecto (y no al proyecto de C# creado por la plantilla).
Haga clic con el botón secundario en el nodo **referencias** en los proyectos de iOS y Android para abrir el **Administrador de referencias**. Desmarque la biblioteca de C# y marque la biblioteca de Visual Basic (no olvide hacerlo en los proyectos de iOS y Android).

    [![Quitar referencia de proyecto anterior, agregar referencia de Visual Basic](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. Elimine el proyecto de C#. Agregue nuevos archivos **. VB** para compilar la aplicación de Xamarin. Forms. `ContentPage`A continuación se muestra una plantilla para nuevos s en Visual Basic:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitaciones de Visual Basic en Xamarin. Forms

Como se indica en la [Página portable Visual Basic.net](~/cross-platform/platform/visual-basic/index.md), Xamarin no admite el lenguaje Visual Basic. Esto significa que hay algunas limitaciones en cuanto a cómo se puede usar Visual Basic:

- Las páginas XAML no se pueden incluir en el proyecto de Visual Basic: el generador de código subyacente solo puede compilar C#. Es posible incluir XAML en una biblioteca de clases portable de C# independiente y a la que se haga referencia, y usar el enlace de enlaces para rellenar los archivos XAML a través de los modelos de Visual Basic (se incluye un ejemplo de esto en el [ejemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)).

- Los representadores personalizados no se pueden escribir en Visual Basic, deben escribirse en C# en los proyectos de la plataforma nativa.

- Las implementaciones de servicios de dependencia no se pueden escribir en Visual Basic, deben escribirse en C# en los proyectos de plataforma nativa.

## <a name="related-links"></a>Vínculos relacionados

- [XamarinFormsVB (ejemplo)](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [Desarrollo multiplataforma con .NET Framework](/dotnet/standard/cross-platform/)