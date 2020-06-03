---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 16. Data binding''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ece93730100001e8339a5f50cdb7ac437d96fa62
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136739"
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumen del capítulo 16. Enlace de datos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

Los programadores a menudo se encuentran escribiendo controladores de eventos que detectan cuándo ha cambiado una propiedad de un objeto, y lo usan para cambiar el valor de una propiedad en otro objeto. Este proceso se puede automatizar con la técnica del *enlace de datos*. Los enlaces de datos se definen normalmente en XAML y pasan a formar parte de la definición de la interfaz de usuario.

A menudo, estos enlaces de datos conectan objetos de la interfaz de usuario a los datos subyacentes. Se trata de una técnica que se explora con mayor profundidad en el [**Capítulo 18. MVVM**](chapter18.md). Sin embargo, los enlaces de datos también pueden conectar dos o más elementos de la interfaz de usuario. La mayoría de los ejemplos anteriores de enlace de datos de este capítulo demuestran esta técnica.

## <a name="binding-basics"></a>Conceptos básicos del enlace

Varias propiedades, métodos y clases están implicados en el enlace de datos:

- La clase [`Binding`](xref:Xamarin.Forms.Binding) se deriva de [`BindingBase`](xref:Xamarin.Forms.BindingBase) y encapsula muchas características de un enlace de datos.
- La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) se define mediante la clase [`BindableObject`](xref:Xamarin.Forms.BindableObject).
- El método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) también se define en la clase [`BindableObject`](xref:Xamarin.Forms.BindableObject).
- La clase [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) define tres métodos de `SetBinding` adicionales.

Las dos clases siguientes admiten las extensiones de marcado XAML para los enlaces:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) admite la extensión de marcado `Binding`
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) admite la extensión de marcado `x:Reference`

Hay dos interfaces implicadas en el enlace de datos:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) en el espacio de nombres `System.ComponentModel` sirve para implementar la notificación cuando cambia una propiedad.
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) se utiliza para definir las clases pequeñas que convierten los valores de un tipo a otro en los enlaces de datos.

Un enlace de datos conecta dos propiedades del mismo objeto, o (más comúnmente) dos objetos diferentes. Estas dos propiedades se denominan *origen* y *destino*. Normalmente, un cambio en la propiedad de origen hace que se produzca un cambio en la propiedad de destino, pero a veces se invierte la dirección. En cualquier caso:

- La propiedad *target* debe estar respaldada por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).
- Por lo general, la propiedad *source* es miembro de una clase que implementa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged).

Una clase que implementa `INotifyPropertyChanged` activa un evento [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) cuando una propiedad cambia de valor. `BindableObject` implementa `INotifyPropertyChanged` y activa automáticamente un evento `PropertyChanged` cuando una propiedad respaldada por `BindableProperty` cambia valores, pero puede escribir sus propias clases que implementan `INotifyPropertyChanged` sin que se deriven de `BindableObject`.

## <a name="code-and-xaml"></a>Código y XAML

En el ejemplo [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) se muestra cómo establecer un enlace de datos en el código:

- El origen es la propiedad `Value` de `Slider`.
- El destino es la propiedad `Opacity` de `Label`.

Los dos objetos se conectan estableciendo el `BindingContext` del objeto `Label` en el objeto `Slider`. Las dos propiedades se conectan llamando a un método de extensión [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) en el `Label` que hace referencia a la propiedad enlazable `OpacityProperty` y la propiedad `Value` de `Slider` expresada como una cadena.

La manipulación de la `Slider` provoca que `Label` aparezca y desaparezca gradualmente de la vista.

El programa [**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) es el mismo con el enlace de datos establecido en XAML. `BindingContext` de `Label` se establece en una extensión de marcado de `x:Reference` que hace referencia a `Slider`, y la propiedad `Opacity` de `Label` se establece en la extensión de marcado `Binding` con su propiedad [`Path`](xref:Xamarin.Forms.Binding.Path) que hace referencia a la propiedad `Value` de `Slider`.

## <a name="source-and-bindingcontext"></a>Source y BindingContext

En el ejemplo [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) se muestra un enfoque alternativo en el código. Para crear un objeto `Binding`, establezca la propiedad [`Source`](xref:Xamarin.Forms.Binding.Source) en el objeto `Slider` y la propiedad [`Path`](xref:Xamarin.Forms.Binding.Path) en "Value". Después, se llama al método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) de `BindableObject` en el objeto `Label`.

También se podría haber usado el [`Binding` constructor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) para definir el objeto `Binding`.

En el ejemplo [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) se muestra la técnica comparable en XAML. La propiedad `Opacity` de `Label` se establece en una extensión de marcado de `Binding` con [`Path`](xref:Xamarin.Forms.Binding.Path) establecido en la propiedad `Value` y [`Source`](xref:Xamarin.Forms.Binding.Source) establecida en una extensión de marcado `x:Reference` insertada.

En resumen, hay dos maneras de hacer referencia al objeto de origen de enlace:

- A través de la propiedad `BindingContext` del destino.
- A través de la propiedad `Source` del propio objeto `Binding`.

Si se especifican ambos, la segunda tiene prioridad. La ventaja de la `BindingContext` es que se propaga a través del árbol visual. Esto es *muy* útil si varias propiedades de destino están enlazadas al mismo objeto de origen.

El programa [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) muestra esta técnica con el elemento [`WebView`](xref:Xamarin.Forms.WebView). Dos elementos `Button` para navegar hacia atrás y hacia delante heredan un objeto `BindingContext` de su elemento primario que hace referencia a `WebView`. Las propiedades de `IsEnabled` de los dos botones tienen extensiones de marcado de `Binding` simples que tienen como destino las propiedades `IsEnabled` del botón basadas en la configuración de las propiedades [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) y [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) de solo lectura de `WebView`.

## <a name="the-binding-mode"></a>Modo de enlace

Establezca la propiedad [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) de `Binding` en un miembro de la enumeración [`BindingMode`](xref:Xamarin.Forms.BindingMode):

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) para que los cambios en la propiedad de origen afecten al destino.
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) para que los cambios en la propiedad de destino afecten al origen.
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) para que los cambios en el origen y el destino se afecten entre sí.
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) para usar el valor [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) especificado cuando se creó el valor `BindableProperty` de destino. Si no se especifica ninguno, el valor predeterminado es `OneWay` para las propiedades normales enlazables y `OneWayToSource` para las propiedades enlazables de solo lectura.

> [!NOTE]
> La enumeración de `BindingMode` ahora también incluye `OnTime` para aplicar un enlace solo cuando cambia el contexto de enlace y no cuando cambia la propiedad de origen.

Las propiedades que es probable que sean los destinos de los enlaces de datos en escenarios de MVVM generalmente tienen un valor `DefaultBindingMode` de `TwoWay`. Estos son:

- Propiedad `Value` de `Slider` y `Stepper`
- Propiedad `IsToggled` de `Switch`
- Propiedad `Text` de `Entry`, `Editor` y `SearchBar`.
- Propiedad `Date` de `DatePicker`
- Propiedad `Time` de `TimePicker`

En el ejemplo [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) se muestran los cuatro modos de enlace con un enlace de datos, donde el destino es la propiedad `FontSize` de un elemento `Label` y el origen es la propiedad `Value` de un elemento `Slider`. Esto permite que cada `Slider` controle el tamaño de fuente del `Label` correspondiente. Sin embargo, los elementos `Slider` no se inicializan porque el `DefaultBindingMode` de la propiedad `FontSize` es `OneWay`.

El ejemplo [**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) establece los enlaces en la propiedad `Value` del `Slider` que hace referencia a la propiedad `FontSize` de cada `Label`. Parece que va para atrás, pero funciona mejor al inicializar los elementos `Slider` porque la propiedad `Value` de `Slider` tiene un `DefaultBindingMode` de `TwoWay`.

[![Captura de pantalla triple de enlace inverso](images/ch16fg06-small.png "Enlace inverso")](images/ch16fg06-large.png#lightbox "Enlace inverso")

Esto es análogo a la forma en que se definen los enlaces en MVVM, y usará este tipo de enlace con frecuencia.

## <a name="string-formatting"></a>Formato de cadena

Cuando la propiedad de destino es de tipo `string`, puede usar la propiedad [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) definida por `BindingBase` para convertir el origen en `string`. Establezca la propiedad `StringFormat` en una cadena de formato de .NET que se usaría con el formato [`String.Format`](xref:System.String.Format(System.String,System.Object)) estático para mostrar el objeto. Al utilizar esta cadena de formato dentro de una extensión de marcado, inclúyala entre comillas simples para que las llaves no se confundan en el caso de una extensión de marcado insertada.

En el ejemplo [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) se muestra cómo utilizar `StringFormat` en XAML.

En el ejemplo [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) se demuestra cómo mostrar el tamaño de la página con enlaces a las propiedades `Width` y `Height` de `ContentPage`.

## <a name="why-is-it-called-path"></a>¿Por qué se denomina "Path"?

La propiedad [`Path`](xref:Xamarin.Forms.Binding.Path) de `Binding` se llama así porque puede ser una serie de propiedades e indexadores separados por puntos. En el ejemplo [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) se muestran varios ejemplos.

## <a name="binding-value-converters"></a>Enlace de convertidores de valores

Cuando las propiedades de origen y de destino de un enlace son tipos diferentes, puede aplicar conversiones a los tipos mediante un convertidor de enlace. Se trata de una clase que implementa la interfaz [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) y contiene dos métodos: [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para convertir el origen en destino y [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para convertir el destino en origen.

La clase [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) es un ejemplo de cómo convertir `int` en `bool`. Se demuestra en el ejemplo [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler), que solo habilita `Button` si se ha escrito al menos un carácter en `Entry`.

La clase [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) convierte `bool` en `string` y define dos propiedades para especificar qué texto se debe devolver para los valores `false` y `true`.
[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) es similar. En el ejemplo [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) se muestra el uso de estos dos convertidores para mostrar textos distintos en diferentes colores basados en una configuración de `Switch`.

El valor [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) genérico puede reemplazar a `BoolToStringConverter` y `BoolToColorConverter` y servir como convertidor generalizado de `bool` a objeto de cualquier tipo.

## <a name="bindings-and-custom-views"></a>Enlaces y vistas personalizadas

Puede simplificar los controles personalizados mediante enlaces de datos. El archivo de código [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) define las propiedades `Text`, `TextColor`, `FontSize`, `FontAttributes` y `IsChecked`, pero no tiene ninguna lógica para los objetos visuales del control.
En su lugar, el archivo [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) contiene todo el marcado de los objetos visuales del control a través de enlaces de datos en los elementos `Label` basados en las propiedades definidas en el archivo de código subyacente.

En el ejemplo [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) se muestra el control personalizado de `NewCheckBox`.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 16 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Ejemplos del capítulo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
