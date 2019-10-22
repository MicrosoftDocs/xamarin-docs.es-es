---
title: DatePicker de Xamarin. Forms
description: DatePicker es una vista de Xamarin. Forms que permite al usuario seleccionar una fecha. En este artículo se explica cómo consumir un DatePicker en una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 15d4159d89a463c0335d9c91b24b55151c91de8c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695913"
---
# <a name="xamarinforms-datepicker"></a>DatePicker de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Una vista de Xamarin. Forms que permite al usuario seleccionar una fecha._

El [`DatePicker`](xref:Xamarin.Forms.DatePicker) de Xamarin. Forms invoca el control selector de fecha y de la plataforma y permite al usuario seleccionar una fecha. `DatePicker` define ocho propiedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de tipo [`DateTime`](xref:System.DateTime), cuyo valor predeterminado es el primer día del año 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de tipo `DateTime`, cuyo valor predeterminado es el último día del año 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de tipo `DateTime`, la fecha seleccionada, que tiene como valor predeterminado el valor [`DateTime.Today`](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de tipo `string`, una cadena de formato .net [estándar](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizada](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , cuyo valor predeterminado es "D", el patrón de fecha larga.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color usado para mostrar la fecha seleccionada, cuyo valor predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), cuyo valor predeterminado es [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) de tipo `string`, cuyo valor predeterminado es `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) de tipo `double`, cuyo valor predeterminado es-1,0.
- `CharacterSpacing`, de tipo `double`, es el espaciado entre los caracteres del texto `DatePicker`.

El `DatePicker` activa un evento de [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) cuando el usuario selecciona una fecha.

> [!WARNING]
> Al establecer `MinimumDate` y `MaximumDate`, asegúrese de que `MinimumDate` sea siempre menor o igual que `MaximumDate`. De lo contrario, `DatePicker` producirá una excepción.

Internamente, la `DatePicker` garantiza que `Date` se encuentra entre `MinimumDate` y `MaximumDate`, ambos incluidos. Si se establece `MinimumDate` o `MaximumDate` para que `Date` no esté entre ellos, `DatePicker` ajustará el valor de `Date`.

Las ocho propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que se pueden aplicar estilos y las propiedades pueden ser destinos de los enlaces de datos. La propiedad `Date` tiene un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), lo que significa que puede ser un destino de un enlace de datos en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Inicializar las propiedades DateTime

En el código, puede inicializar las propiedades `MinimumDate`, `MaximumDate` y `Date` con valores de tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Cuando se especifica un valor de `DateTime` en XAML, el analizador de XAML usa el método de `DateTime.Parse` con un argumento de `CultureInfo.InvariantCulture` para convertir la cadena en un valor de `DateTime`. Las fechas deben especificarse en un formato preciso: los meses de dos dígitos, los días de dos dígitos y los años de cuatro dígitos separados por barras diagonales:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Si la propiedad `BindingContext` de `DatePicker` está establecida en una instancia de un ViewModel que contiene propiedades de tipo `DateTime` denominadas `MinDate`, `MaxDate` y `SelectedDate` (por ejemplo,), puede crear una instancia del `DatePicker` como este. :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

En este ejemplo, las tres propiedades se inicializan en las propiedades correspondientes del ViewModel. Dado que la propiedad `Date` tiene un modo de enlace de `TwoWay`, cualquier fecha nueva que seleccione el usuario se reflejará automáticamente en el ViewModel.

Si el `DatePicker` no contiene un enlace en su propiedad `Date`, una aplicación debe adjuntar un controlador al evento `DateSelected` que se va a notificar cuando el usuario seleccione una nueva fecha.

Para obtener información sobre cómo establecer las propiedades de la fuente, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker y diseño

Es posible usar una opción de diseño horizontal sin restricciones, como `Center`, `Start` o `End` con `DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Sin embargo, esto no es recomendable. En función de la configuración de la propiedad `Format`, las fechas seleccionadas pueden requerir anchos de pantalla diferentes. Por ejemplo, la cadena de formato "D" hace que `DateTime` muestren las fechas en un formato largo y "miércoles, 12 de septiembre de 2018" requiere un ancho de pantalla mayor que "viernes, 4 de mayo de 2018". Dependiendo de la plataforma, esta diferencia podría provocar que la vista de `DateTime` cambie el ancho en el diseño o que la pantalla se trunque.

> [!TIP]
> Es mejor usar el valor predeterminado `HorizontalOptions` de `Fill` con `DatePicker` y no utilizar un ancho de `Auto` al colocar `DatePicker` en una celda de `Grid`.

## <a name="datepicker-in-an-application"></a>DatePicker en una aplicación

El ejemplo [**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) incluye dos vistas `DatePicker` en su página. Se pueden usar para seleccionar dos fechas y el programa calcula el número de días entre esas fechas. El programa no cambia la configuración de las propiedades `MinimumDate` y `MaximumDate`, por lo que las dos fechas deben estar entre 1900 y 2100.

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

A cada `DatePicker` se le asigna una propiedad `Format` de "D" para un formato de fecha larga. Observe también que el objeto `endDatePicker` tiene un enlace que tiene como destino su propiedad `MinimumDate`. El origen de enlace es la propiedad `Date` seleccionada del objeto `startDatePicker`. Esto garantiza que la fecha de finalización sea siempre posterior o igual a la fecha de inicio. Además de los dos objetos `DatePicker`, una `Switch` se etiqueta como "incluir ambos días en total".

Las dos vistas `DatePicker` tienen controladores adjuntados al evento `DateSelected` y el `Switch` tiene un controlador asociado a su evento `Toggled`. Estos controladores de eventos se encuentran en el archivo de código subyacente y desencadenan un nuevo cálculo de los días entre las dos fechas:

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

Cuando se ejecuta por primera vez el ejemplo, las dos vistas de `DatePicker` se inicializan en la fecha de hoy. En la captura de pantalla siguiente se muestra el programa que se ejecuta en iOS, Android y el Plataforma universal de Windows:

[![Días entre las fechas de inicio](datepicker-images/DaysBetweenDatesStart.png "Días entre las fechas de inicio")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Días entre las fechas de inicio")

Al pulsar cualquiera de las `DatePicker` se muestra se invoca el selector de fecha de la plataforma. Las plataformas implementan este selector de fecha de maneras muy diferentes, pero cada enfoque lo conocen los usuarios de esa plataforma:

[![Días entre las fechas seleccione](datepicker-images/DaysBetweenDatesSelect.png "Días entre las fechas seleccione")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Días entre las fechas seleccione")

> [!TIP]
> En Android, el cuadro de diálogo de `DatePicker` se puede personalizar invalidando el método de `CreateDatePickerDialog` en un representador personalizado. Esto permite, por ejemplo, agregar botones adicionales al cuadro de diálogo.

Después de seleccionar dos fechas, la aplicación muestra el número de días entre esas fechas:

[![Resultados de días entre fechas](datepicker-images/DaysBetweenDatesResult.png "Resultados de días entre fechas")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Resultados de días entre fechas")

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API de DatePicker](xref:Xamarin.Forms.DatePicker)
