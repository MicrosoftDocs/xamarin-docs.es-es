---
title: Botón de Xamarin.Forms
description: El botón se responde a un pulse o haga clic en que se dirige a una aplicación para llevar a cabo una tarea determinada.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: f82d590213076f349b21ebdee2832f2bf474d2f2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489718"
---
# <a name="xamarinforms-button"></a>Botón de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_El botón se responde a un pulse o haga clic en que se dirige a una aplicación para llevar a cabo una tarea determinada._

El [ `Button` ](xref:Xamarin.Forms.Button) es el control interactivo más fundamental en todas las de Xamarin.Forms. El `Button` normalmente muestra una cadena de texto breve que indica un comando, pero también puede mostrar una imagen de mapa de bits, o una combinación de texto y una imagen. El usuario presiona el `Button` con un dedo o hace clic en él con el mouse para iniciar ese comando.

La mayoría de los temas se describe a continuación corresponden a las páginas de la [ **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) ejemplo.

## <a name="handling-button-clicks"></a>Control de botón hace clic en

`Button` define un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento que se desencadena cuando el usuario pulsa el `Button` con un puntero dedo o el mouse. El evento se desencadena cuando se suelta el botón dedo o el mouse de la superficie de la `Button`. El `Button` debe tener su [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad establecida en `true` para que responda a las pulsaciones.

El **haga clic en botón básica** página en el [ **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) ejemplo muestra cómo crear una instancia de un `Button` en XAML y controle su `Clicked` eventos. El **BasicButtonClickPage.xaml** archivo contiene un `StackLayout` con ambos un `Label` y un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

El `Button` tiende a ocupar todo el espacio permitido para él. Por ejemplo, si no establece la `HorizontalOptions` propiedad de `Button` a algo distinto `Fill`, el `Button` ocupará todo el ancho de su elemento primario.

De forma predeterminada, el `Button` es rectangular, pero puede dar TI redondeada esquinas mediante el [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) propiedad, como se describe a continuación, en la sección [ **botón apariencia** ](#button-appearance).

La propiedad [`Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`. El [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento está establecido en un controlador de eventos denominado `OnButtonClicked`. Este controlador se encuentra en el archivo de código subyacente, **BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

Cuando el `Button` se pulsa, el `OnButtonClicked` método se ejecuta. El `sender` argumento es el `Button` objeto responsable de este evento. Se puede usar para tener acceso a la `Button` objeto, o para distinguir entre varias `Button` objetos que comparten el mismo `Clicked` eventos.

Esta particular `Clicked` controlador llama a una función de animación que gira la `Label` 360 grados en 1000 milisegundos. Este es el programa que se ejecutan en dispositivos iOS y Android y como una aplicación de plataforma Universal de Windows (UWP) en el escritorio de Windows 10:

[![Clic en el botón básico](button-images/BasicButtonClick.png "Clic en el botón básico")](button-images/BasicButtonClick-Large.png#lightbox "Clic en el botón básico")

Tenga en cuenta que el `OnButtonClicked` método incluye el `async` modificador porque `await` se usa en el controlador de eventos. Un `Clicked` controlador de eventos requiere la `async` modificador solo si usa el cuerpo del controlador `await`.

Cada plataforma representa el `Button` en su propia manera específica. En el [ **botón apariencia** ](#button-appearance) sección, podrá ver cómo establecer colores y hacer el `Button` borde visible para los aspectos más personalizadas. `Button` implementa el [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interfaz, por lo que incluye [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), y [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)propiedades.

## <a name="creating-a-button-in-code"></a>Creación de un botón en el código

Es habitual para crear instancias de un `Button` en XAML, pero también puede crear un `Button` en el código. Esto podría ser conveniente cuando la aplicación necesita para crear varios botones basados en datos que son enumerables con un `foreach` bucle.

El **Click del botón de código** página muestra cómo crear una página que es funcionalmente equivalente a la **haga clic en botón básica** página pero totalmente en C#:

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

Todo lo que se realiza en el constructor de clase. Dado que el `Clicked` controlador es solo una instrucción larga, puede adjuntarse al evento muy sencillo:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Por supuesto, también puede definir el controlador de eventos como un método independiente (al igual que el `OnButtonClick` método **haga clic en botón básica**) y asocie ese método para el evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Deshabilitar el botón

A veces, una aplicación está en un estado determinado donde un determinado `Button` haga clic en no es una operación válida. En esos casos, el `Button` debe deshabilitarse estableciendo su `IsEnabled` propiedad `false`. El ejemplo clásico es un `Entry` control para un nombre de archivo acompañada de un archivo-abrir `Button`: el `Button` debe habilitarse solo si se ha escrito algún texto en el `Entry`.
Puede usar un `DataTrigger` para esta tarea, como se muestra en el [ **datos desencadenadores** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) artículo.

## <a name="using-the-command-interface"></a>Mediante la interfaz de comandos

Es posible que una aplicación responda a `Button` derivaciones sin control el `Clicked` eventos. El `Button` implementa un mecanismo de notificación alternativo denominado el _comando_ o _comandos_ interfaz. Consta de dos propiedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), una interfaz definida en el [ `System.Windows.Input` ](xref:System.Windows.Input) espacio de nombres.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propiedad de tipo [ `Object` ](xref:System.Object).

Este enfoque es especialmente adecuado en relación con el enlace de datos y especialmente al implementar la arquitectura Model-View-ViewModel (MVVM). Estos temas se tratan en los artículos [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md), [desde los enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), y [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

En una aplicación MVVM, el ViewModel define las propiedades de tipo `ICommand` que se conectan a los elementos `Button` XAML con enlaces de datos. Xamarin. Forms también define [`Command`](xref:Xamarin.Forms.Command) y [`Command<T>`](xref:Xamarin.Forms.Command`1) clases que implementan la interfaz `ICommand` y ayudan a ViewModel en la definición de propiedades de tipo `ICommand`.

Los comandos se describen con más detalle en el artículo [ **la interfaz de comandos** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) pero la **básica de botón de comando** página en el [  **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) muestra el enfoque básico.

La clase `CommandDemoViewModel` es un ViewModel muy sencillo que define una propiedad de tipo `double` denominada `Number`y dos propiedades de tipo `ICommand` denominadas `MultiplyBy2Command` y `DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

Los dos `ICommand` propiedades se inicializan en el constructor de clase con dos objetos de tipo `Command`. El `Command` constructores incluyen una pequeña función (denominado el `execute` argumento de constructor) que duplica o mitades el `Number` propiedad.

El **BasicButtonCommand.xaml** archivo establece su `BindingContext` a una instancia de `CommandDemoViewModel`. El `Label` elemento y dos `Button` elementos contengan enlaces a las tres propiedades en `CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

Como los dos `Button` se puntea elementos, los comandos se ejecutan, y el número de cambios de valor:

[![Comando de botón básico](button-images/BasicButtonCommand.png "Comando de botón básico")](button-images/BasicButtonCommand-Large.png#lightbox)

La ventaja de este enfoque en lo que se refiere a los controladores de `Clicked` es que toda la lógica que implica la funcionalidad de esta página se encuentra en el ViewModel en lugar del archivo de código subyacente, con lo que se logra una mejor separación de la interfaz de usuario de la lógica de negocios.

También es posible que el `Command` objetos para controlar la habilitación y deshabilitación de la `Button` elementos. Por ejemplo, suponga que desea limitar el intervalo de valores numéricos entre 2<sup>10</sup> y 2<sup>&ndash;10</sup>. Puede agregar otra función al constructor (denominado el `canExecute` argumento) que devuelve `true` si el `Button` debe estar habilitada. Esta es la modificación de la `CommandDemoViewModel` constructor:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

Las llamadas a la `ChangeCanExecute` método `Command` son necesarios para que el `Command` puede llamar al método el `canExecute` método y determinar si el `Button` debe deshabilitarse o no. Con este cambio de código, como el número alcanza el límite, el `Button` está deshabilitado:

[![Comando de botón básico modificado](button-images/BasicButtonCommandModified.png "Comando de botón básico modificado")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Es posible que dos o más `Button` elementos que se van a enlazarse a la misma `ICommand` propiedad. El `Button` elementos se pueden distinguir mediante el [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) propiedad de `Button`. En este caso, querrá usar genérico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) clase. El `CommandParameter` objeto, a continuación, se pasa como argumento a la `execute` y `canExecute` métodos. Esta técnica se muestra en detalle en la [ **comandos básicos** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) sección de la [ **comando interfaz** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) artículo.

El [ **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) ejemplo también usa esta técnica en su `MainPage` clase. El **MainPage.xaml** archivo contiene un `Button` para cada página del ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Cada `Button` tiene su `Command` propiedad enlazada a una propiedad denominada `NavigateCommand`y el `CommandParameter` está establecido en un [ `Type` ](xref:System.Type) objeto correspondiente a una de las clases de página en el proyecto.

Que `NavigateCommand` propiedad es de tipo `ICommand` y se define en el archivo de código subyacente:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

El constructor inicializa el `NavigateCommand` propiedad a un `Command<Type>` objeto porque `Type` es el tipo de la `CommandParameter` objeto establecido en el archivo XAML. Esto significa que el `execute` método tiene un argumento de tipo `Type` que corresponde a este `CommandParameter` objeto. La función crea una instancia de la página y, a continuación, navega a ella.

Tenga en cuenta que el constructor concluye estableciendo su `BindingContext` a sí mismo. Esto es necesario para las propiedades en el archivo XAML para enlazar con el `NavigateCommand` propiedad.

## <a name="pressing-and-releasing-the-button"></a>Presionar y soltar el botón

Además del evento `Clicked`, `Button` también define los eventos [`Pressed`](xref:Xamarin.Forms.Button.Pressed) y [`Released`](xref:Xamarin.Forms.Button.Released). El `Pressed` evento tiene lugar cuando se presiona un dedo en un `Button`, o se presiona un botón del mouse con el puntero situado sobre el `Button`. El `Released` evento tiene lugar cuando se suelta el botón del dedo o el mouse. Por lo general, un `Clicked` también se desencadena el evento al mismo tiempo que el `Released` evento, pero si el puntero del dedo o el mouse se desliza fuera de la superficie de la `Button` antes de que se publique el `Clicked` eventos podrían no producirse.

El `Pressed` y `Released` eventos no se utilizan a menudo, pero puede usarse para fines especiales, como se muestra en el **presione y suelte el botón** página. El archivo XAML contiene una `Label` y un `Button` con controladores asociados para la `Pressed` y `Released` eventos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

El archivo de código subyacente se anima la `Label` cuando un `Pressed` evento se produce, pero se suspende la rotación cuando un `Released` se produce el evento:

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

El resultado es que el `Label` solo gira mientras un dedo se encuentra en contacto con el `Button`y se detiene cuando se suelta el dedo:

[![Presionar y soltar el botón](button-images/PressAndReleaseButton.png "Presionar y soltar el botón")](button-images/PressAndReleaseButton-Large.png)

Este tipo de comportamiento tiene aplicaciones para juegos: un dedo que se mantiene en un `Button` podría hacer que un objeto en pantalla se mueva en una dirección determinada.

<a name="button-appearance" />

## <a name="button-appearance"></a>Apariencia del botón

El `Button` hereda o define varias propiedades que afectan a su aspecto:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) es el color de la `Button` texto
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) es el color del fondo para que el texto
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) es el color de un área que rodea el `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) se usa la familia de fuentes del texto
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) es el tamaño del texto
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica si el texto está en negrita o cursiva
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) es el ancho del borde
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) es el radio de redondeo de la `Button`
- `CharacterSpacing` es el espaciado entre los caracteres del texto del `Button`

> [!NOTE]
> El `Button` clase también tiene [ `Margin` ](xref:Xamarin.Forms.View.Margin) y [ `Padding` ](xref:Xamarin.Forms.Button.Padding) las propiedades que controlan el comportamiento de diseño de la `Button`. Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Los efectos de seis de estas propiedades (excepto `FontFamily` y `FontAttributes`) se muestran en el **apariencia del botón** página. Otra propiedad [ `Image` ](xref:Xamarin.Forms.Button.ImageSource), se describe en la sección [ **usando mapas de bits con el botón**](#image-button).

Todos los enlaces de datos y vistas en el **apariencia del botón** página se definen en el archivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

El `Button` en la parte superior de la página tiene sus tres `Color` propiedades enlazadas a `Picker` elementos en la parte inferior de la página. Los elementos de la `Picker` elementos son los colores de la `NamedColor` clase incluida en el proyecto. Tres `Slider` elementos contienen enlaces bidireccionales para la `FontSize`, `BorderWidth`, y `CornerRadius` propiedades de la `Button`.

Este programa le permite experimentar con combinaciones de todas estas propiedades:

[![Apariencia del botón](button-images/ButtonAppearance.png "Apariencia del botón")](button-images/ButtonAppearance-Large.png)

Para ver el `Button` borde, deberá establecer un `BorderColor` a algo distinto `Default`y el `BorderWidth` en un valor positivo.

En iOS, observará que los anchos de borde grande interfieren en el interior de la `Button` e interfieren con la presentación del texto. Si opta por utilizar un elemento border con un iOS `Button`, probablemente deseará comenzar y terminar la `Text` propiedad con espacios para conservar su visibilidad.

En UWP, seleccionar un `CornerRadius` que supera la mitad del alto de la `Button` produce una excepción.

## <a name="button-visual-states"></a>Estados del botón visuales

[`Button`](xref:Xamarin.Forms.Button) tiene un `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que puede utilizarse para iniciar un cambio visual en el `Button` cuando presiona por el usuario, siempre que lo esté habilitada.

En el siguiente ejemplo XAML se muestra cómo definir un estado visual para el `Pressed` estado:

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

El `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) especifica que, cuando el [ `Button` ](xref:Xamarin.Forms.Button) está presionado, su [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) se cambiará la propiedad de su valor predeterminado de 1 a 0,8. El `Normal` `VisualState` especifica que, cuando el `Button` está en estado normal, su `Scale` propiedad se establecerá en 1. Por lo tanto, el efecto general es que cuando el `Button` está presionado, se vuelve a escalar para que sea ligeramente más pequeñas pero cuando el `Button` está publicado, se escalan a su tamaño predeterminado.

Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Creación de un botón de alternancia

Es posible crear subclases `Button` para que funcione como un conmutador activar / desactivar: pulse el botón una vez para el botón de alternancia en y pulse de nuevo para desactivarlo.

La siguiente `ToggleButton` clase se deriva de `Button` y define un nuevo evento denominado `Toggled` y una propiedad booleana denominada `IsToggled`. Estas son las mismas dos definido por el Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

El `ToggleButton` constructor adjunta un controlador para el `Clicked` eventos, por lo que TI puede cambiar el valor de la `IsToggled` propiedad. El `OnIsToggledChanged` método activa el `Toggled` eventos.

La última línea de la `OnIsToggledChanged` llamadas de método estático `VisualStateManager.GoToState` método con el texto de dos cadenas "ToggledOn" y "ToggledOff". Puede leer acerca de este método y cómo la aplicación puede responder a estados visuales en el artículo [ **Xamarin.Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md).

Dado que `ToggleButton` realiza la llamada a `VisualStateManager.GoToState`, la propia clase no necesita incluir capacidades adicionales para cambiar la apariencia del botón según su `IsToggled` estado. Es decir, la responsabilidad del XAML que hospeda el `ToggleButton`.

El **demostración de botón de alternancia** página contiene dos instancias de `ToggleButton`, incluido el marcado Visual State Manager que establece el `Text`, `BackgroundColor`, y `TextColor` del botón en función del estado visual:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

El `Toggled` controladores de eventos están en el archivo de código subyacente. Son responsables de la configuración de la `FontAttributes` propiedad de la `Label` según el estado de los botones:

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

Este es el programa que se ejecutan en iOS, Android y UWP:

[![Demostración de botón de alternancia](button-images/ToggleButtonDemo.png "Demostración de botón de alternancia")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Uso de mapas de bits con botones

El `Button` clase define un [ `ImageSource` ](xref:Xamarin.Forms.Button.Image) propiedad que permite mostrar una imagen de mapa de bits en el `Button`, por sí solo o en combinación con el texto. También puede especificar cómo se organizan el texto e imagen.

La propiedad `ImageSource` es de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), lo que significa que los mapas de bits se pueden cargar desde un archivo, un recurso incrustado, un URI o un flujo.

> [!NOTE]
> Aunque un `Button` puede cargar un GIF animado, solo mostrará el primer fotograma del GIF.

Cada plataforma compatible con Xamarin.Forms permite que las imágenes que se almacenará en varios tamaños para las resoluciones de píxeles diferente de los distintos dispositivos que puede ejecutar la aplicación. Estos se denominado varios mapas de bits o se almacenan de manera que el sistema operativo puede elegir a la mejor coincidencia para el dispositivo de vídeo resolución de pantalla.

Para un mapa de bits en un `Button`, normalmente es el mejor tamaño entre 32 y 64 unidades independientes del dispositivo, según el tamaño que desee. Las imágenes utilizadas en este ejemplo se basan en un tamaño de 48 unidades independientes del dispositivo.

En el proyecto de iOS, el **recursos** carpeta contiene tres tamaños de esta imagen:

- Un mapa de bits 48 píxeles cuadrado almacenado como **/Resources/MonkeyFace.png**
- Un mapa de bits 96 píxeles cuadrado almacenado como **/Resource/MonkeyFace@2x.png**
- Un mapa de bits 144 píxeles cuadrado almacenado como **/Resource/MonkeyFace@3x.png**

Todas las tres mapas de bits se asignaron un **acción de compilación** de **BundleResource**.

Para el proyecto Android, los mapas de bits todas tienen el mismo nombre, pero se almacenan en subcarpetas diferentes de la **recursos** carpeta:

- Un mapa de bits 72 píxeles cuadrado almacenado como **/Resources/drawable-hdpi/MonkeyFace.png**
- Un mapa de bits 96 píxeles cuadrado almacenado como **/Resources/drawable-xhdpi/MonkeyFace.png**
- Un mapa de bits 144 píxeles cuadrado almacenado como **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Un mapa de bits 192 píxeles cuadrado almacenado como **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Estos se asignaron un **acción de compilación** de **AndroidResource**.

En el proyecto UWP, se pueden almacenar mapas de bits en cualquier lugar en el proyecto, pero generalmente se almacenan en una carpeta personalizada o **activos** carpeta existente. El proyecto UWP contiene estos mapas de bits:

- Un mapa de bits 48 píxeles cuadrado almacenado como **/Assets/MonkeyFace.scale-100.png**
- Un mapa de bits 96 píxeles cuadrado almacenado como **/Assets/MonkeyFace.scale-200.png**
- Un mapa de bits 192 píxeles cuadrado almacenado como **/Assets/MonkeyFace.scale-400.png**

Todos se asignó un **acción de compilación** de **contenido**.

Puede especificar cómo el `Text` y `ImageSource` propiedades están organizadas en el `Button` utilizando el [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) propiedad de `Button`. Esta propiedad es de tipo [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), que es una clase incrustada en `Button`. El [constructor](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) tiene dos argumentos:

- Un miembro de la [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumeración: `Left`, `Top`, `Right`, o `Bottom` que indica cómo aparece el mapa de bits en relación con el texto.
- Un `double` valor para el espaciado entre el mapa de bits y el texto.

Los valores predeterminados son `Left` y 10 unidades. Dos propiedades de solo lectura de `ButtonContentLayout` denominado [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) y [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) proporcione los valores de esas propiedades.

En el código, puede crear un `Button` y establezca el `ContentLayout` propiedad similar al siguiente:

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

En XAML, debe especificar solo el miembro de enumeración o el espaciado o ambos en cualquier orden separan por comas:

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

El **imagen Button Demo** página usa `OnPlatform` para especificar los nombres de archivo diferente para iOS, Android y UWP, los archivos de mapa de bits. Si desea usar el mismo nombre de archivo para cada plataforma y evitar el uso de `OnPlatform`, necesitará para almacenar los mapas de bits UWP en el directorio raíz del proyecto.

La primera `Button` en el **imagen Button Demo** página establece el `Image` propiedad pero no la `Text` propiedad:

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

Si los mapas de bits UWP se almacenan en el directorio raíz del proyecto, este marcado se puede simplificar considerablemente:

```xaml
<Button ImageSource="MonkeyFace.png" />
```

Para evitar mucha marcado redundante en la **ImageButtonDemo.xaml** archivo implícita `Style` también se define para establecer el `ImageSource` propiedad. Esto `Style` se aplica automáticamente a otros cinco `Button` elementos. Este es el archivo XAML completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

Los cuatro finales `Button` elementos hacen uso de la `ContentLayout` propiedad para especificar una posición y espaciado del texto y mapa de bits:

[![Demostración de botón de imagen](button-images/ImageButtonDemo.png "Demostración de botón de imagen")](button-images/ImageButtonDemo-Large.png#lightbox)

Ahora ha visto las distintas formas que puede controlar `Button` eventos y cambiar el `Button` apariencia.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ButtonDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API de los botones](xref:Xamarin.Forms.Button)
