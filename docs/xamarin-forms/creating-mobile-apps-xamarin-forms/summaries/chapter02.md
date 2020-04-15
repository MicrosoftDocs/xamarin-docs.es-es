---
title: Resumen del capítulo 2. Anatomía de una aplicación
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 2. Anatomía de una aplicación'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724999"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumen del capítulo 2. Anatomía de una aplicación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

En una aplicación de Xamarin.Forms, los objetos que ocupan espacio en la pantalla se conocen como *elementos visuales*, encapsulados por la clase [`VisualElement`](xref:Xamarin.Forms.VisualElement). Los elementos visuales se pueden dividir en tres categorías correspondientes a estas clases:

- [Página](xref:Xamarin.Forms.Page)
- [Diseño](xref:Xamarin.Forms.Layout)
- [Vista](xref:Xamarin.Forms.View)

Un derivado de `Page` ocupa toda o casi toda la pantalla. A menudo, el elemento secundario de una página es un derivado de `Layout` para organizar los elementos visuales secundarios. Los elementos secundarios de `Layout` pueden ser otras clases `Layout` o derivados de `View` (a menudo llamados *elementos*), que son objetos conocidos como texto, mapas de bits, controles deslizantes, botones, cuadros de lista, etc.

En este capítulo se muestra cómo crear una aplicación enfocada a la clase [`Label`](xref:Xamarin.Forms.Label), que es el derivado de `View` que muestra texto.

## <a name="say-hello"></a>Decir Hola

Con la plataforma Xamarin instalada, puede crear una solución de Xamarin.Forms en Visual Studio o Visual Studio para Mac. La solución [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa una biblioteca de clases portable para el código común.

> [!NOTE]
> Las bibliotecas de clases portables se han sustituido por las bibliotecas .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas .NET Standard.

En este ejemplo se muestra una solución de Xamarin.Forms creada en Visual Studio sin modificaciones. La solución consta de cuatro proyectos:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una biblioteca de clases portable (PCL) compartida por otros proyectos.
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un proyecto de aplicación para Android.
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un proyecto de aplicación para iOS.
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un proyecto de aplicación para la Plataforma universal de Windows (Windows 10 y Windows 10 Mobile).

> [!NOTE]
> Xamarin.Forms ya no admite Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, pero las aplicaciones de Xamarin.Forms se ejecutan en Windows 10 Desktop.

Puede hacer que cualquiera de estos proyectos de aplicación sea el proyecto de inicio y, luego, compilar y ejecutar el programa en un dispositivo o simulador.

En muchos de los programas de Xamarin.Forms, no se modificarán los proyectos de aplicación. Con frecuencia permanecen como minúsculo código auxiliar solo para iniciar el programa. La mayor parte de su enfoque será la biblioteca común a todas las aplicaciones.

## <a name="inside-the-files"></a>Dentro de los archivos

Los elementos visuales que se muestran en el programa **Hello** se definen en el constructor de la clase [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs). `App` se deriva de la clase [`Application`](xref:Xamarin.Forms.Application) de Xamarin.Forms.

> [!NOTE]
> Las plantillas de solución de Visual Studio para Xamarin.Forms crean una página con un archivo XAML. XAML no se trata en este libro hasta el [capítulo 7](chapter07.md).

La sección **Referencias** del proyecto de PLC **Hello** incluye los siguientes ensamblados de Xamarin.Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

Las secciones **Referencias** de los cinco proyectos de aplicación incluyen ensamblados adicionales que se aplican a las plataformas individuales:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE]
> En las secciones **Referencias** de estos proyectos ya no se enumeran los ensamblados. En su lugar, el archivo de proyecto contiene una etiqueta **PackageReference** que hace referencia al paquete de NuGet de Xamarin.Forms. En la sección **Referencias** de Visual Studio se muestra el paquete de **Xamarin.Forms** en lugar de los ensamblados de Xamarin.Forms.

Cada uno de los proyectos de aplicación contiene una llamada al método estático `Forms.Init` en el espacio de nombres `Xamarin.Forms`. Este método inicializa la biblioteca de Xamarin.Forms. Se define una versión diferente de `Forms.Init` para cada plataforma. Las llamadas a este método se pueden encontrar en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: clase [`App`, método `OnLaunched`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Además, cada plataforma debe crear una instancia de la ubicación de clase `App` en la biblioteca compartida. Esto sucede en una llamada a `LoadApplication` en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

De lo contrario, estos proyectos de aplicación son programas normales en los que no hay que hacer nada.

## <a name="pcl-or-sap"></a>¿PCL o SAP?

Es posible crear una solución de Xamarin.Forms con el código común en una biblioteca de clases portable (PCL) o en un proyecto de recursos compartidos (SAP). Para crear una solución de SAP, seleccione la opción Compartido en Visual Studio. La solución [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) muestra la plantilla de SAP sin modificaciones.

> [!NOTE]
> Las bibliotecas de clases portables se han sustituido por las bibliotecas .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas .NET Standard. De lo contrario, las bibliotecas PCL y .NET Standard son conceptualmente muy similares.

El enfoque de la biblioteca empaqueta todo el código común en un proyecto de biblioteca al que hacen referencia los proyectos de aplicación de la plataforma. Con el enfoque de SAP, el código común existe de hecho en todos los proyectos de aplicación de la plataforma y se comparte entre ellos.

La mayoría de los desarrolladores de Xamarin.Forms prefieren el enfoque de la biblioteca. En este libro, la mayoría de las soluciones usan una biblioteca. Las que usan SAP incluyen un sufijo **Sap** en el nombre del proyecto.

Con el enfoque de SAP, el código del proyecto compartido puede ejecutar código diferente para las distintas plataformas mediante directivas de preprocesador de C# (`#if`, #`elif` y `#endif`) con estos identificadores predefinidos:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

En una biblioteca compartida, puede determinar en qué plataforma está trabajando en tiempo de ejecución, como verá más adelante en este capítulo.

## <a name="labels-for-text"></a>Etiquetas de texto

En la solución [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) se muestra cómo agregar un nuevo archivo de C# al proyecto **Greetings**. Este archivo define una clase denominada `GreetingsPage` que se deriva de `ContentPage`. En este libro, la mayoría de los proyectos contienen un solo derivado de `ContentPage` cuyo nombre es el nombre del proyecto con el sufijo `Page` anexado.

El constructor `GreetingsPage` crea una instancia de una vista [`Label`](xref:Xamarin.Forms.Label), que es la vista de Xamarin.Forms que muestra el texto. La propiedad [`Text`](xref:Xamarin.Forms.Label.Text) se establece en el texto mostrado por `Label`. Este programa establece `Label` en la propiedad `Content` de `ContentPage`. El constructor de la clase `App` crea entonces una instancia de `GreetingsPage` y la establece en su propiedad `MainPage`.

El texto se muestra en la esquina superior izquierda de la página. En iOS, esto significa que se superpone a la barra de estado de la página. Hay varias soluciones posibles para este problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solución 1. Incluir relleno en la página

Establezca una propiedad [`Padding`](xref:Xamarin.Forms.Page.Padding) en la página. `Padding` es de tipo [`Thickness`](xref:Xamarin.Forms.Thickness), una estructura con cuatro propiedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` define un área dentro de una página donde se excluye el contenido. Esto permite que `Label` evite sobrescribir la barra de estado de iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solución 2. Incluir relleno solo para iOS (solo SAP)

Establezca una propiedad "Padding" solo en iOS mediante un proyecto SAP con una directiva de preprocesador de C#. Esto se muestra en la solución [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap).

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solución 3. Incluir relleno solo para iOS (PCL o SAP)

En la versión de Xamarin.Forms usada para el libro, se puede seleccionar una propiedad `Padding` específica de iOS en una biblioteca PCL o un proyecto SAP mediante el método estático [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) o [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*). Estos métodos están ahora en desuso.

Los métodos `Device.OnPlatform` se usan para ejecutar código específico de la plataforma o para seleccionar valores específicos de la plataforma. A nivel interno, hacen uso de la propiedad estática de solo lectura [`Device.OS`](xref:Xamarin.Forms.Device.OS), que devuelve un miembro de la enumeración [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform):

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) para dispositivos UWP.

Los métodos `Device.OnPlatform`, la propiedad `Device.OS` y la enumeración `TargetPlatform` están todos en desuso actualmente. Use en su lugar la propiedad [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) y compare el valor devuelto de `string` con los siguientes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la cadena "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la cadena "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la cadena "UWP", referida a la Plataforma universal de Windows

La propiedad estática de solo lectura [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) está relacionada. Esto devuelve un miembro de [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom), que tiene estos miembros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) no se utiliza

En iOS y Android, el límite entre `Tablet` y `Phone` es un ancho vertical de 600 unidades. En la plataforma Windows, `Desktop` indica una aplicación UWP que se ejecuta en Windows 10 y `Phone` indica una aplicación UWP que se ejecuta en una aplicación de Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solución 3a. Establecimiento de márgenes en la etiqueta

Aunque la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) se introdujo demasiado tarde como para incluirse en el libro, también es de tipo `Thickness` y puede establecerla en `Label` para definir un área fuera de la vista que se incluya en el cálculo del diseño de la vista.

La propiedad `Padding` solo está disponible en los derivados de [`Layout`](xref:Xamarin.Forms.Layout) y [`Page`](xref:Xamarin.Forms.Page). La propiedad `Margin` está disponible en todos los derivados de [`View`](xref:Xamarin.Forms.View).

## <a name="solution-4-center-the-label-within-the-page"></a>Solución 4. Etiqueta centrada dentro de la página

Puede centrar el objeto `Label` dentro del objeto `Page` (o colocarlo en uno de otros ocho lugares); para ello, establezca las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de `Label` en un valor de tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions). La estructura `LayoutOptions` define dos propiedades:

- Una propiedad [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) de tipo [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment), una enumeración con cuatro miembros: [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), lo que significa izquierda o arriba según la orientación, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End), lo que significa derecha o abajo según la orientación, y [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Una propiedad [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) de tipo `bool`.

Por lo general, estas propiedades no se usan directamente. En su lugar, se proporcionan combinaciones de estas dos propiedades mediante ocho propiedades estáticas de solo lectura de tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` y `VerticalOptions` son las propiedades más importantes en el diseño de Xamarin.Forms y se describen con más detalle en el [**Capítulo 4. Desplazamiento de la pila**](chapter04.md).

Este es el resultado con las propiedades `HorizontalOptions` y `VerticalOptions` de `Label`, ambas establecidas en `LayoutOptions.Center`:

[![Captura de pantalla triple del programa Greetings](images/ch02fg05-small.png "Etiqueta centrada horizontal y verticalmente")](images/ch02fg05-large.png#lightbox "Etiqueta centrada horizontal y verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Solución 5. Texto centrado dentro de la página

También puede centrar el texto (o colocarlo en otros ocho lugares de la página) mediante el establecimiento de las propiedades [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) y [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) de `Label` en un miembro de la enumeración [`TextAlignment`](xref:Xamarin.Forms.TextAlignment):

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), lo que significa izquierda o arriba (según la orientación)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), lo que significa derecha o abajo (según la orientación)

Estas dos propiedades solo se definen mediante `Label`, mientras que las propiedades `HorizontalAlignment` y `VerticalAlignment` se definen mediante `View` y las heredan todos los derivados de `View`. Aunque los resultados visuales pueden parecer similares, son muy diferentes (como se muestra en el siguiente capítulo).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Ejemplos del capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Ejemplos de F# del capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introducción a Xamarin.Forms](~/get-started/index.yml)
