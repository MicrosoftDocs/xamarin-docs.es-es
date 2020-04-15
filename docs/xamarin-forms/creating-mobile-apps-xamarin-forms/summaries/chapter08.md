---
title: Resumen del capítulo 8. Código y XAML en armonía
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 8. Código y XAML en armonía'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334252"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumen del capítulo 8. Código y XAML en armonía

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

En este capítulo se explora XAML más profundamente y, en particular, cómo interactúan el código y XAML.

## <a name="passing-arguments"></a>Paso de argumentos

En general, una clase de la que se ha creado una instancia en XAML debe tener un constructor sin parámetros público; el objeto resultante se inicializa a través de la configuración de las propiedades. Sin embargo, hay otras dos maneras en las que se pueden crear instancias en los objetos e iniciarlos.

Aunque son técnicas de uso general, se usan principalmente en conexión con los modelos de vista MVVM.

### <a name="constructors-with-arguments"></a>Constructores con argumentos

En el ejemplo [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) se muestra cómo utilizar la etiqueta `x:Arguments` para especificar argumentos de constructor. Estos argumentos se deben delimitar mediante etiquetas de elemento que indiquen el tipo del argumento. En el caso de los tipos de datos básicos de .NET, están disponibles las siguientes etiquetas:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>¿Puedo llamar a métodos desde XAML?

En el ejemplo [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) se muestra cómo utilizar el elemento `x:FactoryMethod` para especificar un método Factory Method que se invoca para crear un objeto. Este método debe ser público y estático, y debe crear un objeto del tipo en el que se define. (Por ejemplo, el método [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) es válido porque es público y estático y devuelve un valor de tipo `Color`). Los argumentos del método Factory Method se especifican en etiquetas `x:Arguments`.

## <a name="the-xname-attribute"></a>Atributo x:Name

El atributo `x:Name` permite asignar un nombre a un objeto del que se ha creado una instancia en XAML. Las reglas para estos nombres son las mismas que para los nombres de variable de C#. Después de la devolución de la llamada de `InitializeComponent` en el constructor, el archivo de código subyacente puede hacer referencia a estos nombres para tener acceso al elemento XAML correspondiente. El analizador XAML convierte realmente los nombres en campos privados en la clase parcial generada.

En el ejemplo [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) se muestra el uso de `x:Name` para permitir que el archivo de código subyacente mantenga dos elementos `Label` definidos en XAML actualizados con la fecha y hora actuales.

No se puede usar el mismo nombre para varios elementos de la misma página. Se trata de un problema determinado si usa `OnPlatform` para crear objetos con nombre en paralelo para cada plataforma. En el ejemplo [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) se muestra una mejor manera de hacer una tarea de ese tipo.

## <a name="custom-xaml-based-views"></a>Vistas personalizadas basadas en XAML

Hay varias maneras de evitar la repetición de marcado en XAML. Una técnica común consiste en crear una nueva clase basada en XAML que se derive de [`ContentView`](xref:Xamarin.Forms.ContentView). Esta técnica se muestra en el ejemplo [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList). La clase `ColorView` se deriva de `ContentView` para mostrar un color determinado y su nombre, mientras que la clase `ColorViewListPage` se deriva de `ContentPage` como de costumbre y crea explícitamente 17 instancias de `ColorView`.

El acceso a la clase `ColorView` en XAML requiere otra declaración de espacio de nombres XML, denominada normalmente `local` para las clases del mismo ensamblado.

## <a name="events-and-handlers"></a>Eventos y controladores

Los eventos se pueden asignar a los controladores de eventos en XAML, pero el propio controlador de eventos debe implementarse en el archivo de código subyacente. El ejemplo [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) muestra cómo compilar una interfaz de usuario de teclado en XAML y cómo implementar los controladores de `Clicked` en el archivo de código subyacente.

## <a name="tap-gestures"></a>Gestos de pulsación

Cualquier objeto `View` puede obtener entradas táctiles y generar eventos a partir de esa entrada. La clase `View` define una propiedad de colección [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) que puede contener una o más instancias de las clases que derivan de [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer).

[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) genera eventos [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped). El programa [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) muestra cómo adjuntar objetos de `TapGestureRecognizer` a cuatro elementos de `BoxView` para crear un juego de imitación:

[![Captura de pantalla triple de Monkey Tap](images/ch08fg07-small.png "Juego de imitación")](images/ch08fg07-large.png#lightbox "Juego de imitación")

Pero el programa **MonkeyTap** realmente necesita sonido. (Vea el [capítulo siguiente](chapter09.md)).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 8 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Ejemplos del capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Ejemplo de F# del capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Paso de argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
