---
title: Parte 3. Extensiones de marcado XAML
description: Las extensiones de marcado XAML constituyen una característica importante de XAML que permite establecer las propiedades en objetos o valores a los que se hace referencia indirectamente desde otros orígenes.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3fcea963b253ea34601a36434007f18d925975eb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131344"
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Las extensiones de marcado XAML constituyen una característica importante de XAML que permite establecer las propiedades en objetos o valores a los que se hace referencia indirectamente desde otros orígenes. Las extensiones de marcado XAML son especialmente importantes para compartir objetos y hacer referencia a constantes usadas en una aplicación, pero encuentran su utilidad más grande en los enlaces de datos._

## <a name="xaml-markup-extensions"></a>Extensiones de marcado XAML

En general, se usa XAML para establecer las propiedades de un objeto en valores explícitos, como una cadena, un número, un miembro de enumeración o una cadena que se convierte en un valor en segundo plano.

Sin embargo, en ocasiones, las propiedades deben hacer referencia a valores definidos en otra parte, o que podrían requerir un pequeño procesamiento por código en tiempo de ejecución. Para estos propósitos, están disponibles *las extensiones de marcado* XAML.

Estas extensiones de marcado XAML no son extensiones de XML. XAML es completamente XML legal. Se denominan "extensiones" porque están respaldadas por código en las clases que implementan `IMarkupExtension` . Puede escribir sus propias extensiones de marcado personalizadas.

En muchos casos, las extensiones de marcado XAML se reconocen al instante en los archivos XAML porque aparecen como valores de atributo delimitados por llaves: {y}, pero a veces las extensiones de marcado aparecen en el marcado como elementos convencionales.

## <a name="shared-resources"></a>Recursos compartidos

Algunas páginas XAML contienen varias vistas cuyas propiedades se establecen en los mismos valores. Por ejemplo, muchos de los valores de propiedad de estos `Button` objetos son los mismos:

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

Si es necesario cambiar alguna de estas propiedades, es preferible realizar el cambio una sola vez en lugar de tres veces. Si se tratara de código, es probable que use constantes y objetos estáticos de solo lectura para ayudar a mantener estos valores coherentes y fáciles de modificar.

En XAML, una solución popular es almacenar estos valores u objetos en un *Diccionario de recursos*. La `VisualElement` clase define una propiedad denominada `Resources` de tipo `ResourceDictionary` , que es un diccionario con claves de tipo `string` y valores de tipo `object` . Puede colocar objetos en este diccionario y luego hacer referencia a ellos desde el marcado, todo en XAML.

Para usar un diccionario de recursos en una página, incluya un par de `Resources` etiquetas de elemento de propiedad. Lo más conveniente es colocarlos en la parte superior de la página:

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

También es necesario incluir explícitamente las `ResourceDictionary` Etiquetas:

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

Ahora se pueden agregar objetos y valores de varios tipos al Diccionario de recursos. Estos tipos deben ser instancias. Por ejemplo, no pueden ser clases abstractas. Estos tipos también deben tener un constructor sin parámetros público. Cada elemento requiere una clave de diccionario especificada con el `x:Key` atributo. Por ejemplo:

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

Estos dos elementos son valores del tipo de estructura `LayoutOptions` y cada uno tiene una clave única y una o dos propiedades establecidas. En el código y el marcado, es mucho más habitual usar los campos estáticos de `LayoutOptions` , pero aquí es más conveniente establecer las propiedades.

Ahora es necesario establecer las `HorizontalOptions` `VerticalOptions` propiedades y de estos botones en estos recursos, y esto se hace con la extensión de `StaticResource` marcado XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

La `StaticResource` extensión de marcado siempre está delimitada por llaves e incluye la clave del diccionario.

El nombre `StaticResource` lo distingue de `DynamicResource` , que Xamarin.Forms también admite. `DynamicResource`es para las claves de diccionario asociadas a valores que pueden cambiar durante el tiempo de ejecución, mientras que `StaticResource` obtiene acceso a los elementos del diccionario una sola vez cuando se construyen los elementos de la página.

En el caso de la `BorderWidth` propiedad, es necesario almacenar un Double en el diccionario. XAML define las etiquetas para tipos de datos comunes como `x:Double` y `x:Int32` :

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

No es necesario colocarlo en tres líneas. Esta entrada del diccionario para este ángulo de rotación solo ocupa una línea:

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

Se puede hacer referencia a esos dos recursos de la misma manera que los `LayoutOptions` valores:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

En el caso de los recursos de tipo `Color` , puede usar las mismas representaciones de cadena que se utilizan al asignar atributos directamente de estos tipos. Los convertidores de tipos se invocan cuando se crea el recurso. Este es un recurso de tipo `Color` :

```xaml
<Color x:Key="textColor">Red</Color>
```

A menudo, los programas establecen una `FontSize` propiedad en un miembro de la `NamedSize` enumeración como `Large` . La `FontSizeConverter` clase funciona en segundo plano para convertirla en un valor dependiente de la plataforma mediante el `Device.GetNamedSized` método. Sin embargo, al definir un recurso de tamaño de fuente, tiene más sentido usar un valor numérico, que se muestra aquí como un `x:Double` tipo:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Ahora todas las propiedades `Text` , excepto, se definen mediante la configuración de recursos:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

También es posible usar `OnPlatform` dentro del Diccionario de recursos para definir valores diferentes para las plataformas. A continuación se muestra cómo un `OnPlatform` objeto puede formar parte del Diccionario de recursos para diferentes colores de texto:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Observe que `OnPlatform` obtiene un `x:Key` atributo porque es un objeto en el Diccionario y un `x:TypeArguments` atributo porque es una clase genérica. Los `iOS` `Android` atributos, y `UWP` se convierten en `Color` valores cuando se inicializa el objeto.

Este es el archivo XAML final completo con tres botones que obtienen acceso a seis valores compartidos:

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

Las capturas de pantallas comprueban el estilo coherente y el estilo que depende de la plataforma:

[![Controles con estilo](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Aunque es más común definir la `Resources` colección en la parte superior de la página, tenga en cuenta que la `Resources` propiedad está definida por `VisualElement` y puede tener `Resources` colecciones en otros elementos de la página. Por ejemplo, intente agregar uno a `StackLayout` en este ejemplo:

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

Descubrirá que el color del texto de los botones es ahora azul. Básicamente, cada vez que el analizador XAML encuentra una `StaticResource` extensión de marcado, busca en el árbol visual y usa el primero que `ResourceDictionary` encuentra la clave.

Uno de los tipos más comunes de objetos almacenados en los diccionarios de recursos es Xamarin.Forms `Style` , que define una colección de valores de propiedad. Los estilos se describen en el artículo [estilos](~/xamarin-forms/user-interface/styles/index.md).

A veces, los desarrolladores nuevos en XAML preguntan si pueden colocar un elemento visual como `Label` o `Button` en `ResourceDictionary` . Aunque es probable que sea posible, no tiene mucho sentido. El propósito de `ResourceDictionary` es compartir objetos. No se puede compartir un elemento visual. La misma instancia no puede aparecer dos veces en una sola página.

## <a name="the-xstatic-markup-extension"></a>La extensión de marcado x:Static

A pesar de las similitudes de sus nombres, `x:Static` y `StaticResource` son muy diferentes. `StaticResource`Devuelve un objeto de un diccionario de recursos mientras `x:Static` tiene acceso a uno de los elementos siguientes:

- un campo estático público
- una propiedad estática pública
- un campo de constante pública
- miembro de la enumeración.

La `StaticResource` extensión de marcado es compatible con las implementaciones de XAML que definen un diccionario de recursos, mientras que `x:Static` es una parte intrínseca de XAML, como `x` revela el prefijo.

Estos son algunos ejemplos que muestran cómo `x:Static` puede hacer referencia explícitamente a los campos estáticos y a los miembros de enumeración:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Hasta ahora, esto no es muy impresionante. Sin embargo, la `x:Static` extensión de marcado también puede hacer referencia a propiedades o campos estáticos de su propio código. Por ejemplo, esta es una `AppConstants` clase que contiene algunos campos estáticos que podría querer usar en varias páginas en una aplicación:

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

Para hacer referencia a los campos estáticos de esta clase en el archivo XAML, necesitará alguna manera de indicar dentro del archivo XAML donde se encuentra este archivo. Para ello, debe tener una declaración de espacio de nombres XML.

Recuerde que los archivos XAML creados como parte de la Xamarin.Forms plantilla XAML estándar contienen dos declaraciones de espacio de nombres XML: una para tener acceso a Xamarin.Forms las clases y otra para hacer referencia a etiquetas y atributos intrínsecos de XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Necesitará declaraciones de espacio de nombres XML adicionales para tener acceso a otras clases. Cada declaración de espacio de nombres XML adicional define un nuevo prefijo. Para tener acceso a las clases locales de la biblioteca .NET Standard de aplicaciones compartidas, como `AppConstants` , los programadores de XAML suelen usar el prefijo `local` . La declaración del espacio de nombres debe indicar el nombre del espacio de nombres CLR (Common Language Runtime), también conocido como nombre del espacio de nombres .NET, que es el nombre que aparece en una definición de C# `namespace` o en una `using` Directiva:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

También puede definir declaraciones de espacios de nombres XML para espacios de nombres de .NET en cualquier ensamblado al que haga referencia la biblioteca .NET Standard. Por ejemplo, este es un `sys` prefijo para el espacio de nombres estándar de .net `System` , que se encuentra en el ensamblado **netstandard** . Dado que se trata de otro ensamblado, también debe especificar el nombre del ensamblado, en este caso **netstandard**:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Observe que la palabra clave `clr-namespace` va seguida de dos puntos y el nombre del espacio de nombres de .net, seguido de un punto y coma, la palabra clave `assembly` , un signo igual y el nombre del ensamblado.

Sí, a continuación se indican los dos puntos, `clr-namespace` pero el signo igual `assembly` . La sintaxis se definió de esta manera deliberadamente: la mayoría de las declaraciones de espacios de nombres XML hacen referencia a un URI que comienza con un nombre de esquema de URI como `http` , que siempre va seguido de un signo de dos puntos. La `clr-namespace` parte de esta cadena está pensada para imitar esa Convención.

Ambas declaraciones de espacios de nombres se incluyen en el ejemplo **StaticConstantsPage** . Observe que las `BoxView` dimensiones se establecen en `Math.PI` y `Math.E` , pero se escalan por un factor de 100:

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

El tamaño del resultado `BoxView` relativo a la pantalla depende de la plataforma:

[![Controles mediante la extensión de marcado x:Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Otras extensiones de marcado estándar

Varias extensiones de marcado son intrínsecas de XAML y se admiten en Xamarin.Forms los archivos XAML. Algunos de ellos no se usan con frecuencia, pero son esenciales cuando se necesitan:

- Si una propiedad tiene un valor que no es de `null` forma predeterminada pero desea establecerla en `null` , establézcala en la `{x:Null}` extensión de marcado.
- Si una propiedad es de tipo `Type` , puede asignarla a un `Type` objeto utilizando la extensión de marcado `{x:Type someClass}` .
- Puede definir matrices en XAML mediante la extensión de `x:Array` marcado. Esta extensión de marcado tiene un atributo necesario denominado `Type` que indica el tipo de los elementos de la matriz.
- La `Binding` extensión de marcado se describe en la [parte 4. Aspectos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).
- La `RelativeSource` extensión de marcado se describe en [enlaces relativos](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>La extensión de marcado ConstraintExpression

Las extensiones de marcado pueden tener propiedades, pero no se establecen como atributos XML. En una extensión de marcado, los valores de propiedad se separan mediante comas y no aparecen comillas dentro de las llaves.

Esto se puede ilustrar con la Xamarin.Forms extensión de marcado denominada `ConstraintExpression` , que se usa con la `RelativeLayout` clase. Puede especificar la ubicación o el tamaño de una vista secundaria como una constante, o en relación con un elemento primario u otra vista con nombre. La sintaxis de `ConstraintExpression` permite establecer la posición o el tamaño de una vista usando una vez `Factor` una propiedad de otra vista, más una `Constant` . Algo más complejo que requiere código.

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

Quizás la lección más importante que debe tomar de este ejemplo es la sintaxis de la extensión de marcado: no deben aparecer comillas dentro de las llaves de una extensión de marcado. Al escribir la extensión de marcado en un archivo XAML, es natural incluir los valores de las propiedades entre comillas. Resista la tentación.

Este es el programa que se ejecuta:

[![Diseño relativo mediante restricciones](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Resumen

Las extensiones de marcado XAML que se muestran aquí proporcionan una compatibilidad importante para los archivos XAML. Pero quizás la extensión de marcado XAML más valiosa es `Binding` , que se trata en la siguiente parte de esta serie, [parte 4. Aspectos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis XAML básica](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Del enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
