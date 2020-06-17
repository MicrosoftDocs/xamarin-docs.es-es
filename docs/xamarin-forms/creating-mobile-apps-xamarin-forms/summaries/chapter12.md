---
title: "Resumen del capítulo 12. Estilos" description: "Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 12. Estilos" ms.prod: xamarin ms.technology: xamarin-forms ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3 author: davidbritch ms.author: dabritch ms.date: 07/19/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="summary-of-chapter-12-styles"></a>Resumen del capítulo 12. Estilos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

En Xamarin.Forms, los estilos permiten que varias vistas compartan una colección de valores de propiedad. Esto reduce el marcado y permite mantener temas visuales coherentes.

Los estilos casi siempre se definen y se consumen en el marcado. Se crea una instancia de un objeto de tipo [`Style`](xref:Xamarin.Forms.Style) en un diccionario de recursos y, a continuación, se establece en la propiedad [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de un elemento visual mediante una extensión de marcado `StaticResource` o `DynamicResource`.

## <a name="the-basic-style"></a>Estilo básico

`Style` requiere que su [`TargetType`](xref:Xamarin.Forms.Style.TargetType)se establezca en el tipo del objeto visual al que se aplica. Cuando se crea una instancia de un `Style` en un diccionario de recursos (como es habitual), también se requiere un atributo `x:Key`.

`Style` tiene una propiedad de contenido de tipo [`Setters`](xref:Xamarin.Forms.Style.Setters), que es una colección de objetos [`Setter`](xref:Xamarin.Forms.Setter). Cada `Setter` asocia un [`Property`](xref:Xamarin.Forms.Setter.Property) con [`Value`](xref:Xamarin.Forms.Setter.Value).

En XAML, el valor `Property` es el nombre de una propiedad de CLR (como la propiedad `Text` de `Button`) pero la propiedad con estilo debe estar respaldada por una propiedad enlazable. Además, la propiedad se debe definir en la clase indicada por el valor `TargetType`, o heredada por esa clase.

Puede especificar el valor de `Value` mediante el elemento de propiedad `<Setter.Value>`. Esto le permite establecer `Value` en un objeto que no se puede expresar en una cadena de texto, o en un objeto `OnPlatform`, o en un objeto del que se ha creado una instancia mediante `x:Arguments` o `x:FactoryMethod`. La propiedad `Value` también se puede establecer con una expresión `StaticResource` en otro elemento del diccionario.

El programa [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) muestra la sintaxis básica y muestra cómo hacer referencia a `Style` con una extensión de marcado `StaticResource`:

[![Captura de pantalla triple de estilo básico](images/ch12fg01-small.png "Estilos básicos")](images/ch12fg01-large.png#lightbox "Estilos básicos")

El objeto `Style` y cualquier objeto creado en el objeto `Style` como un valor `Value` se comparten entre todas las vistas que hacen referencia a ese `Style`. `Style` no puede contener nada que no se pueda compartir, como un derivado de `View`.

Los controladores de eventos no se pueden establecer en un `Style`. No se puede establecer la propiedad `GestureRecognizers` en `Style` porque no está respaldada por una propiedad enlazable.

## <a name="styles-in-code"></a>Estilos en el código

Aunque no es habitual, puede crear instancias de objetos `Style` en el código, e iniciarlos. Esto se muestra en el ejemplo [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode).

## <a name="style-inheritance"></a>Herencia de estilo

`Style` tiene una propiedad [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) que se puede establecer en una extensión de marcado `StaticResource` que hace referencia a otro estilo. Esto permite que los estilos hereden de estilos anteriores y agreguen o reemplacen la configuración de propiedades. En el ejemplo [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) se muestra esto.

Si `Style2` se basa en `Style1`, `TargetType` de `Style2` debe ser el mismo que `Style1` o derivarse de `Style1`. El diccionario de recursos en el que se almacena `Style1` debe ser el mismo diccionario de recursos de `Style2` o un diccionario de recursos situado más arriba en el árbol visual.

## <a name="implicit-styles"></a>Estilos implícitos

Si el elemento `Style` de un diccionario de recursos no tiene una configuración de atributo de `x:Key`, se le asigna automáticamente una clave de diccionario y el objeto `Style` se convierte en un *estilo implícito*. Una vista sin un valor de `Style` y cuyo tipo coincida exactamente con `TargetType` buscará ese estilo, como se muestra en el ejemplo [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle).

Un estilo implícito puede derivarse de `Style` con un valor `x:Key`, pero no al revés. No se puede hacer referencia explícita a un estilo implícito.

Puede implementar tres tipos de jerarquías con estilos y `BasedOn`:

- De los estilos definidos en `Application` y `Page` hacia abajo hasta los estilos definidos en los diseños inferiores del árbol visual.
- De los estilos definidos para las clases base como `VisualElement` y `View` a los estilos definidos para clases concretas.
- De los estilos con claves de diccionario explícitas a estilos implícitos.

Estas jerarquías se muestran en el ejemplo [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy).

## <a name="dynamic-styles"></a>Estilos dinámicos

`DynamicResource` puede hacer referencia a un estilo de un diccionario de recursos, en lugar de `StaticResource`. Esto convierte al estilo en un *estilo dinámico*. Si el estilo se reemplaza en el diccionario de recursos por otro estilo con la misma clave, las vistas que hacen referencia a ese estilo con `DynamicResource` cambian automáticamente. Además, la ausencia de una entrada de diccionario con la clave especificada hará que `StaticResource` genere una excepción pero no `DynamicResource`.

Puede usar esta técnica para cambiar de forma dinámica el estilo o los temas, como se muestra en el ejemplo [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles).

Sin embargo, no puede establecer la propiedad `BasedOn` en una extensión de composición de `DynamicResource` porque `BasedOn` no está respaldado por una propiedad enlazable. Para derivar un estilo dinámicamente, no establezca `BasedOn`. En su lugar, establezca la propiedad [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) en la clave del diccionario del estilo del que desea derivar. En el ejemplo [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) se muestra esta técnica.

## <a name="device-styles"></a>Estilos de dispositivo

La clase anidada [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) define doce campos estáticos de solo lectura para seis estilos con `TargetType` de `Label` que puede usar para los tipos comunes de usos de texto.

Seis de estos campos son de tipo `Style` que puede establecer directamente en una propiedad `Style` en el código:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Los otros seis campos son del tipo `string` y se pueden usar como claves de diccionario para los estilos dinámicos:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) igual a "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) igual a "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) igual a "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) igual a "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) igual a "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) igual a "ListItemDetailTextStyle"

Estos estilos se muestran en el ejemplo [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 12 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Ejemplos del capítulo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
