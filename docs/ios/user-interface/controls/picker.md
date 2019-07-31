---
title: Control selector en Xamarin. iOS
description: En este documento se describe cómo diseñar y trabajar con controles de selector en una aplicación de Xamarin. iOS. Describe cómo implementar un selector en el código y en el diseñador de iOS.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 4f4855c3928f05f2593d3d80fb7490a115b36e6a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655809"
---
# <a name="picker-control-in-xamarinios"></a>Control selector en Xamarin. iOS

[`UIPickerView`](xref:UIKit.UIPickerView) Permite elegir un valor de una lista desplazando los componentes individuales de una interfaz de tipo rueda.

Los selectores se usan con frecuencia para seleccionar una fecha y hora; Apple proporciona el[`UIDatePicker`](xref:UIKit.UIDatePicker)
clase para este fin.

En el artículo se describe cómo implementar y usar `UIPickerView` los `UIDatePicker` controles y.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implementar un selector

Implemente un selector mediante la creación de una `UIPickerView`instancia de un nuevo:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>Selectores y guiones gráficos

Para crear un selector en el **Diseñador de iOS**, arrastre una **vista selector** desde el **cuadro de herramientas** a la superficie de diseño.

![Arrastre una vista selector a la superficie de diseño](picker-images/image1.png "Arrastre una vista selector a la superficie de diseño")

### <a name="working-with-a-picker-control"></a>Trabajar con un control selector

Un selector utiliza un _modelo_ para interactuar con los datos:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

La [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) clase base implementa dos interfaces,[`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
y [`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate), que declaran varios métodos que especifican los datos de un selector y cómo controla la interacción:

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

Un selector puede tener varias columnas o _componentes_. Los componentes particionan un selector en varias secciones, lo que permite una selección de datos más sencilla y más específica:

![Selector con dos componentes](picker-images/image3.png "Selector con dos componentes")

Para especificar el número de componentes en un selector, use el[`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
.

### <a name="customizing-a-pickers-appearance"></a>Personalizar la apariencia de un selector

Para personalizar la apariencia de un selector, use el[`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
clase o invalide [`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) los [`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView)) métodos y `UIPickerViewModel`en.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implementar un selector de fecha

Implemente un selector de fecha mediante la creación `UIDatePicker`de una instancia de:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>Selectores de fecha y guiones gráficos

Para crear un selector de fecha en el **Diseñador de iOS**, arrastre un **selector de fecha** desde el cuadro de **herramientas** a la superficie de diseño.

![Arrastrar un selector de fecha a la superficie de diseño](picker-images/image2.png "Arrastrar un selector de fecha a la superficie de diseño")

### <a name="date-picker-properties"></a>Propiedades del selector de fecha

#### <a name="minimum-and-maximum-date"></a>Fecha mínima y máxima

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate)y [`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate) limitan el intervalo de fechas disponibles en el selector de fecha. Por ejemplo, el siguiente código restringe un selector de fecha a los años 60 que llevan hasta el momento actual:

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> Es posible convertir explícitamente un `DateTime` en un: `NSDate`
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervalo de minutos

La [`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval) propiedad establece el intervalo en el que el selector mostrará los minutos:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Mode

Los selectores de fecha admiten cuatro [modos](xref:UIKit.UIDatePickerMode), que se describen a continuación:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time`muestra la hora con un selector de hora y minuto y una designación AM o PM opcional:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date`muestra la fecha con un selector de mes, día y año:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

El orden de los selectores depende de la configuración regional del selector de fecha, que, de forma predeterminada, usa la configuración regional del sistema. La imagen anterior muestra el diseño de los selectores de `en_US` la configuración regional, pero lo siguiente cambia el orden al día | Mes | Anual

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Día | Mes | ](picker-images/image9.png "Día del año | Mes | Año")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime`muestra una vista abreviada de la fecha, la hora en horas y minutos, y una designación AM o PM opcional (dependiendo de si se usa un reloj de 12 o 24 horas):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Como con [`UIDatePickerMode.Date`](#uidatepickermodedate), el orden de los selectores y el uso de un reloj de 12 o 24 horas depende de la configuración regional del selector de fecha.

> [!TIP]
> Utilice la `Date` propiedad para capturar el valor de un selector de fecha en el `UIDatePickerMode.Time`modo `UIDatePickerMode.Date`, o `UIDatePickerMode.DateAndTime`. Este valor se almacena como un `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer`muestra los valores de hora y minuto:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode. CountDownTimer"](picker-images/image5.png "UIDatePickerMode. CountDownTimer")

La `CountDownDuration` propiedad captura el valor de un selector de fecha en `UIDatePickerMode.CountDownTimer` el modo. Por ejemplo, para agregar el valor de cuenta atrás a la fecha actual:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Para dar formato `NSDate`a, [`NSDateFormatter`](xref:Foundation.NSDateFormatter)use.

Para usar `NSDateFormatter`, llame a su [`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate)) método. Por ejemplo:

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

La [`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat) propiedad (una cadena) `NSDateFormatter` de permite una especificación de formato de fecha personalizable:

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

La [`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle) propiedad [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) (de `NSDateFormatter` especifica el formato de hora basado en los estilos predeterminados:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Los `NSDateFormatterStyle` distintos valores muestran las horas siguientes:

- `NSDateFormatterStyle.Full`: 7:46:00 PM hora de verano del este
- `NSDateFormatterStyle.Long`: 7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`: 7:47:00 PM
- `NSDateFormatterSytle.Short`: 7:47 PM

##### <a name="datestyle"></a>DateStyle

La [`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle) propiedad `NSDateFormatterStyle`(a) de un `NSDateFormatter` especifica el formato de fecha basado en estilos predeterminados:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Varios `NSDateFormatterStyle` valores muestran fechas como se indica a continuación:

- `NSDateFormatterStyle.Full`: Miércoles, 2 de agosto de 2017 a las 7:48 p. m.
- `NSDateFormatterStyle.Long`: 2 de agosto de 2017 a las 7:49 p. m.
- `NSDateFormatterStyle.Medium`: 2 de agosto de 2017, 7:49 PM
- `NSDateFormatterStyle.Short`: 8/2/17, 7:50 PM

> [!NOTE]
> `DateFormat`y `DateStyle` proporcionandiferentesmaneras`TimeStyle` de especificar el formato de fecha y hora. / Las propiedades set más recientes determinan la salida del formateador de fecha.

## <a name="related-links"></a>Vínculos relacionados

- [PickerControl (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/pickercontrol)
