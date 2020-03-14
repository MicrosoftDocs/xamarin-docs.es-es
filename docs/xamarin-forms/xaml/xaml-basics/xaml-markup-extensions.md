---
title: 'Parte 3: Extensiones de marcado XAML'
description: Las extensiones de marcado XAML constituyen una característica importante de XAML que permiten establecer objetos o valores que se hace referencia indirectamente desde otros orígenes de propiedades.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 89e2026ff16a9614234d6ee4bfa4df620cf58b56
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305904"
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3: Extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Las extensiones de marcado XAML constituyen una característica importante de XAML que permite establecer las propiedades en objetos o valores a los que se hace referencia indirectamente desde otros orígenes. Las extensiones de marcado XAML son especialmente importantes para compartir objetos y hacer referencia a constantes usadas en una aplicación, pero encuentran su utilidad más grande en los enlaces de datos._

## <a name="xaml-markup-extensions"></a>Extensiones de marcado XAML

En general, usa XAML para establecer las propiedades de un objeto a los valores explícitos, como una cadena, un número, un miembro de enumeración o una cadena que se convierte en un valor en segundo plano.

Sin embargo, en ocasiones, las propiedades en su lugar, deben hacer referencia a valores definidos en alguna parte de lo contrario, o que pueden requerir un procesamiento poco por código en tiempo de ejecución. Para estos propósitos, están disponibles *las extensiones de marcado* XAML.

Estas extensiones de marcado XAML no son extensiones de XML. XAML es XML completamente válido. Se denominan "extensiones" porque están respaldadas por código en clases que implementan `IMarkupExtension`. Puede escribir sus propias extensiones de marcado personalizada.

En muchos casos, las extensiones de marcado XAML son reconocibles al instante en archivos XAML porque aparecen como valores de atributo delimitados por llaves: {y}, pero a veces aparecen las extensiones de marcado en el marcado como elementos convencionales.

## <a name="shared-resources"></a>Recursos compartidos

Algunas páginas XAML contienen varias vistas con propiedades establecidas en los mismos valores. Por ejemplo, muchos de los valores de propiedad de estos objetos `Button` son los mismos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

Si una de estas propiedades debe cambiarse, es preferible realizar el cambio de una sola vez en lugar de tres veces. Si se tratara de código, que es probable que va a utilizar constantes y los objetos de solo lectura estáticos para ayudar a mantener tales valores coherentes y fáciles de modificar.

En XAML, una solución popular es almacenar estos valores u objetos en un *Diccionario de recursos*. La clase `VisualElement` define una propiedad denominada `Resources` de tipo `ResourceDictionary`, que es un diccionario con claves de tipo `string` y valores de tipo `object`. Puede colocar objetos en este diccionario y, a continuación, hacer referencia a ellos desde el marcado, todo ello en XAML.

Para usar un diccionario de recursos en una página, incluya un par de `Resources` etiquetas de elemento de propiedad. Resulta más conveniente colocar estos elementos en la parte superior de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

También es necesario incluir explícitamente `ResourceDictionary` Etiquetas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Ahora se pueden agregar objetos y valores de distintos tipos al diccionario de recursos. Estos tipos deben ser instanciables. No pueden ser clases abstractas, por ejemplo. Estos tipos también deben tener un constructor público sin parámetros. Cada elemento requiere una clave de diccionario especificada con el atributo `x:Key`. Por ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Estos dos elementos son valores del tipo de estructura `LayoutOptions`, y cada uno tiene una clave única y una o dos propiedades establecidas. En el código y el marcado, es mucho más habitual usar los campos estáticos de `LayoutOptions`, pero aquí es más conveniente establecer las propiedades.

Ahora es necesario establecer las propiedades `HorizontalOptions` y `VerticalOptions` de estos botones en estos recursos, y esto se hace con la extensión de marcado XAML `StaticResource`:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

La extensión de marcado de `StaticResource` siempre está delimitada por llaves e incluye la clave del diccionario.

El nombre `StaticResource` lo distingue de `DynamicResource`, que también admite Xamarin. Forms. `DynamicResource` es para las claves de diccionario asociadas a valores que pueden cambiar durante el tiempo de ejecución, mientras `StaticResource` tiene acceso a los elementos del diccionario una sola vez cuando se construyen los elementos de la página.

En el caso de la propiedad `BorderWidth`, es necesario almacenar un Double en el diccionario. XAML define convenientemente las etiquetas para tipos de datos comunes como `x:Double` y `x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

No es necesario ponerlo en tres líneas. Esta entrada del diccionario para este ángulo de giro solo toma una línea hacia arriba:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Se puede hacer referencia a esos dos recursos de la misma manera que los valores `LayoutOptions`:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

En el caso de los recursos de tipo `Color`, puede usar las mismas representaciones de cadena que se utilizan al asignar atributos directamente de estos tipos. Los convertidores de tipos se invocan cuando se crea el recurso. A continuación se muestra un recurso de tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

A menudo, los programas establecen una propiedad `FontSize` en un miembro de la enumeración `NamedSize` como `Large`. La clase `FontSizeConverter` funciona en segundo plano para convertirla en un valor dependiente de la plataforma mediante el método `Device.GetNamedSized`. Sin embargo, al definir un recurso de tamaño de fuente, tiene más sentido usar un valor numérico, que se muestra aquí como un tipo de `x:Double`:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Ahora todas las propiedades, excepto `Text`, se definen mediante la configuración de recursos:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

También es posible usar `OnPlatform` dentro del Diccionario de recursos para definir valores diferentes para las plataformas. A continuación se muestra cómo un objeto `OnPlatform` puede formar parte del Diccionario de recursos para diferentes colores de texto:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Observe que `OnPlatform` obtiene un atributo `x:Key` porque es un objeto del diccionario y un atributo `x:TypeArguments` porque es una clase genérica. Los atributos `iOS`, `Android`y `UWP` se convierten en valores `Color` cuando se inicializa el objeto.

Este es el archivo XAML completo final con tres botones de acceso a seis valores compartidos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

Las capturas de pantalla comprobación un estilo coherente y el estilo depende de la plataforma:

[![controles con estilo](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Aunque es más común definir el `Resources` colección en la parte superior de la página, tenga en cuenta que la propiedad `Resources` está definida por `VisualElement`y puede tener `Resources` colecciones en otros elementos de la página. Por ejemplo, intente agregar uno al `StackLayout` en este ejemplo:

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

Descubrirá que el color del texto de los botones ahora es azul. Básicamente, cada vez que el analizador XAML encuentra una extensión de marcado `StaticResource`, busca en el árbol visual y usa el primer `ResourceDictionary` que encuentra con esa clave.

Uno de los tipos más comunes de objetos almacenados en los diccionarios de recursos es el `Style`de Xamarin. Forms, que define una colección de valores de propiedad. Los estilos se describen en el artículo [estilos](~/xamarin-forms/user-interface/styles/index.md).

A veces, los desarrolladores nuevos en XAML preguntan si pueden colocar un elemento visual como `Label` o `Button` en un `ResourceDictionary`. Si bien es seguramente posible, no tiene mucho sentido. El propósito del `ResourceDictionary` es compartir objetos. No se puede compartir un elemento visual. La misma instancia no puede aparecer dos veces en una sola página.

## <a name="the-xstatic-markup-extension"></a>La extensión de marcado x: Static

A pesar de las similitudes de sus nombres, `x:Static` y `StaticResource` son muy diferentes. `StaticResource` devuelve un objeto de un diccionario de recursos mientras `x:Static` tiene acceso a uno de los siguientes:

- un campo estático público
- una propiedad estática pública
- un campo constante público
- un miembro de enumeración.

La extensión de marcado de `StaticResource` es compatible con las implementaciones de XAML que definen un diccionario de recursos, mientras que `x:Static` es una parte intrínseca de XAML, como revela el prefijo `x`.

Estos son algunos ejemplos en los que se muestra cómo `x:Static` puede hacer referencia explícita a los campos estáticos y a los miembros de enumeración:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Hasta ahora, esto no es muy impresionante. Pero el `x:Static` extensión de marcado también puede hacer referencia a propiedades o campos estáticos de su propio código. Por ejemplo, a continuación se muestra una `AppConstants` clase que contiene algunos campos estáticos que podría querer usar en varias páginas en una aplicación:

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

Para hacer referencia a los campos estáticos de esta clase en el archivo XAML, necesitará alguna manera de indicar dentro del archivo XAML donde se encuentra este archivo. Hacer esto con una declaración de espacio de nombres XML.

Recuerde que los archivos XAML que se creó como parte de la plantilla estándar de XAML de Xamarin.Forms contienen dos declaraciones de espacio de nombres XML: uno para tener acceso a las clases de Xamarin.Forms y otro para hacer referencia a las etiquetas y atributos intrínsecos de XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Necesitará las declaraciones de espacio de nombres XML adicionales para tener acceso a otras clases. Cada declaración de espacio de nombres XML adicional define un nuevo prefijo. Para tener acceso a las clases locales de la biblioteca .NET Standard de aplicaciones compartidas, como `AppConstants`, los programadores de XAML suelen usar el prefijo `local`. La declaración del espacio de nombres debe indicar el nombre del espacio de nombres CLR (Common Language Runtime), también conocido como nombre del espacio de nombres .NET, C# que es el nombre que aparece en una definición de `namespace` o en una directiva `using`:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

También puede definir las declaraciones de espacio de nombres XML para los espacios de nombres de .NET en cualquier ensamblado al que hace referencia la biblioteca .NET Standard. Por ejemplo, a continuación se muestra un prefijo de `sys` para el espacio de nombres estándar de .NET `System`, que se encuentra en el ensamblado **netstandard** . Dado que se trata de otro ensamblado, también debe especificar el nombre del ensamblado, en este caso **netstandard**:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Observe que la palabra clave `clr-namespace` va seguida de dos puntos y el nombre del espacio de nombres de .NET, seguido de un punto y coma, la palabra clave `assembly`, un signo igual y el nombre del ensamblado.

Sí, los dos puntos siguen `clr-namespace` pero el signo igual sigue `assembly`. La sintaxis se definió de esta manera deliberadamente: la mayoría de las declaraciones de espacios de nombres XML hacen referencia a un URI que comienza con un nombre de esquema de URI como `http`, que siempre va seguido de un signo de dos puntos. La parte `clr-namespace` de esta cadena está pensada para imitar esa Convención.

Ambas declaraciones de espacios de nombres se incluyen en el ejemplo **StaticConstantsPage** . Tenga en cuenta que las dimensiones de `BoxView` se establecen en `Math.PI` y `Math.E`, pero se escalan por un factor de 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

El tamaño del `BoxView` resultante con respecto a la pantalla depende de la plataforma:

[![controles mediante la extensión de marcado x:Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Otras extensiones de marcado estándar

Varias extensiones de marcado son intrínsecas de XAML y se admiten en los archivos XAML de Xamarin.Forms. Algunas de ellas no se utilizan muy a menudo, pero son esenciales cuando necesite:

- Si una propiedad tiene un valor que no es de `null` de forma predeterminada pero desea establecerlo en `null`, establézcalo en la extensión de marcado `{x:Null}`.
- Si una propiedad es de tipo `Type`, puede asignarla a un objeto `Type` mediante el `{x:Type someClass}`de la extensión de marcado.
- Puede definir matrices en XAML mediante el `x:Array` extensión de marcado. Esta extensión de marcado tiene un atributo required denominado `Type` que indica el tipo de los elementos de la matriz.
- La extensión de marcado de `Binding` se describe en la [parte 4. Aspectos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).
- La extensión de marcado de `RelativeSource` se describe en [enlaces relativos](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>La extensión de marcado ConstraintExpression

Las extensiones de marcado pueden tener propiedades, pero no se establecen como atributos XML. En una extensión de marcado, valores de las propiedades están separados por comas y comillas no aparece encerrado entre llaves.

Esto se puede ilustrar con la extensión de marcado Xamarin. Forms denominada `ConstraintExpression`, que se usa con la clase `RelativeLayout`. Puede especificar la ubicación o el tamaño de una vista secundaria como una constante o con respecto a un elemento primario o en otra vista con nombre. La sintaxis de la `ConstraintExpression` permite establecer la posición o el tamaño de una vista utilizando un `Factor` veces una propiedad de otra vista, más una `Constant`. Algo más complejo que requiere el código.

Veamos un ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

Quizás la lección más importante que se deben realizar desde este ejemplo es la sintaxis de la extensión de marcado: comillas no deben aparecer dentro de las llaves de una extensión de marcado. Al escribir la extensión de marcado en un archivo XAML, es normal que desee incluir los valores de las propiedades de comillas. ¡Resistir la tentación!

Este es el programa que se ejecuta:

[![el diseño relativo mediante restricciones](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Resumen

Las extensiones de marcado XAML que se muestra a continuación proporcionan compatibilidad importante para los archivos XAML. Pero quizás la extensión de marcado XAML más valiosa es `Binding`, que se trata en la siguiente parte de esta serie, [parte 4. Aspectos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis XAML básica](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Del enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
