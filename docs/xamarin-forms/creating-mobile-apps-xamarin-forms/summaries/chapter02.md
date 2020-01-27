---
title: Resumen del capítulo 2 del documento. Anatomía de una aplicación
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 2. Anatomía de una aplicación'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724999"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumen del capítulo 2 del documento. Anatomía de una aplicación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

En una aplicación de Xamarin.Forms, los objetos que ocupan espacio en la pantalla se conocen como *elementos visuales*, encapsulados por la [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) clase. Elementos visuales pueden dividirse en tres categorías correspondientes a estas clases:

- [Página](xref:Xamarin.Forms.Page)
- [Diseño](xref:Xamarin.Forms.Layout)
- [Vista](xref:Xamarin.Forms.View)

Un `Page` derivado ocupa toda la pantalla, o casi toda la pantalla. A menudo, el elemento secundario de una página es una `Layout` derivado para organizar los elementos visuales secundarios. Los elementos secundarios de la `Layout` puede ser otra `Layout` clases o `View` derivados (a menudo denominada *elementos*), que son objetos familiares como texto, los mapas de bits, los controles deslizantes, botones, cuadros de lista y así sucesivamente.

Este capítulo muestra cómo crear una aplicación que se centran en la [ `Label` ](xref:Xamarin.Forms.Label), que es el `View` derivado que muestra texto.

## <a name="say-hello"></a>Saluda

Con la plataforma Xamarin instalada, puede crear una nueva solución de Xamarin.Forms en Visual Studio o Visual Studio para Mac. El [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) solución utiliza una biblioteca de clases Portable para el código común.

> [!NOTE]
> Bibliotecas de clases portables se han reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares.

Este ejemplo muestra una solución de Xamarin.Forms creada en Visual Studio sin modificaciones. La solución consta de cuatro proyectos:

- [**Hola**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una biblioteca de clases Portable (PCL) compartidos por los otros proyectos
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un proyecto de aplicación para Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un proyecto de aplicación para iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un proyecto de aplicación para la plataforma Universal de Windows (Windows 10 y Windows 10 Mobile)

> [!NOTE]
> Xamarin.Forms ya no es compatible con Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, pero las aplicaciones de Xamarin.Forms se ejecutan en el escritorio de Windows 10.

Puede realizar cualquiera de estos proyectos de aplicación, el proyecto de inicio y, a continuación, compilar y ejecutar el programa en un dispositivo o simulador.

En muchos de los programas de Xamarin.Forms, no se puede modificar los proyectos de aplicación. A menudo, estas permanecen pequeño código auxiliar para iniciar el programa. La mayoría de su enfoque será la biblioteca común a todas las aplicaciones.

## <a name="inside-the-files"></a>Dentro de los archivos

Los objetos visuales mostrados por el **Hello** programa se definen en el constructor de la [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) clase. `App` se deriva de la clase Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application).

> [!NOTE]
> Las plantillas de solución de Visual Studio para Xamarin.Forms, crean una página con un archivo XAML. XAML no se trata en este libro hasta [capítulo 7](chapter07.md).

El **referencias** sección de la **Hello** proyecto PCL incluye los siguientes ensamblados de Xamarin.Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

El **referencias** secciones de los proyectos de cinco aplicación incluyen ensamblados adicionales que se aplican a las plataformas individuales:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE]
> El **referencias** secciones de estos proyectos ya no enumeran los ensamblados. En su lugar, el archivo de proyecto contiene un **PackageReference** etiquetas haciendo referencia al paquete Xamarin.Forms NuGet. El **referencias** sección en las listas de Visual Studio la **Xamarin.Forms** del paquete en lugar de los ensamblados de Xamarin.Forms.

Cada uno de los proyectos de aplicación contiene una llamada a estático `Forms.Init` método en el `Xamarin.Forms` espacio de nombres. Esto inicializa la biblioteca Xamarin.Forms. Una versión diferente de `Forms.Init` se define para cada plataforma. Las llamadas a este método pueden encontrarse en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` (clase), `OnLaunched` (método)](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Además, debe crear una instancia de cada plataforma la `App` clase ubicación en la biblioteca compartida. Esto se produce en una llamada a `LoadApplication` en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

En caso contrario, estos proyectos de aplicación son programas "no hacer nada" normal.

## <a name="pcl-or-sap"></a>¿PCL o SAP?

Es posible crear una solución de Xamarin.Forms con el código común en una biblioteca de clases Portable (PCL) o un proyecto de activos compartidos (SAP). Para crear una solución SAP, seleccione la opción Shared en Visual Studio. El [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) solución muestra la plantilla de SAP sin modificaciones.

> [!NOTE]
> Bibliotecas de clases portables se ha reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares. En caso contrario, las bibliotecas PCL y .NET Standard son conceptualmente muy similares.

Las agrupaciones de enfoque de biblioteca común para todos los de código en un proyecto de biblioteca que se hace referencia a los proyectos de aplicación de plataforma. Con el enfoque SAP, el código común de forma eficaz existe en todos los proyectos de aplicación de plataforma y se comparte entre ellos.

La mayoría de los desarrolladores de Xamarin.Forms prefieran el enfoque de la biblioteca. En este libro, la mayoría de las soluciones usan una biblioteca. Aquellos que usan SAP incluyen un **Sap** sufijo en el nombre del proyecto.

Con el enfoque SAP, el código en el proyecto compartido puede ejecutar código diferente para las diferentes plataformas mediante el uso de directivas de preprocesador de C# (`#if`, #`elif`, y `#endif`) con estos identificadores de predefinidos:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

En una biblioteca compartida, puede determinar qué plataforma se ejecutan en tiempo de ejecución, como verá más adelante en este capítulo.

## <a name="labels-for-text"></a>Etiquetas de texto

El [ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) solución muestra cómo agregar un nuevo archivo de C# para el **Greetings** proyecto. Este archivo define una clase denominada `GreetingsPage` que se deriva de `ContentPage`. En este libro, la mayoría de los proyectos contiene una sola `ContentPage` derivado cuyo nombre es el nombre del proyecto con el sufijo `Page` anexado.

El `GreetingsPage` constructor crea un [ `Label` ](xref:Xamarin.Forms.Label) vista, que es la vista de Xamarin.Forms que muestra texto. El [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedad está establecida en el texto mostrado por el `Label`. Este programa establece la `Label` a la `Content` propiedad de `ContentPage`. El constructor de la `App` , a continuación, crea una instancia de clase `GreetingsPage` y lo establece en su `MainPage` propiedad.

El texto se muestra en la esquina superior izquierda de la página. En iOS, esto significa que se superpone a la barra de estado de la página. Existen varias soluciones para este problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solución 1. Incluir relleno en la página

Establecer un [ `Padding` ](xref:Xamarin.Forms.Page.Padding) propiedad en la página. `Padding` es de tipo [ `Thickness` ](xref:Xamarin.Forms.Thickness), una estructura con cuatro propiedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` define un área dentro de una página donde se excluye el contenido. Esto permite la `Label` para evitar sobrescribir la barra de estado de iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solución 2. Incluir relleno solo para iOS (solo para SAP)

Establecer una propiedad 'Relleno' solo en iOS mediante una SAP con una directiva de preprocesador de C#. Esto se muestra en el [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) solución.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solución 3. Incluir relleno solo para iOS (SAP o PCL)

En la versión de Xamarin.Forms que usa para el libro, un `Padding` propiedad específica de iOS en una PCL o SAP se puede seleccionar mediante el [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) o [ `Device.OnPlatform<T>` ](xref:Xamarin.Forms.Device.OnPlatform*) método estático. Estos métodos están en desuso

El `Device.OnPlatform` métodos se usan para ejecutar código específico de plataforma o para seleccionar los valores específicos de la plataforma. Internamente, hacen uso de la [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) propiedad de solo lectura estático, que devuelve un miembro de la [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) enumeración:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) para dispositivos UWP.

El `Device.OnPlatform` métodos, el `Device.OS` propiedad y el `TargetPlatform` enumeración están ahora en desuso. En su lugar, use el [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) propiedad y compare el `string` devolver valor con los siguientes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la cadena "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la cadena "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la cadena "UWP", que hace referencia a la plataforma Universal de Windows

El [ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom) está relacionado con la propiedad estática de solo lectura. Esto devuelve un miembro de la [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom), que tiene estos miembros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) no se utiliza

Para iOS y Android, el corte entre `Tablet` y `Phone` es un ancho vertical de 600 unidades. Para la plataforma Windows, `Desktop` indica que se ejecutan en Windows 10, una aplicación de UWP y `Phone` indica una aplicación de UWP que se ejecutan en la aplicación de Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solución 3a. Establecer margen en la etiqueta

El [ `Margin` ](xref:Xamarin.Forms.View.Margin) propiedad se introdujo demasiado tarde para incluirse en el libro, pero también es de tipo `Thickness` y establézcalo en la `Label` para definir un área fuera de la vista que se incluye en el cálculo de la diseño de la vista.

El `Padding` solo está disponible en la propiedad [ `Layout` ](xref:Xamarin.Forms.Layout) y [ `Page` ](xref:Xamarin.Forms.Page) derivados. El `Margin` propiedad está disponible en todos los [ `View` ](xref:Xamarin.Forms.View) derivados.

## <a name="solution-4-center-the-label-within-the-page"></a>Solución 4. Centro de la etiqueta dentro de la página

Puede Centrar el `Label` dentro de la `Page` (o colocarla en uno de los ocho otros lugares) estableciendo el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades de la `Label` en un valor de tipo [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions). El `LayoutOptions` estructura define dos propiedades:

- Un [ `Alignment` ](xref:Xamarin.Forms.LayoutOptions.Alignment) propiedad de tipo [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment), una enumeración con cuatro miembros: [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), lo que significa izquierdo o superior en función de la orientación, [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), lo que significa derecho o inferior, según la orientación, y [ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Un [ `Expands` ](xref:Xamarin.Forms.LayoutOptions.Expands) propiedad de tipo `bool`.

Generalmente estas propiedades no se usan directamente. En su lugar, las combinaciones de estas dos propiedades proporcionadas por ocho propiedades estáticas de sólo lectura de tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` y `VerticalOptions` son las propiedades más importantes en el diseño de Xamarin. Forms y se describen con más detalle en el [**capítulo 4. Desplazarse por la pila**](chapter04.md).

Este es el resultado con el `HorizontalOptions` y `VerticalOptions` propiedades de `Label` establecidos en `LayoutOptions.Center`:

[![Captura de pantalla triple del programa Greetings](images/ch02fg05-small.png "Etiqueta centrada horizontalmente y verticalmente")](images/ch02fg05-large.png#lightbox "Etiqueta centrada horizontalmente y verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Solución 5. Centrar el texto dentro de la etiqueta

También puede Centrar el texto (o colocarlo en ocho otras ubicaciones en la página) estableciendo el [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) y [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) propiedades de `Label` a un miembro de la [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumeración:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), significado izquierda o superior (según la orientación)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), lo que significa que el derecho o inferior (según la orientación)

Estas dos propiedades se definen únicamente por `Label`, mientras que el `HorizontalAlignment` y `VerticalAlignment` propiedades se definen mediante `View` y hereda todos `View` derivados. Los resultados visuales parezcan similares, pero son muy diferentes, como se muestra en el siguiente capítulo.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 2 del documento de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Ejemplos del capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Capítulo 2 F# ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introducción a Xamarin.Forms](~/get-started/index.yml)
