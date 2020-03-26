---
title: Vistas nativas en XAML
description: Vistas nativas de iOS, Android y la plataforma Universal de Windows pueden hacer referencia directamente desde los archivos XAML de Xamarin.Forms. Se pueden establecer las propiedades y los controladores de eventos en vistas nativas, y pueden interactuar con las vistas de Xamarin.Forms. En este artículo se muestra cómo consumir vistas nativas en archivos XAML de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
ms.openlocfilehash: 6d3954f44cd769ed02535eb260b9952e81e67c98
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247631"
---
# <a name="native-views-in-xaml"></a>Vistas nativas en XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Se puede hacer referencia directamente a las vistas nativas de iOS, Android y el Plataforma universal de Windows desde los archivos XAML de Xamarin. Forms. Las propiedades y los controladores de eventos se pueden establecer en las vistas nativas y pueden interactuar con las vistas de Xamarin. Forms. En este artículo se muestra cómo consumir vistas nativas de archivos XAML de Xamarin. Forms._

En este artículo se tratan los temas siguientes:

- [Consumir vistas nativas](#consuming) : proceso para consumir una vista nativa desde XAML.
- [Usar enlaces nativos](#native_bindings) : enlace de datos a y desde las propiedades de las vistas nativas.
- [Pasar argumentos a vistas nativas](#passing_arguments) : pasar argumentos a constructores de vistas nativas y llamar a métodos de generador de vistas nativas.
- [Referencia a vistas nativas desde el código](#native_view_code) : recuperación de instancias de vista nativa declaradas en un archivo XAML, desde el archivo de código subyacente.
- [Subclase de vistas nativas](#subclassing) : subclase de vistas nativas para definir una API compatible con XAML.  

<a name="overview" />

## <a name="overview"></a>Información general

Para insertar una vista nativa a un archivo XAML de Xamarin.Forms:

1. Agregue una declaración de espacio de nombres `xmlns` en el archivo XAML para el espacio de nombres que contiene la vista nativa.
1. Cree una instancia de la vista nativa en el archivo XAML.

> [!IMPORTANT]
> El código XAML compilado debe estar deshabilitado para cualquier página XAML que use vistas nativas. Esto se puede lograr decorando la clase de código subyacente de la página XAML con el atributo `[XamlCompilation(XamlCompilationOptions.Skip)]`. Para obtener más información sobre la compilación XAML, vea [compilar XAML en Xamarin. Forms](~/xamarin-forms/xaml/xamlc.md).

Para hacer referencia a una vista nativa desde un archivo de código subyacente, debe usar un proyecto de activos compartidos (SAP) y ajustar el código específico de plataforma con directivas de compilación condicional. Para obtener más información, vea [hacer referencia a vistas nativas desde el código](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Consumo de vistas nativas

En el ejemplo de código siguiente se muestra cómo consumir vistas nativas para cada plataforma en un [`ContentPage`](xref:Xamarin.Forms.ContentPage)de Xamarin. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Además de especificar el `clr-namespace` y `assembly` para un espacio de nombres de vista nativa, también se debe especificar una `targetPlatform`. Debe establecerse en `iOS`, `Android`, `UWP``Windows` (que es equivalente a `UWP`), `macOS`, `GTK`, `Tizen`o `WPF`. En tiempo de ejecución, el analizador de XAML omitirá cualquier prefijo de espacio de nombres XML que tenga un `targetPlatform` que no coincida con la plataforma en la que se ejecuta la aplicación.

Cada declaración de espacio de nombres puede utilizarse para hacer referencia a cualquier clase o estructura del espacio de nombres especificado. Por ejemplo, la declaración de espacio de nombres `ios` se puede usar para hacer referencia a cualquier clase o estructura del espacio de nombres `UIKit` de iOS. Se pueden establecer propiedades de la vista nativa a través de XAML, pero deben coincidir con los tipos de propiedad y objeto. Por ejemplo, la propiedad `UILabel.TextColor` se establece en `UIColor.Red` mediante la extensión de marcado `x:Static` y el espacio de nombres `ios`.

Las propiedades enlazables y las propiedades enlazables asociadas también se pueden establecer en vistas nativas mediante la sintaxis de `Class.BindableProperty="value"`. Cada vista nativa se encapsula en una instancia de `NativeViewWrapper` específica de la plataforma, que deriva de la clase [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) . Establecer una propiedad enlazable o una propiedad enlazable adjunta en una vista nativa, el valor de propiedad transfiere al contenedor. Por ejemplo, se puede especificar un diseño horizontal centrado estableciendo `View.HorizontalOptions="Center"` en la vista nativa.

> [!NOTE]
> Tenga en cuenta que los estilos no se pueden usar con vistas nativas, ya que los estilos solo pueden tener como destino propiedades respaldadas por objetos `BindableProperty`.

Los constructores de widgets de Android normalmente requieren el objeto de `Context` de Android como argumento, y se puede hacer que esté disponible a través de una propiedad estática en la clase `MainActivity`. Por consiguiente, al crear un widget de Android en XAML, el objeto de `Context` se debe pasar generalmente al constructor del widget mediante el atributo de `x:Arguments` con una extensión de marcado de `x:Static`. Para obtener más información, vea [pasar argumentos a vistas nativas](#passing_arguments).

> [!NOTE]
> Tenga en cuenta que no es posible asignar un nombre a una vista nativa con `x:Name` en un proyecto de biblioteca de .NET Standard o en un proyecto de recursos compartidos (SAP). Si lo hace, se generará una variable del tipo nativo, lo que provocará un error de compilación. Sin embargo, las vistas nativas se pueden encapsular en `ContentView` instancias y recuperar en el archivo de código subyacente, siempre que se use un SAP. Para obtener más información, vea [hacer referencia a una vista nativa desde el código](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Enlaces nativos

Enlace de datos se utiliza para sincronizar una interfaz de usuario con su origen de datos y simplifica la forma en que una aplicación de Xamarin.Forms muestra e interactúa con sus datos. Siempre que el objeto de origen implementa la interfaz `INotifyPropertyChanged`, el marco de enlace inserta automáticamente los cambios en el objeto de *origen* en el objeto de *destino* , y los cambios en el objeto de *destino* se pueden insertar opcionalmente en el objeto de *origen* .

Las propiedades de las vistas nativas también pueden usar el enlace de datos. El siguiente ejemplo de código demuestra el enlace de datos usando propiedades de vistas nativas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

La página contiene una [`Entry`](xref:Xamarin.Forms.Entry) cuya propiedad [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) se enlaza a la propiedad `NativeSwitchPageViewModel.IsSwitchOn`. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en una nueva instancia de la clase `NativeSwitchPageViewModel` en el archivo de código subyacente, con la clase ViewModel que implementa la interfaz `INotifyPropertyChanged`.

La página también contiene un conmutador para cada plataforma. Cada conmutador nativo usa un enlace de [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) para actualizar el valor de la propiedad `NativeSwitchPageViewModel.IsSwitchOn`. Por lo tanto, cuando el modificador está desactivado, el `Entry` está deshabilitado y, cuando el modificador está activado, el `Entry` está habilitado. Las capturas de pantalla siguientes muestran cómo esta funcionalidad en cada plataforma:

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

Los enlaces bidireccionales se admiten automáticamente siempre que la propiedad nativa implementa `INotifyPropertyChanged`, o admite la observación de valor de clave (KVO) en iOS o es un `DependencyProperty` en UWP. Sin embargo, muchas vistas nativas no admiten la notificación de cambio de propiedad. Para estas vistas, puede especificar un valor de propiedad [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) como parte de la expresión de enlace. Esta propiedad debe establecerse en el nombre de un evento en la vista nativo que indica cuándo ha cambiado la propiedad de destino. A continuación, cuando cambia el valor del modificador nativo, se notifica a la clase `Binding` que el usuario ha cambiado el valor del modificador y que se actualiza el valor de la propiedad `NativeSwitchPageViewModel.IsSwitchOn`.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Pasar argumentos a vistas nativas

Los argumentos de constructor se pueden pasar a vistas nativas mediante el `x:Arguments` atributo con una extensión de marcado `x:Static`. Además, se puede llamar a los métodos de generador de vista nativa (`public static` métodos que devuelven objetos o valores del mismo tipo que la clase o estructura que define los métodos) especificando el nombre del método mediante el atributo `x:FactoryMethod` y sus argumentos mediante el atributo `x:Arguments`.

El ejemplo de código siguiente muestra ambas técnicas:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

El Factory Method [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) se usa para establecer la propiedad [`UILabel.Font`](xref:UIKit.UILabel.Font) en una nueva [`UIFont`](xref:UIKit.UIFont) en iOS. Los argumentos del método que son elementos secundarios del atributo `x:Arguments` especifican el nombre y el tamaño de la `UIFont`.

El Factory Method [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) se utiliza para establecer la propiedad [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) en una nueva [`Typeface`](xref:Android.Graphics.Typeface) en Android. El nombre y el estilo de la familia de `Typeface` se especifican mediante los argumentos del método que son secundarios del atributo `x:Arguments`.

El constructor [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) se usa para establecer la propiedad [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) en una nueva `FontFamily` en el plataforma universal de Windows (UWP). El nombre del `FontFamily` se especifica mediante el argumento de método que es un elemento secundario del atributo `x:Arguments`.

> [!NOTE]
> Argumentos deben coincidir con los tipos requeridos por el constructor o método generador.

Las capturas de pantalla siguientes muestran el resultado de especificar los argumentos de método y constructor del generador para establecer la fuente en diferentes vistas nativas:

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

Para obtener más información sobre cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Que hace referencia a vistas nativas desde código

Aunque no es posible asignar un nombre a una vista nativa con el atributo `x:Name`, es posible recuperar una instancia de vista nativa declarada en un archivo XAML de su archivo de código subyacente en un proyecto de acceso compartido, siempre que la vista nativa sea un elemento secundario de un [`ContentView`](xref:Xamarin.Forms.ContentView) que especifique un valor de atributo `x:Name`. A continuación, dentro de las directivas de compilación condicional en el archivo de código subyacente, debe:

1. Recupera el valor de la propiedad [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) y lo convierte en un tipo de `NativeViewWrapper` específico de la plataforma.
1. Recupere la propiedad `NativeViewWrapper.NativeElement` y conviértala en el tipo de vista nativa.

A continuación, se pueden invocar las API nativas en la vista nativa para realizar las operaciones deseadas. Este enfoque también ofrece la ventaja de que varias vistas nativas de XAML para distintas plataformas pueden ser elementos secundarios del mismo [`ContentView`](xref:Xamarin.Forms.ContentView). En el ejemplo de código siguiente se muestra esta técnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

En el ejemplo anterior, las vistas nativas de cada plataforma son elementos secundarios de [`ContentView`](xref:Xamarin.Forms.ContentView) controles, con el valor de atributo `x:Name` que se usa para recuperar el `ContentView` en el código subyacente:

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

Se tiene acceso a la propiedad [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) para recuperar la vista nativa ajustada como una instancia de `NativeViewWrapper` específica de la plataforma. A continuación, se tiene acceso a la propiedad `NativeViewWrapper.NativeElement` para recuperar la vista nativa como su tipo nativo. A continuación, se invoca la API nativa de la vista para llevar a cabo las operaciones deseadas.

Los botones nativos de iOS y Android comparten el mismo `OnButtonTap` controlador de eventos, porque cada botón nativo consume un `EventHandler` delegado en respuesta a un evento Touch. Sin embargo, el Plataforma universal de Windows (UWP) usa un `RoutedEventHandler`independiente que, a su vez, utiliza el controlador de eventos `OnButtonTap` en este ejemplo. Por lo tanto, cuando se hace clic en un botón nativo, se ejecuta el controlador de eventos `OnButtonTap`, que escala y gira el control nativo incluido en el [`ContentView`](xref:Xamarin.Forms.ContentView) denominado `contentViewTextParent`. Las capturas de pantalla siguientes muestran este que se producen en cada plataforma:

![](xaml-images/contentview.png "ContentView Containing a Native Control")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Creación de subclases de vistas nativas

Muchos iOS y Android vistas nativas no son adecuadas para crear instancias en XAML porque usan métodos, en lugar de propiedades, para configurar el control. Vistas nativas de subclase de contenedores que definen una API compatible con XAML más que usa las propiedades para el control de la instalación, y que usa eventos independientes de la plataforma es la solución a este problema. El ajustada nativas vistas pueden, a continuación, se coloca en un proyecto de activos compartidos (SAP) y rodeadas con directivas de compilación condicional, o colocar en los proyectos específicos de la plataforma y de XAML al que hace referencia en un proyecto de biblioteca de .NET Standard.

En el ejemplo de código siguiente se muestra una página de Xamarin.Forms que consume una subclase de vistas nativas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

La página contiene un [`Label`](xref:Xamarin.Forms.Label) que muestra los frutos elegidos por el usuario desde un control nativo. La `Label` enlaza a la propiedad `SubclassedNativeControlsPageViewModel.SelectedFruit`. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en una nueva instancia de la clase `SubclassedNativeControlsPageViewModel` en el archivo de código subyacente, con la clase ViewModel que implementa la interfaz `INotifyPropertyChanged`.

La página también contiene una vista de selector nativo para cada plataforma. Cada vista nativa muestra la colección de Fruits enlazando su `ItemSource` propiedad a la colección `SubclassedNativeControlsPageViewModel.Fruits`. Esto permite al usuario elegir una frutas, como se muestra en las capturas de pantalla siguiente:

![](xaml-images/sub-classed.png "Subclassed Native Views")

En iOS y Android los selectores nativos usar métodos para configurar los controles. Por lo tanto, deben ser una subclase estos selectores para exponer propiedades para que sean compatible con XAML. En el Plataforma universal de Windows (UWP), el `ComboBox` ya es compatible con XAML y, por tanto, no requiere la subclase.

### <a name="ios"></a>iOS

La implementación de iOS subclase la [`UIPickerView`](xref:UIKit.UIPickerView) vista y expone propiedades y un evento que se puede consumir fácilmente desde XAML:

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

La clase `MyUIPickerView` expone `ItemsSource` y `SelectedItem` propiedades, y un evento `SelectedItemChanged`. Un [`UIPickerView`](xref:UIKit.UIPickerView) requiere un modelo de datos de [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) subyacente, al que se tiene acceso mediante las propiedades y el evento de `MyUIPickerView`. La clase `PickerModel` proporciona el modelo de datos `UIPickerViewModel`:

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

La clase `PickerModel` proporciona el almacenamiento subyacente para la clase `MyUIPickerView`, a través de la propiedad `Items`. Cada vez que cambia el elemento seleccionado en el `MyUIPickerView`, se ejecuta el método [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) , que actualiza el índice seleccionado y activa el evento `ItemChanged`. Esto garantiza que la propiedad `SelectedItem` siempre devolverá el último elemento seleccionado por el usuario. Además, la clase `PickerModel` invalida los métodos que se usan para configurar la instancia de `MyUIPickerView`.

### <a name="android"></a>Android

La implementación de Android subclase la [`Spinner`](xref:Android.Widget.Spinner) vista y expone propiedades y un evento que se puede consumir fácilmente desde XAML:

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

La clase `MySpinner` expone `ItemsSource` y `SelectedObject` propiedades, y un evento `ItemSelected`. Los elementos mostrados por la clase `MySpinner` los proporciona el [`Adapter`](xref:Android.Widget.Adapter) asociado a la vista, y los elementos se rellenan en el `Adapter` cuando la propiedad `ItemsSource` se establece por primera vez. Cada vez que cambia el elemento seleccionado en la clase `MySpinner`, el controlador de eventos `OnBindableSpinnerItemSelected` actualiza la propiedad `SelectedObject`.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir vistas nativas en archivos XAML de Xamarin.Forms. Se pueden establecer las propiedades y los controladores de eventos en vistas nativas, y pueden interactuar con las vistas de Xamarin.Forms.

## <a name="related-links"></a>Vínculos relacionados

- [NativeSwitch (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
- [Paso de argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
