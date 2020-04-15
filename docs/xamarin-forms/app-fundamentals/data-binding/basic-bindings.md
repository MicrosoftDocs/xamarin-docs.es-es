---
title: Enlaces básicos de Xamarin.Forms
description: En este artículo se explica cómo usar el enlace de datos de Xamarin.Forms, que enlaza un par de propiedades entre dos objetos, del que al menos uno suele ser un objeto de interfaz de usuario. Estos dos objetos se denominan el destino y el origen.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2019
ms.custom: video
ms.openlocfilehash: 2227e2bd47a5b4960d28be67bac7947a4fb57a93
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303784"
---
# <a name="xamarinforms-basic-bindings"></a>Enlaces básicos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Un enlace de datos de Xamarin.Forms enlaza un par de propiedades entre dos objetos, del que al menos uno suele ser un objeto de interfaz de usuario. Estos dos objetos se denominan el *destino* y el *origen*:

- El *destino* es el objeto (y la propiedad) en la que se establece el enlace de datos.
- El *origen* es el objeto (y la propiedad) al que hace referencia el enlace de datos.

Esta distinción a veces puede ser un poco confusa: en el caso más simple, los datos fluyen desde el origen al destino, lo que significa que el valor de la propiedad de destino se establece en el valor de la propiedad de origen. Pero en algunos casos, los datos también pueden fluir desde el destino al origen, o en ambas direcciones. Para evitar confusiones, tenga en cuenta que el destino siempre es el objeto en el que se establece el enlace de datos, incluso si ofrece datos en lugar de recibirlos.

## <a name="bindings-with-a-binding-context"></a>Enlaces con un contexto de enlace

Aunque los enlaces de datos normalmente se especifican totalmente en XAML, es útil verlos en el código. La página **Enlace básico de código** contiene un archivo XAML con un elemento `Label` y un elemento `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El elemento `Slider` se establece para un intervalo de 0 a 360. El propósito de este programa es girar el elemento `Label` mediante la manipulación de `Slider`.

Sin los enlaces de datos, se establecería el evento `ValueChanged` del elemento `Slider` en un controlador de eventos que accede a la propiedad `Value` del elemento `Slider` y establece ese valor en la propiedad `Rotation` del elemento `Label`. El enlace de datos automatiza ese trabajo; el controlador de eventos y el código que contiene ya no son necesarios.

Puede establecer un enlace en una instancia de cualquier clase que se derive de [`BindableObject`](xref:Xamarin.Forms.BindableObject), lo que incluye las derivadas `Element`, `VisualElement`, `View` y `View`.  El enlace siempre se establece en el objeto de destino. El enlace hace referencia al objeto de origen. Para establecer el enlace de datos, use los dos miembros siguientes de la clase de destino:

- La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) especifica el objeto de origen.
- El método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) especifica la propiedad de destino y la de origen.

En este ejemplo, `Label` es el destino de enlace y `Slider` es el origen de enlace. Los cambios en el origen de `Slider` afectan a la rotación del destino de `Label`. Los datos fluyen del origen al destino.

El método `SetBinding` definido por `BindableObject` tiene un argumento de tipo [`BindingBase`](xref:Xamarin.Forms.BindingBase) del que se deriva la clase [`Binding`](xref:Xamarin.Forms.Binding), pero existen otros métodos `SetBinding` definidos por la clase [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions). En el archivo de código subyacente del ejemplo **Enlace de código básico** se usa un método de extensión [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) más sencillo de esta clase.

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

El objeto `Label` es el destino de enlace, por lo que es el objeto en el que se establece esta propiedad y en el que se llama al método. La propiedad `BindingContext` indica el origen de enlace, que es el elemento `Slider`.

El método `SetBinding` se llama en el destino de enlace, pero especifica tanto la propiedad de destino como la de origen. La propiedad de destino se especifica como un objeto `BindableProperty`: `Label.RotationProperty`. La propiedad de origen se especifica como una cadena e indica la propiedad `Value` de `Slider`.

El método `SetBinding` revela una de las reglas de enlaces de datos más importantes:

*La propiedad de destino debe estar respaldada por una propiedad enlazable.*

Esta regla implica que el objeto de destino debe ser una instancia de una clase que se derive de `BindableObject`. Vea el artículo [**Propiedades enlazables**](~/xamarin-forms/xaml/bindable-properties.md) para obtener información general sobre los objetos y las propiedades enlazables.

No hay ninguna regla de este tipo para la propiedad de origen, que se especifica como una cadena. De forma interna, se usa la reflexión para acceder a la propiedad real. Pero en este caso concreto, la propiedad `Value` también está respaldada por una propiedad enlazable.

El código se puede simplificar ligeramente: la propiedad enlazable `RotationProperty` se define mediante `VisualElement`, y también la heredan `Label` y `ContentPage`, por lo que no es necesario el nombre de clase en la llamada a `SetBinding`:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Pero la inclusión del nombre de clase es un buen recordatorio del objeto de destino.

Al manipular el elemento `Slider`, el elemento `Label` gira según corresponda:

[![Enlace de código básico](basic-bindings-images/basiccodebinding-small.png "Enlace de código básico")](basic-bindings-images/basiccodebinding-large.png#lightbox "Enlace de código básico")

La página **Enlace Xaml básico** es idéntica a la página **Enlace de código básico**, con la excepción de que define el enlace de datos completo en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Al igual que en el código, el enlace de datos se establece en el objeto de destino, que es el elemento `Label`. Intervienen dos extensiones de marcado XAML. Son reconocibles al instante por los delimitadores de llave:

- La extensión de marcado `x:Reference` es necesaria para hacer referencia al objeto de origen, que es el elemento `Slider` denominado `slider`.
- La extensión de marcado `Binding` enlaza la propiedad `Rotation` de `Label` a la propiedad `Value` de `Slider`.

Vea el artículo [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md) para obtener más información sobre las extensiones de marcado XAML. La extensión de marcado `x:Reference` es compatible con la clase [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension); `Binding` es compatible con la clase [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension). Como indican los prefijos de espacio de nombres XML, `x:Reference` forma parte de la especificación 2009 de XAML, mientras que `Binding` forma parte de Xamarin.Forms. Observe que no aparecen comillas entre las llaves.

Es fácil olvidar la extensión de marcado `x:Reference` cuando se establece el valor `BindingContext`. Un error habitual es establecer la propiedad directamente en el nombre del origen de enlace, como en este caso:

```xaml
BindingContext="slider"
```

Pero eso no es correcto. Ese marcado establece la propiedad `BindingContext` en un objeto `string` cuyos caracteres deletrean "slider".

Observe que la propiedad de origen se especifica con la propiedad [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) de `BindingExtension`, que se corresponde con la propiedad [`Path`](xref:Xamarin.Forms.Binding.Path) de la clase [`Binding`](xref:Xamarin.Forms.Binding).

El marcado que se muestra en la página **Basic XAML Binding** (Enlace XAML básico) se puede simplificar: en las extensiones de marcado XAML como `x:Reference` y `Binding` se pueden definir atributos de *propiedad de contenido*, lo que para las extensiones de marcado XAML significa que no es necesario que aparezca el nombre de propiedad. La propiedad `Name` es la propiedad de contenido de `x:Reference` y la propiedad `Path` es la propiedad de contenido de `Binding`, lo que significa que se pueden eliminar de las expresiones:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Enlaces sin un contexto de enlace

La propiedad `BindingContext` es un componente importante de los enlaces de datos, pero no siempre es necesaria. En su lugar, el objeto de origen se puede especificar en la llamada a `SetBinding` o en la extensión de marcado `Binding`.

Esto se muestra en el ejemplo **Enlace de código alternativo**. El archivo XAML es similar al ejemplo **Enlace de código básico**, salvo que el elemento `Slider` se define para controlar la propiedad `Scale` del elemento `Label`. Por ese motivo, el elemento `Slider` se establece para un intervalo de &ndash;2 a 2:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente establece el enlace con el método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definido por el objeto `BindableObject`. El argumento es un [constructor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) para la clase [`Binding`](xref:Xamarin.Forms.Binding):

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

El constructor `Binding` tiene seis parámetros, por lo que el parámetro `source` se especifica con un argumento con nombre. El argumento es el objeto `slider`.

La ejecución de este programa podría ser un poco sorprendente:

[![Enlace de código alternativo](basic-bindings-images/alternativecodebinding-small.png "Enlace de código alternativo")](basic-bindings-images/alternativecodebinding-large.png#lightbox "Enlace de código alternativo")

En la pantalla de iOS de la izquierda se muestra el aspecto de la pantalla cuando aparece la página por primera vez. ¿Dónde está el elemento `Label`?

El problema es que el elemento `Slider` tiene un valor inicial de 0. Esto hace que la propiedad `Scale` de `Label` también se establezca en 0, y se reemplace su valor predeterminado de 1. Como resultado, el elemento `Label` es invisible inicialmente. Como se muestra en la captura de pantalla de Android, el elemento `Slider` se puede manipular para que `Label` aparezca de nuevo, pero su desaparición inicial es desconcertante.

En el [artículo siguiente](binding-mode.md) verá cómo evitar este problema si inicializa el elemento `Slider` a partir del valor predeterminado de la propiedad `Scale`.

> [!NOTE]
> La clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) también define las propiedades [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) y [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), que pueden escalar el elemento `VisualElement` de forma diferente en dirección horizontal y vertical.

En la página **Enlace XAML alternativo** se muestra el enlace equivalente completamente en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Ahora la extensión de marcado `Binding` tiene dos propiedades establecidas, `Source` y `Path`, separadas por una coma. Si lo prefiere, pueden aparecer en la misma línea:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

La propiedad `Source` se establece en una extensión de marcado `x:Reference` insertada que, en caso contrario, tiene la misma sintaxis que la configuración de `BindingContext`. Observe que no aparecen comillas dentro de las llaves, y que las dos propiedades se deben separar por una coma.

La propiedad de contenido de la extensión de marcado `Binding` es `Path`, pero la parte `Path=` de la extensión de marcado solo se puede eliminar si es la primera propiedad en la expresión. Para eliminar la parte `Path=`, es necesario intercambiar las dos propiedades:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Aunque las extensiones de marcado XAML suelen estar delimitadas por llaves, también se pueden expresar como elementos de objeto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

Ahora las propiedades `Source` y `Path` son atributos XAML normales: los valores aparecen entre comillas y los atributos no están separados por una coma. La extensión de marcado `x:Reference` también se puede convertir en un elemento de objeto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

Esta sintaxis no es común, pero a veces es necesaria cuando hay objetos complejos implicados.

En los ejemplos mostrados hasta ahora se establecen las propiedades `BindingContext` y `Source` de `Binding` en una extensión de marcado `x:Reference` para hacer referencia a otra vista en la página. Estas dos propiedades son de tipo `Object`, y se pueden establecer en cualquier objeto que incluya propiedades adecuadas para los orígenes de enlace.

En los artículos siguientes, descubrirá que puede establecer la propiedad `BindingContext` o `Source` en una extensión de marcado `x:Static` para hacer referencia al valor de una propiedad estática o un campo, o bien en una extensión de marcado `StaticResource` para hacer referencia a un objeto almacenado en un diccionario de recursos, o directamente en un objeto, que suele ser (pero no siempre) una instancia de una clase ViewModel.

La propiedad `BindingContext` se puede establecer en un objeto `Binding` para que las propiedades `Source` y `Path` de `Binding` definan el contexto de enlace.

## <a name="binding-context-inheritance"></a>Herencia del contexto de enlace

En este artículo, ha visto que puede especificar el objeto de origen mediante las propiedades `BindingContext` o `Source` del objeto `Binding`. Si se establecen las dos, la propiedad `Source` de `Binding` tiene prioridad sobre `BindingContext`.

La propiedad `BindingContext` tiene una característica muy importante:

*La configuración de la propiedad `BindingContext` se hereda a través del árbol visual.*

Como verá, esto puede ser muy útil para simplificar las expresiones de enlace y, en algunos casos, especialmente en escenarios de Model-View-ViewModel (MVVM), es esencial.

El ejemplo **Herencia de contexto de enlace** es una simple demostración de la herencia de contexto de enlace:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

La propiedad `BindingContext` de `StackLayout` se establece en el objeto `slider`. `Label` y `BoxView` heredan este contexto de enlace, y en los dos sus propiedades `Rotation` se establecen en la propiedad `Value` del elemento `Slider`:

[![Herencia del contexto de enlace](basic-bindings-images/bindingcontextinheritance-small.png "Herencia del contexto de enlace")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "Herencia del contexto de enlace")

En el [artículo siguiente](binding-mode.md), verá cómo el *modo de enlace* puede cambiar el flujo de datos entre los objetos de origen y destino.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Data-Binding/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
