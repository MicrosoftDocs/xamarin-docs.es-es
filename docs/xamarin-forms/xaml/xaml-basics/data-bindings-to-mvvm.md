---
title: Parte 5. Enlaces de datos a MVVM
description: 'El patrón MVVM exige una separación entre tres niveles de software: la interfaz de usuario de XAML, denominada vista. los datos subyacentes, denominados modelo; y un intermediario entre la vista y el modelo, denominado ViewModel.'
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 805bdef812b33d3f4329346a437e1202a16fe3ae
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937324"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. Enlaces de datos a MVVM

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_El patrón arquitectónico Model-View-ViewModel (MVVM) se inventó pensando en XAML. El patrón exige una separación entre tres niveles de software: la interfaz de usuario de XAML, denominada vista; los datos subyacentes, denominados modelo; y un intermediario entre la vista y el modelo, denominado ViewModel. A menudo, la vista y el ViewModel se conectan a través de los enlaces de datos definidos en el archivo XAML. El BindingContext de la vista suele ser una instancia de ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel sencillo

Como introducción a ViewModels, echemos un vistazo primero a un programa sin ninguno.
Anteriormente, vimos cómo definir una nueva declaración de espacio de nombres XML para permitir que un archivo XAML haga referencia a clases de otros ensamblados. Este es un programa que define una declaración de espacio de nombres XML para el `System` espacio de nombres:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

El programa puede usar `x:Static` para obtener la fecha y hora actuales de la `DateTime.Now` propiedad estática y establecer ese `DateTime` valor en `BindingContext` en un `StackLayout` :

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`es una propiedad especial: cuando se establece `BindingContext` en un elemento, lo heredan todos los elementos secundarios de ese elemento. Esto significa que todos los elementos secundarios de `StackLayout` tienen este mismo `BindingContext` y pueden contener enlaces simples a las propiedades de ese objeto.

En el programa de **fecha y hora de una captura** , dos de los elementos secundarios contienen enlaces a las propiedades de ese `DateTime` valor, pero otros dos elementos secundarios contienen enlaces a los que parece que falta una ruta de acceso de enlace. Esto significa que el `DateTime` propio valor se utiliza para `StringFormat` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

El problema es que la fecha y la hora se establecen una vez cuando la página se compila por primera vez y nunca cambian:

[![Vista que muestra la fecha y la hora](data-bindings-to-mvvm-images/oneshotdatetime.png)](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "Vista que muestra la fecha y la hora")

Un archivo XAML puede mostrar un reloj que siempre muestra la hora actual, pero necesita código para ayudarlo. Al pensar en términos de MVVM, el modelo y el ViewModel son clases escritas completamente en el código. La vista suele ser un archivo XAML que hace referencia a las propiedades definidas en el ViewModel a través de los enlaces de datos.

Un modelo adecuado no es lo mismo que el ViewModel y un ViewModel adecuado se ignora en la vista. Sin embargo, a menudo un programador se pone a la cola de los tipos de datos expuestos por el ViewModel en los tipos de datos asociados a determinadas interfaces de usuario. Por ejemplo, si un modelo obtiene acceso a una base de datos que contiene cadenas ASCII de caracteres de 8 bits, el ViewModel necesitará convertir esas cadenas en cadenas Unicode para dar cabida al uso exclusivo de Unicode en la interfaz de usuario.

En ejemplos sencillos de MVVM (como los que se muestran aquí), a menudo no hay ningún modelo, y el patrón implica solo una vista y ViewModel vinculados a los enlaces de datos.

Aquí se muestra un ViewModel para un reloj con solo una propiedad denominada `DateTime` , que actualiza esa `DateTime` propiedad cada segundo:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

ViewModels generalmente implementa la `INotifyPropertyChanged` interfaz, lo que significa que la clase activa un `PropertyChanged` evento cada vez que cambia una de sus propiedades. El mecanismo de enlace de datos de Xamarin.Forms asocia un controlador a este `PropertyChanged` evento para que se le pueda notificar cuando una propiedad cambie y mantener el destino actualizado con el nuevo valor.

Un reloj basado en este ViewModel puede ser tan sencillo como el siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

Observe cómo `ClockViewModel` se establece en el `BindingContext` de las `Label` etiquetas de elemento de propiedad using. Como alternativa, puede crear una instancia `ClockViewModel` de en una `Resources` colección y establecerla en a `BindingContext` través de una `StaticResource` extensión de marcado. O bien, el archivo de código subyacente puede crear una instancia del ViewModel.

La `Binding` extensión de marcado de la `Text` propiedad de `Label` da formato a la `DateTime` propiedad. Esta es la pantalla:

[![Vista que muestra la fecha y la hora a través de ViewModel](data-bindings-to-mvvm-images/clock.png)](data-bindings-to-mvvm-images/clock-large.png#lightbox "Vista que muestra la fecha y la hora a través de ViewModel")

También es posible obtener acceso a las propiedades individuales de la `DateTime` propiedad del ViewModel separando las propiedades con puntos:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interactivo

MVVM se usa a menudo con enlaces de datos bidireccionales para una vista interactiva basada en un modelo de datos subyacente.

Esta es una clase denominada `HslViewModel` que convierte un `Color` valor en `Hue` `Saturation` los valores, y `Luminosity` , y viceversa:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Los cambios en `Hue` las `Saturation` propiedades, y `Luminosity` hacen que la `Color` propiedad cambie y los cambios en `Color` hacen que las otras tres propiedades cambien. Esto podría parecer un bucle infinito, salvo que la clase no invoca el `PropertyChanged` evento a menos que la propiedad haya cambiado. Esto coloca un extremo en el bucle de comentarios no controlable en caso contrario.

El siguiente archivo XAML contiene una `BoxView` cuya `Color` propiedad está enlazada a la `Color` propiedad del ViewModel y tres `Slider` y tres `Label` vistas enlazadas a las `Hue` propiedades, `Saturation` y `Luminosity` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

El enlace en cada `Label` es el valor predeterminado `OneWay` . Solo necesita mostrar el valor. Pero el enlace en cada `Slider` es `TwoWay` . Esto permite `Slider` que se inicialice desde el ViewModel. Observe que la `Color` propiedad se establece en `Aqua` cuando se crea una instancia del ViewModel. Pero un cambio en `Slider` también necesita establecer un nuevo valor para la propiedad en el ViewModel, que a continuación calcula un nuevo color.

[![MVVM con enlaces de datos bidireccionales](data-bindings-to-mvvm-images/hslcolorscroll.png)](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM con enlaces de datos bidireccionales")

## <a name="commanding-with-viewmodels"></a>Comandos con ViewModels

En muchos casos, el patrón MVVM está restringido a la manipulación de los elementos de datos: objetos de la interfaz de usuario en la vista objetos de datos en paralelo en el ViewModel.

Sin embargo, a veces la vista debe contener botones que desencadenen varias acciones en el ViewModel. Sin embargo, el ViewModel no debe contener `Clicked` Controladores para los botones porque esto enlazaría el modelo de administración a un paradigma determinado de la interfaz de usuario.

Para permitir que ViewModels sea más independiente de los objetos de interfaz de usuario concretos pero que todavía permiten llamar a los métodos dentro de ViewModel, existe una interfaz de *comandos* . Esta interfaz de comandos es compatible con los siguientes elementos de Xamarin.Forms :

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell`(y, por tanto, también `ImageCell` )
- `ListView`
- `TapGestureRecognizer`

A excepción del `SearchBar` `ListView` elemento y, estos elementos definen dos propiedades:

- `Command`de tipo`System.Windows.Input.ICommand`
- `CommandParameter`de tipo`Object`

`SearchBar`Define las `SearchCommand` `SearchCommandParameter` propiedades y, mientras que `ListView` define una `RefreshCommand` propiedad de tipo `ICommand` .

La `ICommand` interfaz define dos métodos y un evento:

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

El ViewModel puede definir propiedades de tipo `ICommand` . Después, puede enlazar estas propiedades a la `Command` propiedad de cada `Button` u otro elemento, o quizás una vista personalizada que implemente esta interfaz. Opcionalmente, puede establecer la `CommandParameter` propiedad para identificar `Button` objetos individuales (u otros elementos) que estén enlazados a esta propiedad ViewModel. Internamente, `Button` llama al `Execute` método cada vez que el usuario puntea `Button` y pasa al `Execute` método `CommandParameter` .

El `CanExecute` método y el `CanExecuteChanged` evento se usan para los casos en los que una `Button` derivación no es válida actualmente, en cuyo caso se `Button` debe deshabilitar. `Button`Llama a `CanExecute` cuando la `Command` propiedad se establece por primera vez y cada vez que `CanExecuteChanged` se desencadena el evento. Si `CanExecute` devuelve `false` , se `Button` deshabilita a sí mismo y no genera `Execute` llamadas.

Para obtener ayuda con la adición de comandos a ViewModels, Xamarin.Forms define dos clases que implementan `ICommand` : `Command` y `Command<T>` donde `T` es el tipo de los argumentos de `Execute` y `CanExecute` . Estas dos clases definen varios constructores más un `ChangeCanExecute` método al que puede llamar el ViewModel para forzar al `Command` objeto a desencadenar el `CanExecuteChanged` evento.

Este es un ViewModel para un teclado simple que está pensado para especificar números de teléfono. Observe que el `Execute` `CanExecute` método y se definen como funciones lambda directamente en el constructor:

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Este ViewModel supone que la `AddCharCommand` propiedad está enlazada a la `Command` propiedad de varios botones (o cualquier otro que tenga una interfaz de comandos), cada uno de los cuales se identifica mediante `CommandParameter` . Estos botones agregan caracteres a una `InputString` propiedad, a la que se le aplica el formato de número de teléfono para la `DisplayText` propiedad.

También hay una segunda propiedad de tipo `ICommand` denominada `DeleteCharCommand` . Está enlazado a un botón de espaciado de retroceso, pero el botón debe deshabilitarse si no hay ningún carácter para eliminar.

El siguiente teclado no es tan sofisticado como podría ser. En su lugar, el marcado se ha reducido a un mínimo para demostrar más claramente el uso de la interfaz de comandos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

La `Command` propiedad de la primera `Button` que aparece en este marcado se enlaza a `DeleteCharCommand` ; el resto se enlaza al `AddCharCommand` con un `CommandParameter` que es igual que el carácter que aparece en la `Button` superficie. Este es el programa en acción:

[![Calculadora con MVVM y comandos](data-bindings-to-mvvm-images/keypad.png)](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculadora con MVVM y comandos")

### <a name="invoking-asynchronous-methods"></a>Invocar métodos asincrónicos

Los comandos también pueden invocar métodos asincrónicos. Esto se logra mediante el uso de las `async` `await` palabras clave y al especificar el `Execute` método:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Esto indica que el `DownloadAsync` método es `Task` y se debe esperar:

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>Implementar un menú de navegación

El programa [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) que contiene todo el código fuente de esta serie de artículos usa un ViewModel para su página principal. Este ViewModel es una definición de una clase corta con tres propiedades denominadas `Type` , `Title` y `Description` que contienen el tipo de cada una de las páginas de ejemplo, un título y una breve descripción. Además, el ViewModel define una propiedad estática denominada `All` que es una colección de todas las páginas del programa:

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

El archivo XAML para `MainPage` define una `ListBox` cuya `ItemsSource` propiedad se establece en esa `All` propiedad y que contiene un `TextCell` para mostrar las `Title` `Description` propiedades y de cada página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Las páginas se muestran en una lista desplazable:

[![Lista desplazable de páginas](data-bindings-to-mvvm-images/mainpage.png)](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Lista desplazable de páginas")

El controlador del archivo de código subyacente se desencadena cuando el usuario selecciona un elemento. El controlador establece la `SelectedItem` propiedad del de `ListBox` nuevo en `null` y, a continuación, crea una instancia de la página seleccionada y navega a ella:

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin evolucione 2016: MVVM facilitado con Xamarin.Forms y Prism**

## <a name="summary"></a>Resumen

XAML es una herramienta eficaz para definir interfaces de usuario en Xamarin.Forms aplicaciones, especialmente cuando se usan enlace de datos y MVVM. El resultado es una representación limpia, elegante y potencialmente compatible de una interfaz de usuario con toda la compatibilidad en segundo plano en el código.

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis XAML básica](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>Vídeos relacionados

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
