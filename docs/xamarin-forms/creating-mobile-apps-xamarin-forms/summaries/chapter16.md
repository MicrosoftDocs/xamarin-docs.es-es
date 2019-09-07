---
title: Resumen del capítulo 16. Enlace de datos
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 16. Enlace de datos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771105"
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumen del capítulo 16. Enlace de datos

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Los programadores a menudo se encuentran escribir controladores de eventos que detectan cuando ha cambiado una propiedad de un objeto y usarlo para cambiar el valor de una propiedad en otro objeto. Este proceso se puede automatizar con la técnica de *enlace de datos*. Enlaces de datos normalmente se definen en XAML y se pasan a formar parte de la definición de la interfaz de usuario.

Muy a menudo, estos enlaces de datos conexión a los objetos de interfaz de usuario a los datos subyacentes. Esta es una técnica que se explora más en [ **capítulo 18. MVVM**](chapter18.md). Sin embargo, los enlaces de datos también pueden conectar dos o más elementos de interfaz de usuario. La mayoría de los primeros ejemplos de enlace de datos en este capítulo muestra esta técnica.

## <a name="binding-basics"></a>Conceptos básicos del enlace

Varias clases, métodos y propiedades están implicadas en el enlace de datos:

- El [ `Binding` ](xref:Xamarin.Forms.Binding) clase se deriva de [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) y encapsula muchas características de un enlace de datos
- El [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) viene definida por el [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) clase
- El [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método también se define mediante el [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) clase
- El [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) clase define tres adicionales `SetBinding` métodos

Las dos clases siguientes admiten extensiones de marcado XAML para los enlaces:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) admite la `Binding` extensión de marcado
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) admite la `x:Reference` extensión de marcado

Son necesarios dos interfaces de enlace de datos:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) en el `System.ComponentModel` espacio de nombres es para implementar la notificación cuando cambia una propiedad
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) se utiliza para definir clases pequeñas que convierten los valores de un tipo a otro de los enlaces de datos

Un enlace de datos conecta a dos propiedades del objeto mismo, o (más comúnmente) dos objetos diferentes. Estas dos propiedades se conocen como el *origen* y *destino*. Por lo general, un cambio en la propiedad de origen hace un cambio que se produzca en la propiedad de destino, pero a veces se invierte la dirección. En cualquier caso:

- el *destino* propiedad debe estar respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- el *origen* propiedad generalmente es un miembro de una clase que implementa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Una clase que implementa `INotifyPropertyChanged` se activa un [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) evento cuando cambia una propiedad de valor. `BindableObject` implementa `INotifyPropertyChanged` desencadena automáticamente una `PropertyChanged` evento cuando una propiedad respaldada por un `BindableProperty` cambia los valores, pero se puede escribir sus propias clases que implementan `INotifyPropertyChanged` sin que derivar de `BindableObject`.

## <a name="code-and-xaml"></a>Código y XAML

El [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) ejemplo muestra cómo establecer un enlace de datos en el código:

- El origen es el `Value` propiedad de un `Slider`
- El destino es el `Opacity` propiedad de un `Label`

Los dos objetos están conectados mediante el establecimiento del `BindingContext` de la `Label` de objeto para el `Slider` objeto. Las dos propiedades que están conectadas mediante una llamada a un [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) método de extensión en el `Label` que hacen referencia a la `OpacityProperty` propiedad enlazable y `Value` propiedad de la `Slider` expresado como un cadena.

Manipular el `Slider` , a continuación, hace que el `Label` aparezca progresivamente dentro y fuera de la vista.

El [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) es el mismo programa con el enlace de datos establecido en XAML. El `BindingContext` de la `Label` está establecido en un `x:Reference` referencia de extensión de marcado el `Slider`y el `Opacity` propiedad de la `Label` está establecido en el `Binding` extensión de marcado con su [ `Path` ](xref:Xamarin.Forms.Binding.Path) propiedad hace referencia a la `Value` propiedad de la `Slider`.

## <a name="source-and-bindingcontext"></a>Origen y BindingContext

El [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) muestra un enfoque alternativo en el código. Un `Binding` se crea el objeto estableciendo la [ `Source` ](xref:Xamarin.Forms.Binding.Source) propiedad a la `Slider` objeto y el [ `Path` ](xref:Xamarin.Forms.Binding.Path) propiedad a "Value". El [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método `BindableObject` , a continuación, se llama en el `Label` objeto.

El [ `Binding` constructor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) también se podría haber usado para definir el `Binding` objeto.

El [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) muestra la técnica comparable en XAML. El `Opacity` propiedad de la `Label` está establecido en un `Binding` extensión de marcado con [ `Path` ](xref:Xamarin.Forms.Binding.Path) establecido en el `Value` propiedad y [ `Source` ](xref:Xamarin.Forms.Binding.Source) establecido en un Embedded `x:Reference` extensión de marcado.

En resumen, hay dos maneras de hacer referencia al objeto de origen de enlace:

- A través de la `BindingContext` propiedad del destino
- A través de la `Source` propiedad de la `Binding` propio objeto

Si se especifican ambos, el segundo tiene prioridad. La ventaja de la `BindingContext` es que se propaga a través del árbol visual. Se trata de *muy* útil si varias propiedades de destino están enlazadas al mismo objeto de origen.

El [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programa demuestra esta técnica con la [ `WebView` ](xref:Xamarin.Forms.WebView) elemento. Dos `Button` heredan los elementos para navegar hacia delante y hacia atrás un `BindingContext` de su grupo primario que hace referencia a la `WebView`. El `IsEnabled` las propiedades de los dos botones, a continuación, tienen simple `Binding` las extensiones de marcado que tienen como destino el botón `IsEnabled` propiedades según la configuración de la [ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack) y [ `CanGoForward` ](xref:Xamarin.Forms.WebView.CanGoForward) propiedades de solo lectura de la `WebView`.

## <a name="the-binding-mode"></a>El modo de enlace

Establecer el [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) propiedad de `Binding` a un miembro de la [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) enumeración:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) para que los cambios en la propiedad de origen afectan el destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) para que los cambios en la propiedad de destino afecta al origen
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) para que los cambios en el origen y destino afectan entre sí
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) Para usar el [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) especificado cuando el destino `BindableProperty` se creó. Si se ha especificado ninguno, el valor predeterminado es `OneWay` para las propiedades enlazables normales, y `OneWayToSource` para las propiedades enlazables de solo lectura.

> [!NOTE]
> El `BindingMode` enumeración ahora también incluye `OnTime` para aplicar un enlace sólo cuando cambia el contexto de enlace y no cuando se cambia la propiedad de origen.

Las propiedades que suelen ser los destinos de los enlaces de datos en escenarios MVVM generalmente tienen un `DefaultBindingMode` de `TwoWay`. Estos son:

- `Value` propiedad de `Slider` y `Stepper`
- `IsToggled` propiedad de `Switch`
- `Text` propiedad de `Entry`, `Editor`, y `SearchBar`
- `Date` propiedad de `DatePicker`
- `Time` propiedad de `TimePicker`

El [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) muestra los modos de cuatro enlace con un enlace de datos que el destino es el `FontSize` propiedad de un `Label` y el origen es el `Value` propiedad de un `Slider`. Esto permite que cada `Slider` para controlar el tamaño de fuente de los correspondientes `Label`. Pero la `Slider` elementos no se inicializan porque el `DefaultBindingMode` de la `FontSize` propiedad es `OneWay`.

El [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) ejemplo establece los enlaces en el `Value` propiedad de la `Slider` que hacen referencia a la `FontSize` propiedad de cada uno `Label`. Esto parece ser con versiones anteriores, pero funciona mejor en inicializar el `Slider` elementos porque el `Value` propiedad de la `Slider` tiene un `DefaultBindingMode` de `TwoWay`.

[![Captura de pantalla de triple del invierta enlace](images/ch16fg06-small.png "enlace inverso")](images/ch16fg06-large.png#lightbox "enlace inverso")

Esto es análogo a cómo se definen los enlaces en MVVM, y a usar este tipo de enlace con frecuencia.

## <a name="string-formatting"></a>Formato de cadena

Cuando la propiedad de destino es de tipo `string`, puede usar el [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) propiedad definida por `BindingBase` para convertir el origen en un `string`. Establecer el `StringFormat` propiedad a un formato de cadena que se usarían con el método estático de .NET [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) formato para mostrar el objeto. Cuando se usa esta cadena de formato dentro de una extensión de marcado, inclúyalo entre comillas simples para no confundir con las llaves de cierre para una extensión de marcado incrustado.

El [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) ejemplo muestra cómo usar `StringFormat` en XAML.

El [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) ejemplo muestra cómo mostrar el tamaño de la página con los enlaces a la `Width` y `Height` propiedades de la `ContentPage`.

## <a name="why-is-it-called-path"></a>¿Por qué se lo denomina "Path"?

El [ `Path` ](xref:Xamarin.Forms.Binding.Path) propiedad de `Binding` se denomina así porque puede ser una serie de propiedades e indizadores separados por puntos. El [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) muestra varios ejemplos.

## <a name="binding-value-converters"></a>Convertidores de valores de enlace

Cuando las propiedades de un enlace de origen y destino son tipos diferentes, puede convertir entre los tipos utilizando un conversor de enlaces. Se trata de una clase que implementa el [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interfaz y contiene dos métodos: [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para convertir el origen al destino, y [ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para convertir el destino en el origen.

El [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca es un ejemplo para convertir un `int` a un `bool`. Se muestra por el [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) ejemplo, lo que permite solo el `Button` si al menos un carácter se ha escrito en un `Entry`.

El [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) clase convierte un `bool` a un `string` y define dos propiedades para especificar qué texto se debe devolver para `false` y `true` valores.
El [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) es similar. El [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) ejemplo muestra cómo utilizar estos dos convertidores para mostrar diferentes textos en diferentes colores según un `Switch` configuración.

La interfaz genérica [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) puede reemplazar el `BoolToStringConverter` y `BoolToColorConverter` y servir como generalizada `bool`-a-convertidor de objetos de cualquier tipo.

## <a name="bindings-and-custom-views"></a>Los enlaces y vistas personalizadas

Puede simplificar los controles personalizados mediante los enlaces de datos. El [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) define el archivo de código `Text`, `TextColor`, `FontSize`, `FontAttributes`, y `IsChecked` propiedades, pero no tiene ninguna lógica en absoluto para los objetos visuales del control.
En su lugar el [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) archivo contiene todo el marcado para elementos visuales del control a través de enlaces de datos en el `Label` elementos según las propiedades definidas en el archivo de código subyacente.

El [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) muestra el `NewCheckBox` control personalizado.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 16 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Ejemplos de capítulo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
