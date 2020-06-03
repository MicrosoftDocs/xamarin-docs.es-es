---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 11. The Bindable infrastructure''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edc3dfd97457fe93a04edd82574f6ed419f5fdc1
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136804"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumen del capítulo 11. La infraestructura enlazable

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Cada programador de C# está familiarizado con las *propiedades* de C#. Las propiedades contienen un descriptor de acceso *set* o un descriptor de acceso *get*. A menudo se les llama *propiedades de CLR* en el contexto de Common Language Runtime.

Xamarin.Forms establece una definición de propiedad mejorada denominada *propiedad enlazable* encapsulada por la clase [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) y admitida por la clase [`BindableObject`](xref:Xamarin.Forms.BindableObject). Estas clases están relacionadas, pero son bastante distintas: `BindableProperty` se utiliza para definir la propiedad en sí; `BindableObject` es como `object` en que es una clase base para las clases que definen propiedades enlazables.

## <a name="the-xamarinforms-class-hierarchy"></a>Jerarquía de clases de Xamarin.Forms

En el ejemplo [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) se usa la reflexión para mostrar una jerarquía de clases de Xamarin.Forms y mostrar el rol fundamental que desempeña `BindableObject` en esta jerarquía. `BindableObject` se deriva de `Object` y es la clase primaria de [`Element`](xref:Xamarin.Forms.Element) de la que se deriva [`VisualElement`](xref:Xamarin.Forms.VisualElement). Se trata de la clase primaria de [`Page`](xref:Xamarin.Forms.Page) y [`View`](xref:Xamarin.Forms.View), que es la clase primaria de [`Layout`](xref:Xamarin.Forms.Layout):

[![Captura de pantalla triple de uso compartido de jerarquía de clases](images/ch11fg01-small.png "Uso compartido de jerarquía de clases")](images/ch11fg01-large.png#lightbox "Uso compartido de jerarquía de clases")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un vistazo a BindableObject y BindableProperty

En las clases que derivan de `BindableObject` muchas propiedades de CLR se dice que están "respaldadas" por las propiedades enlazables. Por ejemplo, la propiedad [`Text`](xref:Xamarin.Forms.Label.Text) de la clase `Label` es una propiedad de CLR, pero la clase `Label` también define un campo público estático de solo lectura denominado [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) de tipo `BindableProperty`.

Una aplicación puede establecer u obtener la propiedad `Text` de `Label` normalmente, o bien la aplicación puede establecer `Text` mediante una llamada al método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) definido por `BindableObject` con un argumento `Label.TextProperty`. Del mismo modo, una aplicación puede obtener el valor de la propiedad `Text` mediante una llamada al método [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)), de nuevo con un argumento `Label.TextProperty`. Esto se muestra en el ejemplo [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings).

La propiedad de CLR `Text` incluso se implementa por completo con los métodos `SetValue` y `GetValue` definidos por `BindableObject` junto con la propiedad estática `Label.TextProperty`.

`BindableObject` y `BindableProperty` proporcionan compatibilidad con:

- Asignar valores predeterminados a propiedades
- Almacenar sus valores actuales
- Proporcionar mecanismos para validar valores de propiedad
- Mantener la coherencia entre las propiedades relacionadas en una única clase
- Responder a los cambios de propiedad
- Desencadenar notificaciones cuando una propiedad está a punto de cambiar o ha cambiado
- Admitir el enlace de datos
- Admitir estilos
- Admitir recursos dinámicos

Cada vez que cambia una propiedad respaldada por una propiedad enlazable, `BindableObject` desencadena un evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) que identifica la propiedad que ha cambiado. Este evento no se desencadena cuando la propiedad se establece en el mismo valor.

Algunas propiedades no están respaldadas por propiedades enlazables y algunas clases de Xamarin.Forms, como `Span`, no se derivan de `BindableObject`. Solo una clase que se deriva de `BindableObject` puede admitir propiedades enlazables porque `BindableObject` define los métodos `SetValue` y `GetValue`.

Dado que `Span` no se deriva de `BindableObject`, ninguna de sus propiedades &mdash; como `Text` &mdash; están respaldadas por una propiedad enlazable. Esta es la razón por la que un valor `DynamicResource` en la propiedad `Text` de `Span` produce una excepción en el ejemplo [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) del capítulo anterior. En el ejemplo [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) se muestra cómo establecer recursos dinámicos en el código mediante el método [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) definido por `Element`. El primer argumento es un objeto de tipo `BindableProperty`.

Del mismo modo, el método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definido por `BindableObject` tiene un primer argumento de tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definición de propiedades enlazables

Puede definir sus propias propiedades enlazables mediante el método estático [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) para crear un campo estático de solo lectura de tipo `BindableProperty`.

Esto se muestra en la clase [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit). La clase se deriva de `Label` y permite especificar un tamaño de fuente en puntos. Se muestra en el ejemplo [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText).

Se requieren cuatro argumentos del método `BindableProperty.Create`:

- `propertyName`: el nombre de texto de la propiedad (el mismo que el nombre de la propiedad de CLR)
- `returnType`: el tipo de la propiedad de CLR
- `declaringType`: el tipo de la clase que declara la propiedad.
- `defaultValue`: el valor predeterminado de la propiedad

Dado que `defaultValue` es de tipo `object`, el compilador debe ser capaz de determinar el tipo del valor predeterminado. Por ejemplo, si `returnType` es `double`, `defaultValue` debe establecerse en algo como 0,0 en lugar de solo en 0, o la falta de coincidencia de tipos desencadenará una excepción en tiempo de ejecución.

También es muy común que una propiedad enlazable incluya:

- `propertyChanged`: un método estático al que se llama cuando cambia el valor de la propiedad. El primer argumento es la instancia de la clase cuya propiedad ha cambiado.

Los demás argumentos para `BindableProperty.Create` no son tan comunes:

- `defaultBindingMode`: se usa en relación con el enlace de datos (como se describe en [**Capítulo 16. Enlace de datos**](chapter16.md))
- `validateValue`: devolución de llamada para comprobar si hay un valor válido.
- `propertyChanging`: devolución de llamada que indica cuándo está a punto de cambiar la propiedad.
- `coerceValue`: devolución de llamada para convertir un valor establecido en otro valor.
- `defaultValueCreate`: devolución de llamada para crear un valor predeterminado que no se puede compartir entre las instancias de la clase (por ejemplo, una colección).

### <a name="the-read-only-bindable-property"></a>Propiedad enlazable de solo lectura.

Una propiedad enlazable puede ser de solo lectura. La creación de una propiedad enlazable de solo lectura requiere una llamada al método estático [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) para definir un campo de solo lectura estático privado de tipo [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey).

Después, defina el descriptor de acceso `set` de la propiedad de CLR como `private` para llamar a una sobrecarga [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) con el objeto `BindablePropertyKey`. Esto evita que la propiedad se establezca fuera de la clase.

Esto se muestra en la clase [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) utilizada en el ejemplo [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 11 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Ejemplos del capítulo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md)
