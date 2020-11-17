---
title: Resumen del capítulo 10. Extensiones de marcado XAML
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 10. Extensiones de marcado XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b2083a8dd17749074258c8bd5e6d1a7374c758f9
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368497"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumen del capítulo 10. Extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

> [!NOTE]
> Este libro se publicó en la primavera de 2016 y no se ha actualizado desde entonces. Gran parte del libro sigue siendo útil, pero algunos de los materiales están anticuados y algunos temas ya no son completamente correctos o completos.

Normalmente, el analizador de XAML convierte cualquier cadena establecida como un valor de atributo en el tipo de la propiedad basándose en las conversiones estándar de los tipos de datos de .NET básicos, o en un derivado de [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) asociado a la propiedad o su tipo con [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Pero a veces es conveniente establecer un atributo de un origen diferente, por ejemplo, un elemento de un diccionario, el valor de un campo o una propiedad estática o un cálculo de algún tipo.

Este es el trabajo de una *extensión de marcado XAML*. A pesar del nombre, las extensiones de marcado XAML *no* son una extensión a XML. XAML es siempre XML válido.

## <a name="the-code-infrastructure"></a>Infraestructura de código

Una extensión de marcado XAML es una clase que implementa la interfaz [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension). Una clase de este tipo suele tener la palabra `Extension` al final de su nombre, pero normalmente aparece en XAML sin ese sufijo.

Todas las implementaciones de XAML admiten las siguientes extensiones de marcado XAML:

- `x:Static` compatible con [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` compatible con [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` compatible con [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` compatible con [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` compatible con [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Estas cuatro extensiones de marcado XAML son compatibles con muchas implementaciones de XAML, entre las que se incluyen Xamarin.Forms:

- `StaticResource` compatible con [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` compatible con [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` compatible con [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;descrito en [Capítulo 16. Data binding](chapter16.md)
- `TemplateBinding` compatible con [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;no se trata en el libro.

En Xamarin.Forms se incluye una extensión de marcado XAML adicional en conexión con [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;no se trata en el libro.

## <a name="accessing-static-members"></a>Acceso a miembros estáticos

Use el elemento [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) para establecer un atributo en el valor de una propiedad, un campo o un miembro de enumeración estáticos públicos. Establezca la propiedad [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) en el miembro estático. Normalmente es más fácil especificar `x:Static` y el nombre del miembro entre llaves. No es necesario incluir el nombre de la propiedad `Member`, solo el propio miembro. Esta sintaxis común se muestra en el ejemplo [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics). Los propios campos estáticos se definen en la clase [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs). Esta técnica permite establecer constantes usadas en un programa.

Con una declaración de espacio de nombres XML adicional, puede hacer referencia a propiedades, campos o miembros de enumeración estáticos públicos definidos en .NET Framework, tal como se muestra en el ejemplo [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics).

## <a name="resource-dictionaries"></a>Diccionarios de recursos

La clase `VisualElement` define una propiedad denominada [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) que se puede establecer en un objeto de tipo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Dentro de XAML, puede almacenar los elementos de este diccionario e identificarlos con el atributo `x:Key`. Los elementos almacenados en el diccionario de recursos se comparten entre todas las referencias al elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource para la mayoría de los fines

En la mayoría de los casos, usará la extensión de marcado [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) para hacer referencia a un elemento del diccionario de recursos, tal y como se muestra en el ejemplo [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing). Puede usar un elemento `StaticResourceExtension` o `StaticResource` entre llaves:

[![Captura de pantalla triple de uso compartido de recursos](images/ch10fg03-small.png "Uso compartido de recursos")](images/ch10fg03-large.png#lightbox "Uso compartido de recursos")

No confunda la extensión de marcado`x:Static` y la extensión de marcado `StaticResource`.

### <a name="a-tree-of-dictionaries"></a>Árbol de diccionarios

Cuando el analizador XAML encuentra `StaticResource`, comienza a buscar una clave coincidente en el árbol visual y, a continuación, busca `ResourceDictionary` en la clase `App` de la aplicación. Esto permite profundizar en los elementos de un diccionario de recursos en el árbol visual para reemplazar un diccionario de recursos situado más arriba en el árbol visual. Esto se muestra en el ejemplo [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees).

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fines especiales

La extensión de marcado `StaticResource` hace que un elemento se recupere del diccionario cuando se compila un árbol visual durante la llamada de `InitializeComponent`. Una alternativa a `StaticResource` es [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), que mantiene un vínculo a la clave del diccionario y actualiza el destino cuando cambia el elemento al que hace referencia la clave.

La diferencia entre `StaticResource` y `DynamicResource` se muestra en el ejemplo [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic).

Una propiedad establecida por `DynamicResource` debe estar respaldada por una propiedad enlazable, tal y como se describe en [Capítulo 11l. La infraestructura enlazable](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensiones de marcado de menor uso

Use la extensión de marcado [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) para establecer una propiedad en `null`.

Use la extensión de marcado [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) para establecer una propiedad en un objeto `Type` de .NET.

Utilice [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) para definir una matriz. Especifique el tipo de los miembros de la matriz estableciendo la propiedad [`Type`] en una extensión de marcado `x:Type`.

## <a name="a-custom-markup-extension"></a>Extensión de marcado personalizada

Puede crear sus propias extensiones de marcado XAML escribiendo una clase que implemente la interfaz [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) con un método [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)).

La clase [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) cumple esos requisitos. Crea un valor de tipo `Color` basándose en los valores de las propiedades denominadas `H`, `S`, `L` y `A`. Esta clase es el primer elemento de una biblioteca de Xamarin.Forms denominada [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que se crea y usa en el transcurso de este libro.

En el ejemplo [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) se muestra cómo hacer referencia a esta biblioteca y utilizar la extensión de marcado personalizada.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 10 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Ejemplos del capítulo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
