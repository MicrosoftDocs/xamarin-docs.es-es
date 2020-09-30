---
title: Xamarin.Forms Botón
description: El botón responde a una pulsación o clic que dirige una aplicación para llevar a cabo una tarea determinada.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fba8dcb344f79c21815a58cff0d8132967381cca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556742"
---
# <a name="no-locxamarinforms-button"></a>Xamarin.Forms Botón

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_El botón responde a una pulsación o clic que dirige una aplicación para llevar a cabo una tarea determinada._

[`Button`](xref:Xamarin.Forms.Button)Es el control interactivo más importante en todos Xamarin.Forms . `Button`Normalmente muestra una cadena de texto breve que indica un comando, pero también puede mostrar una imagen de mapa de bits o una combinación de texto y una imagen. El usuario presiona el `Button` con un dedo o hace clic con el mouse para iniciar ese comando.

La mayoría de los temas descritos a continuación corresponden a las páginas del ejemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) .

## <a name="handling-button-clicks"></a>Control de clics de botón

`Button` define un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento que se desencadena cuando el usuario puntea `Button` con un dedo o un puntero del mouse. El evento se desencadena cuando el dedo o el botón del mouse se suelta de la superficie de `Button` . El `Button` debe tener su [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad establecida en para que `true` responda a los grifos.

La página de **clics del botón básico** en el ejemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) muestra cómo crear una instancia `Button` de en XAML y controlar su `Clicked` evento. El archivo **BasicButtonClickPage. Xaml** contiene un `StackLayout` con un `Label` y un `Button` :

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

`Button`Tiende a ocupar todo el espacio permitido. Por ejemplo, si no establece la `HorizontalOptions` propiedad de `Button` en un valor distinto de `Fill` , `Button` ocupará el ancho completo de su elemento primario.

De forma predeterminada, `Button` es rectangular, pero puede asignarle esquinas redondeadas mediante la [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) propiedad, como se describe a continuación en la sección [**apariencia del botón**](#button-appearance).

La [`Text`](xref:Xamarin.Forms.Button.Text) propiedad especifica el texto que aparece en `Button` . El [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento se establece en un controlador de eventos denominado `OnButtonClicked` . Este controlador se encuentra en el archivo de código subyacente, **BasicButtonClickPage.Xaml.CS**:

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

Cuando se pulsa `Button`, se ejecuta el método `OnButtonClicked`. El `sender` argumento es el `Button` objeto responsable de este evento. Puede utilizar esto para tener acceso al `Button` objeto o para distinguir entre varios `Button` objetos que comparten el mismo `Clicked` evento.

Este `Clicked` controlador concreto llama a una función de animación que gira los `Label` 360 grados en 1000 milisegundos. Este es el programa que se ejecuta en dispositivos iOS y Android, y como una aplicación Plataforma universal de Windows (UWP) en el escritorio de Windows 10:

[![Clic en el botón básico](button-images/BasicButtonClick.png "Clic en el botón básico")](button-images/BasicButtonClick-Large.png#lightbox "Clic en el botón básico")

Observe que el `OnButtonClicked` método incluye el `async` modificador porque `await` se usa dentro del controlador de eventos. Un `Clicked` controlador de eventos solo requiere el `async` modificador si el cuerpo del controlador utiliza `await` .

Cada plataforma representa el `Button` de forma específica. En la sección [**apariencia del botón**](#button-appearance) , verá cómo establecer colores y hacer que el borde sea visible para ver una `Button` apariencia más personalizada. `Button` implementa la [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) interfaz, por lo que incluye [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) [`FontSize`](xref:Xamarin.Forms.Button.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) las propiedades, y.

## <a name="creating-a-button-in-code"></a>Crear un botón en el código

Es habitual crear una instancia de `Button` en XAML, pero también se puede crear un `Button` en el código. Esto puede resultar práctico cuando la aplicación necesita crear varios botones basados en datos enumerables con un `foreach` bucle.

La página de **clics del botón de código** muestra cómo crear una página que sea funcionalmente equivalente a la página de **clics del botón básico** , pero completamente en C#:

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

Todo se realiza en el constructor de la clase. Dado que el `Clicked` controlador es solo una instrucción Long, se puede adjuntar al evento muy simplemente:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Por supuesto, también puede definir el controlador de eventos como un método independiente (al igual que el `OnButtonClick` método en el **clic de botón básico**) y asociar ese método al evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Deshabilitar el botón

A veces, una aplicación se encuentra en un estado determinado en el que un `Button` clic determinado no es una operación válida. En esos casos, `Button` se debe deshabilitar estableciendo su `IsEnabled` propiedad en `false` . El ejemplo clásico es un `Entry` control para un nombre de archivo que va acompañado de un archivo abierto `Button` : el `Button` solo se debe habilitar si se ha escrito texto en `Entry` .
Puede usar `DataTrigger` para esta tarea, tal como se muestra en el artículo [**desencadenadores de datos**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) .

## <a name="using-the-command-interface"></a>Usar la interfaz de comandos

Es posible que una aplicación responda a `Button` las pulsaciones sin controlar el `Clicked` evento. `Button`Implementa un mecanismo de notificación alternativo denominado _comando_ o interfaz de _comandos_ . Consta de dos propiedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo [`ICommand`](xref:System.Windows.Input.ICommand) , una interfaz definida en el [`System.Windows.Input`](xref:System.Windows.Input) espacio de nombres.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propiedad de tipo [`Object`](xref:System.Object) .

Este enfoque es especialmente adecuado en relación con el enlace de datos, especialmente cuando se implementa la arquitectura Model-View-ViewModel (MVVM). Estos temas se tratan en los artículos [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md), [desde enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)y [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

En una aplicación MVVM, el ViewModel define las propiedades de tipo `ICommand` que se conectan a los `Button` elementos XAML con enlaces de datos. Xamarin.Forms también define [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command`1) las clases y que implementan la `ICommand` interfaz y ayudan al ViewModel en la definición de propiedades de tipo `ICommand` .

Los comandos se describen con más detalle en el artículo [**la interfaz de comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , pero la página de **comandos del botón básico** en el ejemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) muestra el enfoque básico.

La `CommandDemoViewModel` clase es un ViewModel muy sencillo que define una propiedad de tipo `double` denominado y `Number` dos propiedades de tipo `ICommand` denominadas `MultiplyBy2Command` y `DivideBy2Command` :

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

Las dos `ICommand` propiedades se inicializan en el constructor de la clase con dos objetos de tipo `Command` . Los `Command` constructores incluyen una pequeña función (denominada `execute` argumento de constructor) que hace doble o la mitad de la `Number` propiedad.

El archivo **BasicButtonCommand. Xaml** establece su `BindingContext` en una instancia de `CommandDemoViewModel` . El `Label` elemento y dos `Button` elementos contienen enlaces a las tres propiedades de `CommandDemoViewModel` :

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

A medida que `Button` se puntean los dos elementos, los comandos se ejecutan y el número cambia de valor:

[![Comando de botón básico](button-images/BasicButtonCommand.png "Comando de botón básico")](button-images/BasicButtonCommand-Large.png#lightbox)

La ventaja de este enfoque en lo `Clicked` que se refiere a los controladores es que toda la lógica que implica la funcionalidad de esta página se encuentra en el ViewModel en lugar del archivo de código subyacente, con lo que se logra una mejor separación de la interfaz de usuario de la lógica de negocios.

También es posible que los `Command` objetos controlen la habilitación y deshabilitación de los `Button` elementos. Por ejemplo, supongamos que desea limitar el intervalo de valores numéricos entre 2<sup>10</sup> y 2<sup> &ndash; 10</sup>. Puede agregar otra función al constructor (denominado el `canExecute` argumento) que devuelve `true` si `Button` debe estar habilitado. Esta es la modificación del `CommandDemoViewModel` constructor:

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

Las llamadas al `ChangeCanExecute` método de `Command` son necesarias para que el `Command` método pueda llamar al `canExecute` método y determinar si `Button` debe estar deshabilitado o no. Con este cambio de código, a medida que el número alcanza el límite, el `Button` está deshabilitado:

[![Comando de botón básico modificado](button-images/BasicButtonCommandModified.png "Comando de botón básico modificado")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Es posible que dos o más `Button` elementos se enlacen a la misma `ICommand` propiedad. Los `Button` elementos se pueden distinguir mediante la [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propiedad de `Button` . En este caso, querrá usar la [`Command<T>`](xref:Xamarin.Forms.Command`1) clase genérica. `CommandParameter`A continuación, el objeto se pasa como argumento a `execute` los `canExecute` métodos y. Esta técnica se muestra en detalle en la sección de [**comandos básicos**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) del artículo sobre la [**interfaz de comandos**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) .

El ejemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) también usa esta técnica en su `MainPage` clase. El archivo **mainpage. Xaml** contiene una `Button` para cada página del ejemplo:

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

Cada `Button` una tiene su `Command` propiedad enlazada a una propiedad denominada `NavigateCommand` y `CommandParameter` se establece en un [`Type`](xref:System.Type) objeto que corresponde a una de las clases de página del proyecto.

Esa `NavigateCommand` propiedad es de tipo `ICommand` y se define en el archivo de código subyacente:

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

El constructor inicializa la `NavigateCommand` propiedad en un `Command<Type>` objeto porque `Type` es el tipo del conjunto de `CommandParameter` objetos en el archivo XAML. Esto significa que el `execute` método tiene un argumento de tipo `Type` que corresponde a este `CommandParameter` objeto. La función crea una instancia de la página y, a continuación, navega hasta ella.

Tenga en cuenta que el constructor termina estableciendo su `BindingContext` en sí mismo. Esto es necesario para que las propiedades del archivo XAML se enlacen a la `NavigateCommand` propiedad.

## <a name="pressing-and-releasing-the-button"></a>Presionar y soltar el botón

Además del `Clicked` evento, `Button` también define [`Pressed`](xref:Xamarin.Forms.Button.Pressed) [`Released`](xref:Xamarin.Forms.Button.Released) los eventos y. El `Pressed` evento tiene lugar cuando se presiona un dedo en un control `Button` , o cuando se presiona un botón del mouse con el puntero situado sobre `Button` . El `Released` evento tiene lugar cuando se suelta el dedo o el botón del mouse. Por lo general, un `Clicked` evento también se desencadena al mismo tiempo que el `Released` evento, pero si el puntero del dedo o del mouse se desplaza fuera de la superficie de `Button` antes de su lanzamiento, el `Clicked` evento podría no producirse.

Los `Pressed` `Released` eventos y no se usan a menudo, pero se pueden usar para fines especiales, como se muestra en la página del **botón presionar y soltar** . El archivo XAML contiene un `Label` y un `Button` con los controladores adjuntos para los `Pressed` `Released` eventos y:

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

El archivo de código subyacente anima el `Label` cuando `Pressed` se produce un evento, pero suspende el giro cuando `Released` se produce un evento:

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

El resultado es que el `Label` solo gira mientras un dedo está en contacto con el `Button` , y se detiene cuando se suelta el dedo:

[![Presionar y soltar el botón](button-images/PressAndReleaseButton.png "Presionar y soltar el botón")](button-images/PressAndReleaseButton-Large.png)

Este tipo de comportamiento tiene aplicaciones para juegos: un dedo que se mantiene en un `Button` puede hacer que un objeto en pantalla se mueva en una dirección determinada.

## <a name="button-appearance"></a>Apariencia del botón

`Button`Hereda o define varias propiedades que afectan a su apariencia:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) es el color del `Button` texto.
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) es el color de fondo de ese texto
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) es el color de un área que rodea el `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) es la familia de fuentes utilizada para el texto
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) es el tamaño del texto.
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica si el texto está en cursiva o en negrita
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) es el ancho del borde.
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) es el radio de la esquina del `Button`
- [`CharacterSpacing`](xref:Xamarin.Forms.Button.CharacterSpacing) es el espaciado entre los caracteres del `Button` texto.
- `TextTransform` determina las mayúsculas y minúsculas del `Button` texto.

> [!NOTE]
> La `Button` clase también tiene [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Button.Padding) propiedades y que controlan el comportamiento de diseño de `Button` . Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Los efectos de seis de estas propiedades (excepto `FontFamily` y `FontAttributes` ) se muestran en la página **apariencia del botón** . Otra propiedad, [`Image`](xref:Xamarin.Forms.Button.ImageSource) , se describe en la sección [**uso de mapas de bits con el botón**](#using-bitmaps-with-buttons).

Todas las vistas y los enlaces de datos de la página **apariencia del botón** se definen en el archivo XAML:

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

`Button`En la parte superior de la página tiene sus tres `Color` propiedades enlazadas a `Picker` elementos en la parte inferior de la página. Los elementos de los `Picker` elementos son colores de la `NamedColor` clase incluida en el proyecto. Tres `Slider` elementos contienen enlaces bidireccionales a las `FontSize` propiedades, `BorderWidth` y `CornerRadius` de `Button` .

Este programa le permite experimentar con combinaciones de todas estas propiedades:

[![Apariencia del botón](button-images/ButtonAppearance.png "Apariencia del botón")](button-images/ButtonAppearance-Large.png)

Para ver el `Button` borde, debe establecer `BorderColor` en un valor distinto de `Default` y `BorderWidth` en un valor positivo.

En iOS, observará que los anchos de borde grandes infieren al interior de `Button` e interfieren con la presentación del texto. Si decide usar un borde con un iOS `Button` , probablemente querrá empezar y finalizar la `Text` propiedad con espacios para conservar su visibilidad.

En UWP, al seleccionar un `CornerRadius` que supera la mitad del alto del se `Button` produce una excepción.

## <a name="button-visual-states"></a>Estados visuales del botón

[`Button`](xref:Xamarin.Forms.Button) tiene un `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar un cambio visual en `Button` cuando lo presiona el usuario, siempre que esté habilitado.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual para el `Pressed` Estado:

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

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) Especifica que cuando [`Button`](xref:Xamarin.Forms.Button) se presiona, su propiedad se [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) cambiará de su valor predeterminado de 1 a 0,8. `Normal` `VisualState` Especifica que cuando el `Button` está en un estado normal, su `Scale` propiedad se establecerá en 1. Por lo tanto, el efecto general es que, cuando `Button` se presiona, se vuelve a escalar para que sea ligeramente menor y, cuando `Button` se libera, se vuelve a escalar a su tamaño predeterminado.

Para obtener más información sobre los Estados visuales, consulte [el Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Crear un botón de alternancia

Es posible crear una subclase para `Button` que funcione como un modificador OFF: Pulse el botón una vez para activar o desactivar el botón y púlselo de nuevo para desactivarlo.

La siguiente `ToggleButton` clase deriva de `Button` y define un nuevo evento denominado `Toggled` y una propiedad booleana denominada `IsToggled` . Estas son las dos propiedades definidas por Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) :

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

El `ToggleButton` constructor adjunta un controlador al `Clicked` evento para que pueda cambiar el valor de la `IsToggled` propiedad. El `OnIsToggledChanged` método desencadena el `Toggled` evento.

La última línea del `OnIsToggledChanged` método llama al método estático `VisualStateManager.GoToState` con las dos cadenas de texto "ToggledOn" y "ToggledOff". Puede leer sobre este método y cómo la aplicación puede responder a los Estados visuales en el artículo [** Xamarin.Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md).

Dado que `ToggleButton` realiza la llamada a `VisualStateManager.GoToState` , la propia clase no necesita incluir recursos adicionales para cambiar la apariencia del botón en función de su `IsToggled` Estado. Es responsabilidad del código XAML que hospeda `ToggleButton` .

La página de **demostración del botón de alternancia** contiene dos instancias de `ToggleButton` , incluido el marcado de Visual State Manager que establece `Text` , `BackgroundColor` y `TextColor` del botón en función del estado visual:

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

Los `Toggled` controladores de eventos se encuentran en el archivo de código subyacente. Son responsables del establecimiento de la `FontAttributes` propiedad de `Label` basándose en el estado de los botones:

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

Este es el programa que se ejecuta en iOS, Android y UWP:

[![Demostración de botón de alternancia](button-images/ToggleButtonDemo.png "Demostración de botón de alternancia")](button-images/ToggleButtonDemo-Large.png#lightbox)

## <a name="using-bitmaps-with-buttons"></a>Uso de mapas de bits con botones.

La `Button` clase define una [`ImageSource`](xref:Xamarin.Forms.Button.Image) propiedad que permite mostrar una imagen de mapa de bits en `Button` , ya sea solo o en combinación con el texto. También puede especificar cómo se organizan el texto y la imagen.

La `ImageSource` propiedad es de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , lo que significa que los mapas de bits se pueden cargar desde un archivo, un recurso incrustado, un URI o un flujo.

> [!NOTE]
> Aunque un `Button` puede cargar un GIF animado, solo mostrará el primer fotograma del GIF.

Cada plataforma compatible con Xamarin.Forms permite almacenar imágenes en varios tamaños para diferentes resoluciones de píxeles de los distintos dispositivos en los que se puede ejecutar la aplicación. Estos varios mapas de bits se denominan o almacenan de manera que el sistema operativo puede elegir la mejor coincidencia para la resolución de pantalla del vídeo del dispositivo.

En el caso de un mapa de bits en un `Button` , el mejor tamaño suele ser de entre 32 y 64 unidades independientes del dispositivo, en función de lo grande que desee. Las imágenes que se usan en este ejemplo se basan en un tamaño de 48 unidades independientes del dispositivo.

En el proyecto de iOS, la carpeta **recursos** contiene tres tamaños de esta imagen:

- Mapa de bits cuadrado de 48 píxeles almacenado como **/Resources/MonkeyFace.png**
- Mapa de bits cuadrado de 96 píxeles almacenado como **/Resource/MonkeyFace@2x.png**
- Mapa de bits cuadrado de 144 píxeles almacenado como **/Resource/MonkeyFace@3x.png**

A los tres mapas de bits se les proporcionó una **acción de compilación** de **BundleResource**.

En el caso del proyecto de Android, todos los mapas de bits tienen el mismo nombre, pero se almacenan en diferentes subcarpetas de la carpeta de **recursos** :

- Mapa de bits cuadrado de 72 píxeles almacenado como **/Resources/drawable-hdpi/MonkeyFace.png**
- Mapa de bits cuadrado de 96 píxeles almacenado como **/Resources/drawable-xhdpi/MonkeyFace.png**
- Mapa de bits cuadrado de 144 píxeles almacenado como **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Mapa de bits cuadrado de 192 píxeles almacenado como **/Resources/drawable-xxxhdpi/MonkeyFace.png**

A estos se les proporcionó una **acción de compilación** de **AndroidResource**.

En el proyecto de UWP, los mapas de bits se pueden almacenar en cualquier parte del proyecto, pero normalmente se almacenan en una carpeta personalizada o en la carpeta de **recursos** existentes. El proyecto de UWP contiene estos mapas de bits:

- Mapa de bits cuadrado de 48 píxeles almacenado como **/Assets/MonkeyFace.scale-100.png**
- Mapa de bits cuadrado de 96 píxeles almacenado como **/Assets/MonkeyFace.scale-200.png**
- Mapa de bits cuadrado de 192 píxeles almacenado como **/Assets/MonkeyFace.scale-400.png**

Se les ha dado una **acción de compilación** de **contenido**.

Puede especificar cómo `Text` `ImageSource` se organizan las propiedades y en `Button` mediante la [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) propiedad de `Button` . Esta propiedad es de tipo [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout) , que es una clase incrustada en `Button` . [Constructor] (XREF: Xamarin.Forms . Button. ButtonContentLayout .% 23ctor ( Xamarin.Forms . Button. ButtonContentLayout. ImagePosition, System. Double)) tiene dos argumentos:

- Miembro de la [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumeración: `Left` , `Top` , `Right` o `Bottom` que indica cómo aparece el mapa de bits en relación con el texto.
- Un `double` valor para el espaciado entre el mapa de bits y el texto.

Los valores predeterminados son `Left` y 10 unidades. Dos propiedades de solo lectura de `ButtonContentLayout` con nombre [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) y [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) proporcionan los valores de esas propiedades.

En el código, puede crear `Button` y establecer la propiedad de la siguiente `ContentLayout` manera:

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

En XAML, solo es necesario especificar el miembro de la enumeración, el espaciado o ambos en cualquier orden separado por comas:

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

En la página de **demostración del botón imagen** `OnPlatform` se usa para especificar nombres de archivo diferentes para los archivos de mapa de bits de iOS, Android y UWP. Si desea usar el mismo nombre de archivo para cada plataforma y evitar el uso de `OnPlatform` , deberá almacenar los mapas de bits de UWP en el directorio raíz del proyecto.

La primera `Button` de la página de **demostración del botón de imagen** establece la `Image` propiedad pero no la `Text` propiedad:

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

Si los mapas de bits de UWP se almacenan en el directorio raíz del proyecto, este marcado se puede simplificar considerablemente:

```xaml
<Button ImageSource="MonkeyFace.png" />
```

Para evitar una gran cantidad de marcado redundante resulta en el archivo **ImageButtonDemo. Xaml** , `Style` también se define un implícito para establecer la `ImageSource` propiedad. Esto `Style` se aplica automáticamente a otros cinco `Button` elementos. Este es el archivo XAML completo:

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

Los cuatro `Button` elementos finales hacen uso de la `ContentLayout` propiedad para especificar la posición y el espaciado del texto y el mapa de bits:

[![Demostración de botón de imagen](button-images/ImageButtonDemo.png "Demostración de botón de imagen")](button-images/ImageButtonDemo-Large.png#lightbox)

Ahora ha visto las distintas formas en que puede controlar `Button` eventos y cambiar la `Button` apariencia.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ButtonDemos](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API de los botones](xref:Xamarin.Forms.Button)