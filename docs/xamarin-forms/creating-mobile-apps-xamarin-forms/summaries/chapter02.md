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

En una aplicación de Xamarin. Forms, los objetos que ocupan espacio en la pantalla se conocen como *elementos visuales*, encapsulados por la clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Elementos visuales pueden dividirse en tres categorías correspondientes a estas clases:

- [Page](xref:Xamarin.Forms.Page)
- [Diseño](xref:Xamarin.Forms.Layout)
- [Vista](xref:Xamarin.Forms.View)

Una `Page` derivada ocupa toda la pantalla o casi toda la pantalla. A menudo, el elemento secundario de una página es una `Layout` derivada para organizar los elementos visuales secundarios. Los elementos secundarios del `Layout` pueden ser otras clases de `Layout` o derivados de `View` (a menudo denominados *elementos*), que son objetos conocidos como texto, mapas de bits, controles deslizantes, botones, cuadros de lista, etc.

En este capítulo se muestra cómo crear una aplicación centrándose en el [`Label`](xref:Xamarin.Forms.Label), que es el derivado `View` que muestra el texto.

## <a name="say-hello"></a>Saluda

Con la plataforma Xamarin instalada, puede crear una nueva solución de Xamarin.Forms en Visual Studio o Visual Studio para Mac. La solución [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa una biblioteca de clases portable para el código común.

> [!NOTE]
> Bibliotecas de clases portables se han reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares.

Este ejemplo muestra una solución de Xamarin.Forms creada en Visual Studio sin modificaciones. La solución consta de cuatro proyectos:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), una biblioteca de clases portable (PCL) compartida por otros proyectos
- [**Hello. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un proyecto de aplicación para Android
- [**Hello. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un proyecto de aplicación para iOS
- [**Hello. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un proyecto de aplicación para el plataforma universal de Windows (Windows 10 y Windows 10 Mobile)

> [!NOTE]
> Xamarin.Forms ya no es compatible con Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, pero las aplicaciones de Xamarin.Forms se ejecutan en el escritorio de Windows 10.

Puede realizar cualquiera de estos proyectos de aplicación, el proyecto de inicio y, a continuación, compilar y ejecutar el programa en un dispositivo o simulador.

En muchos de los programas de Xamarin.Forms, no se puede modificar los proyectos de aplicación. A menudo, estas permanecen pequeño código auxiliar para iniciar el programa. La mayoría de su enfoque será la biblioteca común a todas las aplicaciones.

## <a name="inside-the-files"></a>Dentro de los archivos

Los objetos visuales mostrados por el programa **Hola** se definen en el constructor de la clase [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) . `App` deriva de la clase de Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application).

> [!NOTE]
> Las plantillas de solución de Visual Studio para Xamarin.Forms, crean una página con un archivo XAML. XAML no se trata en este libro hasta el [capítulo 7](chapter07.md).

La sección **referencias** del proyecto **Hello** PCL incluye los siguientes ensamblados de Xamarin. Forms:

- **Xamarin. Forms. Core**
- **Xamarin. Forms. Xaml**
- **Xamarin. Forms. Platform**

Las secciones **referencias** de los cinco proyectos de aplicación incluyen ensamblados adicionales que se aplican a las distintas plataformas:

- **Xamarin. Forms. Platform. Android**
- **Xamarin. Forms. Platform. iOS**
- **Xamarin. Forms. Platform. UWP**
- **Xamarin. Forms. Platform. WinRT**
- **Xamarin. Forms. Platform. WinRT. Tablet**
- **Xamarin. Forms. Platform. WinRT. Phone**

> [!NOTE]
> Las secciones **referencias** de estos proyectos ya no enumeran los ensamblados. En su lugar, el archivo de proyecto contiene etiquetas **PackageReference** que hacen referencia al paquete de NuGet de Xamarin. Forms. En la sección **referencias** de Visual Studio se muestra el paquete **Xamarin. Forms** en lugar de los ensamblados de Xamarin. Forms.

Cada uno de los proyectos de aplicación contiene una llamada al método estático `Forms.Init` en el espacio de nombres `Xamarin.Forms`. Esto inicializa la biblioteca Xamarin.Forms. Se define una versión diferente de `Forms.Init` para cada plataforma. Las llamadas a este método pueden encontrarse en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [clase de`App`, método de `OnLaunched`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Además, cada plataforma debe crear una instancia de la `App` ubicación de la clase en la biblioteca compartida. Esto sucede en una llamada a `LoadApplication` en las clases siguientes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

En caso contrario, estos proyectos de aplicación son programas "no hacer nada" normal.

## <a name="pcl-or-sap"></a>¿PCL o SAP?

Es posible crear una solución de Xamarin.Forms con el código común en una biblioteca de clases Portable (PCL) o un proyecto de activos compartidos (SAP). Para crear una solución SAP, seleccione la opción Shared en Visual Studio. La solución [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) muestra la plantilla de SAP sin modificaciones.

> [!NOTE]
> Bibliotecas de clases portables se ha reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares. En caso contrario, las bibliotecas PCL y .NET Standard son conceptualmente muy similares.

Las agrupaciones de enfoque de biblioteca común para todos los de código en un proyecto de biblioteca que se hace referencia a los proyectos de aplicación de plataforma. Con el enfoque SAP, el código común de forma eficaz existe en todos los proyectos de aplicación de plataforma y se comparte entre ellos.

La mayoría de los desarrolladores de Xamarin.Forms prefieran el enfoque de la biblioteca. En este libro, la mayoría de las soluciones usan una biblioteca. Los que usan SAP incluyen un sufijo **SAP** en el nombre del proyecto.

Con el enfoque de SAP, el código del proyecto compartido puede ejecutar código diferente para las distintas plataformas mediante C# las directivas de preprocesador (`#if`, #`elif`y `#endif`) con estos identificadores predefinidos:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

En una biblioteca compartida, puede determinar qué plataforma se ejecutan en tiempo de ejecución, como verá más adelante en este capítulo.

## <a name="labels-for-text"></a>Etiquetas de texto

La solución [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) muestra cómo agregar un nuevo C# archivo al proyecto **Greetings** . Este archivo define una clase denominada `GreetingsPage` que se deriva de `ContentPage`. En este libro, la mayoría de los proyectos contienen un único `ContentPage` derivado cuyo nombre es el nombre del proyecto con el sufijo `Page` anexado.

El constructor `GreetingsPage` crea una instancia de una vista de [`Label`](xref:Xamarin.Forms.Label) , que es la vista de Xamarin. Forms que muestra texto. La propiedad [`Text`](xref:Xamarin.Forms.Label.Text) se establece en el texto mostrado por el `Label`. Este programa establece el `Label` en la propiedad `Content` de `ContentPage`. Después, el constructor de la clase `App` crea una instancia de `GreetingsPage` y lo establece en su propiedad `MainPage`.

El texto se muestra en la esquina superior izquierda de la página. En iOS, esto significa que se superpone a la barra de estado de la página. Existen varias soluciones para este problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solución 1. Incluir relleno en la página

Establezca una propiedad de [`Padding`](xref:Xamarin.Forms.Page.Padding) en la página. `Padding` es de tipo [`Thickness`](xref:Xamarin.Forms.Thickness), una estructura con cuatro propiedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` define un área dentro de una página en la que se excluye el contenido. Esto permite que el `Label` Evite sobrescribir la barra de estado de iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solución 2. Incluir relleno solo para iOS (solo para SAP)

Establecer una propiedad 'Relleno' solo en iOS mediante una SAP con una directiva de preprocesador de C#. Esto se muestra en la solución [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solución 3. Incluir relleno solo para iOS (SAP o PCL)

En la versión de Xamarin. Forms usada para el libro, se puede seleccionar una propiedad `Padding` específica de iOS en una PCL o SAP mediante el método estático [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) o [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) . Estos métodos están en desuso

Los métodos de `Device.OnPlatform` se usan para ejecutar código específico de la plataforma o para seleccionar valores específicos de la plataforma. Internamente, hacen uso de la propiedad de solo lectura estática [`Device.OS`](xref:Xamarin.Forms.Device.OS) , que devuelve un miembro de la enumeración [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) :

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) para dispositivos UWP.

Los métodos de `Device.OnPlatform`, la propiedad `Device.OS` y la enumeración `TargetPlatform` ahora están en desuso. En su lugar, use la propiedad [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) y compare el valor devuelto `string` con los siguientes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la cadena "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la cadena "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la cadena "UWP", que hace referencia al plataforma universal de Windows

La propiedad de solo lectura estática [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) está relacionada. Esto devuelve un miembro del [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom), que tiene estos miembros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) no se usa

Para iOS y Android, el límite entre `Tablet` y `Phone` es un ancho vertical de 600 unidades. En la plataforma Windows, `Desktop` indica una aplicación UWP que se ejecuta en Windows 10 y `Phone` indica una aplicación UWP que se ejecuta en una aplicación de Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solución 3a. Establecer margen en la etiqueta

La propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) se presentó demasiado tarde para incluirse en el libro, pero también es de tipo `Thickness` y se puede establecer en el `Label` para definir un área fuera de la vista que se incluye en el cálculo del diseño de la vista.

La propiedad `Padding` solo está disponible en [`Layout`](xref:Xamarin.Forms.Layout) y [`Page`](xref:Xamarin.Forms.Page) derivadas. La propiedad `Margin` está disponible en todos los [`View`](xref:Xamarin.Forms.View) derivados.

## <a name="solution-4-center-the-label-within-the-page"></a>Solución 4. Centro de la etiqueta dentro de la página

Puede centrar el `Label` en el `Page` (o colocarlo en uno de ocho otros lugares) estableciendo las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) del `Label` en un valor de tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions). La estructura `LayoutOptions` define dos propiedades:

- Una propiedad [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) de tipo [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment), una enumeración con cuatro miembros: [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), lo que significa Left o Top en función de la orientación, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End), lo que significa Right o Bottom en función de la orientación y [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Una propiedad [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) de tipo `bool`.

Generalmente estas propiedades no se usan directamente. En su lugar, las combinaciones de estas dos propiedades las proporcionan ocho propiedades de solo lectura estáticas de tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` y `VerticalOptions` son las propiedades más importantes en el diseño de Xamarin. Forms y se describen con más detalle en el [**capítulo 4. Desplazarse por la pila**](chapter04.md).

Este es el resultado con las propiedades `HorizontalOptions` y `VerticalOptions` de `Label` establecidas en `LayoutOptions.Center`:

[![Captura de pantalla triple del programa Greetings](images/ch02fg05-small.png "Etiqueta centrada horizontalmente y verticalmente")](images/ch02fg05-large.png#lightbox "Etiqueta centrada horizontalmente y verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Solución 5. Centrar el texto dentro de la etiqueta

También puede centrar el texto (o colocarlo en ocho otras ubicaciones en la página) estableciendo las propiedades [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) y [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) de `Label` en un miembro de la enumeración [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) :

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), es decir, izquierda o superior (en función de la orientación)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), es decir, derecha o inferior (en función de la orientación)

Estas dos propiedades se definen solo mediante `Label`, mientras que las propiedades `HorizontalAlignment` y `VerticalAlignment` están definidas por `View` y heredadas por todos los derivados de `View`. Los resultados visuales parezcan similares, pero son muy diferentes, como se muestra en el siguiente capítulo.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Ejemplos de capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Ejemplos de F# capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introducción con Xamarin. Forms](~/get-started/index.yml)
