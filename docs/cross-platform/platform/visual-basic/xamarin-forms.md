---
title: Xamarin. Forms con Visual Basic.NET
description: La plantilla de proyecto de Xamarin. Forms se puede modificar para usar Visual Basic para el ensamblado principal, lo que le permite compilar aplicaciones móviles multiplataforma con VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: ed7e1d65ed361a94ce72a724d797309b40ef8b6c
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959146"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms con Visual Basic.NET

Xamarin no admite Visual Basic directamente: siga las instrucciones de esta página para crear una C# solución de Xamarin. Forms y, a continuación C# , reemplace el proyecto de .net Standard por Visual Basic.

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![crear una solución de Xamarin. Forms y luego reemplazar el proyecto de .NET Standard por Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Debe usar Visual Studio en Windows para programar con Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Tutorial de Xamarin. Forms con Visual Basic

Siga estos pasos para crear un proyecto sencillo de Xamarin. Forms que usa Visual Basic:

1. En Visual Studio 2019, elija **crear un nuevo proyecto**.

2. En la ventana **crear un nuevo proyecto** , escriba **Xamarin. Forms** para filtrar la lista y seleccione **aplicación móvil (Xamarin. Forms)** y, a continuación, presione **siguiente**.

    [Filtro de![para aplicaciones de Xamarin. Forms](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. En la siguiente pantalla, escriba un nombre para el proyecto y presione **crear**.

4. Elija la plantilla **en blanco** y haga clic en **Aceptar**:

    [![plantilla de Xamarin. Forms en blanco](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    Esto crea una solución de Xamarin. Forms en Visual Studio C#, mediante. Los pasos siguientes modifican la solución para utilizar Visual Basic.

5. Haga clic con el botón derecho en la solución y elija **agregar > nuevo proyecto...**

6. Escriba **Visual Basic biblioteca** para filtrar las opciones del proyecto y elija la opción **biblioteca de clases (.net Standard)** con el icono de Visual Basic:

    [![filtro de Visual Basic biblioteca](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. En la siguiente pantalla, escriba un nombre para el proyecto y presione **crear**.

8. Haga clic con el botón derecho en el proyecto de Visual Basic y elija **propiedades**y, a continuación, cambie C# el espacio de **nombres predeterminado** para que coincida con los proyectos existentes:

    [![asegurarse de que el espacio de nombres raíz Visual Basic coincide con la aplicación Xamarin. Forms](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. Haga clic con el botón derecho en el nuevo proyecto de Visual Basic, elija **administrar paquetes NuGet**, instale **Xamarin. Forms** y cierre la ventana Administrador de paquetes.

    [![formularios y cerrar la ventana del administrador de paquetes](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. Cambie el nombre del archivo **Class1. VB** predeterminado a **app. VB**:

    [![cambiar el nombre del archivo y la clase de Class1 predeterminados a la aplicación](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

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

12. Actualice los proyectos de iOS y Android para que hagan referencia a nuevos Visual Basic proyecto (y C# no al proyecto creado por la plantilla).
Haga clic con el botón secundario en el nodo **referencias** en los proyectos de iOS y Android para abrir el **Administrador de referencias**. Desmarque la C# biblioteca y marque la biblioteca de Visual Basic (no olvide hacerlo en los proyectos de iOS y Android).

    [![quitar la referencia de proyecto anterior, agregue Visual Basic referencia](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. Elimine C# el proyecto. Agregue nuevos archivos **. VB** para compilar la aplicación de Xamarin. Forms. A continuación se muestra una plantilla para las nuevas `ContentPage`s en Visual Basic:

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

- Las páginas XAML no se pueden incluir en el proyecto de Visual Basic: el generador de código subyacente C#solo puede compilar. Se puede incluir XAML en una biblioteca de clases C# portable, a la que se hace referencia y que se usa para rellenar los archivos XAML a través de modelos de Visual Basic (un ejemplo de esto se incluye en el [ejemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

- Los representadores personalizados no se pueden escribir en Visual Basic, deben escribirse en los proyectos C# de la plataforma nativa.

- Las implementaciones de servicios de dependencia no se pueden escribir en Visual Basic, deben C# escribirse en los proyectos de la plataforma nativa.

## <a name="related-links"></a>Vínculos relacionados

- [XamarinFormsVB (ejemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [Desarrollo multiplataforma con el .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
