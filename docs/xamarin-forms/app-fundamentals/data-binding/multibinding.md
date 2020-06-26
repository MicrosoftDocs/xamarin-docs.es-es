---
title: Enlaces múltiples de Xamarin.Forms
description: En este artículo se explica cómo adjuntar una colección de objetos de enlace a una única propiedad de destino de enlace mediante la clase MultiBinding.
ms.prod: xamarin
ms.assetid: E73AE622-664C-4A90-B5B2-BD47D0E7A1A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2020
ms.openlocfilehash: dfe6da8a76b447bf0c2a6c0a3bea9823e498d5e4
ms.sourcegitcommit: 8a18471b3d96f3f726b66f9bc50a829f1c122f29
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988191"
---
# <a name="xamarinforms-multi-bindings"></a>Enlaces múltiples de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Los enlaces múltiples proporcionan la capacidad de asociar una colección de objetos [`Binding`](xref:Xamarin.Forms.Binding) a una única propiedad de destino de enlace. Se crean con la clase `MultiBinding`, que evalúa todos sus objetos `Binding` y devuelve un valor único a través de una instancia de `IMultiValueConverter` proporcionada por la aplicación. Además, `MultiBinding` vuelve a evaluar todos sus objetos `Binding` cuando cambia alguno de los datos enlazados.

La clase `MultiBinding` define las propiedades siguientes:

- `Bindings`, de tipo `IList<BindingBase>`, que representa la colección de objetos [`Binding`](xref:Xamarin.Forms.Binding) dentro de la instancia de `MultiBinding`.
- `Converter`, de tipo `IMultiValueConverter`, que representa el convertidor que se utilizará para convertir los valores al valor de destino (o desde este).
- `ConverterParameter`, de tipo `object`, que representa un parámetro opcional que pasar a `Converter`.

La propiedad `Bindings` es la propiedad de contenido de la clase `MultiBinding` y, por tanto, no es necesario establecerla explícitamente desde XAML.

Además, la clase `MultiBinding` hereda las siguientes propiedades de la clase `BindingBase`:

- `FallbackValue`, de tipo `object`, que representa el valor que se va a usar cuando el enlace múltiple no puede devolver un valor.
- `Mode`, de tipo [`BindingMode`](xref:Xamarin.Forms.BindingMode), que indica la dirección del flujo de datos del enlace múltiple.
- `StringFormat`, de tipo `string`, que especifica cómo dar formato al resultado del enlace múltiple si se muestra como una cadena.
- `TargetNullValue`, de tipo `object`, que representa el valor que se usa en el destino cuando el valor del origen es `null`.

Un elemento `MultiBinding` debe usar `IMultiValueConverter` para generar un valor para el destino de enlace, en función del valor de los enlaces de la colección de `Bindings`. Por ejemplo, [`Color`](xref:Xamarin.Forms.Color) podría calcularse a partir de los valores de rojo, azul y verde, que pueden ser valores de los mismos objetos de origen del enlace (o diferentes). Cuando un valor se mueve del destino a los orígenes, el valor de la propiedad de destino se convierte en un conjunto de valores que se devuelven en los enlaces.

> [!IMPORTANT]
> Los enlaces individuales de la colección `Bindings` pueden tener sus propios convertidores de valores.

El valor de la propiedad `Mode` determina la funcionalidad de `MultiBinding` y se utiliza como modo de enlace para todos los enlaces de la colección, a menos que un enlace individual invalide la propiedad. Por ejemplo, si la propiedad `Mode` de un objeto `MultiBinding` está establecida en [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), se considerarán todos los enlaces de la colección `TwoWay`, a menos que establezca explícitamente un valor `Mode` diferente en uno de los enlaces.

## <a name="define-a-imultivalueconverter"></a>Definición de IMultiValueConverter

La interfaz `IMultiValueConverter` permite aplicar la lógica personalizada a un elemento `MultiBinding`. Para asociar un convertidor a `MultiBinding`, cree una clase que implemente la interfaz `IValueConverter` y, a continuación, implemente los métodos `Convert` y `ConvertBack`:

```csharp
public class AllTrueMultiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
    {
        if (values == null || !targetType.IsAssignableFrom(typeof(bool)))
        {
            return false;
            // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
        }

        foreach (var value in values)
        {
            if (!(value is bool b))
            {
                return false;
                // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
            }
            else if (!b)
            {
                return false;
            }
        }
        return true;
    }

    public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
    {
        if (!(value is bool b) || targetTypes.Any(t => !t.IsAssignableFrom(typeof(bool))))
        {
            // Return null to indicate conversion back is not possible
            return null;
        }

        if (b)
        {
            return targetTypes.Select(t => (object)true).ToArray();
        }
        else
        {
            // Can't convert back from false because of ambiguity
            return null;
        }
    }
}
```

El método `Convert` convierte valores de origen a un valor para el destino de enlace. Xamarin.Forms llama a este método cuando propaga los valores de los enlaces de origen al destino de enlace. Este método acepta cuatro argumentos:

- `object[]` de tipo `MultiBinding`, es una matriz de valores que generan los enlaces de origen en `values`.
- `targetType`, de tipo `Type`, es el tipo de la propiedad del destino de enlace.
- `parameter`, de tipo `object`, es el parámetro de convertidor que se va a usar.
- `culture`, de tipo `CultureInfo`, es la referencia cultural que se va a usar en el convertidor.

El método `Convert` devuelve un elemento `object` que representa un valor convertido. Este método debe devolver:

- `BindableProperty.UnsetValue` para indicar que el convertidor no generó un valor y que el enlace usará `FallbackValue`.
- `Binding.DoNothing` para indicar a Xamarin.Forms que no realice ninguna acción. Por ejemplo, para indicar a Xamarin.Forms que no transfiera un valor al destino de enlace, o no use `FallbackValue`.
- `null` para indicar que el convertidor no puede realizar la conversión y que el enlace usará `TargetNullValue`.

> [!IMPORTANT]
> Un objeto `MultiBinding` que recibe `BindableProperty.UnsetValue` de un método `Convert` debe definir su propiedad [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue). De forma similar, un objeto `MultiBinding` que recibe `null` de un método `Convert` debe definir su propiedad [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue).

El método `ConvertBack` convierte un valor de destino de enlace en valores de enlace de origen. Este método acepta cuatro argumentos:

- `value`, de tipo `object`, es el valor que genera el destino de enlace.
- `targetTypes`, de tipo `Type[]`, es la matriz de tipos a los que se convertirá. La longitud de la matriz indica el número y los tipos de valores que se sugiere que el método devuelva.
- `parameter`, de tipo `object`, es el parámetro de convertidor que se va a usar.
- `culture`, de tipo `CultureInfo`, es la referencia cultural que se va a usar en el convertidor.

El método `ConvertBack` devuelve una matriz de valores, de tipo `object[]`, que se han convertido del valor de destino de nuevo a los valores de origen. Este método debe devolver:

- `BindableProperty.UnsetValue` en la posición `i` para indicar que el convertidor no puede proporcionar un valor para el enlace de origen en el índice `i`, y que no se establecerá ningún valor en él.
- `Binding.DoNothing` en la posición `i` para indicar que no se va a establecer ningún valor en el enlace de origen en el índice `i`.
- `null` para indicar que el convertidor no puede realizar la conversión o que no admite la conversión en esta dirección.

## <a name="consume-a-imultivalueconverter"></a>Consumo de un elemento IMultiValueConverter

Un elemento `IMultiValueConverter` se consume creando una instancia de él en un diccionario de recursos y, a continuación, haciendo referencia a él mediante la extensión de marcado `StaticResource` para establecer la propiedad `MultiBinding.Converter`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MultiBindingConverterPage"
             Title="MultiBinding Converter demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AllTrueConverter}">
                <Binding Path="Employee.IsOver16" />
                <Binding Path="Employee.HasPassedTest" />
                <Binding Path="Employee.IsSuspended"
                         Converter="{StaticResource InverterConverter}" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>    
```

En este ejemplo, el objeto `MultiBinding` usa la instancia de `AllTrueMultiConverter` para establecer la propiedad [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) en `true`, siempre que los tres objetos [`Binding`](xref:Xamarin.Forms.Binding) se evalúen como `true`. De lo contrario, la propiedad `CheckBox.IsChecked` se establece en `false`.

De forma predeterminada, la propiedad [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) utiliza un enlace [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay). Por lo tanto, el método `ConvertBack` de la instancia de `AllTrueMultiConverter` se ejecuta cuando el usuario no ha desactivado [`CheckBox`](xref:Xamarin.Forms.CheckBox), que establece los valores de enlace de origen en el valor de la propiedad `CheckBox.IsChecked`.

## <a name="format-strings"></a>Cadenas de formato

Un elemento `MultiBinding` puede dar formato a cualquier resultado de enlace múltiple que se muestra como una cadena, con la propiedad `StringFormat`. Esta propiedad se puede establecer en una cadena de formato estándar de .NET, con marcadores de posición, que especifica cómo dar formato al resultado de enlace múltiple:

```xaml
<Label>
    <Label.Text>
        <MultiBinding StringFormat="{}{0} {1} {2}">
            <Binding Path="Employee1.Forename" />
            <Binding Path="Employee1.MiddleName" />
            <Binding Path="Employee1.Surname" />
        </MultiBinding>
    </Label.Text>
</Label>
```

En este ejemplo, la propiedad `StringFormat` combina los tres valores enlazados en una sola cadena que se muestra por [`Label`](xref:Xamarin.Forms.Label).

> [!IMPORTANT]
> El número de parámetros en un formato de cadena compuesto no puede superar el número de objetos `Binding` secundarios de `MultiBinding`.

Al establecer las propiedades `Converter` y `StringFormat`, el convertidor se aplica primero al valor de los datos y, a continuación, se aplica `StringFormat`.

Para obtener más información sobre el formato de cadena en Xamarin.Forms, vea [Formato de cadena de Xamarin.Forms](string-formatting.md).

## <a name="provide-fallback-values"></a>Provisión de valores de reserva

Los enlaces de datos se pueden fortalecer mediante la definición de valores de reserva para usarlos si se produce un error en el proceso de enlace. Esto puede realizarse opcionalmente mediante la definición de las propiedades [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) y [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) en un objeto `MultiBinding`.

Un objeto `MultiBinding` usará su valor [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) cuando el método `Convert` de una instancia de `IMultiValueConverter` devuelva `BindableProperty.UnsetValue`, lo que indica que el convertidor no generó un valor. Un objeto `MultiBinding` usará su valor [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) cuando el método `Convert` de una instancia de `IMultiValueConverter` devuelva `null`, lo que indica que el convertidor no puede realizar la conversión.

Para obtener más información sobre las reservas de enlace, consulte [Reservas de enlace de Xamarin.Forms](binding-fallbacks.md).

## <a name="nest-multibinding-objects"></a>Anidación de objetos MultiBinding

Los objetos `MultiBinding` se pueden anidar para que se evalúen varios objetos `MultiBinding` a fin de devolver un valor a través de una instancia de `IMultiValueConverter`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.NestedMultiBindingPage"
             Title="Nested MultiBinding demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:AnyTrueMultiConverter x:Key="AnyTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AnyTrueConverter}">
                <MultiBinding Converter="{StaticResource AllTrueConverter}">
                    <Binding Path="Employee.IsOver16" />
                    <Binding Path="Employee.HasPassedTest" />
                    <Binding Path="Employee.IsSuspended" Converter="{StaticResource InverterConverter}" />                        
                </MultiBinding>
                <Binding Path="Employee.IsMonarch" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>
```

En este ejemplo, el objeto `MultiBinding` usa su instancia de `AnyTrueMultiConverter` para establecer la propiedad [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) en `true`, siempre que todos los objetos [`Binding`](xref:Xamarin.Forms.Binding) del objeto `MultiBinding` interno se evalúen como `true` o siempre que el objeto `Binding` del objeto `MultiBinding` externo se evalúe como `true`. De lo contrario, la propiedad `CheckBox.IsChecked` se establece en `false`.

## <a name="use-a-relativesource-binding-in-a-multibinding"></a>Uso de un enlace RelativeSource en un elemento MultiBinding

Los objetos `MultiBinding` admiten enlaces relativos, lo cual proporciona la capacidad de establecer el origen de enlace en relación con la posición del destino de enlace:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:DataBindingDemos">
    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />

        <ControlTemplate x:Key="CardViewExpanderControlTemplate">
            <Expander BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                      IsExpanded="{Binding IsExpanded, Source={RelativeSource TemplatedParent}}"
                      BackgroundColor="{Binding CardColor}">
                <Expander.IsVisible>
                    <MultiBinding Converter="{StaticResource AllTrueConverter}">
                        <Binding Path="IsExpanded" />
                        <Binding Path="IsEnabled" />
                    </MultiBinding>
                </Expander.IsVisible>
                <Expander.Header>
                    <Grid>
                        <!-- XAML that defines Expander header goes here -->
                    </Grid>
                </Expander.Header>
                <Grid>
                    <!-- XAML that defines Expander content goes here -->
                </Grid>
            </Expander>
        </ControlTemplate>
    </ContentPage.Resources>

    <StackLayout>
        <controls:CardViewExpander BorderColor="DarkGray"
                                   CardTitle="John Doe"
                                   CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                                   IconBackgroundColor="SlateGray"
                                   IconImageSource="user.png"
                                   ControlTemplate="{StaticResource CardViewExpanderControlTemplate}"
                                   IsEnabled="True"
                                   IsExpanded="True" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, el modo de enlace relativo `TemplatedParent` se utiliza para enlazar desde una plantilla de control a la instancia de objeto de tiempo de ejecución a la que se aplica la plantilla. El elemento `Expander`, que es el elemento raíz de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), tiene su `BindingContext` establecido en la instancia de objeto de tiempo de ejecución a la que se aplica la plantilla. Por lo tanto, `Expander` y sus elementos secundarios resuelven sus expresiones de enlace, y los objetos [`Binding`](xref:Xamarin.Forms.Binding), con las propiedades del objeto `CardViewExpander`. El elemento `MultiBinding` usa la instancia de `AllTrueMultiConverter` para establecer la propiedad `Expander.IsVisible` en `true`, siempre que los dos objetos [`Binding`](xref:Xamarin.Forms.Binding) se evalúen como `true`. De lo contrario, la propiedad `Expander.IsVisible` se establece en `false`.

Para más información sobre los enlaces relativos, consulte [Enlaces relativos de Xamarin.Forms](relative-bindings.md). Para obtener más información sobre las plantillas de control, consulte [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Formato de cadena de Xamarin.Forms](string-formatting.md)
- [Reservas de enlace de Xamarin.Forms](binding-fallbacks.md)
- [Enlaces relativos de Xamarin.Forms](relative-bindings.md)
- [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
