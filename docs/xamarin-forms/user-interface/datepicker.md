---
title: Xamarin.Forms DatePicker
description: DatePicker es una Xamarin.Forms vista que permite al usuario seleccionar una fecha. En este artículo se explica cómo consumir un DatePicker en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fed80f0954613a6bff49d0611d56549405c78333
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564100"
---
# <a name="no-locxamarinforms-datepicker"></a>Xamarin.Forms DatePicker

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Una Xamarin.Forms vista que permite al usuario seleccionar una fecha._

Xamarin.Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) Invoca el control selector de fecha y de la plataforma y permite al usuario seleccionar una fecha. `DatePicker` define ocho propiedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de tipo [`DateTime`](xref:System.DateTime) , que tiene como valor predeterminado el primer día del año 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de tipo `DateTime` , cuyo valor predeterminado es el último día del año 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de tipo `DateTime` , la fecha seleccionada, que tiene como valor predeterminado el valor [`DateTime.Today`](xref:System.DateTime.Today) .
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de tipo `string` , una cadena de formato .net [estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , cuyo valor predeterminado es "D", el patrón de fecha larga.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) de tipo [`Color`](xref:Xamarin.Forms.Color) , el color que se usa para mostrar la fecha seleccionada, cuyo valor predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , cuyo valor predeterminado es [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) de tipo `string` , cuyo valor predeterminado es `null` .
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) de tipo `double` , que tiene como valor predeterminado-1,0.
- `CharacterSpacing`, del tipo `double`, es el espaciado entre los caracteres del texto de `DatePicker`.

`DatePicker`Desencadena un [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento cuando el usuario selecciona una fecha.

> [!WARNING]
> Al establecer `MinimumDate` y `MaximumDate` , asegúrese de que `MinimumDate` siempre es menor o igual que `MaximumDate` . De lo contrario, generará `DatePicker` una excepción.

Internamente, `DatePicker` garantiza que `Date` está entre `MinimumDate` y `MaximumDate` , ambos incluidos. Si `MinimumDate` `MaximumDate` se establece o para que `Date` no esté entre ellos, `DatePicker` ajustará el valor de `Date` .

Las ocho propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que se pueden aplicar estilos y las propiedades pueden ser destinos de los enlaces de datos. La `Date` propiedad tiene un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , lo que significa que puede ser un destino de un enlace de datos en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Inicializar las propiedades DateTime

En el código, puede inicializar `MinimumDate` las `MaximumDate` propiedades, y `Date` para los valores de tipo `DateTime` :

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Cuando `DateTime` se especifica un valor en XAML, el analizador de XAML usa el `DateTime.Parse` método con un `CultureInfo.InvariantCulture` argumento para convertir la cadena en un `DateTime` valor. Las fechas deben especificarse en un formato preciso: los meses de dos dígitos, los días de dos dígitos y los años de cuatro dígitos separados por barras diagonales:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Si la `BindingContext` propiedad de `DatePicker` se establece en una instancia de un ViewModel que contiene propiedades de tipo `DateTime` denominados `MinDate` , `MaxDate` y `SelectedDate` (por ejemplo,), puede crear una instancia de de la `DatePicker` manera siguiente:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

En este ejemplo, las tres propiedades se inicializan en las propiedades correspondientes del ViewModel. Dado `Date` que la propiedad tiene un modo de enlace de `TwoWay` , cualquier fecha nueva que seleccione el usuario se reflejará automáticamente en el ViewModel.

Si no `DatePicker` contiene un enlace en su `Date` propiedad, una aplicación debe adjuntar un controlador al `DateSelected` evento que se va a notificar cuando el usuario seleccione una nueva fecha.

Para obtener información sobre cómo establecer las propiedades de la fuente, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker y diseño

Es posible utilizar una opción de diseño horizontal sin restricciones como `Center` , `Start` o `End` con `DatePicker` :

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Sin embargo, esto no es recomendable. Dependiendo del valor de la `Format` propiedad, las fechas seleccionadas pueden requerir anchos de pantalla diferentes. Por ejemplo, la cadena de formato "D" hace que `DateTime` se muestren las fechas en un formato largo y "miércoles, 12 de septiembre de 2018" requiere un ancho de pantalla mayor que "viernes, 4 de mayo de 2018". Dependiendo de la plataforma, esta diferencia podría hacer que la `DateTime` vista cambie el ancho en el diseño o que la pantalla se trunque.

> [!TIP]
> Es preferible usar la configuración predeterminada `HorizontalOptions` de `Fill` con `DatePicker` y no utilizar un ancho `Auto` cuando se coloca `DatePicker` en una `Grid` celda.

## <a name="datepicker-in-an-application"></a>DatePicker en una aplicación

El ejemplo [**DaysBetweenDates**](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) incluye dos `DatePicker` vistas en su página. Se pueden usar para seleccionar dos fechas y el programa calcula el número de días entre esas fechas. El programa no cambia la configuración de las `MinimumDate` `MaximumDate` propiedades y, por lo que las dos fechas deben estar entre 1900 y 2100.

Este es el archivo XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

A cada una `DatePicker` se le asigna una `Format` propiedad de "D" para un formato de fecha larga. Observe también que el `endDatePicker` objeto tiene un enlace que tiene como destino su `MinimumDate` propiedad. El origen de enlace es la `Date` propiedad seleccionada del `startDatePicker` objeto. Esto garantiza que la fecha de finalización sea siempre posterior o igual a la fecha de inicio. Además de los dos `DatePicker` objetos, `Switch` se etiqueta "incluir ambos días en total".

Las dos `DatePicker` vistas tienen controladores adjuntados al `DateSelected` evento y `Switch` tienen un controlador asociado a su `Toggled` evento. Estos controladores de eventos se encuentran en el archivo de código subyacente y desencadenan un nuevo cálculo de los días entre las dos fechas:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

Cuando se ejecuta por primera vez el ejemplo, ambas `DatePicker` vistas se inicializan en la fecha de hoy. En la captura de pantalla siguiente se muestra el programa que se ejecuta en iOS y Android:

[![Días entre las fechas de inicio](datepicker-images/DaysBetweenDatesStart.png "Días entre las fechas de inicio")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Días entre las fechas de inicio")

Al pulsar cualquiera de las `DatePicker` pantallas se invoca el selector de fecha de la plataforma. Las plataformas implementan este selector de fecha de maneras muy diferentes, pero cada enfoque lo conocen los usuarios de esa plataforma:

[![Días entre las fechas seleccione](datepicker-images/DaysBetweenDatesSelect.png "Días entre las fechas seleccione")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Días entre las fechas seleccione")

> [!TIP]
> En Android, el `DatePicker` cuadro de diálogo se puede personalizar invalidando el `CreateDatePickerDialog` método en un representador personalizado. Esto permite, por ejemplo, agregar botones adicionales al cuadro de diálogo.

Después de seleccionar dos fechas, la aplicación muestra el número de días entre esas fechas:

[![Resultados de días entre fechas](datepicker-images/DaysBetweenDatesResult.png "Resultados de días entre fechas")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Resultados de días entre fechas")

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DaysBetweenDates](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API de DatePicker](xref:Xamarin.Forms.DatePicker)